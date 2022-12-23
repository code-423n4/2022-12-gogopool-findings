  # Vulnerability details

## Impact
The changes to the code include the addition of a new constructor parameter, _newAddress, and the related assignment of the address to the newAddress variable.

## Proof of Concept
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Base.sol#L11


## Code Refactored

```
// SPDX-License-Identifier: GPL-3.0-only
pragma solidity 0.8.17;

import "./BaseAbstract.sol";
import {Storage} from "./Storage.sol";

abstract contract Base is BaseAbstract {
	/// @dev Set the main GoGo Storage address
	constructor(Storage _gogoStorageAddress, address _newAddress) {
		// Update the contract address
		gogoStorage = Storage(_gogoStorageAddress);
		// Update the new address
		newAddress = address(_newAddress);
	}
}
```