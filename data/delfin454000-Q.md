## Summary	
### Low risk findings

| Issue | Description | Instances |
| -- | ----------- | -------- |
|1| Use `require` rather than `assert`|1|
|2| Important open TODOs|1|
|| Total|2|

### Non-critical findings

| Issue | Description | Instances |
| -- | ----------- | -------- |
|1| `nonReentrant` modifier should appear before all other modifiers|2|
|2| Unused override function arguments|1|
|3| Unclear comment|1|
|4| TODOs, warnings and other open items|6|
|5| `pragma solidity` version should be made consistent|2|
|6| Typos|17|
|7| Long single-line comments|7|
|8-1| Natspec is entirely missing|59|
|8-2| Natspec is missing at least one `@param`|21|
|8-3| Only `@return` is missing|39|
|| Total|155|

### Low risk findings

| No. | Explanation + instances | 
|--| ----------- | 
|1|**Use `require` rather than `assert`**|
|  | On failure, the `assert` function causes a `Panic` error and, unlike `require`, does not generate an error string. According to Solidity v0.8.17, "properly functioning code should never create a Panic."|

[TokenggAVAX.sol: L83](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L83)		
```solidity		
		assert(msg.sender == address(asset));
```		
___
| No. | Explanation + instances | 
|--| ------------------------------------------------------------------------------------------- | 
|2|**Important open TODO**|
||Significant TODOs should be addressed before deployment, if possible|

[Staking.sol: L203](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L203)	
```solidity	
	// TODO cant use onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) since we also call from increaseMinipoolCount. Wat do?
```	
___	
___

### Non-critical findings

| No. | Description |
|--| ----------- | 
|1|`nonReentrant` modifier should appear before all other modifiers|
|  | Adopting this rule protects against the possibility of reentrancy in other modifiers|
		
[Vault.sol: L61](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L61)		
```solidity		
	function withdrawAVAX(uint256 amount) external onlyRegisteredNetworkContract nonReentrant {	
```		
Similarly for the following:
	
[Vault.sol: L141](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L141)		
___		
| No. | Description |
|--| ----------- | 
|2|Unused override function arguments|
|  |If an override function argument is never used, the variable name should be removed or else commented out to avoid compiler warnings. Below, `newImplementation` is unused.|
	
[TokenggAVAX.sol: L255](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L255)	
```solidity	
	function _authorizeUpgrade(address newImplementation) internal override onlyGuardian {}
```	
___	
| No. | Description |
|--| ----------- | 
|3|Unclear comment|
|  |The readability of the comment below could be improved:|	
	
[MinipoolManager.sol: L309](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L309)			
```solidity			
	/// @notice Verifies that the minipool related the the given node ID is able to a validator		
```			
Suggestion: Change to the comment below, assuming it reflects what was intended			
```solidity			
	/// @notice Verifies that the minipool related to the given node ID is able to become a validator		
```			
___			
| No. | Description |
|--| ----------- | 
|4|TODOs, warnings and other open items|
|  |Comments that refer to open items should be addressed before implementation|

