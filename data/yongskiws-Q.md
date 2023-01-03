
### [NC-1] buffer overflow in the totalAssets function

Consider there is use of multiplication operator which can cause buffer overflow if (lastRewardsAmt_ * (block.timestamp - lastSync_)) exceeds uint256 maximum limit.

``` solidity
File: 2022-12-gogopool\contracts\contract\tokens\TokenggAVAX.sol
129: 	uint256 unlockedRewards = (lastRewardsAmt_ * (block.timestamp - lastSync_)) / (rewardsCycleEnd_ - lastSync_);
130: 	return totalReleasedAssets_ + unlockedRewards;
```

### [NC-2] loops are not limited to the syncRewards function

Consider there is use of division operator which can cause infinite iteration if rewardsCycleEnd is not divisible by rewardsCycleLength.

``` solidity
File: 2022-12-gogopool\contracts\contract\tokens\TokenggAVAX.sol
103: uint32 nextRewardsCycleEnd = ((timestamp + rewardsCycleLength) / rewardsCycleLength) * rewardsCycleLength;
104: 
105: 		lastRewardsAmt = nextRewardsAmt.safeCastTo192();
106: 		lastSync = timestamp;
107: 		rewardsCycleEnd = nextRewardsCycleEnd;
108: 		totalReleasedAssets = totalReleasedAssets_ + lastRewardsAmt_;
```

### [NC-3] Consider against reentrancy and unlimited ETH requests in the depositFromStaking&&withdrawForStaking function
consider using the IWAVAX.deposit() function which can trigger a reentrancy attack.
``` solidity
File: 2022-12-gogopool\contracts\contract\tokens\TokenggAVAX.sol
143: function depositFromStaking(uint256 baseAmt, uint256 rewardAmt) public payable onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
144: 		uint256 totalAmt = msg.value;
145: 		if (totalAmt != (baseAmt + rewardAmt) || baseAmt > stakingTotalAssets) {
146: 			revert InvalidStakingDeposit();
147: 		}
148: 
149: 		emit DepositedFromStaking(msg.sender, baseAmt, rewardAmt);
150: 		stakingTotalAssets -= baseAmt;
151: 		IWAVAX(address(asset)).deposit{value: totalAmt}();
152: 	}
153: 
```
consider use of the addition operator which can cause unlimited ETH requests if the totalAmt exceeds the uint256 maximum limit.

``` solidity
File: 2022-12-gogopool\contracts\contract\tokens\TokenggAVAX.sol
145: 		if (totalAmt != (baseAmt + rewardAmt) || baseAmt > stakingTotalAssets) {
146: 			revert InvalidStakingDeposit();
147: 		}
```

the withdrawForStaking function which calls the withdraw function on the WAVAX contract and the acceptWithdrawalAVAX function on the MinipoolManager contract. If these functions call back to the withdrawForStaking function before the function has finished executing, an unpredictable loop will occur.
uses the 'checks-effect-interactions' pattern to ensure that the effect of a function is done before calling another function.
``` solidity
File: c:\Users\pc\Desktop\code\2022-12-gogopool\contracts\contract\tokens\TokenggAVAX.sol
154: 	function withdrawForStaking(uint256 assets) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
155: 		if (assets > amountAvailableForStaking()) {
156: 			revert WithdrawAmountTooLarge();
157: 		}
158: 
159: 		emit WithdrawnForStaking(msg.sender, assets);
160: 
161: 		stakingTotalAssets += assets;
162: 		IWAVAX(address(asset)).withdraw(assets);
163: 		IWithdrawer withdrawer = IWithdrawer(msg.sender);
164: 		withdrawer.receiveWithdrawalAVAX{value: assets}();
165: 	}
```