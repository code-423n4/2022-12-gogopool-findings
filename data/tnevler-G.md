# Report
## Gas Optimizations ##
### [G-1]: The increment in for loop post condition can be made unchecked
**Context:**

1. ```for (uint256 i = offset; i < max; i++) {``` [L619](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L619) 
1. ```for (uint256 i = 0; i < total; i++) {``` [L84](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L84) 
1. ```for (uint256 i = 0; i < count; i++) {``` [L61](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L61) 
1. ```for (uint256 i = 0; i < inflationIntervalsElapsed; i++) {``` [L74](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L74) 
1. ```for (uint256 i = 0; i < count; i++) {``` [L215](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L215) 
1. ```for (uint256 i = 0; i < enabledMultisigs.length; i++) {``` [L230](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L230) 
1. ```for (uint256 i = offset; i < max; i++) {``` [L428](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L428) 

**Description:**

[This](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked) can save 30-40 gas per loop iteration.

**Recommendation:**

Example how to fix. Change:
```
for (uint256 i = 0; i < orders.length; ++i) {
   // Do the thing
}
```

To:
```
for (uint256 i = 0; i < orders.length;) {
   // Do the thing
   unchecked { ++i; }
}
```

### [G-2]: Place subtractions where the operands can't underflow in unchecked {} block
**Context:**

1. ```avaxBalances[contractName] = avaxBalances[contractName] - amount;``` [L75](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L75) 
1. ```avaxBalances[fromContractName] = avaxBalances[fromContractName] - amount;``` [L99](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L99) 
1. ```tokenBalances[contractKey] = tokenBalances[contractKey] - amount;``` [L155](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L155) 
1. ```tokenBalances[contractKeyFrom] = tokenBalances[contractKeyFrom] - amount;``` [L187](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L187) 
1. ```stakingTotalAssets -= baseAmt;``` [L149](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L149) 

**Description:**

Some gas can be saved by using an unchecked {} block if an underflow isn't possible because of a previous require() or if-statement.

### [G-3]: Cache a value from a mapping/array in local memory
**Context:**

1. ```if (avaxBalances[contractName] < amount) {``` [L71](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L71) 
1. ```avaxBalances[contractName] = avaxBalances[contractName] - amount;``` [L75](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L75) 
1. ```if (avaxBalances[fromContractName] < amount) {``` [L95](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L95) 
1. ```avaxBalances[fromContractName] = avaxBalances[fromContractName] - amount;``` [L99](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L99) 
1. ```if (tokenBalances[contractKey] < amount) {``` [L151](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L151) 
1. ```tokenBalances[contractKey] = tokenBalances[contractKey] - amount;``` [L155](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L155) 
1. ```if (tokenBalances[contractKeyFrom] < amount) {``` [L183](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L183) 
1. ```tokenBalances[contractKeyFrom] = tokenBalances[contractKeyFrom] - amount;``` [L187](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L187) 

**Description:**

If you read value from mapping/array more than once within a function then it is cheaper to cache it in local memory and then read it from memory wnen it is neaded. This will save about 40 gas.

**Recommendation:**

Example how to fix. Change:
```
		if (avaxBalances[contractName] < amount) {
			revert InsufficientContractBalance();
		}
		// Update balance
		avaxBalances[contractName] = avaxBalances[contractName] - amount;
```

to:
```
		uint256 avaxBalance = avaxBalances[contractName];
   		if (avaxBalance < amount) {
			revert InsufficientContractBalance();
		}
		// Update balance
		avaxBalances[contractName] = avaxBalance - amount;
```
