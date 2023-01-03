### [Gas-01] Some ```public``` and ```view``` functions like ```getAVAXAssigned()```, ```getAVAXAssignedHighWater()```, ```getMinipoolCount()```, ```getRewardsStartTime()```, ```getGGPRewards()```, ```getLastRewardsCycleCompleted()``` all can optimizable. 

Here above all functions take ```address stakerAddr``` as parameter and check index of stakerAddr where if staker is not included so return index will be -1.
But without checking that these all above function call ```getUint()``` to get coresponding info associated with that staker, as staker not exists so return value will be default.

If before calling ```getUint()``` if all those function make sure presence staker then these wrong default data will not receive, some gas can be saved

For that purpose(Whether staker address is included or not) there already a function exists i.e ```requireValidStaker()```, so simply replacing ```getIndexOf()``` by  ```requireValidStaker()``` on above those functions get our job done.  

*Instances(6)*
```solidity
File :
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L126
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L152
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L173
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L196
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L217
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L240
```



### [Gas-02] ```rewardsPool.getRewardsCycleCount()``` can be cached
*Instances(2)*
```solidity
File : ClaimNodeOp.sol

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L65
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L68

```

### [Gas-03] Divided by 2 should always be Bit Shifted instead of Division

*Instances(1)*
```solidity
File : MinipoolManager.sol
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L413
```

### [Gas-04] By unchecking arithmatic operations that could never be Over/Underflow, we can save gave gas
*Instances(8)*
```solidity
File : MinipoolManager.sol
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L619
```
```solidity
File : MultisigManager.sol
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L84
```
```solidity
File : Ocyticus.sol
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L61
```
```solidity
File : RewardsPool.sol
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L74
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L215
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L230
```
```solidity
File : Staking.sol
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L428
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L431
```

