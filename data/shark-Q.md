## 1. Typo mistakes

It is recommended to use the proper spelling of words. Typos can make it difficult for readers to decipher the intended meaning, which can lead to confusion.

File: `ClaimNodeOp.sol` [Line 45](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/ClaimNodeOp.sol#L45)

```
       /// @audit Eligiblity -> Eligibility
45:    /// @dev Eligiblity: time in protocol (secs) > RewardsEligibilityMinSeconds. Rialto will call this.
```

File: `MinipoolManager.sol` ([Line 309](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/MinipoolManager.sol#L309), [Line 411](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/MinipoolManager.sol#L411))

```
        /// @audit "the" is repeated twice             vvvvvvv
309:    /// @notice Verifies that the minipool related the the given node ID is able to a validator

        /// @audit Avoid using confusing acronyms                              vvvvv
411:    // Calculate rewards splits (these will all be zero if no rewards were recvd)
```

File: `Ocyticus.sol` [Line 46](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/Ocyticus.sol#L46)

```
           /// @audit dont -> don't
46:	   /// @dev Multisigs will need to be enabled seperately, we dont know which ones to enable
```

File: `ProtocolDAO.sol` [Line 205](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/ProtocolDAO.sol#L205)

```
        /// @audit registring --> registering
205:	/// @notice Upgrade a contract by unregistering the existing address, and registring a new address and name
```

File: `Vault.sol` ([Line 68](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/Vault.sol#L68), [Line 81](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/Vault.sol#L81), [Line 82](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/Vault.sol#L82), [Line 158](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/Vault.sol#L158), [Line 192](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/Vault.sol#L192), [Line 198](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/Vault.sol#L198))

```
        /// @audit withdrawl --> withdrawal
68:	// Emit the withdrawl event for that contract

        /// @audit bookeeping --> bookkeeping
81:	/// @dev No funds actually move, just bookeeping

        /// @audit funcs --> funds
82:	/// @param toContractName Name of the contract fucns are being transferred to

        /// @audit ", ," Unnecessary double comma
158:	// Withdraw to the withdrawal address, , safeTransfer will revert if it fails

        /// @audit who's -> whose
192:	/// @param networkContractName Name of the contract who's AVAX balance is being requested

        /// @audit who's -> whose
198:	/// @param networkContractName Name of the contract who's token balance is being requested
```

File: `TokenggAVAX.sol` ([Line 49](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/tokens/TokenggAVAX.sol#L49), [Line 68](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/tokens/TokenggAVAX.sol#L68), [Line 111](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/tokens/TokenggAVAX.sol#L111))

```
        /// @audit "a the" -> "the"
49:	/// @notice the amount of rewards distributed in a the most recent cycle.

        /// @audit it's -> its
68:	// so prevent it's reinitialization

        /// @audit "share holders" -> "shareholders"
111: /// @notice Compute the amount of tokens available to share holders.
```

## 2. Insufficient NatSpec

It is recommended that all functions and state variables be thoroughly documented using [NatSpec](https://docs.soliditylang.org/en/develop/natspec-format.html).

The following contract is missing NatSpec in its entirety:

[File: `BaseUpgradeable.sol`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseUpgradeable.sol)

## 3. Open TODOs

Open TODOs can hint at programming or architectural errors that have yet to be resolved.

Here a few instances of this issue:

- File: `BaseAbstract.sol` [Line 6](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/BaseAbstract.sol#L6)
- File: `Staking.sol` [Line 203](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/Staking.sol#L203)
- File: `MinipoolManager.sol` [Line 412](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/MinipoolManager.sol#L412)

## 4. Missing zero value check in `withdrawAVAX()`

If the parameter `uint256 assets` is accidentally input as zero, a zero value could still be assigned to `shares` even though it is rounded up. As such, a zero value check could be implemented.

For instance:

File: `TokenggAVAX.sol` [Line 180 - Line 189](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L180-L189)

```solidity
	function withdrawAVAX(uint256 assets) public returns (uint256 shares) {
		shares = previewWithdraw(assets); // No need to check for rounding error, previewWithdraw rounds up.
		beforeWithdraw(assets, shares);
		_burn(msg.sender, shares);

		emit Withdraw(msg.sender, msg.sender, msg.sender, assets, shares);

		IWAVAX(address(asset)).withdraw(assets);
		msg.sender.safeTransferETH(assets);
	}
```

As seen above, there is no zero value check for the parameter `uint256 assets`. Consider adding the following check at the start of `withdrawAVAX`:

```solidity
	if (assets == 0) {
        	revert ZeroAssets();
	}
```

## 5. No storage gap for upgradeable contracts

For upgradeable contracts, there should be a storage gap at the end of the contract. Without a storage gap, Storage clashes could occur while using inheritance.

For example:

[File: `TokenggAVAX.sol`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol)

Consider adding the variable `__gap` at the end of the contract:

```solidity
uint256[50] private __gap;
```

## 6. Custom errors can be used with parameters

For example, the following custom errors could use some parameters to show what actually went wrong:

- File `MinipoolManager.sol` [Line 64](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L64)
- File: `MinipoolManager.sol` [Line 65](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L65)
- File: `Vault.sol` [Line 23](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L23)
- File: `Vault.sol` [Line 24](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L24)
- File: `Oracle.sol` [Line 18](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Oracle.sol#L18)

## 7. Unused imports

Unused imports can lead to confusion for a human looking at the code. Also, it's bad practice.

- File: `MinipoolManager.sol` [Line 13](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L13)

## 8. Incorrect Comments

In the following comments, it is stated that "2% is 0.2 ether". However, it should instead be "20% is 0.2 ether". Alternatively, it may also be changed to "2% is 0.02 ether".

Here are the instances:

- File: `MinipoolManager.sol` [Line 35](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L35)
- File: `MinipoolManager.sol` [Line 194](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L194)
