## [L-01] USE OF BLOCK.TIMESTAMP
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimNodeOp.sol#L49
```
uint256 elapsedSecs = (block.timestamp - rewardsStartTime);
```
Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), 
locking funds for periods of time, and various state-changing conditional statements that are time-dependent. 
Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts. References: SWC ID: 116
Recommended Mitigation Steps
Block timestamps should not be used for entropy or generating random numbers—i.e., they should not be the deciding factor (either directly or through some derivation) for winning a game or changing an important state.
Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, or enforcing expiry dates. 
It is sometimes recommended to use block.number and an average block time to estimate times; with a 10 second block time, 1 week equates to approximately, 60480 blocks. 
Thus, specifying a block number at which to change a contract state can be more secure, as miners are unable to easily manipulate the block number.

## [L-02] USE OF BLOCK.TIMESTAMP
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L226
```
staking.setRewardsStartTime(msg.sender, block.timestamp);
```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L279
```
if (block.timestamp - staking.getRewardsStartTime(msg.sender) < dao.getMinipoolCancelMoratoriumSeconds()) {
			revert CancellationTooEarly();
```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L356
```
if (startTime > block.timestamp) {
			revert InvalidStartTime();
		}
```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L394
```
if (endTime <= startTime || endTime > block.timestamp) {
			revert InvalidEndTime();
		}
```
Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), 
locking funds for periods of time, and various state-changing conditional statements that are time-dependent. 
Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts. References: SWC ID: 116
Recommended Mitigation Steps
Block timestamps should not be used for entropy or generating random numbers—i.e., they should not be the deciding factor (either directly or through some derivation) for winning a game or changing an important state.
Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, or enforcing expiry dates. 
It is sometimes recommended to use block.number and an average block time to estimate times; with a 10 second block time, 1 week equates to approximately, 60480 blocks. 
Thus, specifying a block number at which to change a contract state can be more secure, as miners are unable to easily manipulate the block number.

## [L-03] USE OF BLOCK.TIMESTAMP
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Oracle.sol#L40
```
timestamp = block.timestamp;
```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Oracle.sol#L59
```
if (timestamp < lastTimestamp || timestamp > block.timestamp) {
			revert InvalidTimestamp();
		}
```
Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), 
locking funds for periods of time, and various state-changing conditional statements that are time-dependent. 
Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts. References: SWC ID: 116
Recommended Mitigation Steps
Block timestamps should not be used for entropy or generating random numbers—i.e., they should not be the deciding factor (either directly or through some derivation) for winning a game or changing an important state.
Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, or enforcing expiry dates. 
It is sometimes recommended to use block.number and an average block time to estimate times; with a 10 second block time, 1 week equates to approximately, 60480 blocks. 
Thus, specifying a block number at which to change a contract state can be more secure, as miners are unable to easily manipulate the block number.

## [L-04] USE OF BLOCK.TIMESTAMP
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L40
```
setUint(keccak256("RewardsPool.RewardsCycleStartTime"), block.timestamp);
```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L41
```
setUint(keccak256("RewardsPool.InflationIntervalStartTime"), block.timestamp);
```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L60
```
return (block.timestamp - startTime) / dao.getInflationIntervalSeconds();
```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L84
```
uint256 inflationIntervalElapsedSeconds = (block.timestamp - getInflationIntervalStartTime());
```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L128
```
return (block.timestamp - startTime) / dao.getRewardsCycleSeconds();
```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L164
```
setUint(keccak256("RewardsPool.RewardsCycleStartTime"), block.timestamp);
```
Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), 
locking funds for periods of time, and various state-changing conditional statements that are time-dependent. 
Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts. References: SWC ID: 116
Recommended Mitigation Steps
Block timestamps should not be used for entropy or generating random numbers—i.e., they should not be the deciding factor (either directly or through some derivation) for winning a game or changing an important state.
Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, or enforcing expiry dates. 
It is sometimes recommended to use block.number and an average block time to estimate times; with a 10 second block time, 1 week equates to approximately, 60480 blocks. 
Thus, specifying a block number at which to change a contract state can be more secure, as miners are unable to easily manipulate the block number.

