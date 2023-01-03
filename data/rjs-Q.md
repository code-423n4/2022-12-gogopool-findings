# Introduction

## Low Risk Summary

| ID  | Finding | Instances |
| --- | ------- | --------- |
| L-01    |        |     N/A      |

## Non-critical Summary

| ID  | Finding | Instances |
| --- | ------- | --------- |
| N-01    |        | N/A          |

# Low Risk Findings

## \[L-01\] 


# Non-critical Findings

## \[N-01\] Redundant `return` statement

```solidity
contracts/contract/RewardsPool.sol
66: 	function getInflationAmt() public view returns (uint256 currentTotalSupply, uint256 newTotalSupply) {
...
77: 		return (currentTotalSupply, newTotalSupply); // <-- can be removed
```