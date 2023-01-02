# QA report

## Author: rotcivegaf

## Low Risk
| L-N    |Issue|Instances|
|:------:|:----|:-------:|
| [L&#x2011;01] | Open TODO | 2 |


## Non-critical

| N-N    |Issue|Instances|
|:------:|:----|:-------:|
| [N&#x2011;01] | Typo | 24 |
| [N&#x2011;02] | Unused imports | 8 |
| [N&#x2011;03] | Unused comments | 1 |
| [N&#x2011;04] | Unused error | 3 |

## Low Risk

### [L-01] Open TODO

Open TODO can point to architecture or programming issues that still need to be resolved.

```solidity
File: contracts/contract/BaseAbstract.sol

6:// TODO remove this when dev is complete
```

```solidity
File: contracts/contract/MinipoolManager.sol

412:		// TODO Revisit this logic if we ever allow unequal matched funds
```

## Non-critical

### [N‑01] Typo

```solidity
File: contracts/contract/ClaimNodeOp.sol

/// @audit: `Eligiblity` to `Eligibility`
45:	/// @dev Eligiblity: time in protocol (secs) > RewardsEligibilityMinSeconds. Rialto will call this.
```

```solidity
File: contracts/contract/MinipoolManager.sol

/// @audit: `validater` to `validator`
347:	/// @param txID The ID of the transaction that successfully registered the node with Avalanche to become a validater

/// @audit: `recieved` to `received`
383:	/// @param avaxTotalRewardAmt The rewards the node recieved from Avalanche for being a validator

/// @audit: `recvd` to `received`
411:		// Calculate rewards splits (these will all be zero if no rewards were recvd)

/// @audit: `recvd` to `received`
442:	/// @notice Re-stake a minipool, compounding all rewards recvd

/// @audit: `occured` to `occurred`
480:	/// @notice A staking error occured while registering the node as a validator

/// @audit: `recieve` to `recieved`
556:  /// @return The approximate rewards the node should recieve from Avalanche for beign a validator

/// @audit: `beign` to `begin`
556:  /// @return The approximate rewards the node should recieve from Avalanche for beign a validator

/// @audit: `adhear` to `adhere`
606:  /// @return minipools in the protocol that adhear to the paramaters

/// @audit: `paramaters` to `parameters`
606:  /// @return minipools in the protocol that adhear to the paramaters
```

```solidity
File: contracts/contract/Ocyticus.sol

/// @audit: `seperately` to `separately`
46:	 /// @dev Multisigs will need to be enabled seperately, we dont know which ones to enable
```

```solidity
File: contracts/contract/ProtocolDAO.sol

/// @audit: `commision` to `commission`
134:  /// @notice The node commision fee for running the hardware for the minipool

/// @audit: `registring` to `registering`
205:  /// @notice Upgrade a contract by unregistering the existing address, and registring a new address and name
```

```solidity
File: contracts/contract/RewardsPool.sol

/// @audit: `ClaimNodeOpRewardsTransfered` to `ClaimNodeOpRewardsTransferred`
 26:  event ClaimNodeOpRewardsTransfered(uint256 value);
192:			emit ClaimNodeOpRewardsTransfered(nopClaimContractAllotment);

/// @audit: `ProtocolDAORewardsTransfered` to `ProtocolDAORewardsTransferred`
 27:  event ProtocolDAORewardsTransfered(uint256 value);
182:			emit ProtocolDAORewardsTransfered(daoClaimContractAllotment);

/// @audit: `MultisigRewardsTransfered` to `MultisigRewardsTransferred`
 28:  event MultisigRewardsTransfered(uint256 value);
187:			emit MultisigRewardsTransfered(multisigClaimContractAllotment);
```

```solidity
File: contracts/contract/Staking.sol

/// @audit: `adhear` to `adhere`
419:  /// @return stakers in the protocol that adhear to the paramaters

/// @audit: `paramaters` to `parameters`
419:  /// @return stakers in the protocol that adhear to the paramaters
```

```solidity
File: contracts/contract/Vault.sol

/// @audit: `withdrawl` to `withdrawal`
68:    // Emit the withdrawl event for that contract

/// @audit: `bookeeping` to `bookkeeping`
81:	/// @dev No funds actually move, just bookeeping

/// @audit: `recieve` to `receive`
134:	/// @param withdrawalAddress Address that will recieve the token

/// @audit: `toke` to `token`
156:		// Get the toke ERC20 instance

/// @audit: `, , ` to `, `
158:		// Withdraw to the withdrawal address, , safeTransfer will revert if it fails
```

```solidity
File: contracts/contract/tokens/TokenggAVAX.sol

/// @audit: `exectued` to `executed`
67:		// The constructor is exectued only when creating implementation contract
```

### [N‑02] Unused imports

```solidity
File: contracts/contract/ClaimNodeOp.sol

 5:import {MinipoolManager} from "./MinipoolManager.sol";

10:import {TokenGGP} from "./tokens/TokenGGP.sol";
```

```solidity
File: contracts/contract/MinipoolManager.sol

13:import {TokenGGP} from "./tokens/TokenGGP.sol";
```

```solidity
File: contracts/contract/ProtocolDAO.sol

5:import {TokenGGP} from "./tokens/TokenGGP.sol";
```

```solidity
File: contracts/contract/Staking.sol

5:import {MinipoolManager} from "./MinipoolManager.sol";
```

```solidity
File: contracts/contract/Vault.sol

7:import {TokenGGP} from "./tokens/TokenGGP.sol";

8:import {WAVAX} from "./utils/WAVAX.sol";
```

```solidity
File: contracts/contract/tokens/TokenggAVAX.sol

7:import {ERC20Upgradeable} from "./upgradeable/ERC20Upgradeable.sol";
```

### [N‑03] Unused comments

```solidity
File: contracts/contract/ClaimNodeOp.sol

6:// TODO remove this when dev is complete
7:// import {console} from "forge-std/console.sol";
8:// import {format} from "sol-utils/format.sol";
```

### [N‑04] Unused error

```solidity
File: contracts/contract/Vault.sol

25:	error InvalidNetworkContract();

26:	error TokenTransferFailed();

27:	error VaultTokenWithdrawalFailed();
```