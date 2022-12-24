## Variables initialized with default value

Some variables in the smart contracts are initialized with the types default value. It wastes gas to explicitly initialize a variable with its default value.

- [`MinipoolManager.sol#L618`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L618)
- [`RewardsPool.sol#L141`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L141)
- [`Staking.sol#427`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L427)

**Solution / Example**

`uint256 var = 0;` change it to `uint256 var;`

**References**
- [G001 - Don't Initialize Variables with Default Value](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g001---dont-initialize-variables-with-default-value)
- [Solidity tips and tricks to save gas and reduce bytecode size](https://mudit.blog/solidity-tips-and-tricks-to-save-gas-and-reduce-bytecode-size/)

## Array length not cached outside of a loop

If the array's length is not changed during a loop, caching it outside the loop saves reading it on each iteration.

- [`RewardsPool.sol#L230`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L230)

**Solution / Example**

```
for (uint256 i = 0; i < enabledMultisigs.length; i++) {
	vault.withdrawToken(enabledMultisigs[i], ggp, tokensPerMultisig);
}
``` 

change it to

```
uint256 len = enabledMultisigs.length;
for (uint256 i = 0; i < len; i++) {
	vault.withdrawToken(enabledMultisigs[i], ggp, tokensPerMultisig);
}
```

**References**
- [G002 - Cache Array Length Outside of Loop](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g002---cache-array-length-outside-of-loop)
- [Avoid unnecessary read of array length in for loops can save gas](https://github.com/code-423n4/2021-11-badgerzaps-findings/issues/36)

## If possible, implement Shift Right/Left for Division and Multiplication

The `SHR` opcode only utilizes 3 gas, compared to the 5 gas used by the `DIV` opcode. Additionally, shifting is used to get around Solidity's division operation's division-by-0 prohibition.

- [`MinipoolManager.sol#L413`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L413)

**Solution / Example**

`uint256 avaxHalfRewards = avaxTotalRewardAmt / 2;` change it to `uint256 avaxHalfRewards = avaxTotalRewardAmt >> 1;`

**Resources**
- [G008 - Use Shift Right/Left instead of Division/Multiplication if possible](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g008---use-shift-rightleft-instead-of-divisionmultiplication-if-possible)
- [EVM Opcodes](https://www.evm.codes/)