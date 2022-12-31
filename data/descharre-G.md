Put the code directly into the if statement instead of assigning it to a boolean.

- [BaseAbstract.sol#L73](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L73)


Instead of using i++ or ++i in the for loops make the incremention unchecked if there is no risk of overflow. In this case there should be no risk of overflow. This will have worse readability because it needs to be a seperate function. However, this will be a big gas saver.

- [RewardsPool.sol#L74](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L74)
- [RewardsPool.sol#L216](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L216)
- [Staking.sol#L428](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L428)
- [MinipoolManager.sol#L619](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L619)
- [Ocyticus.sol#L61](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L61)