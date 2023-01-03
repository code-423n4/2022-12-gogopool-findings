# Introduction

## Non-critical Summary

| ID  | Finding | Instances |
| --- | ------- | --------- |
| N-01    |  Redundant `return` statement      | 1         |

# Non-critical Findings

## \[N-01\] Redundant `return` statement

```solidity
contracts/contract/RewardsPool.sol
66: 	function getInflationAmt() public view returns (uint256 currentTotalSupply, uint256 newTotalSupply) {
...
77: 		return (currentTotalSupply, newTotalSupply); // <-- can be removed
```