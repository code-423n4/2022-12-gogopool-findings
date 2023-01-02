
## 1 . Balance of token check must before emitting events .:-

In transferAVAX() and transferToken() emitting the events before checking sufficient amount of tokens contract have .
it will emit even if the tokencontractFrom doesn't have enough amount to transfer. It's a good practice to check whether  tokencontractFrom have sufficient tokens to transfer .

code snippet:-
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L183
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L95



## 2. Access control function should be in top before than other fucntion :-

Summary :-
In cancelMinipool() first get staking and protocolDAO then access control function onlyOwner(). So rearrange the access control function first in function it's a good practice . 

code snippet:-
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L276
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L277


## 3 arrange variable which fits in slot :-

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
    
These four variables will fit one slot saves delpoy and gas Cost .Or use uint256 because  EVM works with 256bit/32byte words (debatable design decision). Every operation is based on these base units. If your data is smaller, further operations are needed to downscale from 256 bits to 32 bits or less than 256bits this operation cost more gas consumption . 
