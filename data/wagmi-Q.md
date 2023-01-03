# Summary

| Id | Title |
| -- | ----- |
| 1 | Funds are locked if Rialto use function `finishFailedMinipoolByMultisig()` |
| 2 | Division before mul in many places reduce precision |
| 3 | Modifier `onlyRegisteredNetworkContract` is not worked as expected |
| 4 | No need to use `mstore` in function `getStakers()` |

# 1. Funds are locked if Rialto use function `finishFailedMinipoolByMultisig()`

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L528

## Detail
Function `finishFailedMinipoolByMultisig()` did not transfer any funds or doing any data change, only updating state of minipool to `Finished`. In the comment, it said that it used to finish error pool. 
However, if minipool is at state `Error`, it still has AVAX stored in Vault. If it just update state to `Finished`, this amount of AVAX is locked and cannot return to owner.

Even though this issue is loss of funds but since it can only happen by Rialto calling so I put it in Low.

## Recommendation
Allowing Rialto to withdraw funds and maybe return to owner later.


# 2. Division before multiply reduce calculation precision

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimNodeOp.sol#L70-L72

## Detail
In calculation block where division is done before multiplication, it will reduce the precision. For example
```solidity
uint256 percentage = ggpEffectiveStaked.divWadDown(totalEligibleGGPStaked);
uint256 rewardsCycleTotal = getRewardsCycleTotal();
uint256 rewardsAmt = percentage.mulWadDown(rewardsCycleTotal);
```

The above codeblock does
```solidity
(ggpEffectiveStaked / totalEligibleGGPStaked) * rewardsCycleTotal
```

When it can be done like this
```solidity
(ggpEffectiveStaked * rewardsCycleTotal) / totalEligibleGGPStaked
```

# 3. Modifier `onlyRegisteredNetworkContract` is not worked as expected

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L28

## Detail
As the name of it, it should only allow registered network contract to call functions. However, in the implementation, it allow guardian to call too.
```solidity
// @audit should be guardianOrRegisteredContract
modifier onlyRegisteredNetworkContract() { 
    if (booleanStorage[keccak256(abi.encodePacked("contract.exists", msg.sender))] == false && msg.sender != guardian) {
        revert InvalidOrOutdatedContract();
    }
    _;
}
```

# 4. No need to use `mstore` in function `getStakers()`
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L436

## Details

`stakers` list are resized to `max-offset` before the loop. And it's guaranteed to fill all list, which means `total` will always be equal to `max-offset`. So there is no need to do the `mstore(stakers, total)`.
```solidity
stakers = new Staker[](max - offset);
uint256 total = 0;
for (uint256 i = offset; i < max; i++) {
    Staker memory s = getStaker(int256(i));
    stakers[total] = s;
    total++;
}
// Dirty hack to cut unused elements off end of return value (from RP)
// solhint-disable-next-line no-inline-assembly
assembly {
    mstore(stakers, total) // @audit no need this
}
```
