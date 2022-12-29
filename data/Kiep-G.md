### Class : RewardsPool.sol
### Line : 74-76

` for (uint256 i = 0; i < inflationIntervalsElapsed; i++) {: `

This loop iterates over a variable number of iterations, which can be expensive in terms of gas. If possible, try to refactor the code to minimize the number of iterations in the loop, or to avoid the loop altogether.

### Solution 

To minimize the number of iterations in a loop, you can refactor your code to calculate the desired result using a mathematical formula rather than a loop. For example, instead of this loop:

` for (uint256 i = 0; i < inflationIntervalsElapsed; i++) {
  newTotalSupply = newTotalSupply.mulWadDown(inflationRate);
} `

You could use this formula:

` newTotalSupply = newTotalSupply.mulWadDown(inflationRate.pow(inflationIntervalsElapsed)); `

***