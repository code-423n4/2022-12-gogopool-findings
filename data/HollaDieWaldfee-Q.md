# GoGoPool Low Risk and Non-Critical Issues
## Summary
| Risk      | Title | File | Instances
| ----------- | ----------- | ----------- | ----------- |
| L-00      | Contracts inherit from `Base` but do not set `version` variable | - | 2 |
| L-01      | Function should emit event | - | 8 |  
| L-02      | It should be possible to pause / unpause contracts individually | Ocyticus.sol | 1 |  
| L-03      | `NewRewardsCycleStarted` event emits wrong amount | RewardsPool.sol | 1 |  
| L-04      | `Staking.restakeGGP` function should have `whenNotPaused` modifier | Staking.sol | 1 |  
| L-05      | `Staking.getStaker` function does not set `avaxAssignedHighWater` value | Staking.sol | 1 |  
| L-06      | Function access modifier can be more restrictive | Staking.sol | 2 |  
| N-00      | `amountAvailableForStaking` should return 0 instead of revert due to underflow | TokenggAVAX.sol | 1 |  
| N-01      | Remove `TODO` comments | - | 3 |  
| N-02      | Remove unnecessary imports | - | 2 |  
| N-03      | "Data Storage Schema" comment is missing entries | Staking.sol | 1 |  
| N-04      | Use `require` instead of `assert` | TokenggAVAX.sol | 1 |  
| N-05      | Use `requireValidStaker` instead of `getIndexOf` | Staking.sol | 6 |  


## [L-00] Contracts inherit from `BaseAbstract` but do not set `version` variable
The `BaseAbstract` contract has a `version` variable that should be set in the child contracts. Some child contracts set `version` to `1`. Others fail to set the `version` variable which means it is set to `0`.  

This can cause issues in any components integrating with the GoGoPool protocol that check the version of the contracts.  

These are the contracts that do not but should set the `version` variable:  

