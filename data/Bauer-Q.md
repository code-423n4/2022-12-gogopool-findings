# No same value input control
## Storage.sol:
	function setGuardian(address newAddress) external {
		// Check tx comes from current guardian
		if (msg.sender != guardian) {
			revert MustBeGuardian();
		}
		// Store new address awaiting confirmation
		newGuardian = newAddress;
	}
## Vault.sol:
	function addAllowedToken(address tokenAddress) external onlyGuardian {
		allowedTokens[tokenAddress] = true;
	}



Recommended Mitigation Steps:

Add code like this; if (newAddress == guardian revert ADDRESS_SAME()); 

 if (allowedTokens[tokenAddress] == true revert ADDRESS_SAME()); 

# No check for address existence
## Vault.sol:
	function removeAllowedToken(address tokenAddress) external onlyGuardian {
		allowedTokens[tokenAddress] = false;
	}

Recommended Mitigation Steps:
Add code like this; if (allowedTokens[tokenAddress] == true); 
