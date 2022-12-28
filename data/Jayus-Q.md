1. Events should be emitted only after actions have occurred and not prior. Some occurrences of this exist but not limited to Vault.sol and Staking.sol
2. No check for zero amount in `stakeGGP(uint256 amount)` in `Staking.sol`.
3. No check for zero assets `withdrawAvax(uint256 assets)` in `TokenggAvax.sol`.
4. `invoiceID` not validated in function `spend(string memory invoiceID, address recipientAddress, uint256 amount)` located in `ClaimProtocolDao.sol`. One invoiceID could be used for multiple unrelated spending which will be misleading and cause auditing conflicts when reading contract logs.
>Solution
use a mapping to store the `keccack256` hash of invoiceID and place a check to ensure the hash of an invoiceID doesn't exist.
5. No check for address(0) of parameter `address recipientAddress` in `spend(string memory invoiceID, address recipientAddress, uint256 amount)` located in `ClaimProtocolDao.sol`. 
