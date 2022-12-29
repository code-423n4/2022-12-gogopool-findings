## 1 arrange variable which fits in slot :-

code snippet:-

/// @notice the effective start of the current cycle
	uint32 public lastSync;

	/// @notice the maximum length of a rewards cycle
	uint32 public rewardsCycleLength;

	/// @notice the end of the current cycle. Will always be evenly divisible by `rewardsCycleLength`.
	uint32 public rewardsCycleEnd;

	/// @notice the amount of rewards distributed in a the most recent cycle.
	uint192 public lastRewardsAmt;

chnage to :-
/// @notice the effective start of the current cycle
	uint43 public lastSync;

	/// @notice the maximum length of a rewards cycle
	uint42 public rewardsCycleLength;

	/// @notice the end of the current cycle. Will always be evenly divisible by `rewardsCycleLength`.
	uint43 public rewardsCycleEnd;

	/// @notice the amount of rewards distributed in a the most recent cycle.
	uint128 public lastRewardsAmt;                             
    
These four variables will fit two slots saves delpoy and gas Cost .Or use uint256 because  EVM works with 256bit/32byte words (debatable design decision). Every operation is based on these base units. If your data is smaller, further operations are needed to downscale from 256 bits to 32 bits or less than 256bits this operation cost more gas consumption . 