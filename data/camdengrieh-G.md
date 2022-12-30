Reentrancy attack not possible within withdrawToken(); nonReentrant modifier can be removed

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
