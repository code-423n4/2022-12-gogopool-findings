QA1: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L73
Instead of passing ``asset``as an argument, it is better to define it as a constant since the address for the WAVAX contract can be predetermined. 

QA2. https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L2
Lock all files with the most recent version of Solidity  0.8.17.

QA3: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L207-L209
The check of if-paused for ``TokenggAVAX`` should be done for the following important functions as well: 
``depositAVAX()``, ``withdrawAVAX()``, ``redeemAVAX()``, ``depositFromStaking()``, and ``withdrawForStaking()``. 

QA4: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L106
We need to ensure that ``receiveWithdrawalAVAX()`` can only be called by the ``TokenggAVAX`` contract or the ``Vault`` contract.
```
function receiveWithdrawalAVAX() external payable {
      assert(msg.sender == address(ggAVAX) || msg.sender == getContractAddress("Vault"));
}
```

QA5: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L239-L246
The implementation fails to conform to the documentation that says "If nodeID exists, only allow overwriting if node is finished or canceled``). It only checked the next status is ``MinipoolStatus.Prelaunch`` without checking the current status. For example, the current status might be MinipoolStatus.Withdrawable`` see the logic in ``requireValidStateTransition()``.
To fix, we need to explicitly check the current status as well:
```
if (minipoolIndex != -1) {
                       bytes32 statusKey = keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status"));
		       MinipoolStatus currentStatus = MinipoolStatus(getUint(statusKey));

                       if(currentStutus != MinipoolStaus.Finished && currentStutus != MinipoolStatus.Canceled) revert InvalidStateTransition(); // @audit: check current status explicitly!

			requireValidStateTransition(minipoolIndex, MinipoolStatus.Prelaunch);
        		resetMinipoolData(minipoolIndex);
			// Also reset initialStartTime as we are starting a whole new validation
			setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".initialStartTime")), 0);
		} else {
			minipoolIndex = int256(getUint(keccak256("minipool.count")));
			// The minipoolIndex is stored 1 greater than actual value. The 1 is subtracted in getIndexOf()
			setUint(keccak256(abi.encodePacked("minipool.index", nodeID)), uint256(minipoolIndex + 1));
			setAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".nodeID")), nodeID);
			addUint(keccak256("minipool.count"), 1);
		}
```

QA6: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L509-L510
Fail to call ``staking.decreaseMinipoolCount(owner);`` to decrease the minipool count. The fix is:

```
                Staking staking = Staking(getContractAddress("Staking"));
		staking.decreaseAVAXAssigned(owner, avaxLiquidStakerAmt);
		staking.decreaseMinipoolCount(owner);
 
```

QA7: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L165
The function ``increaseRewardsCycleCount()`` assumes that ``getRewardsCyclesElapsed() = 1``, this assumption might not be true. We should implement `increaseRewardsCycleCount()`` such that its correctness does not depend on this assumption, and will work regardless how many reward cycles have passed.
```

function increaseRewardsCycleCount() internal {
		addUint(keccak256("RewardsPool.RewardsCycleCount"), getRewardsCyclesElapsed());
}


```

