1) Reentrancy attack not possible within withdrawToken(); nonReentrant modifier can be removed

Gas Saved:  12162 - tested running just test

Because balance is subtracted from the registered contract prior to the transfer of the tokens in the execution flow of the withdrawToken() method. If a reentrancy is made the balance will be checked again and the calling contract will require a greater balance than the amount it's expecting to withdraw.

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L137

150: 		// Verify there are enough funds
151: 		        if (tokenBalances[contractKey] < amount) {
152: 			revert InsufficientContractBalance();
153: 		}
154: 		// Update balances
155: 		tokenBalances[contractKey] = tokenBalances[contractKey] - amount;
156: 		// Get the toke ERC20 instance
157: 		ERC20 tokenContract = ERC20(tokenAddress);
158: 		// Withdraw to the withdrawal address, , safeTransfer will revert if it fails
159: 		tokenContract.safeTransfer(withdrawalAddress, amount);



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