## [L-05] USE OF BLOCK.TIMESTAMP
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L206
```
if (time > block.timestamp) {
			revert InvalidRewardsStartTime();
		}
```
Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), 
locking funds for periods of time, and various state-changing conditional statements that are time-dependent. 
Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts. References: SWC ID: 116
Recommended Mitigation Steps
Block timestamps should not be used for entropy or generating random numbers—i.e., they should not be the deciding factor (either directly or through some derivation) for winning a game or changing an important state.
Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, or enforcing expiry dates. 
It is sometimes recommended to use block.number and an average block time to estimate times; with a 10 second block time, 1 week equates to approximately, 60480 blocks. 
Thus, specifying a block number at which to change a contract state can be more secure, as miners are unable to easily manipulate the block number.

## [L-06] USE OF BLOCK.TIMESTAMP
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L78
```
rewardsCycleEnd = (block.timestamp.safeCastTo32() / rewardsCycleLength) * rewardsCycleLength;
```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L89
```
uint32 timestamp = block.timestamp.safeCastTo32();
```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L120
```
if (block.timestamp >= rewardsCycleEnd_)
```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L128
```
uint256 unlockedRewards = (lastRewardsAmt_ * (block.timestamp - lastSync_)) / (rewardsCycleEnd_ - lastSync_);
```
Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), 
locking funds for periods of time, and various state-changing conditional statements that are time-dependent. 
Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts. References: SWC ID: 116
Recommended Mitigation Steps
Block timestamps should not be used for entropy or generating random numbers—i.e., they should not be the deciding factor (either directly or through some derivation) for winning a game or changing an important state.
Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, or enforcing expiry dates. 
It is sometimes recommended to use block.number and an average block time to estimate times; with a 10 second block time, 1 week equates to approximately, 60480 blocks. 
Thus, specifying a block number at which to change a contract state can be more secure, as miners are unable to easily manipulate the block number.

## [L-07] USE OF BLOCK.TIMESTAMP
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L127
```
require(deadline >= block.timestamp, "PERMIT_DEADLINE_EXPIRED");
```
Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), 
locking funds for periods of time, and various state-changing conditional statements that are time-dependent. 
Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts. References: SWC ID: 116
Recommended Mitigation Steps
Block timestamps should not be used for entropy or generating random numbers—i.e., they should not be the deciding factor (either directly or through some derivation) for winning a game or changing an important state.
Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, or enforcing expiry dates. 
It is sometimes recommended to use block.number and an average block time to estimate times; with a 10 second block time, 1 week equates to approximately, 60480 blocks. 
Thus, specifying a block number at which to change a contract state can be more secure, as miners are unable to easily manipulate the block number.

## [L-08] LOSS OF PRECISION DUE TO ROUNDING
URL: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L413
```
MinipoolManager.sol:
	uint256 avaxHalfRewards = avaxTotalRewardAmt / 2;
```

## [L-09] LOSS OF PRECISION DUE TO ROUNDING
URL: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L60
```
RewardsPool.sol:
	return (block.timestamp - startTime) / dao.getInflationIntervalSeconds();
```

## [L-10] LOSS OF PRECISION DUE TO ROUNDING
URL: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L128
```
RewardsPool.sol:
	return (block.timestamp - startTime) / dao.getRewardsCycleSeconds();
```

## [L-11] LOSS OF PRECISION DUE TO ROUNDING
URL: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L229
```
RewardsPool.sol:
	uint256 tokensPerMultisig = allotment / enabledCount;
```

## [L-12] LOSS OF PRECISION DUE TO ROUNDING
URL: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L78
```
TokenggAVAX.sol:
	rewardsCycleEnd = (block.timestamp.safeCastTo32() / rewardsCycleLength) * rewardsCycleLength;
```

## [L-13] LOSS OF PRECISION DUE TO ROUNDING
URL: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L78
```
TokenggAVAX.sol:
	rewardsCycleEnd = (block.timestamp.safeCastTo32() / rewardsCycleLength) * rewardsCycleLength;
```

## [L-14] REQUIRE() SHOULD BE USED INSTEAD OF ASSERT()
Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction’s available gas rather than returning it, 
as require()/revert() do. assert() should be avoided even past solidity version 0.8.0 as its documentation states that 
The assert function creates an error of type Panic(uint256). 
Properly functioning code should never create a Panic, not even on invalid external input. 
If this happens, then there is a bug in your contract which you should fix.
URL: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L83
```
File: TokenggAVAX.sol
assert(msg.sender == address(asset));
```
