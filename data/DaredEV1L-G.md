##  `++i` COSTS LESS GAS COMPARED TO `i++`

`++i` costs less gas compared to `i++` for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration). This statement is true even with the optimizer enabled.

`i++` increments `i` and returns the initial value of `i`. Which means:
```
uint i = 1;
i++; // == 1 but i == 2
```
But `++i` returns the actual incremented value:
```
uint i = 1;
++i; // == 2 and i == 2 too, so no need for a temporary variable
```
In the first case, the compiler has to create a temporary variable (when used) for returning 1 instead of 2.
I suggest using `++i` instead of `i++` to increment the value of an uint variable. 

### Affected source code:

[MinipoolManager.sol#L619](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L619)

[MultisigManager.sol#L84](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MultisigManager.sol#L84)

[Ocyticus.sol#L61](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Ocyticus.sol#L61)

[BaseAbstract.sol#L74](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L74)

[RewardsPool.sol#L215](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L215)

[RewardsPool.sol#L230](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L230)

[Staking.sol#L428](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L428)