[https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Ocyticus.sol#L10](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Ocyticus.sol#L10)  

[https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L24](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L24)  

## [L-01] Function should emit event
Functions that make important changes to the protocol should emit events such that other components can listen for them on the blockchain.  

This allows for more robust monitoring of the application.  

### Ocyticus.sol
There are 5 functions that should emit an event.  

`addDefender`:  
[https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Ocyticus.sol#L27-L29](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Ocyticus.sol#L27-L29)  

`removeDefender`:  
[https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Ocyticus.sol#L32-L34](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Ocyticus.sol#L32-L34)  

`pauseEverything`:  
[https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Ocyticus.sol#L37-L43](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Ocyticus.sol#L37-L43)  

`resumeEverything`:  
[https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Ocyticus.sol#L47-L52](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Ocyticus.sol#L47-L52)  

`disableAllMultisigs`:  
[https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Ocyticus.sol#L55-L67](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Ocyticus.sol#L55-L67)  

### MinipoolManager.sol
The `withdrawMinipoolFunds` function changes the status of a minipool.  
So it should emit the `MinipoolStatusChangedEvent` like the other functions that change the minipool status.  

[https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L287-L303](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L287-L303)  

### ClaimNodeOp.sol
There is 1 function that should emit an event.  

`calculateAndDistributeRewards`:  
[https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimNodeOp.sol#L56-L85](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimNodeOp.sol#L56-L85)  

## [L-02] It should be possible to pause / unpause contracts individually
The current functionality to pause contracts is located in `Ocyticus.sol`.  
There you can see that it is only possible to pause all pausable contracts at once using the `pauseEverything` function ([https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Ocyticus.sol#L37-L43](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Ocyticus.sol#L37-L43)).  

It should be possible to pause contracts individually.  
Specifically the `TokenggAVAX` contract can in some cases still be unpaused while `MinipoolManager` and `Staking` are paused such that users can still redeem their shares.  

Similarly it should be possible to unpause contracts individually.  

## [L-03] `NewRewardsCycleStarted` event emits wrong amount
The `RewardsPool.startRewardsCycle` function emits a `NewRewardsCycleStarted` event ([https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L161](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L161)).  
However the event uses the `RewardsCycleTotalAmount` before it is updated in the `inflate` function.  

The updated amount is the amount that is actually distributed as reward so it should be the amount that is emitted as well.  

So emitting the event should take place after `inflate` is called.  

## [L-04] `Staking.restakeGGP` function should have `whenNotPaused` modifier
When the `Staking` contract is paused, `Staking.stakeGGP` and `Staking.withdrawGGP` cannot be called because they have the `whenNotPaused` modifier:  

[https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L319](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L319)  

[https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L358](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L358)  

The issue is that the `Staking.restakeGGP` function ([https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L328](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L328)) does not have this modifier which allows stakers to restake their GGP rewards using the `ClaimNodeOp.claimAndRestake` function when the `Staking` contract is paused.  

This behavior is inconsistent because pausing the `Staking` contract does not actually ensure that the staked GGP amounts do not change.  

So you should add the `whenNotPaused` modifier to the `Staking.restakeGGP` function.  

This ensures that by calling `ClaimNodeOp.claimAndRestake`, stakers can only withdraw their rewards but not restake them.  

## [L-05] `Staking.getStaker` function does not set `avaxAssignedHighWater` value
The `Staking.getStaker` function ([https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L405-L414](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L405-L414)) returns all information about a staker.  

However it does not set the `avaxAssignedHighWater` variable of the `Staker` struct.  
This means that `staker.avaxAssignedHighWater` will always equal zero which can be the wrong value, leading to errors in any components that rely on this value.  

You should add the following line to the `Staking.getStaker` function:  

```solidity
staker.avaxAssignedHighWater = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssignedHighWater")));
```

## [L-06] Function access modifier can be more restrictive
The `Staking.increaseAVAXAssignedHighWater` and `Staking.resetAVAXAssignedHighWater` functions currently make use of the `onlyRegisteredNetworkContract` modifier:  

[https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L156](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L156)  

[https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L163](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L163)  

Both functions are only called in one other contract so they can use a more restrictive access modifier.  

`Staking.increaseAVAXAssignedHighWater` should use `onlySpecificRegisteredContract("MinipoolManager", msg.sender)`.  

`Staking.resetAVAXAssignedHighWater` should use `onlySpecificRegisteredContract("ClaimNodeOp", msg.sender)`.  

## [N-00] `amountAvailableForStaking` should return 0 instead of revert due to underflow
The `amountAvailableForStaking` function ([https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L132-L140](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L132-L140)) in `TokenggAVAX.sol` calculates:  
```solidity
return totalAssets_ - reservedAssets - stakingTotalAssets;
```

This calculation can revert due to underflow because users can withdraw so much funds that `stakingTotalAssets` is equal to `totalAssets_`. So if `reservedAssets` is subtracted as well this causes an underflow.  

You should add this check above the line that can underflow:  

```solidity
if (totalAssets_ < reservedAssets + stakingTotalAssets) {
    return 0;
}
```

It looks like your existing code does not expect the `amountAvailableForStaking` function to revert and actually expects it to return `0` instead.  

There is no security risk when the code reverts due to underflow but making it return `0` makes the code cleaner.  

## [N-01] Remove `TODO` comments
`TODO` comments left over from development should not stay in production code.  

[https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseAbstract.sol#L6-L8](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseAbstract.sol#L6-L8)  

[https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L412](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L412)  

[https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L203](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L203)  

## [N-02] Remove unnecessary imports
Imports that are not actually used should be removed to make the code cleaner and accurately show the dependencies.  

[https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L7](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L7)  

[https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L8](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L8)  

## [N-03] "Data Storage Schema" comment is missing entries
In the `Staking.sol` file there is a "Data Storage Schema" comment that explains the storage schema of a "staker" ([https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L15-L27](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L15-L27)).  

However there are missing entries (`minipoolCount`, `rewardsStartTime`, `ggpRewards`, `lastRewardsCycleCompleted`).  

Add these entries to complete the storage schema.  

## [N-04] Use `require` instead of `assert`
The `receive` function in `TokenggAVAX` ([https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L82-L84](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L82-L84)) uses `assert` to make sure that the `msg.sender` is the `WAVAX` token.  

The reason you should use `require` instead of `assert` is that `assert` consumes all remaining Gas if the condition is not met, whereas `require` returns the remaining Gas to the caller.  

By using `require`, if someone sends AVAX mistakenly, his remaining Gas will be returned.  

## [N-05] Use `requireValidStaker` instead of `getIndexOf`
There are 6 instances where `getIndexOf` is used and should be replaced.  
Instead you should use `requireValidStaker` which reverts if the staker address is invalid.  

[https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L127](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L127)  

[https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L150](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L150)  

[https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L174](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L174)  

[https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L197](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L197)  

[https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L218](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L218)  

[https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L241](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L241)  

