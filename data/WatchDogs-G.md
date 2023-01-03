## [Gas - 01] No need to explicitly initialize variables with default values

Declaration of variables with default values cost gas. such as

`uint256 a = 0;`  change to → `uint256 a;`


*Instances of this issue :* 

* Ocyticus.sol [L61](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L61)
* RewardsPool.sol [L74](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L74) [L215](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L215) [L230](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L230)
* MultisigManager.sol [L84](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L84) 

## [Gas - 02] Use `++x` instead of `x++`

Use Prefix increment rather than Postfix increment, it saves gas.

*Instances of this issue :*

* Ocyticus.sol [L61](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L61)
* RewardsPool.sol [L74](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L74) [L215](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L215) [L230](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L230)
* MultisigManager.sol [L84](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L84) 
* Staking.sol [L428](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L428)

## [Gas - 03] ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for- and while-loops

*Instances of this issue :*

* Ocyticus.sol [L61](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L61)
* RewardsPool.sol [L74](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L74) [L215](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L215) [L230](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L230)
* MultisigManager.sol [L84](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L84)
* Staking.sol [L428](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L428)

## [Gas - 04] An array's length should be cached to save gas in for-loops
Caching the array length outside a loop saves reading it on each iteration, as long as the array’s length is not changed during the loop.

*Instances of this issue :*

* RewardsPool.sol [L230](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L230)

## [Gas - 05] Public function not called by the contract should be declared external instead.
Contracts are allowed to override their parents’ functions and change the visibility from external to public and can save gas by doing so.

*Instances of this issue :*

* MultisigManager.sol [L102](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L102)

## [Gas - 06] For array parameters in functions, it is recommended to use `calldata` over `memory`
Use calldata instead of memory for function parameters saves gas if the function argument is only read.

*Instances of this issue :*

* Vault.sol [L193](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L193) [L200](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L200)















