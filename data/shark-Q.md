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
