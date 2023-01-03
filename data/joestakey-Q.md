## Summary
### Low Risk
|      | Issue                                                                                                                             |
|------|-----------------------------------------------------------------------------------------------------------------------------------|
| L-01 | `tokenggAVAX.withdrawAVAX()` can revert for last user , preventing a user to withdraw all their rewards before `rewardsCycleEnd`. |
| L-02 | use require instead of assert                                                                                                     |
| L-03 | Users should be able to withdraw `AVAX` even when `tokenGGAvax` is paused                                                         |
| L-04 | Wrong comment                                                                                                                     |
| L-05 | 1000 AVAX is not enough to create a minipool, creating a mismatch with the documentation                                          |
| L-06 | If a function should only be called off-chain, it should be enforced at contract level                                            |
| L-07 | Add more checks to `setClaimingContractPct`                                                                                       |

### Non Critical
|      | Issue                                                                                 |
|------|---------------------------------------------------------------------------------------|
| N-01 | avoid shadowing local variables                                                       |
| N-02 | remove open TODOs                                                                     |


## Low
## [L‑01] `tokenggAVAX.withdrawAVAX()` can revert for last user , preventing a user to withdraw all their rewards before `rewardsCycleEnd`.



`totalReleasedAssets` only includes the rewards when the cycle ends.

```solidity
88: function syncRewards() public {
89: 		uint32 timestamp = block.timestamp.safeCastTo32();
90: 
91: 		if (timestamp < rewardsCycleEnd) {
92: 			revert SyncError();
93: 		}
94: 
...
107: 		totalReleasedAssets = totalReleasedAssets_ + lastRewardsAmt_;
108: 		emit NewRewardsCycle(nextRewardsCycleEnd, nextRewardsAmt);
109: 	}
```

If a user owns the majority of the shares (or all of them) 
, and try to call `withdrawAVAX()` with `assets == totalAssets()`

```solidity
180: 	function withdrawAVAX(uint256 assets) public returns (uint256 shares) {
181: 		shares = previewWithdraw(assets); // No need to check for rounding error, previewWithdraw rounds up.
182: 		beforeWithdraw(assets, shares);
```

```solidity
142: 	function previewWithdraw(uint256 assets) public view virtual returns (uint256) {
143: 		uint256 supply = totalSupply; // Saves an extra SLOAD if totalSupply is non-zero.
144: 
145: 		return supply == 0 ? assets : assets.mulDivUp(supply, totalAssets());
146: 	}
```

`totalAssets()` will return `totalReleasedAssets_ + unlockedRewards`, which is greater than `totalReleasedAssets`

```
solidity
113: 	function totalAssets() public view override returns (uint256) {
114: 		// cache global vars
115: 		uint256 totalReleasedAssets_ = totalReleasedAssets;
116: 		uint192 lastRewardsAmt_ = lastRewardsAmt;
117: 		uint32 rewardsCycleEnd_ = rewardsCycleEnd;
118: 		uint32 lastSync_ = lastSync;
119: 
120: 		if (block.timestamp >= rewardsCycleEnd_) {
121: 			// no rewards or rewards are fully unlocked
122: 			// entire reward amount is available
123: 			return totalReleasedAssets_ + lastRewardsAmt_;
124: 		}
125: 
126: 		// rewards are not fully unlocked
127: 		// return unlocked rewards and stored total
128: 		uint256 unlockedRewards = (lastRewardsAmt_ * (block.timestamp - lastSync_)) / (rewardsCycleEnd_ - lastSync_);
129: 		return totalReleasedAssets_ + unlockedRewards;
130: 	}
```

which will make the [call to beforeWithdraw(assets == totalAssets(), shares)](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L182) revert with an underflow error as `totalReleasedAssets < totalAssets()`

```solidity
241: 	function beforeWithdraw(
242: 		uint256 amount,
243: 		uint256 /* shares */
244: 	) internal override {
245: 		totalReleasedAssets -= amount;
246: 	}
```

