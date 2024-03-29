### 1 VARIABLE PACKING IN LINES: 

#### Impact
Issue Information: [G001]( https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L46-L53)

#### Findings:
Instead of instanciating the units as 256, find the bits that can work for the length of your variables and pack them.
Like this:
struct Staker {
		address stakerAddr;
		uint16 ggpStaked;
		uint16 avaxStaked;
		uint32 avaxAssigned;
		uint32 avaxAssignedHighWater;
		uint8 minipoolCount;
		uint8 rewardsStartTime;
		uint16 ggpRewards;
		uint16 lastRewardsCycleCompleted;
	}
As said, not sure if the bits are correct for every variable, but there can be room for improvement

### 2 INCREMENT AND DECREMENT OF VARIABLES CAN BE OPTIMIZED: 

#### Impact
Issue Information: [G002]( https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L149)

#### Findings:
gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L149 AND https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L160 
It is cheaper to write: stakingTotalAssets = stakingTotalAssets  + assets;  than stakingTotalAssets += assets;

The same optimization can be done in lines: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L245 and https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L252




### 3 FOR LOOP GAS OPTIMIZATION:

#### Findings:
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L74
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L215
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L230
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Ocyticus.sol#L61
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L428-L432
It is cheaper to add unchecked when it does not over/underflow
Just like this:

BEFORE:
	for (uint256 i = 0; i < inflationIntervalsElapsed; i++) {
			newTotalSupply = newTotalSupply.mulWadDown(inflationRate);
		}

AFTER:
for (uint256 i = 0; i < inflationIntervalsElapsed; ) {
	newTotalSupply = newTotalSupply.mulWadDown(inflationRate);
    unchecked {   ++ i ; }        
}


### 4 DOBLE OPTIMIZATION USING UNCHEKED AND ++in front 

#### Findings:
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L619-L623 AND IN: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L428-L431

4.1 Use unchecked in the for loop in line: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L619, AND https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L428, the same as issue number 3 instantiated before.
4.2 It is cheaper to use ++total than total++ in line https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L623 AND https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L431


