Unused variable 'version' - Hardcoded in the constructor not auto-incremented or tracked

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseAbstract.sol#L19

`uint8 public version;`

The version is always set to 1 in the constructor of the following contracts inheriting Base.sol:

ClaimProtocolDao.sol
MinipoolManager.sol
MultisigManager.sol
Oracle.sol
ProtocolDAO.sol
RewardsPool.sol
Staking.sol
Vault.sol

If this is intended for version tracking, I think it would be reasonable to move this variable to a contract that wont be redeployed frequently and auto increment the variable on that contract through the constructor of the contract that needs version tracking, to prevent human errors, where it could be forgotten to be changed.

If the variable is not going to be used, it should be removed to save a storage slot.