## Impact

While the rewards will be available to the user at the end of the cycle, it is stated on the [Notion website](https://multisiglabs.notion.site/Audit-Parameters-3ad0ab58f96743c1b8087d131413a9bc) that TokenggAVAX should:
```
streams rewards to stakers continuously on a 14 day cycle
```

This is broken here, as the user has to wait until the end of the reward cycle to retrieve their reward, ie several days, and cannot retrieve the rewards at the time they want. 

However, this issue:
- only occur in an extreme complete withdrawal of the contract.
- only happens if there is no further deposits

For these reasons making the issue rather unlikely to happen, I consider it a Low severity issue.


## Tools Used

Manual Analysis

## Mitigation

A possible solution is to change `beforeWithdraw()` 

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L165
```diff
241: 	function beforeWithdraw(
242: 		uint256 amount,
243: 		uint256 /* shares */
244: 	) internal override {
+    if (amount >= totalReleasedAsset) {
+        lastRewardAmount -= totalAssets() - totalReleasedAssets;
+        lastSync = block.timestamp;
+        totalReleasedAssets = totalAssets() - amount;
+    } else {
245: 		totalReleasedAssets -= amount;
+ }
246: 	}
```

## [L‑02] use require instead of assert

As per Solidity's [documentation](https://docs.soliditylang.org/en/v0.8.17/control-structures.html?highlight=assert#panic-via-assert-and-error-via-require):

```
The assert function creates an error of type Panic(uint256). The same error is created by the compiler in certain situations as listed below.

Assert should only be used to test for internal errors, and to check invariants. Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix
```

### Code affected

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L83
```solidity
82: 	receive() external payable {
83: 		assert(msg.sender == address(asset));
84: 	}
```

### Recommendation

```diff
82: 	receive() external payable {
-83: 		assert(msg.sender == address(asset));
+83: 		require(msg.sender == address(asset));
84: 	}
```

## [L‑03] Users should be able to withdraw `AVAX` even when `tokenGGAvax` is paused

A paused `tokenGGAvax` should not allow users to mint anymore `ggAVAX`, but it currently also prevent users from withdrawing their `AVAX`.
This means users funds are essentially frozen when the contract is paused.

### Code affected

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L233-L239
```solidity
233: function previewWithdraw(uint256 assets) public view override whenTokenNotPaused(assets) returns (uint256) {
234: 		return super.previewWithdraw(assets);
235: 	}
236: 
237: 	function previewRedeem(uint256 shares) public view override whenTokenNotPaused(shares) returns (uint256) {
238: 		return super.previewRedeem(shares);
239: 	}
```

### Recommendation

Remove the `whenTokenNotPaused` modifier from these functions.

## [L‑04] Wrong comment

in `MinipoolManager`, `delegationFee` is used to determine the Percentage of delegation fee in units of ether, but the values given in the comment are incorrect: 0.2 ether corresponds to 20%, not 2% - as per what is used across the protocol (see values in ProtocolDAO.sol)

### Code affected

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/MinipoolManager.sol#L194
```solidity
194: /// @param delegationFee Percentage delegation fee in units of ether (2% is 0.2 ether)
```

### Recommendation

```diff
-194: /// @param delegationFee Percentage delegation fee in units of ether (2% is 0.2 ether)
+194: /// @param delegationFee Percentage delegation fee in units of ether (20% is 0.2 ether)
```

## [L‑05] 1000 AVAX is not enough to create a minipool, creating a mismatch with the documentation

Currently, sending 1_000 AVAX while calling `createMinipool` will revert, while docs specify this amount: `GoGoPool allows users with hardware and 1000 AVAX to create a validator node in conjunction with funds deposited by liquid staking users.`

This is because a strict equality is used, checking the amount provided is `< dao.getMinipoolMinAVAXAssignment()`, which is [equal](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ProtocolDAO.sol#L50) to `1_000 AVAX`

### Code affected

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/MinipoolManager.sol#L207-L214
```solidity
207: if (
208: 			// Current rule is matched funds must be 1:1 nodeOp:LiqStaker
209: 			msg.value != avaxAssignmentRequest ||
210: 			avaxAssignmentRequest > dao.getMinipoolMaxAVAXAssignment() ||
211: 			avaxAssignmentRequest < dao.getMinipoolMinAVAXAssignment()
212: 		) {
213: 			revert InvalidAVAXAssignmentRequest();
214: 		}
```

### Recommendation

```diff
207: if (
208: 			// Current rule is matched funds must be 1:1 nodeOp:LiqStaker
209: 			msg.value != avaxAssignmentRequest ||
-210: 			avaxAssignmentRequest > dao.getMinipoolMaxAVAXAssignment() ||
-211: 			avaxAssignmentRequest < dao.getMinipoolMinAVAXAssignment()
+210: 			avaxAssignmentRequest >= dao.getMinipoolMaxAVAXAssignment() ||
+211: 			avaxAssignmentRequest <= dao.getMinipoolMinAVAXAssignment()
212: 		) {
213: 			revert InvalidAVAXAssignmentRequest();
214: 		}
```

## [L‑06] If a function should only be called off-chain, it should be enforced at contract level

As per comments, `getGGPPriceInAVAXFromOneInch()` should never be called off-chain.
This should be enforced at contract level by using `msg.sender == tx.origin`, to prevent smart contracts from calling this function on-chain.

### Code affected

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/Oracle.sol#L33-L36
```solidity
33: 	/// @dev NEVER call this on-chain, only off-chain oracle should call, then send a setGGPPriceInAVAX tx
34: 	/// @return price of ggp in AVAX
35: 	/// @return timestamp representing the current time
36: 	function getGGPPriceInAVAXFromOneInch() external view returns (uint256 price, uint256 timestamp) {
```

### Recommendation

```diff
33: 	/// @dev NEVER call this on-chain, only off-chain oracle should call, then send a setGGPPriceInAVAX tx
34: 	/// @return price of ggp in AVAX
35: 	/// @return timestamp representing the current time
36: 	function getGGPPriceInAVAXFromOneInch() external view returns (uint256 price, uint256 timestamp) {
+       require(msg.sender == tx.origin);
```

## [L‑07] Add more checks to `setClaimingContractPct`

`valueNotGreaterThanOne` checks the percentage that the `claimingContract` is set at is less than 100%, but not that the total of percentage claimed is 100%.
These values are used in `RewardsPool.getClaimingContractDistribution()`, which is then used in `RewardsPool.startRewardsCycle()`. If the total is less than 100%, [this line](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L174) will revert.

### Code affected

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/ProtocolDAO.sol#L107-L109
```solidity
107: function setClaimingContractPct(string memory claimingContract, uint256 decimal) public onlyGuardian valueNotGreaterThanOne(decimal) {
108: 		setUint(keccak256(abi.encodePacked("ProtocolDAO.ClaimingContractPct.", claimingContract)), decimal);
109: 	}
```

### Recommendation

Add further checks to ensure the total claiming distribution is equal to `RewardsPool.getRewardsCycleTotalAmt()`

## Non-Critical
## [N‑01] avoid shadowing of variables

Avoiding variable name collisions avoids confusion

### Code affected

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L73

```solidity
73: __ERC4626Upgradeable_init(asset, "GoGoPool Liquid Staking Token", "ggAVAX"); //@audit - asset is shadowing a storage variable
```

## [N‑02] remove open TODOs


### Code affected

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseAbstract.sol#L6
```solidity
6: // TODO remove this when dev is complete
```

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol.sol#L412
```solidity
412: // TODO Revisit this logic if we ever allow unequal matched funds
```

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L203
```solidity
203: // TODO cant use onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) since we also call from increaseMinipoolCount. Wat do?
```