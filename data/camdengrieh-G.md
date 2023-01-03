1) Reentrancy attack not possible within withdrawToken() and withdrawAvax(); nonReentrant modifier can be removed

2 instances

Gas Saved, reported from each test run:  12162 - Tested running testDepositTokenFromRegisteredContract(), testDepositAvaxFromRegisteredContract()

Because balance is subtracted from the registered contract prior to the transfer of the tokens in the execution flow of the withdrawToken() method. If a reentrancy is made the balance will be checked again and the calling contract will require a greater balance than the amount it's expecting to withdraw.

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L139-L162

```solidity
	function withdrawToken(
		address withdrawalAddress,
		ERC20 tokenAddress,
		uint256 amount
	) external onlyRegisteredNetworkContract  {
		// Valid Amount?
		if (amount == 0) {
			revert InvalidAmount();
		}
		// Get contract key
		bytes32 contractKey = keccak256(abi.encodePacked(getContractName(msg.sender), tokenAddress));
		// Emit token withdrawn event
		emit TokenWithdrawn(contractKey, address(tokenAddress), amount);
		// Verify there are enough funds
		if (tokenBalances[contractKey] < amount) {
			revert InsufficientContractBalance();
		}
		// Update balances
		tokenBalances[contractKey] = tokenBalances[contractKey] - amount;
		// Get the toke ERC20 instance
		ERC20 tokenContract = ERC20(tokenAddress);
		// Withdraw to the withdrawal address, , safeTransfer will revert if it fails
		tokenContract.safeTransfer(withdrawalAddress, amount);
	}
```



2) Use unchecked on variables that cannot over/undeflow | Vault.sol

Gas Saved:
Running testTransferTokenFromRegisteredContract() without unchecked = 179695
Running testTransferTokenFromRegisteredContract() with unchecked = 179473

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L164-L196

```solidity
	/// @notice Transfer token from one contract to another
	/// @param networkContractName Name of the contract that the token will be transferred to
	/// @param tokenAddress ERC20 token
	/// @param amount Number of tokens to be withdrawn
	function transferToken(
		string memory networkContractName,
		ERC20 tokenAddress,
		uint256 amount
	) external onlyRegisteredNetworkContract {
		// Valid Amount?
		if (amount == 0) {
			revert InvalidAmount();
		}
		// Make sure the network contract is valid (will revert if not)
		getContractAddress(networkContractName);
		// Get contract keys
		bytes32 contractKeyFrom = keccak256(abi.encodePacked(getContractName(msg.sender), tokenAddress));
		bytes32 contractKeyTo = keccak256(abi.encodePacked(networkContractName, tokenAddress));
		// emit token transfer event
		emit TokenTransfer(contractKeyFrom, contractKeyTo, address(tokenAddress), amount);
		// Verify there are enough funds
		if (tokenBalances[contractKeyFrom] < amount) {
			revert InsufficientContractBalance();
		}
		// Update Balances

		//Uncheck because they cannot over/underflow
		unchecked {
		tokenBalances[contractKeyFrom] = tokenBalances[contractKeyFrom] - amount;
		tokenBalances[contractKeyTo] = tokenBalances[contractKeyTo] + amount;
		}
		
	}
```
