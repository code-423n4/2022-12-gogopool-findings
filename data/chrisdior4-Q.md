# QA report

## Low Risk
| L-R    |Issue|Instances|
|:------:|:----|:-------:|
| [L&#x2011;01] | Missing zero address check  | 1 |
| [L&#x2011;02] | Signature malleability for `ecrecover`  | 1 |
| [L&#x2011;03] | Race condition on ERC20 approval | 2 |

Total: 4 instances over 3 issues

## Non-critical

| N-C    |Issue|Instances|
|:------:|:----|:-------:|
| [N&#x2011;01] | The nonReentrant modifier should occur before all other modifiers | 2 |
| [N&#x2011;02] | Typos | 5 |
| [N&#x2011;03] | It is best practice to declare the events inside the interface, not in the implementation | 1 |
| [N&#x2011;04] | Event is missing indexed fields | 3 |
| [N&#x2011;05] | Lack of event emitting  | 8 |
| [N&#x2011;06] | Open TODO in the code | 2 |
| [N&#x2011;07] | No need to do `== false` or `== true` on boolean expressions  | 2 |
| [N&#x2011;08] | Constants should be defined rather than using magic numbers | 1 |
| [N&#x2011;09] | Unused import in `TokenggAVAX.sol` | 1 |

Total: 25 instances over 9 issues

## Low Risk

### \[L-01\] Missing zero address check 

Consider adding a check for zero address in `MultisigManager.sol`

```solidity
function registerMultisig(address addr) external onlyGuardian {
```

Line of code:

- https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/MultisigManager.sol#L35

### \[L-02\] Signature malleability for `ecrecover` 

File: `ERC20Upgradable.sol`

The implementation of a cryptographic signature system in Ethereum contracts often assumes that the signature is unique, but signatures can be altered without the possession of the private key and still be valid. The EVM specification defines several so-called ‘precompiled’ contracts one of them being ecrecover which executes the elliptic curve public key recovery. A malicious user can slightly modify the three values v, r and s to create other valid signatures. A system that performs signature verification on contract level might be susceptible to attacks if the signature is part of the signed message hash. Valid signatures could be created by a malicious user to replay previously signed messages.

```solidity
address recoveredAddress = ecrecover(
```

Line of code:

- https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L132


Use OpenZeppelin ECDSA library.


### \[L-03\] Race condition on ERC20 approval

Using approve() to manage allowances opens yourself and users of the token up to frontrunning.
Best practice, but doesn't usually matter.

Here is a possible attack scenario:
1. Alice allows Bob to transfer N of Alice's tokens (N>0)  by calling the approve method on a Token smart contract, passing the Bob's address and N as the method arguments

2. After some time, Alice decides to change from N to M (M>0) the number of Alice's tokens Bob is allowed to transfer, so she calls the approve method again, this time passing the Bob's address and M as the method arguments

3. Bob notices the Alice's second transaction before it was mined and quickly sends another transaction that calls the transferFrom method to transfer N Alice's tokens somewhere

4. If the Bob's transaction will be executed before the Alice's transaction, then Bob will successfully transfer N Alice's tokens and will gain an ability to transfer another M tokens

5. Before Alice noticed that something went wrong, Bob calls the transferFrom method again, this time to transfer M Alice's tokens.

6. So, an Alice's attempt to change the Bob's allowance from N to M (N>0 and M>0) made it possible for Bob to transfer N+M of Alice's tokens, while Alice never wanted to allow so many of her tokens to be transferred by Bob.

File: `ClaimNodeOp.sol`

```solidity
ggp.approve(address(staking), restakeAmt);
```

Lines of code:

- https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/ClaimNodeOp.sol#L105


File: `Staking.sol`

```solidity
ggp.approve(address(vault), amount);
```

Line of code:

- https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/Staking.sol#L342


Add increaseAllowance and decreaseAllowance methods 


## Non-critical


### \[NC-01\] The nonReentrant modifier should occur before all other modifiers

This is a best-practice to protect against reentrancy in other modifiers.

File: `Vault.sol`

```solidity
function withdrawAVAX(uint256 amount) external onlyRegisteredNetworkContract nonReentrant {
```

```solidity
function withdrawToken(
		address withdrawalAddress,
		ERC20 tokenAddress,
		uint256 amount
	) external onlyRegisteredNetworkContract nonReentrant {
```

Lines of code:

- https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/Vault.sol#L61

- https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/Vault.sol#L141


### \[NC-02\] Typos 

File: `MinipoolManager.sol`

```solidity
/// @param avaxTotalRewardAmt The rewards the node recieved from Avalanche for being a validator
```

```solidity
// Calculate rewards splits (these will all be zero if no rewards were recvd)
```

```solidity
/// @notice A staking error occured while registering the node as a validator
```

```solidity
/// @return The approximate rewards the node should recieve from Avalanche for beign a validator
```

```solidity
/// @return minipools in the protocol that adhear to the paramaters
```

Lines of code:

- https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/MinipoolManager.sol#L383

- https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/MinipoolManager.sol#L411

- https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/MinipoolManager.sol#L480

- https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/MinipoolManager.sol#L556

- https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/MinipoolManager.sol#L606

### \[NC-03\] It is best practice to declare the events, custom errors and structs inside the interface, not in the implementation

In `MinipoolManager.sol` there is a lot of documentation, custom errors, couple of events and struct declared, consider creating an interface. By moving them inside the interface, the contract focuses more on the logic and how it does things, providing better clarity, separation of concerns and clean code.

### \[NC-04\] Event is missing indexed fields

Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

File `MultisigManager.sol`

```solidity
event DisabledMultisig(address indexed multisig, address actor);
event EnabledMultisig(address indexed multisig, address actor);
event RegisteredMultisig(address indexed multisig, address actor);
```

Lines of code:

- https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/MultisigManager.sol#L23

- https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/MultisigManager.sol#L24

- https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/MultisigManager.sol#L25

### \[NC-05\] Lack of event emitting 

In `Storage.sol` there is a section for setter functions from row 104 to 128. All of the functions there are missing event emitting.

Couple of examples:

```solidity
function setAddress(bytes32 key, address value) external onlyRegisteredNetworkContract {
		addressStorage[key] = value;
	}

	function setBool(bytes32 key, bool value) external onlyRegisteredNetworkContract {
		booleanStorage[key] = value;
```

Consider emitting events after sensitive changes take place, to facilitate tracking and notify off-chain clients following the contract’s activity

### \[NC-06\] Open TODO in the code

There is an open TODO in  - this implies changes that might not be audited. Resolve it or remove it.

File: `MinipoolManager.sol`

```solidity
// TODO Revisit this logic if we ever allow unequal matched funds
```

Line of code: 

- https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/MinipoolManager.sol#L412

File: `Staking.sol`

```solidity
// TODO cant use onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) since we also call from increaseMinipoolCount. Wat do?
```

Line of code:

- https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/Staking.sol#L203

### \[NC-07\] No need to do `== false` or `== true` on boolean expressions 

Just use `!boolexpr` for `== false` and `boolexpr` for `==true`

File: `BaseAbstract.sol`

```solidity
if (getBool(keccak256(abi.encodePacked("contract.exists", msg.sender))) == false) 
```

```solidity
if (enabled == false) {
```

Lines of code:

- https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/BaseAbstract.sol#L25

- https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/BaseAbstract.sol#L74

### \[NC-08\] Constants should be defined rather than using magic numbers

File: `MinipoolManager.sol`

```solidity
uint256 avaxHalfRewards = avaxTotalRewardAmt / 2;
```

Line of code:

- https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/MinipoolManager.sol#L413

### \[NC-09\]  Unused import in `TokenggAVAX.sol`

```solidity
import {ERC20Upgradeable} from "./upgradeable/ERC20Upgradeable.sol";
```

Remove the unused import.

