
## Gas Optimizations

|       | Issue                                       | Instances |
| ----- |:------------------------------------------- |:---------:|
| GAS-1 | Using `unchecked` math operations saves gas |    13     |


**Apply `unchecked` to for-loop index increment**

Structure for-loop in the below optimized style and apply `unchecked` to for-loop index increment:
```solidity
for (uint256 index; index < forLoopLength;){
  ...carry out operations...
  unchecked { ++index; }
}
```


[Link to code](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol)
```solidity
File: contracts/contract/MinipoolManager.sol
  
619:       for (uint256 i = offset; i < max; i++) {
```

[Link to code](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol)
```solidity
File: contracts/contract/MultisigManager.sol

84: 		for (uint256 i = 0; i < total; i++) {
```

[Link to code](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol)
```solidity
File: contracts/contract/Ocyticus.sol

61:    for (uint256 i = 0; i < count; i++) {
```

[Link to code](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol)
```solidity
File: contracts/contract/RewardsPool.sol

74: 		for (uint256 i = 0; i < inflationIntervalsElapsed; i++) {

215: 		for (uint256 i = 0; i < count; i++) {

230: 		for (uint256 i = 0; i < enabledMultisigs.length; i++) {
```

[Link to code](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol)
```solidity
File: contracts/contract/Staking.sol

428:    for (uint256 i = offset; i < max; i++) {
```

**Apply `unchecked` math operation to the below instances**


[Link to code](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol)
```solidity
File: contracts/contract/Staking.sol

426:    stakers = new Staker[](max - offset);  

431:    total++;   // @audit, see L427-428
```

[Link to code](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol)
```solidity
File: contracts/contract/Vault.sol

75:   avaxBalances[contractName] = avaxBalances[contractName] - amount;  // @audit, see L71

99:    avaxBalances[fromContractName] = avaxBalances[fromContractName] - amount;  // @audit, see L95

155:    tokenBalances[contractKey] = tokenBalances[contractKey] - amount;  // @audit, see L151

187:    tokenBalances[contractKeyFrom] = tokenBalances[contractKeyFrom] - amount;// @audit, see L183
```

