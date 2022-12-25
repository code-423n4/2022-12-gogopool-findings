## Missing implementation Shift Right/Left for division and multiplication

The `SHR` opcode only utilizes 3 gas, compared to the 5 gas used by the `DIV` opcode. Additionally, shifting is used to get around Solidity's division operation's division-by-0 prohibition.

- [`MinipoolManager.sol#L413`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L413)

**Solution / Example**

`uint256 avaxHalfRewards = avaxTotalRewardAmt / 2;` change it to `uint256 avaxHalfRewards = avaxTotalRewardAmt >> 1;`

**Resources**
- [G008 - Use Shift Right/Left instead of Division/Multiplication if possible](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g008---use-shift-rightleft-instead-of-divisionmultiplication-if-possible)
- [EVM Opcodes](https://www.evm.codes/)