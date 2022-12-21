# GAS
## ++I/I++ SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR-LOOP AND WHILE-LOOPS.
### Summary
In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.

### Github Permalinks

* [RewardsPool.sol#Line74](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L74)

* [RewardsPool.sol#Line215](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L215)

* [Ocyticus.sol#Line61](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L61)

### Mitigation
  The code should change to:
```
    for (uint256 i; i < numIterations;) {
      // ...
      unchecked { ++i; }
    }
    //The risk of overflow is inexistent for a uint256 here.
```


## Variables should not be initialized as zero.
### Summary 
It cost more gas to initialize a variable to zero than to let the default of zero be applied to it by solidity.

##  Github
* [RewardsPool.sol#Line141](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L141)

### Mitigation 
 Uinitialize the variable to save some gas on deployment.

``` 
  uint256 contractRewardsTotal;
```