Different pragma solidity

Vulnerability details

Impact

ERC20Upgradeable.sol and ERC4626Upgradeable.sol contracts have a different pragma statement than the rest, they use version >=0.8.0;

It's cleaner to use the same versions.

Proof of Concept

Base.sol: pragma solidity 0.8.17;
BaseUpgradeable.sol: pragma solidity 0.8.17;
BaseAbstract.sol: pragma solidity 0.8.17;
ClaimNodeOp.sol: pragma solidity 0.8.17;
ClaimProtocolDAO.sol: pragma solidity 0.8.17;
MinipoolManager.sol: pragma solidity 0.8.17;
MultisigManager.sol: pragma solidity 0.8.17;
Ocyticus.sol: pragma solidity 0.8.17;
Oracle.sol: pragma solidity 0.8.17;
ProtocolDAO.sol: pragma solidity 0.8.17;
RewardsPool.sol: pragma solidity 0.8.17;
Staking.sol: pragma solidity 0.8.17;
Storage.sol: pragma solidity 0.8.17;
Vault.sol: pragma solidity 0.8.17;
TokenGGP.sol: pragma solidity 0.8.17;
TokenggAVAX.sol: pragma solidity 0.8.17;
ERC20Upgradeable.sol: pragma solidity >=0.8.0;
ERC4626Upgradeable.sol pragma solidity >=0.8.0;

Tools Used
Editor