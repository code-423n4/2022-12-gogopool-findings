**ERC4626Upgradeable.sol**
- There is already a standard OpenZeppelin implementation of ERCUpgradeable (https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/master/contracts/token/ERC20/extensions/ERC4626Upgradeable.sol),
therefore, for a matter of not generating confusion, it would be beneficial to give the contract another name.


**TokenggAVAX.sol
- L7 - A class that is never used is imported: ERC20Upgradeable, therefore it should be removed.

- L128 - Before executing a division for a value that we do not know if it will return 0, the corresponding exception should be validated and thrown, since if it is not done the user will not know why it reverts.


**ClaimNodeOp.sol**
- L5/10 - Some classes that are never used are imported: MinipoolManager and TokenGGP, therefore they should be removed.


**MinipoolManager.sol**
- L13 - A class that is never used is imported: TokenGGP, therefore it should be removed.

- L21-54 - A description of the code is commented out, but not done with natspec.


**ProtocolDAO.sol**
- L5 - A class that is never used is imported: TokenGGP, therefore it should be removed.


**Staking.sol**
- L5 - A class that is never used is imported: MinipoolManager, therefore it should be removed.


**Vault.sol**
- L7/8 - Some classes that are never used are imported: WAVAX and TokenGGP, therefore they should be removed.

- L26/27/28 - 3 errors are created that are never used, therefore they should be eliminated: InvalidNetworkContract, TokenTransferFailed and VaultTokenWithdrawalFailed.


**BaseAbstract.sol**
- L19 - A variable is created in storage, version, which is not used throughout the contract, therefore it should be deleted. In the case that it is used in contracts that are inherited, it should be created there.


**ERC20Upgradeable.sol**
- There is already a standard OpenZeppelin implementation of ERCUpgradeable (https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/master/contracts/token/ERC20/ERC20Upgradeable.sol),
therefore, for a matter of not generating confusion, it would be beneficial to give the contract another name.


