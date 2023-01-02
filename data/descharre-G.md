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

Memory variable is only read once, so there is no point in writing it to memory. It's cheaper to read it directly from storage.
- [TokenggAVAX.sol#L97](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L97)
- [MinipoolManager.sol#L341](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L341)
- [Storage.sol#L81](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L81)
- [Storage.sol#L88](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L88)
- [Storage.sol#L95](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L95)
- [Storage.sol#L104](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L104)
- [Storage.sol#L111](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L111)
- [Storage.sol#L118](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L118)
- [Storage.sol#L127](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L127)
- [Storage.sol#L134](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L134)
- [Storage.sol#L141](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L141)
- [Storage.sol#L150](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L150)
- [Storage.sol#L157](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L157)
- [Storage.sol#L174](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L174)
- [Storage.sol#L181](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L181)
- [Storage.sol#L188](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L188)
- [Storage.sol#L197](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L197)
- [Storage.sol#L205](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L205)
- [Storage.sol#L218](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L218)
- [Storage.sol#L225](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L225)
- [Storage.sol#L232](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L232)
- [Storage.sol#L241](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L241)
- [Storage.sol#L249](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L249)

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

When contract instance is only used once, don't assign it to a variable, the functions below keep almost the same readability. I didn't include the functions where the readibility will get worse
Instead of:
ProtocolDAO dao = ProtocolDAO(getContractAddress("ProtocolDAO"));
uint256 rate = dao.getExpectedAVAXRewardsRate();

You can do:
uint256 rate = ProtocolDAO(getContractAddress("ProtocolDAO")).getExpectedAVAXRewardsRate();

Can be done at:
- [MinipoolManager.sol#L267](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L267)
- [MinipoolManager.sol#L274](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L274)
- [MinipoolManager.sol#L275](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L275)
- [MinipoolManager.sol#L297](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L297)
- [MinipoolManager.sol#L300](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L300)
- [MinipoolManager.sol#L411](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L411)
- [MinipoolManager.sol#L416](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L416)
- [MinipoolManager.sol#L429](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L429)
- [MinipoolManager.sol#L503](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L503)
- [MinipoolManager.sol#L548](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L548)
- [MinipoolManager.sol#L557](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L557)
- [MinipoolManager.sol#L662](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L662)
- [MinipoolManager.sol#L681](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L681)

- [ClaimNodeOp.sol#L47](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L47)

- [Staking.sol#L258](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L258)
- [Staking.sol#L275](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L275)
- [Staking.sol#L295](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L295)
- [Staking.sol#L300](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L300)
- [Staking.sol#L309](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L309)
- [Staking.sol#L367](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L367)
- [Staking.sol#L380](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L380)

