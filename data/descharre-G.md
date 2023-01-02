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

Use custom error instead of require() to save gas (4 instances)
- [ERC20Upgradeable.sol#L127](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L127)
- [ERC20Upgradeable.sol#L154](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L154)
- [ERC4626Upgradeable.sol#L44](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L44)
- [ERC4626Upgradeable.sol#L103](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L103)

Use bit shifting for division by 2 (1 instance)
x = x >> 1 is the same as x = x/2;
- [MinipoolManager.sol#L413](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L413)

StakingtotalAssets is only read once, so there is no point in writing it to memory. It's cheaper to read it directly from storage.
- [TokenggAVAX.sol#L97](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L97)

Functions with a modifier can be marked as payable to save gas for legitimate callers.
Every function with a modifier in 
- [Staking.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/Staking.sol)
- [Storage.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/Storage.sol)
- [ClaimNodeOp.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ClaimNodeOp.sol)
- [ClaimProtocolDAO.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ClaimProtocolDAO.sol)
- [MultisigManager.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/MultisigManager.sol)
- [Ocyticus.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/Ocyticus.sol)
- [ProtocolDao.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ProtocolDao.sol)
- [Vault.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/Vault.sol)