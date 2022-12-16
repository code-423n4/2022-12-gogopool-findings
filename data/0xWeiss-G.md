1 VARIABLE PACKING IN LINES: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L46-L53

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



2 INCREMENT AND DECREMENT OF VARIABLES CAN BE OPTIMIZED IN LINES: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L149 AND https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L160 
It is cheaper to write: stakingTotalAssets = stakingTotalAssets  + assets;  than stakingTotalAssets += assets;

SAME optimization can be done in lines: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L245 and https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L252