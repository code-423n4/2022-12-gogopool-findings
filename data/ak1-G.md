1. MinipoolManager.sol#L168-L170

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L168-L170

below block code can be removed to save gas since the bool default value is false.
   
    		} else {
			isValid = false;
		}

2. ClaimNodeOp.sol
 
rewardsPool.getRewardsCycleCount() can be get once and used in following places.

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimNodeOp.sol#L61
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimNodeOp.sol#L65
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimNodeOp.sol#L68

3. Too many time requireValidStaker is used in createMinipool function.

some places are https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L220-L227

		Staking staking = Staking(getContractAddress("Staking"));
		staking.increaseMinipoolCount(msg.sender);
		staking.increaseAVAXStake(msg.sender, msg.value);
		staking.increaseAVAXAssigned(msg.sender, avaxAssignmentRequest);


		if (staking.getRewardsStartTime(msg.sender) == 0) {
			staking.setRewardsStartTime(msg.sender, block.timestamp);
		}

I have seen in other places too. due to time constraint I am not able to detail all the place.

We suggest to refactor the codes to minimize the valid staker check.

 