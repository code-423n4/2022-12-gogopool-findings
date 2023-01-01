Put the code directly into the if statement instead of assigning it to a boolean.

- [BaseAbstract.sol#L73](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L73)


Instead of using i++ or ++i in the for loops make the incremention unchecked if there is no risk of overflow. In this case there should be no risk of overflow. This will have worse readability because it needs to be a seperate function. However, this will be a big gas saver.

- [RewardsPool.sol#L74](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L74)
- [RewardsPool.sol#L215](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L215)
- [RewardsPool.sol#L230](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L230)
- [Staking.sol#L428](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L428)
- [MinipoolManager.sol#L619](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L619)
- [Ocyticus.sol#L61](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L61)
- [MultisigManager.sol#L84](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L84)

x = x +y costs less gas than x += y (12 instances)
- [TokenggAVAX.sol#L149](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L149)
- [TokenggAVAX.sol#L160](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L160)
- [TokenggAVAX.sol#L245](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L245)
- [TokenggAVAX.sol#L252](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L252)

- [ERC20Upgradeable.sol#L79](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L79)
- [ERC20Upgradeable.sol#L84](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L84)
- [ERC20Upgradeable.sol#L101](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L101)
- [ERC20Upgradeable.sol#L106](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L106)
- [ERC20Upgradeable.sol#L184](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L184)
- [ERC20Upgradeable.sol#L184](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L189)
- [ERC20Upgradeable.sol#L196](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L196)
- [ERC20Upgradeable.sol#L201](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L201)

