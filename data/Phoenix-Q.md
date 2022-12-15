## Check for zero address

File: [`Vault.sol#L204`]  (https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L204)

```solidity
function addAllowedToken(address tokenAddress) 
}
```


File: [`Storage.sol#L41-L48`]  (https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L41-L48)

```solidity
function setGuardian(address newAddress) external {
		// Check tx comes from current guardian
		if (msg.sender != guardian) {
			revert MustBeGuardian();
		}
		// Store new address awaiting confirmation
		newGuardian = newAddress;
}
```