___									
[BaseAbstract.sol: L6](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseAbstract.sol#L6)				
```solidity				
// TODO remove this when dev is complete				
```				
___				
[MinipoolManager.sol: L412](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L412)				
```solidity				
		// TODO Revisit this logic if we ever allow unequal matched funds		
```				
___				
[Oracle.sol: L33](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Oracle.sol#L33)				
```solidity				
	/// @dev NEVER call this on-chain, only off-chain oracle should call, then send a setGGPPriceInAVAX tx			
```				
___				
[ProtocolDAO.sol: L155](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ProtocolDAO.sol#L155)				
```solidity				
	/// @dev Used for testing			
```				
___				
[Vault.sol: L14](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L14)				
```solidity				
// !!!WARNING!!! The Vault contract must not be upgraded				
```				
___				
[ERC20Upgradeable.sol: L9](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L9)				
```solidity				
/// @dev Do not manually set balances without updating totalSupply, as the sum of all user balances must not exceed it.				
```				
___				
| No. | Description |
|--| ----------- | 
|5|`pragma solidity` version should be made consistent|
|  |The solidity version is given as `pragma solidity 0.8.17` (the latest version) in the majority of the contracts. However, it is given as `pragma solidity >=0.8.0` in two cases, as shown below. The versions should be made consistent before finalization.|				
		
[ERC20Upgradeable.sol: L2](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L2)			
```solidity			
pragma solidity >=0.8.0;			
```			
Similarly for the following:
	
[ERC4626Upgradeable.sol: L2](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L2)			
___			
| No. | Description |
|--| ----------- | 
|6|Typos|

___	
[ClaimNodeOp.sol: L45](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimNodeOp.sol#L45)		
```solidity		
	/// @dev Eligiblity: time in protocol (secs) > RewardsEligibilityMinSeconds. Rialto will call this.	
```		
Change `Eligiblity` to `Eligibility`		
___		
[MinipoolManager.sol: L347](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L347)		
```solidity		
	/// @param txID The ID of the transaction that successfully registered the node with Avalanche to become a validater	
```		
Change `validater` to `validator`		
___		
[MinipoolManager.sol: L383](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L383)		
```solidity		
	/// @param avaxTotalRewardAmt The rewards the node recieved from Avalanche for being a validator	
```		
Change `recieved` to `received`		
___		
[MinipoolManager.sol: L480](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L480)		
```solidity		
	/// @notice A staking error occured while registering the node as a validator	
```		
Change `occured` to `occurred`		
___		
[MinipoolManager.sol: L556](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L556)		
```solidity		
	/// @return The approximate rewards the node should recieve from Avalanche for beign a validator	
```		
Change `recieve` to `receive` and `beign` to `being`		
___		
The same typos (`adhear` and `paramaters`) occur in both lines referenced below:		
		
[MinipoolManager.sol: L606](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L606)		
		
[Staking.sol: L419](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L419)		
```solidity		
	/// @return minipools in the protocol that adhear to the paramaters	
```		
Change `adhear` to `adhere` and `paramaters` to `parameters` in both cases		
___		
[Ocyticus.sol: L46](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Ocyticus.sol#L46)		
```solidity		
	/// @dev Multisigs will need to be enabled seperately, we dont know which ones to enable	
```		
Change `seperately` to `separately`		
___		
[ProtocolDAO.sol: L134](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ProtocolDAO.sol#L134)		
```solidity		
	/// @notice The node commision fee for running the hardware for the minipool	
```		
Change `commision` to `commission`		
___		
[ProtocolDAO.sol: L205](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ProtocolDAO.sol#L205)		
```solidity		
	/// @notice Upgrade a contract by unregistering the existing address, and registring a new address and name	
```		
Change `registring` to `registering`		
___		
[Staking.sol: L203](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L203)		
```solidity		
	// TODO cant use onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) since we also call from increaseMinipoolCount. Wat do?	
```		
Change `Wat` to `What to`, if that was what was intended		
___		
[Vault.sol: L81](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L81)		
```solidity		
	/// @dev No funds actually move, just bookeeping	
```		
Change `bookeeping` to `bookkeeping`		
___		
[Vault.sol: L158](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L158)		
```solidity		
		// Withdraw to the withdrawal address, , safeTransfer will revert if it fails
```		
Change `address, , safeTransfer` to `address, safeTransfer`		
___		
[TokenggAVAX: L67](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L67)		
```solidity		
		// The constructor is exectued only when creating implementation contract
```		
Change `exectued` to `executed`		
___		
| No. | Description |
|--| ----------- | 
|7|Long single-line comments|
|  |In theory, comments over 80 characters should wrap using multi-line comment syntax. Even if longer comments (up to 120 characters) are becoming acceptable, very long comments can interfere with readability. Below are instances of extra-long comments (over 120 characters) whose readability could be improved by wrapping, as shown. Note that a small indentation is used in each continuation line (which flags for the reader that the comment is ongoing), along with a period at the close (to signal the end of the comment). In addition, when an extra-long line includes both code and comments, it makes sense to put the comment in the line above.|
___			
[MinipoolManager.sol: L191](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L191)			
```solidity			
	/// @notice Accept AVAX deposit from node operator to create a Minipool. Node Operator must be staking GGP. Open to public.		
```			
Suggestion:			
```solidity			
	/// @notice Accept AVAX deposit from node operator to create a Minipool — node Operator 		
	///   must be staking GGP. Open to public.		
```			
___			
[MinipoolManager.sol: L285](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L285)			
```solidity			
	/// @notice Withdraw function for a Node Operator to claim all AVAX funds they are due (original AVAX staked, plus any AVAX rewards)		
```			
Suggestion:			
```solidity			
	/// @notice Withdraw function for a Node Operator to claim all AVAX funds they are due —		
	///   original AVAX staked, plus any AVAX rewards.		
```			
___			
[MinipoolManager.sol: L321](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L321)			
```solidity			
	/// @notice Removes the AVAX associated with a minipool from the protocol to stake it on Avalanche and register the node as a validator		
```			
Suggestion:			
```solidity			
	/// @notice Removes the AVAX associated with a minipool from the protocol to		
	///   stake it on Avalanche and register the node as a validator.		
```			
___			
[MultisigManager.sol: L67](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MultisigManager.sol#L67)			
```solidity			
	/// @dev this will prevent the multisig from completing validations. The minipool will need to be manually reassigned to a new multisig		
```			
Suggestion:			
```solidity			
	/// @dev this will prevent the multisig from completing validations. The minipool		
	///   will need to be manually reassigned to a new multisig.		
```			
___			
[ProtocolDAO.sol: L33](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ProtocolDAO.sol#L33)			
```solidity			
		setUint(keccak256("ProtocolDAO.RewardsCycleSeconds"), 28 days); // The time in which a claim period will span in seconds - 28 days by default	
```			
Suggestion:			
```solidity			
		// The time in which a claim period will span in seconds - 28 days by default	
		setUint(keccak256("ProtocolDAO.RewardsCycleSeconds"), 28 days);	
```			
___			
[ProtocolDAO.sol: L41](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ProtocolDAO.sol#L41)			
```solidity			
		setUint(keccak256("ProtocolDAO.InflationIntervalRate"), 1000133680617113500); // 5% annual calculated on a daily interval - Calculate in js example: let dailyInflation = web3.utils.toBN((1 + 0.05) ** (1 / (365)) * 1e18);	
```			
Suggestion:			
```solidity			
		// 5% annual calculated on a daily interval - Calculate in js example: 	
		//   let dailyInflation = web3.utils.toBN((1 + 0.05) ** (1 / (365)) * 1e18).	
		setUint(keccak256("ProtocolDAO.InflationIntervalRate"), 1000133680617113500); 	
```			
___			
[Staking.sol: L203](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L203)			
```solidity			
	// TODO cant use onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) since we also call from increaseMinipoolCount. Wat do?		
```			
Suggestion:			
```solidity			
	// TODO cant use onlySpecificRegisteredContract("ClaimNodeOp", msg.sender)		
	//   since we also call from increaseMinipoolCount. What to do?		
```			
___			
I've divided missing Natspec for functions into three types: (1) Natspec is entirely missing; (2) Natspec is missing at least one `@param`; and (3) only `@return` is missing. While Natspec may be informative for `internal` or `private` functions, it is not required. Therefore, I am excluding such functions. 
			
| No. | Description |
|--| ----------- | 
|8-1|Natspec is entirely missing|
|  | Natspec is absent for `external` or `public` functions|

___			
[MinipoolManager.sol: L106](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L106)				
```solidity				
	function receiveWithdrawalAVAX() external payable {}
```				
___				
[ProtocolDAO.sol: L23](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ProtocolDAO.sol#L23)				
```solidity				
	function initialize() external onlyGuardian {			
```				
___				
[RewardsPool.sol: L34](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L34)				
```solidity				
	function initialize() external onlyGuardian {			
```				
___
			
Natspec is similarly absent for the following functions:				
				
***Storage.sol***				
				
[L72](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L72), [L76](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L76), [L80](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L80), [L84](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L84), [L88](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L88), [L92](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L92)				
				
[L96](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L96), [L104](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L104), [L108](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L108), [L112](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L112), [L116](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L116)				
				
[L120](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L120), [L124](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L124), [L128](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L128), [L136](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L136), [L140](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L140)				
				
[L144](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L144), [L148](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L148), [L152](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L152), [L156](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L156), [L160](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L160)				
				
***Vault.sol***				
				
[L204](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L204), [L208](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L208)				
				
***TokenggAVAX.sol***				
				
[L72](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L72), [L132](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L132), [L142](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L142), [L153](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L153), [L166](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L166)				
				
[L180](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L180), [L191](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L191), [L225](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L225), [L229](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L229), [L233](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L233)				
				
[L237](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L237), [L241-244](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L241-L244), [L248-251](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L248-L251)				
				
***ERC20Upgradeable.sol***				
				
[L70](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L70), [L78](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L78), [L92-96](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L92-L96)				
				
[L118-126](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L118-L126), [L162](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L162)				
				
***ERC4626Upgradeable.sol***				
				
[L42](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L42), [L56](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L56), [L69-73](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L69-L73), [L91-95](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L91-L95), [L118](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L118) 				
				
[L120](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L120), [L126](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L126), [L132](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L132), [L136](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L136), [L142](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L142)				
				
[L148](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L148), [L156](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L156), [L160](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L160), [L164](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L164), [L168](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L168)				
___				
				
| No. | Description |
|--| ----------- | 
|8-2|Natspec is missing at least one `@param`|
|  | The following functions are missing one or more `@param` statements (and possibly other Natspec):|				

___						
[ClaimNodeOp.sol: L39-40](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimNodeOp.sol#L39-L40)				
```solidity				
	/// @dev Sets the total rewards for the most recent cycle			
	function setRewardsCycleTotal(uint256 amount) public onlySpecificRegisteredContract("RewardsPool", msg.sender) {			
```				
Missing: `@param amount`				
___				
[ClaimNodeOp.sol: L44-46](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimNodeOp.sol#L44-L46)				
```solidity				
	/// @notice Determines if a staker is eligible for the upcoming rewards cycle			
	/// @dev Eligiblity: time in protocol (secs) > RewardsEligibilityMinSeconds. Rialto will call this.			
	function isEligible(address stakerAddr) external view returns (bool) {			
```				
Missing: `@param stakerAddr` and `@return`				
___				
[ClaimNodeOp.sol: L54-56](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimNodeOp.sol#L54-L56)				
```solidity				
	/// @notice Set the share of rewards for a staker as a fraction of 1 ether			
	/// @dev Rialto will call this			
	function calculateAndDistributeRewards(address stakerAddr, uint256 totalEligibleGGPStaked) external onlyMultisig {			
```				
Missing: `@param stakerAddr` and `@param totalEligibleGGPStaked`				
___
				
The following functions are also missing `@param` statements:				
				
***ClaimProtocolDAO.sol***				
				
[L19-24](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimProtocolDAO.sol#L19-L24)				
				
***Ocyticus.sol***				
				
[L26-27](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Ocyticus.sol#L26-L27)				
				
***Oracle.sol***				
				
[L27-28](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Oracle.sol#L27-L28)				
				
***ProtocolDAO.sol***				
				
[L95-96](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ProtocolDAO.sol#L95-L96), [L100-102](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ProtocolDAO.sol#L100-L102), [L106-107](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ProtocolDAO.sol#L106-L107), [L154-156](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ProtocolDAO.sol#L154-L156)				
				
***Staking.sol***				
				
[L108-110](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L108-L110), [L115-117](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L115-L117), [L131-133](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L131-L133), [L138-140](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L138-L140)				
				
[L154-156](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L154-L156), [L201-204](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L201-L204), [L222-224](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L222-L224), [L229-231](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L229-L231)				
				
***Storage.sol***				
				
[L40-41](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L40-L41)				
				
***TokenggAVAX.sol***				
				
[L205-206](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L205-L206), [L215-216](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L215-L216)				
___								
| No. | Description |
|--| ----------- | 
|8-3|Only `@return` is missing|
|  |Although it is common for `@return` statements to be omitted in `GoGoPool`, they are included in some cases, as shown in the example below:|				
											
[MinipoolManager.sol: L563-566](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L563-L566)				
```solidity				
	/// @notice The index of a minipool. Returns -1 if the minipool is not found			
	/// @param nodeID 20-byte Avalanche node ID			
	/// @return The index for the given minipool			
	function getIndexOf(address nodeID) public view returns (int256) {			
```								
The following functions have complete Natspec, with the exception of missing `@return` statements, which should be added:
				
___				
[ClaimNodeOp.sol: L34-35](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimNodeOp.sol#L34-L35)				
```solidity				
	/// @notice Get the total rewards for the most recent cycle			
	function getRewardsCycleTotal() public view returns (uint256) {			
```				
___				
[MinipoolManager.sol: L539-541](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L539-L541)				
```solidity				
	/// @notice Get the total amount of AVAX from liquid stakers that is being used for minipools			
	/// @dev Get the total AVAX *actually* withdrawn from ggAVAX and sent to Rialto			
	function getTotalAVAXLiquidStakerAmt() public view returns (uint256) {			
```				
___				
[MinipoolManager.sol: L545-547](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L545-L547)				
```solidity				
	/// @notice Calculates how much GGP should be slashed given an expected avaxRewardAmt			
	/// @param avaxRewardAmt The amount of AVAX that should have been awarded to the validator by Avalanche			
	function calculateGGPSlashAmt(uint256 avaxRewardAmt) public view returns (uint256) {			
```	
___
			
The following functions are also missing `@return` statements:				
				
***MinipoolManager.sol***				
				
[L570-572](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L570-L572), [L633-634](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L633-L634)				
				
***MultisigManager.sol***				
				
[L78-80](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MultisigManager.sol#L78-L80)				
				
***ProtocolDAO.sol***				
				
[L59-61](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ProtocolDAO.sol#L59-L61), [L85-86](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ProtocolDAO.sol#L85-L86), [L90-91](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ProtocolDAO.sol#L90-L91), [L129-130](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ProtocolDAO.sol#L129-L130) 				
				
[L134-135](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ProtocolDAO.sol#L134-L135), [L139-140](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ProtocolDAO.sol#L139-L140), [L144-145](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ProtocolDAO.sol#L144-L145), [L149-150](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ProtocolDAO.sol#L149-L150) 				
				
[L160-161](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ProtocolDAO.sol#L160-L161), [L167-168](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ProtocolDAO.sol#L167-L168), [L172-173](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ProtocolDAO.sol#L172-L173)				
				
***RewardsPool.sol***				
				
[L104-105](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L104-L105), [L114-115](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L114-L115), [L119-120](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L119-L120)				
				
[L124-125](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L124-L125), [L149-151](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L149-L151)				
				
***Staking.sol***				
				
[L65-66](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L65-L66), [L71-72](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L71-L72), [L78-80](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L78-L80), [L101-103](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L101-L103), [L124-126](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L124-L126)  				
				
[L147-149](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L147-L149), [L171-173](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L171-L173), [L194-196](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L194-L196), [L215-217](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L215-L217), 				
				
[L238-240](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L238-L240), [L306-308](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L306-L308), [L385-387](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L385-L387), [L396-399](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L396-L399)				
				
***Storage.sol***				
				
[L50-51](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L50-L51)				
				
***Vault.sol***				
				
[L191-193](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L191-L193), [L197-200](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L197-L200)				
				
***TokenggAVAX.sol***				
				
[L111-113](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L111-L113)				
___	
___