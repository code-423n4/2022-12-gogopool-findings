# variable can be declared as constant

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L44

```solidity
uint32 public rewardsCycleLength;
```
as it also be init as 14 days, so it should be declared as constant

```solidity
uint32 public constant rewardsCycleLength = 14 days;
```