 # Vulnerability details

## Impact
The changes that were made were to optimize the code for gas. The event GGPTokensSentByDAOProtocol was changed to use indexed types for all parameters, and the parameter types of the spend() function were changed from string and address to bytes32 and address, respectively. These changes will reduce the gas cost of emitting the event and executing the spend() function.

## Proof of Concept
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimProtocolDAO.sol#L13
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimProtocolDAO.sol#L20

## Code Refactored

```
// SPDX-License-Identifier: GPL-3.0-only
pragma solidity 0.8.17;

import "./Base.sol";
import {Storage} from "./Storage.sol";
import {TokenGGP} from "./tokens/TokenGGP.sol";
import {Vault} from "./Vault.sol";

/// @title Protocol DAO claiming GGP Rewards
contract ClaimProtocolDAO is Base {
	error InvalidAmount();

	event GGPTokensSentByDAOProtocol(bytes32 indexed invoiceID, address indexed from, address indexed to, uint256 amount);

	constructor(Storage storageAddress) Base(storageAddress) {
		version = 1;
	}

	/// @notice Spends the ProtocolDAO's GGP rewards
	function spend(
		bytes32 invoiceID,
		address recipientAddress,
		uint256 amount
	) external onlyGuardian {
		Vault vault = Vault(getContractAddress("Vault"));
		TokenGGP ggpToken = TokenGGP(getContractAddress("TokenGGP"));

		if (amount == 0 || amount > vault.balanceOfToken("ClaimProtocolDAO", ggpToken)) {
			revert InvalidAmount();
		}

		vault.withdrawToken(recipientAddress, ggpToken, amount);

		emit GGPTokensSentByDAOProtocol(invoiceID, address(this), recipientAddress, amount);
	}
}
```
