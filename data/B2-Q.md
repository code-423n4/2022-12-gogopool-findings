## Missing `zero address` check in `constructor`

Missing checks for zero-addresses may lead to infunctional protocol, if the variable addresses are updated incorrectly.

```
	address private guardian;
	address public newGuardian;
```

>File: contract/Storage.sol [(line 24-25)](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L24-L25)


## Upgradeable contract is missing a `__gap[50]` storage variable to allow for new storage variables in later versions

While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.

```
		contract TokenggAVAX is Initializable, ERC4626Upgradeable, UUPSUpgradeable, BaseUpgradeable {
```
>File: contract/tokens/TokenggAVAX.sol [(line 24)](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L24)

```
		contract MinipoolManager is Base, ReentrancyGuard, IWithdrawer {
```
>File: contract/MinipoolManager.sol [(line 57)](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L57)

```
		abstract contract ERC4626Upgradeable is Initializable, ERC20Upgradeable {
```
>File: contract/tokens/upgradeable/ERC4626Upgradeable.sol [(line 11)](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L11)


## open `TODO` comments

Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment.

```
		// TODO cant use onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) since we also call from increaseMinipoolCount. Wat do?
```
* File: contracts/contract/Staking.sol [(line 203)](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L203)

```
		// TODO Revisit this logic if we ever allow unequal matched funds

```
* File: contracts/contract/MinipoolManager.sol [(line 412)](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L412)

```
		// TODO remove this when dev is complete
	
```
* File: contracts/contract/BaseAbstract.sol [(line 6)](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L6)




## `TYPOS`

```
		//@audit `exectued`
		// The constructor is exectued only when creating implementation contract
```
>File: contract/tokens/TokenggAVAX.sol [(line 67)](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L67)

```
		//@audit `fucns`
		/// @param toContractName Name of the contract fucns are being transferred to
```
>File: contracts/contract/Vault.sol [(line 82)](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L82)

```
     Other instances of this issue are :
```
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L134
//@audit `recieve`
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L203
//@audit `Wat`
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L419
//@audit `paramaters`&`adhear`
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L134
//@audit `commision`
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L46
//@audit `seperately`
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L383
//@audit `recieved`
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L556
//@audit `beign`
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L606
//@audit `adhear` & `paramaters`



## Use of `block.timestamp`

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

```
		require(deadline >= block.timestamp, "PERMIT_DEADLINE_EXPIRED");
```
* File: contract/tokens/upgradeable/ERC20Upgradeable.sol [(line 127)](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L127)

```
		rewardsCycleEnd = (block.timestamp.safeCastTo32() / rewardsCycleLength) * rewardsCycleLength;
```
* File: contract/tokens/TokenggAVAX.sol [(line 78)](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L78)

```
     Other instances of this issue are :
```
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L89
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L120
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L128
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L206
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L40-L41
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L60
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L84
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L128
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L164
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L226
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L279
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L356
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L394

## `require()` should be used instead of `assert()`

```
		assert(msg.sender == address(asset));
```

>File: contract/tokens/TokenggAVAX.sol [(line 83)](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L83)


## Use of ecrecover()`
```
			address recoveredAddress = ecrecover(
```
>File: contract/tokens/upgradeable/ERC20Upgradeable.sol [(line 132)](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L132)


## `Cross-Chain Replay` attack

Storing the `block.chainid` is not safe. 

```
		INITIAL_CHAIN_ID = block.chainid;
```
* File: contract/tokens/upgradeable/ERC20Upgradeable.sol [(line 62)](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L62)




## Set `garbage` value in `mapping` for deleting that

If there is a mapping data structure present inside struct, then deleting the struct doesn't delete the mapping. Instead one should use lock to lock that data structure from further use.


```
		delete defenders[defender];
```
>File:contract/Ocyticus.sol [(line 33)](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L33)
```
		delete addressStorage[key];
```
>File:contract/Storage.sol [(line 137)](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L137)

```
     Other instances of this issue are :
```
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L141
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L145
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L149
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L153
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L157
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L161


## `NatSpec` is incomplete

```
   	/// @audit Missing: `@param`
	/// @notice Set the rewards rate for validating Avalanche's p-chain
	/// @dev Used for testing
	function setExpectedAVAXRewardsRate(uint256 rate) public onlyMultisig valueNotGreaterThanOne(rate) {

```
* File: contract/ProtocolDAO.sol [(line 154-156)](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L154-L156)


```
   	/// @audit Missing: `@param`
	/// @notice Set the percentage a contract is owed for a rewards cycle
	function setClaimingContractPct(string memory claimingContract, uint256 decimal) public onlyGuardian 				valueNotGreaterThanOne(decimal) {

```
* File: contract/ProtocolDAO.sol [(line 106-107)](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L106-L107)
```
     Other instances of this issue are :
```
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L191-L193 
   /// @audit Missing: `return`
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L197-L200
   /// @audit Missing: `return`
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L65-L66
   /// @audit Missing: `return`
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L71-L72
   /// @audit Missing: `return`
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L78-L80
   /// @audit Missing: `return`
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L85-L87
   /// @audit Missing: `return`&'param'
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L92-L94
   /// @audit Missing: `return`&`param`
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L101-L103
   /// @audit Missing: `return`
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L149-L151
   /// @audit Missing: `return`
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L59-L61
   /// @audit Missing: `return`
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L100-L102
   /// @audit Missing: `param`
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L539-L541
   /// @audit Missing: `return`
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L545-L547
   /// @audit Missing: `return`
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L570-L572
   /// @audit Missing: `return`
