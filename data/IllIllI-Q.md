

## Summary

### Low Risk Issues
| |Issue|Instances|
|-|:-|:-:|
| [L&#x2011;01] | Inflation not locked for four years | 1 | 
| [L&#x2011;02] | Contract will stop functioning in the year 2106 | 1 | 
| [L&#x2011;03] | Lower-level initializations should come first | 1 | 
| [L&#x2011;04] | Cycle end may be be too soon | 1 | 
| [L&#x2011;05] | Incorrect percentage conversion | 1 | 
| [L&#x2011;06] | Missing checks for value of `msg.value` | 1 | 
| [L&#x2011;07] | Loss of precision | 2 | 
| [L&#x2011;08] | Signatures vulnerable to malleability attacks | 1 | 
| [L&#x2011;09] | `require()` should be used instead of `assert()` | 1 | 
| [L&#x2011;10] | Open TODOs | 1 | 

Total: 11 instances over 10 issues


### Non-critical Issues
| |Issue|Instances|
|-|:-|:-:|
| [N&#x2011;01] | Common code should be refactored | 1 | 
| [N&#x2011;02] | String constants used in multiple places should be defined as constants | 1 | 
| [N&#x2011;03] | Constants in comparisons should appear on the left side | 1 | 
| [N&#x2011;04] | Inconsistent address separator in storage names | 1 | 
| [N&#x2011;05] | Confusing function name | 1 | 
| [N&#x2011;06] | Misplaced punctuation | 1 | 
| [N&#x2011;07] | Upgradeable contract is missing a `__gap[50]` storage variable to allow for new storage variables in later versions | 1 | 
| [N&#x2011;08] | Import declarations should import specific identifiers, rather than the whole file | 13 | 
| [N&#x2011;09] | Missing `initializer` modifier on constructor | 1 | 
| [N&#x2011;10] | The `nonReentrant` `modifier` should occur before all other modifiers | 2 | 
| [N&#x2011;11] | `override` function arguments that are unused should have the variable name removed or commented out to avoid compiler warnings | 1 | 
| [N&#x2011;12] | `constant`s should be defined rather than using magic numbers | 2 | 
| [N&#x2011;13] | Missing event and or timelock for critical parameter change | 1 | 
| [N&#x2011;14] | Events that mark critical parameter changes should contain both the old and the new value | 2 | 
| [N&#x2011;15] | Use a more recent version of solidity | 1 | 
| [N&#x2011;16] | Use a more recent version of solidity | 1 | 
| [N&#x2011;17] | Constant redefined elsewhere | 2 | 
| [N&#x2011;18] | Lines are too long | 1 | 
| [N&#x2011;19] | Variable names that consist of all capital letters should be reserved for `constant`/`immutable` variables | 2 | 
| [N&#x2011;20] | Using `>`/`>=` without specifying an upper bound is unsafe | 2 | 
| [N&#x2011;21] | Typos | 3 | 
| [N&#x2011;22] | File is missing NatSpec | 3 | 
| [N&#x2011;23] | NatSpec is incomplete | 27 | 
| [N&#x2011;24] | Not using the named return variables anywhere in the function is confusing | 1 | 
| [N&#x2011;25] | Contracts should have full test coverage | 1 | 
| [N&#x2011;26] | Large or complicated code bases should implement fuzzing tests | 1 | 
| [N&#x2011;27] | Function ordering does not follow the Solidity style guide | 15 | 
| [N&#x2011;28] | Contract does not follow the Solidity style guide's suggested layout ordering | 9 | 

Total: 98 instances over 28 issues





## Low Risk Issues

### [L&#x2011;01]  Inflation not locked for four years
The [litepaper](https://docs.gogopool.com/about-gogopool/gogopool-litepaper) says that there will be no inflation for four years, but there is no code enforcing this

*There is 1 instance of this issue:*

```solidity
File: /contracts/contract/ProtocolDAO.sol

39   		// GGP Inflation
40   		setUint(keccak256("ProtocolDAO.InflationIntervalSeconds"), 1 days);
41:  		setUint(keccak256("ProtocolDAO.InflationIntervalRate"), 1000133680617113500); // 5% annual calculated on a daily interval - Calculate in js example: let dailyInflation = web3.utils.toBN((1 + 0.05) ** (1 / (365)) * 1e18);

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ProtocolDAO.sol#L39-L41

### [L&#x2011;02]  Contract will stop functioning in the year 2106
limiting the timestamp to fit in a `uint32` will cause the call below to start reverting in 2106

*There is 1 instance of this issue:*

```solidity
File: /contracts/contract/tokens/TokenggAVAX.sol

89:  		uint32 timestamp = block.timestamp.safeCastTo32();

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L89

### [L&#x2011;03]  Lower-level initializations should come first
There may not be an issue now, but if `ERC4626` changes to rely on some of the functions `BaseUpgradeable` provides, things will break

*There is 1 instance of this issue:*

```solidity
File: /contracts/contract/tokens/TokenggAVAX.sol

72   	function initialize(Storage storageAddress, ERC20 asset) public initializer {
73   		__ERC4626Upgradeable_init(asset, "GoGoPool Liquid Staking Token", "ggAVAX");
74:  		__BaseUpgradeable_init(storageAddress);

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L72-L74

### [L&#x2011;04]  Cycle end may be be too soon
The first timestamp should be based on when things are first synced, so that an appropriate duration of the cycle occurs, rather than during deployment

*There is 1 instance of this issue:*

```solidity
File: /contracts/contract/tokens/TokenggAVAX.sol

78:  		rewardsCycleEnd = (block.timestamp.safeCastTo32() / rewardsCycleLength) * rewardsCycleLength;

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L78

### [L&#x2011;05]  Incorrect percentage conversion
0.2 ether should be 20%, not 2%. [Other](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ProtocolDAO.sol#L44) areas use 0.X as X0%

*There is 1 instance of this issue:*

```solidity
File: /contracts/contract/MinipoolManager.sol

194: 	/// @param delegationFee Percentage delegation fee in units of ether (2% is 0.2 ether)

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L194

### [L&#x2011;06]  Missing checks for value of `msg.value`
Other functions in this contract such as [recordStakingError()](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L492-L494) ensure that the correct value is passed, but `recordStakingEnd()` does not

*There is 1 instance of this issue:*

```solidity
File: /contracts/contract/MinipoolManager.sol

385  	function recordStakingEnd(
386  		address nodeID,
387  		uint256 endTime,
388  		uint256 avaxTotalRewardAmt
389: 	) external payable {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L385-L389

### [L&#x2011;07]  Loss of precision
Division by large numbers may result in the result being zero, due to solidity not supporting fractions. Consider requiring a minimum amount for the numerator to ensure that it is always larger than the denominator

*There are 2 instances of this issue:*

```solidity
File: contracts/contract/RewardsPool.sol

60:   		return (block.timestamp - startTime) / dao.getInflationIntervalSeconds();

128:  		return (block.timestamp - startTime) / dao.getRewardsCycleSeconds();

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L60

### [L&#x2011;08]  Signatures vulnerable to malleability attacks
`ecrecover()` accepts as valid, two versions of signatures, meaning an attacker can use the same signature twice. Consider adding checks for signature malleability, or using OpenZeppelin's `ECDSA` library to perform the extra checks necessary in order to prevent this attack.

*There is 1 instance of this issue:*

```solidity
File: contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol

132   			address recoveredAddress = ecrecover(
133   				keccak256(
134   					abi.encodePacked(
135   						"\x19\x01",
136   						DOMAIN_SEPARATOR(),
137   						keccak256(
138   							abi.encode(
139   								keccak256("Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)"),
140   								owner,
141   								spender,
142   								value,
143   								nonces[owner]++,
144   								deadline
145   							)
146   						)
147   					)
148   				),
149   				v,
150   				r,
151   				s
152:  			);

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L132-L152

### [L&#x2011;09]  `require()` should be used instead of `assert()`
Prior to solidity version 0.8.0, hitting an assert consumes the **remainder of the transaction's available gas** rather than returning it, as `require()`/`revert()` do. `assert()` should be avoided even past solidity version 0.8.0 as its [documentation](https://docs.soliditylang.org/en/v0.8.14/control-structures.html#panic-via-assert-and-error-via-require) states that "The assert function creates an error of type Panic(uint256). ... Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix".

*There is 1 instance of this issue:*

```solidity
File: contracts/contract/tokens/TokenggAVAX.sol

83:   		assert(msg.sender == address(asset));

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L83

### [L&#x2011;10]  Open TODOs
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

*There is 1 instance of this issue:*

```solidity
File: contracts/contract/MinipoolManager.sol

412:  		// TODO Revisit this logic if we ever allow unequal matched funds

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L412

## Non-critical Issues

### [N&#x2011;01]  Common code should be refactored
[BaseAbstract](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseAbstract.sol#L71-L74) performs similar operations, so the common code should be refactored to a function

*There is 1 instance of this issue:*

```solidity
File: /contracts/contract/MultisigManager.sol

110: 		addr = getAddress(keccak256(abi.encodePacked("multisig.item", index, ".address")));

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MultisigManager.sol#L110

### [N&#x2011;02]  String constants used in multiple places should be defined as constants

*There is 1 instance of this issue:*

```solidity
File: /contracts/contract/MultisigManager.sol

110: 		addr = getAddress(keccak256(abi.encodePacked("multisig.item", index, ".address")));

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MultisigManager.sol#L110

### [N&#x2011;03]  Constants in comparisons should appear on the left side
Doing so will prevent [typo bugs](https://www.moserware.com/2008/01/constants-on-left-are-better-but-this.html)

*There is 1 instance of this issue:*

```solidity
File: /contracts/contract/ClaimNodeOp.sol

92:  		if (ggpRewards == 0) {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimNodeOp.sol#L92

### [N&#x2011;04]  Inconsistent address separator in storage names
Most addresses in storage names don't separate the prefix from the address with a period, but this one has one

*There is 1 instance of this issue:*

```solidity
File: /contracts/contract/ProtocolDAO.sol

102  	function getClaimingContractPct(string memory claimingContract) public view returns (uint256) {
103  		return getUint(keccak256(abi.encodePacked("ProtocolDAO.ClaimingContractPct.", claimingContract)));
104  	}
105  
106  	/// @notice Set the percentage a contract is owed for a rewards cycle
107  	function setClaimingContractPct(string memory claimingContract, uint256 decimal) public onlyGuardian valueNotGreaterThanOne(decimal) {
108  		setUint(keccak256(abi.encodePacked("ProtocolDAO.ClaimingContractPct.", claimingContract)), decimal);
109: 	}

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ProtocolDAO.sol#L102-L109

### [N&#x2011;05]  Confusing function name
Consider changing the name to `stakeGGPAs` or `stakeGGPFor`

*There is 1 instance of this issue:*

```solidity
File: /contracts/contract/Staking.sol

328: 	function restakeGGP(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L328

### [N&#x2011;06]  Misplaced punctuation
There's an extra comma - it looks like a find-and-replace error

*There is 1 instance of this issue:*

```solidity
File: /contracts/contract/Vault.sol

154  		// Update balances
155  		tokenBalances[contractKey] = tokenBalances[contractKey] - amount;
156  		// Get the toke ERC20 instance
157  		ERC20 tokenContract = ERC20(tokenAddress);
158  		// Withdraw to the withdrawal address, , safeTransfer will revert if it fails
159  		tokenContract.safeTransfer(withdrawalAddress, amount);
160: 	}

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L154-L160

### [N&#x2011;07]  Upgradeable contract is missing a `__gap[50]` storage variable to allow for new storage variables in later versions
See [this](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps) link for a description of this storage variable. While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.

*There is 1 instance of this issue:*

```solidity
File: contracts/contract/tokens/TokenggAVAX.sol

24:   contract TokenggAVAX is Initializable, ERC4626Upgradeable, UUPSUpgradeable, BaseUpgradeable {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L24

### [N&#x2011;08]  Import declarations should import specific identifiers, rather than the whole file
Using import declarations of the form `import {<identifier_name>} from "some/file.sol"` avoids polluting the symbol namespace making flattened files smaller, and speeds up compilation

*There are 13 instances of this issue:*

```solidity
File: contracts/contract/Base.sol

4:    import "./BaseAbstract.sol";

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Base.sol#L4

```solidity
File: contracts/contract/BaseUpgradeable.sol

4:    import "./BaseAbstract.sol";

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseUpgradeable.sol#L4

```solidity
File: contracts/contract/ClaimNodeOp.sol

4:    import "./Base.sol";

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimNodeOp.sol#L4

```solidity
File: contracts/contract/ClaimProtocolDAO.sol

4:    import "./Base.sol";

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimProtocolDAO.sol#L4

```solidity
File: contracts/contract/MinipoolManager.sol

4:    import "./Base.sol";

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L4

```solidity
File: contracts/contract/MultisigManager.sol

4:    import "./Base.sol";

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MultisigManager.sol#L4

```solidity
File: contracts/contract/Ocyticus.sol

4:    import "./Base.sol";

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Ocyticus.sol#L4

```solidity
File: contracts/contract/Oracle.sol

4:    import "./Base.sol";

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Oracle.sol#L4

```solidity
File: contracts/contract/ProtocolDAO.sol

4:    import "./Base.sol";

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ProtocolDAO.sol#L4

```solidity
File: contracts/contract/RewardsPool.sol

4:    import "./Base.sol";

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L4

```solidity
File: contracts/contract/Staking.sol

4:    import "./Base.sol";

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L4

```solidity
File: contracts/contract/tokens/TokenggAVAX.sol

6:    import "../BaseUpgradeable.sol";

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L6

```solidity
File: contracts/contract/Vault.sol

4:    import "./Base.sol";

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L4

### [N&#x2011;09]  Missing `initializer` modifier on constructor
OpenZeppelin [recommends](https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680/5) that the `initializer` modifier be applied to constructors in order to avoid potential griefs, [social engineering](https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680/4), or exploits. Ensure that the modifier is applied to the implementation contract. If the default constructor is currently being used, it should be changed to be an explicit one with the modifier applied.

*There is 1 instance of this issue:*

```solidity
File: contracts/contract/BaseUpgradeable.sol

9:    contract BaseUpgradeable is Initializable, BaseAbstract {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseUpgradeable.sol#L9

### [N&#x2011;10]  The `nonReentrant` `modifier` should occur before all other modifiers
This is a best-practice to protect against reentrancy in other modifiers

*There are 2 instances of this issue:*

```solidity
File: contracts/contract/Vault.sol

61:   	function withdrawAVAX(uint256 amount) external onlyRegisteredNetworkContract nonReentrant {

141:  	) external onlyRegisteredNetworkContract nonReentrant {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L61

### [N&#x2011;11]  `override` function arguments that are unused should have the variable name removed or commented out to avoid compiler warnings

*There is 1 instance of this issue:*

```solidity
File: contracts/contract/tokens/TokenggAVAX.sol

255:  	function _authorizeUpgrade(address newImplementation) internal override onlyGuardian {}

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L255

### [N&#x2011;12]  `constant`s should be defined rather than using magic numbers
Even [assembly](https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39) can benefit from using readable constants instead of hex/numeric literals

*There are 2 instances of this issue:*

```solidity
File: contracts/contract/MinipoolManager.sol

/// @audit 365
560:  		return (avaxAmt.mulWadDown(rate) * duration) / 365 days;

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L560

```solidity
File: contracts/contract/tokens/TokenggAVAX.sol

/// @audit 14
76:   		rewardsCycleLength = 14 days;

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L76

### [N&#x2011;13]  Missing event and or timelock for critical parameter change
Events help non-contract tools to track changes, and events prevent users from being surprised by changes

*There is 1 instance of this issue:*

```solidity
File: contracts/contract/Storage.sol

41    	function setGuardian(address newAddress) external {
42    		// Check tx comes from current guardian
43    		if (msg.sender != guardian) {
44    			revert MustBeGuardian();
45    		}
46    		// Store new address awaiting confirmation
47    		newGuardian = newAddress;
48:   	}

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L41-L48

### [N&#x2011;14]  Events that mark critical parameter changes should contain both the old and the new value
This should especially be done if the new value is not required to be different from the old value

*There are 2 instances of this issue:*

```solidity
File: contracts/contract/MultisigManager.sol

/// @audit registerMultisig()
50:   		emit RegisteredMultisig(addr, msg.sender);

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MultisigManager.sol#L50

```solidity
File: contracts/contract/Oracle.sol

/// @audit setGGPPriceInAVAX()
67:   		emit GGPPriceUpdated(price, timestamp);

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Oracle.sol#L67

### [N&#x2011;15]  Use a more recent version of solidity
Use a solidity version of at least 0.8.13 to get the ability to use `using for` with a list of free functions

*There is 1 instance of this issue:*

```solidity
File: contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol

2:    pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L2

### [N&#x2011;16]  Use a more recent version of solidity
Use a solidity version of at least 0.8.4 to get `bytes.concat()` instead of `abi.encodePacked(<bytes>,<bytes>)`
Use a solidity version of at least 0.8.12 to get `string.concat()` instead of `abi.encodePacked(<str>,<str>)`

*There is 1 instance of this issue:*

```solidity
File: contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol

2:    pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L2

### [N&#x2011;17]  Constant redefined elsewhere
Consider defining in only one contract so that values cannot become out of sync when only one location is updated. A [cheap way](https://medium.com/coinmonks/gas-cost-of-solidity-library-functions-dbe0cedd4678) to store constants in a single location is to create an `internal constant` in a `library`. If the variable is a local cache of another contract's value, consider making the cache variable internal or private, which will require external users to query the contract with the source of truth, so that callers don't get out of sync.

*There are 2 instances of this issue:*

```solidity
File: contracts/contract/MinipoolManager.sol

/// @audit seen in contracts/contract/ClaimNodeOp.sol 
78:   	ERC20 public immutable ggp;

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L78

```solidity
File: contracts/contract/Staking.sol

/// @audit seen in contracts/contract/MinipoolManager.sol 
58:   	ERC20 public immutable ggp;

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L58

### [N&#x2011;18]  Lines are too long
Usually lines in source code are limited to [80](https://softwareengineering.stackexchange.com/questions/148677/why-is-80-characters-the-standard-limit-for-code-width) characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over [164](https://github.com/aizatto/character-length) characters, the lines below should be split when they reach that length

*There is 1 instance of this issue:*

```solidity
File: contracts/contract/ProtocolDAO.sol

41:   		setUint(keccak256("ProtocolDAO.InflationIntervalRate"), 1000133680617113500); // 5% annual calculated on a daily interval - Calculate in js example: let dailyInflation = web3.utils.toBN((1 + 0.05) ** (1 / (365)) * 1e18);

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ProtocolDAO.sol#L41

### [N&#x2011;19]  Variable names that consist of all capital letters should be reserved for `constant`/`immutable` variables
If the variable needs to be different based on which class it comes from, a `view`/`pure` _function_ should be used instead (e.g. like [this](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/76eee35971c2541585e05cbf258510dda7b2fbc6/contracts/token/ERC20/extensions/draft-IERC20Permit.sol#L59)).

*There are 2 instances of this issue:*

```solidity
File: contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol

43:   	uint256 internal INITIAL_CHAIN_ID;

45:   	bytes32 internal INITIAL_DOMAIN_SEPARATOR;

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L43

### [N&#x2011;20]  Using `>`/`>=` without specifying an upper bound is unsafe
There _will_ be breaking changes in future versions of solidity, and at that point your code will no longer be compatable. While you may have the specific version to use in a configuration file, others that include your source files may not.

*There are 2 instances of this issue:*

```solidity
File: contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol

2:    pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L2

```solidity
File: contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol

2:    pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L2

### [N&#x2011;21]  Typos

*There are 3 instances of this issue:*

```solidity
File: contracts/contract/MinipoolManager.sol

/// @audit avalanchego
47:   	minipool.item<index>.avaxTotalRewardAmt = Actual total avax rewards paid by avalanchego to the TSS P-chain addr

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L47

```solidity
File: contracts/contract/tokens/TokenggAVAX.sol

/// @audit exectued
67:   		// The constructor is exectued only when creating implementation contract

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L67

```solidity
File: contracts/contract/Vault.sol

/// @audit withdrawl
68:   		// Emit the withdrawl event for that contract

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L68

### [N&#x2011;22]  File is missing NatSpec

*There are 3 instances of this issue:*

```solidity
File: contracts/contract/BaseUpgradeable.sol


```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseUpgradeable.sol

```solidity
File: contracts/contract/tokens/TokenGGP.sol


```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenGGP.sol

```solidity
File: contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol


```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol

### [N&#x2011;23]  NatSpec is incomplete

*There are 27 instances of this issue:*

```solidity
File: contracts/contract/MinipoolManager.sol

/// @audit Missing: '@return'
544   
545   	/// @notice Calculates how much GGP should be slashed given an expected avaxRewardAmt
546   	/// @param avaxRewardAmt The amount of AVAX that should have been awarded to the validator by Avalanche
547:  	function calculateGGPSlashAmt(uint256 avaxRewardAmt) public view returns (uint256) {

/// @audit Missing: '@return'
569   
570   	/// @notice Gets the minipool information from the node ID
571   	/// @param nodeID 20-byte Avalanche node ID
572:  	function getMinipoolByNodeID(address nodeID) public view returns (Minipool memory mp) {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L544-L547

```solidity
File: contracts/contract/ProtocolDAO.sol

/// @audit Missing: '@return'
58    
59    	/// @notice Get if a contract is paused
60    	/// @param contractName The contract that is being checked
61:   	function getContractPaused(string memory contractName) public view returns (bool) {

/// @audit Missing: '@param claimingContract'
99    
100   	/// @notice The percentage a contract is owed for a rewards cycle
101   	/// @return uint256 Rewards percentage a contract will receive this cycle
102:  	function getClaimingContractPct(string memory claimingContract) public view returns (uint256) {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ProtocolDAO.sol#L58-L61

```solidity
File: contracts/contract/Staking.sol

/// @audit Missing: '@return'
77    
78    	/// @notice The amount of GGP a given staker is staking
79    	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
80:   	function getGGPStake(address stakerAddr) public view returns (uint256) {

/// @audit Missing: '@param amount'
84    
85    	/// @notice Increase the amount of GGP a given staker is staking
86    	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
87:   	function increaseGGPStake(address stakerAddr, uint256 amount) internal {

/// @audit Missing: '@param amount'
91    
92    	/// @notice Decrease the amount of GGP a given staker is staking
93    	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
94:   	function decreaseGGPStake(address stakerAddr, uint256 amount) internal {

/// @audit Missing: '@return'
100   
101   	/// @notice The amount of AVAX a given staker is staking
102   	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
103:  	function getAVAXStake(address stakerAddr) public view returns (uint256) {

/// @audit Missing: '@param amount'
107   
108   	/// @notice Increase the amount of AVAX a given staker is staking
109   	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
110:  	function increaseAVAXStake(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

/// @audit Missing: '@param amount'
114   
115   	/// @notice Decrease the amount of AVAX a given staker is staking
116   	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
117:  	function decreaseAVAXStake(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

/// @audit Missing: '@return'
123   
124   	/// @notice The amount of AVAX a given staker is assigned by the protocol (for minipool creation)
125   	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
126:  	function getAVAXAssigned(address stakerAddr) public view returns (uint256) {

/// @audit Missing: '@param amount'
130   
131   	/// @notice Increase the amount of AVAX a given staker is assigned by the protocol (for minipool creation)
132   	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
133:  	function increaseAVAXAssigned(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

/// @audit Missing: '@param amount'
137   
138   	/// @notice Decrease the amount of AVAX a given staker is assigned by the protocol (for minipool creation)
139   	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
140:  	function decreaseAVAXAssigned(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

/// @audit Missing: '@return'
146   
147   	/// @notice Largest total AVAX amt assigned to a staker during a rewards period
148   	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
149:  	function getAVAXAssignedHighWater(address stakerAddr) public view returns (uint256) {

/// @audit Missing: '@param amount'
153   
154   	/// @notice Increase the AVAXAssignedHighWater
155   	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
156:  	function increaseAVAXAssignedHighWater(address stakerAddr, uint256 amount) public onlyRegisteredNetworkContract {

/// @audit Missing: '@return'
170   
171   	/// @notice The number of minipools the given staker has
172   	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
173:  	function getMinipoolCount(address stakerAddr) public view returns (uint256) {

/// @audit Missing: '@return'
193   
194   	/// @notice The timestamp when the staker registered for GGP rewards
195   	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
196:  	function getRewardsStartTime(address stakerAddr) public view returns (uint256) {

/// @audit Missing: '@param time'
200   
201   	/// @notice Set the timestamp when the staker registered for GGP rewards
202   	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
203   	// TODO cant use onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) since we also call from increaseMinipoolCount. Wat do?
204:  	function setRewardsStartTime(address stakerAddr, uint256 time) public onlyRegisteredNetworkContract {

/// @audit Missing: '@return'
214   
215   	/// @notice The amount of GGP rewards the staker has earned and not claimed
216   	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
217:  	function getGGPRewards(address stakerAddr) public view returns (uint256) {

/// @audit Missing: '@param amount'
221   
222   	/// @notice Increase the amount of GGP rewards the staker has earned and not claimed
223   	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
224:  	function increaseGGPRewards(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {

/// @audit Missing: '@param amount'
228   
229   	/// @notice Decrease the amount of GGP rewards the staker has earned and not claimed
230   	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
231:  	function decreaseGGPRewards(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {

/// @audit Missing: '@return'
237   
238   	/// @notice The most recent reward cycle number that the staker has been paid out for
239   	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
240:  	function getLastRewardsCycleCompleted(address stakerAddr) public view returns (uint256) {

/// @audit Missing: '@return'
305   
306   	/// @notice GGP that will count towards rewards this cycle
307   	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
308:  	function getEffectiveGGPStaked(address stakerAddr) external view returns (uint256) {

/// @audit Missing: '@return'
384   
385   	/// @notice Verifying the staker exists in the protocol
386   	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
387:  	function requireValidStaker(address stakerAddr) public view returns (int256) {

/// @audit Missing: '@param stakerAddr'
395   
396   	/// @notice Get index of the staker
397   	/// @return staker index or -1 if the value was not found
398:  	function getIndexOf(address stakerAddr) public view returns (int256) {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L77-L80

```solidity
File: contracts/contract/Vault.sol

/// @audit Missing: '@return'
190   
191   	/// @notice Get the AVAX balance held by a network contract
192   	/// @param networkContractName Name of the contract who's AVAX balance is being requested
193:  	function balanceOf(string memory networkContractName) external view returns (uint256) {

/// @audit Missing: '@return'
197   	/// @notice Get the balance of a token held by a network contract
198   	/// @param networkContractName Name of the contract who's token balance is being requested
199   	/// @param tokenAddress address of the ERC20 token
200:  	function balanceOfToken(string memory networkContractName, ERC20 tokenAddress) external view returns (uint256) {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L190-L193

### [N&#x2011;24]  Not using the named return variables anywhere in the function is confusing
Consider changing the variable to be an unnamed one

*There is 1 instance of this issue:*

```solidity
File: contracts/contract/MinipoolManager.sol

/// @audit mp
572:  	function getMinipoolByNodeID(address nodeID) public view returns (Minipool memory mp) {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L572

### [N&#x2011;25]  Contracts should have full test coverage
While 100% code coverage does not guarantee that there are no bugs, it often will catch easy-to-find bugs, and will ensure that there are fewer regressions when the code invariably has to be modified. Furthermore, in order to get full coverage, code authors will often have to re-organize their code so that it is more modular, so that each component can be tested separately, which reduces interdependencies between modules and layers, and makes for code that is easier to reason about and audit.

*There is 1 instance of this issue:*

```solidity
File: Various Files


```

### [N&#x2011;26]  Large or complicated code bases should implement fuzzing tests
Large code bases, or code with lots of inline-assembly, complicated math, or complicated interactions between multiple contracts, should implement [fuzzing tests](https://medium.com/coinmonks/smart-contract-fuzzing-d9b88e0b0a05). Fuzzers such as Echidna require the test writer to come up with invariants which should not be violated under any circumstances, and the fuzzer tests various inputs and function calls to ensure that the invariants always hold. Even code with 100% code coverage can still have bugs due to the order of the operations a user performs, and fuzzers, with properly and extensively-written invariants, can close this testing gap significantly.

*There is 1 instance of this issue:*

```solidity
File: Various Files


```

### [N&#x2011;27]  Function ordering does not follow the Solidity style guide
According to the [Solidity style guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions), functions should be laid out in the following order :`constructor()`, `receive()`, `fallback()`, `external`, `public`, `internal`, `private`, but the cases below do not follow this pattern

*There are 15 instances of this issue:*

```solidity
File: contracts/contract/ClaimNodeOp.sol

/// @audit setRewardsCycleTotal() came earlier
46:   	function isEligible(address stakerAddr) external view returns (bool) {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimNodeOp.sol#L46

```solidity
File: contracts/contract/MinipoolManager.sol

/// @audit requireValidStateTransition() came earlier
177   	constructor(
178   		Storage storageAddress,
179   		ERC20 ggp_,
180   		TokenggAVAX ggAVAX_
181:  	) Base(storageAddress) {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L177-L181

```solidity
File: contracts/contract/ProtocolDAO.sol

/// @audit setClaimingContractPct() came earlier
115:  	function getInflationIntervalRate() external view returns (uint256) {

/// @audit getMinCollateralizationRatio() came earlier
181:  	function getTargetGGAVAXReserveRate() external view returns (uint256) {

/// @audit unregisterContract() came earlier
209   	function upgradeExistingContract(
210   		address newAddr,
211   		string memory newName,
212   		address existingAddr
213:  	) external onlyGuardian {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ProtocolDAO.sol#L115

```solidity
File: contracts/contract/RewardsPool.sol

/// @audit inflate() came earlier
105:  	function getRewardsCycleCount() public view returns (uint256) {

/// @audit increaseRewardsCycleCount() came earlier
115:  	function getRewardsCycleStartTime() public view returns (uint256) {

/// @audit canStartRewardsCycle() came earlier
156   	function startRewardsCycle() external {
157:  		if (!canStartRewardsCycle()) {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L105

```solidity
File: contracts/contract/Staking.sol

/// @audit decreaseGGPStake() came earlier
103:  	function getAVAXStake(address stakerAddr) public view returns (uint256) {

/// @audit getEffectiveRewardsRatio() came earlier
308:  	function getEffectiveGGPStaked(address stakerAddr) external view returns (uint256) {

/// @audit _stakeGGP() came earlier
358:  	function withdrawGGP(uint256 amount) external whenNotPaused {

/// @audit getStaker() came earlier
420:  	function getStakers(uint256 offset, uint256 limit) external view returns (Staker[] memory stakers) {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L103

```solidity
File: contracts/contract/tokens/TokenggAVAX.sol

/// @audit initialize() came earlier
82    	receive() external payable {
83:   		assert(msg.sender == address(asset));

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L82-L83

```solidity
File: contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol

/// @audit __ERC20Upgradeable_init() came earlier
70:   	function approve(address spender, uint256 amount) public virtual returns (bool) {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L70

```solidity
File: contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol

/// @audit __ERC4626Upgradeable_init() came earlier
42:   	function deposit(uint256 assets, address receiver) public virtual returns (uint256 shares) {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L42

### [N&#x2011;28]  Contract does not follow the Solidity style guide's suggested layout ordering
The [style guide](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-layout) says that, within a contract, the ordering should be 1) Type declarations, 2) State variables, 3) Events, 4) Modifiers, and 5) Functions, but the contract(s) below do not follow this ordering

*There are 9 instances of this issue:*

```solidity
File: contracts/contract/ClaimNodeOp.sol

/// @audit event GGPRewardsClaimed came earlier
27:   	ERC20 public immutable ggp;

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimNodeOp.sol#L27

```solidity
File: contracts/contract/MinipoolManager.sol

/// @audit event MinipoolStatusChanged came earlier
78:   	ERC20 public immutable ggp;

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L78

```solidity
File: contracts/contract/MultisigManager.sol

/// @audit event RegisteredMultisig came earlier
27:   	uint256 public constant MULTISIG_LIMIT = 10;

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MultisigManager.sol#L27

```solidity
File: contracts/contract/Staking.sol

/// @audit event GGPWithdrawn came earlier
56:   	uint256 internal constant TENTH = 0.1 ether;

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L56

```solidity
File: contracts/contract/Storage.sol

/// @audit event GuardianChanged came earlier
15    	mapping(bytes32 => address) private addressStorage;
16    	mapping(bytes32 => bool) private booleanStorage;
17    	mapping(bytes32 => bytes) private bytesStorage;
18    	mapping(bytes32 => bytes32) private bytes32Storage;
19    	mapping(bytes32 => int256) private intStorage;
20    	mapping(bytes32 => string) private stringStorage;
21:   	mapping(bytes32 => uint256) private uintStorage;

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L15-L21

```solidity
File: contracts/contract/tokens/TokenggAVAX.sol

/// @audit event DepositedFromStaking came earlier
41:   	uint32 public lastSync;

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L41

```solidity
File: contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol

/// @audit event Approval came earlier
23:   	string public name;

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L23

```solidity
File: contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol

/// @audit event Withdraw came earlier
27:   	ERC20 public asset;

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L27

```solidity
File: contracts/contract/Vault.sol

/// @audit event TokenWithdrawn came earlier
37:   	mapping(string => uint256) private avaxBalances;

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L37


___

## Excluded findings
These findings are excluded from awards calculations because there are publicly-available automated tools that find them. The valid ones appear here for completeness

## Summary

### Low Risk Issues
| |Issue|Instances|
|-|:-|:-:|
| [L&#x2011;01] | Missing checks for `address(0x0)` when assigning values to `address` state variables | 1 | 
| [L&#x2011;02] | `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccak256()` | 155 | 

Total: 156 instances over 2 issues


### Non-critical Issues
| |Issue|Instances|
|-|:-|:-:|
| [N&#x2011;01] | Return values of `approve()` not checked | 2 | 
| [N&#x2011;02] | `public` functions not called by the contract should be declared `external` instead | 54 | 
| [N&#x2011;03] | Event is missing `indexed` fields | 26 | 

Total: 82 instances over 3 issues





## Low Risk Issues

### [L&#x2011;01]  Missing checks for `address(0x0)` when assigning values to `address` state variables

*There is 1 instance of this issue:*

```solidity
File: contracts/contract/Storage.sol

/// @audit (valid but excluded finding)
47:   		newGuardian = newAddress;

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L47

### [L&#x2011;02]  `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccak256()`
Use `abi.encode()` instead which will pad items to 32 bytes, which will [prevent hash collisions](https://docs.soliditylang.org/en/v0.8.13/abi-spec.html#non-standard-packed-mode) (e.g. `abi.encodePacked(0x123,0x456)` => `0x123456` => `abi.encodePacked(0x1,0x23456)`, but `abi.encode(0x123,0x456)` => `0x0...1230...456`). "Unless there is a compelling reason, `abi.encode` should be preferred". If there is only one argument to `abi.encodePacked()` it can often be cast to `bytes()` or `bytes32()` [instead](https://ethereum.stackexchange.com/questions/30912/how-to-compare-strings-in-solidity#answer-82739).

If all arguments are strings and or bytes, `bytes.concat()` should be used instead

*There are 155 instances of this issue:*

```solidity
File: contracts/contract/BaseAbstract.sol

/// @audit (valid but excluded finding)
25:   		if (getBool(keccak256(abi.encodePacked("contract.exists", msg.sender))) == false) {

/// @audit (valid but excluded finding)
33:   		if (contractAddress != getAddress(keccak256(abi.encodePacked("contract.address", contractName)))) {

/// @audit (valid but excluded finding)
41:   		bool isContract = getBool(keccak256(abi.encodePacked("contract.exists", msg.sender)));

/// @audit (valid but excluded finding)
52:   		bool isContract = contractAddress == getAddress(keccak256(abi.encodePacked("contract.address", contractName)));

/// @audit (valid but excluded finding)
71:   		int256 multisigIndex = int256(getUint(keccak256(abi.encodePacked("multisig.index", msg.sender)))) - 1;

/// @audit (valid but excluded finding)
72:   		address addr = getAddress(keccak256(abi.encodePacked("multisig.item", multisigIndex, ".address")));

/// @audit (valid but excluded finding)
73:   		bool enabled = (addr != address(0)) && getBool(keccak256(abi.encodePacked("multisig.item", multisigIndex, ".enabled")));

/// @audit (valid but excluded finding)
83:   		if (getBool(keccak256(abi.encodePacked("contract.paused", contractName)))) {

/// @audit (valid but excluded finding)
91:   		address contractAddress = getAddress(keccak256(abi.encodePacked("contract.address", contractName)));

/// @audit (valid but excluded finding)
100:  		string memory contractName = getString(keccak256(abi.encodePacked("contract.name", contractAddress)));

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseAbstract.sol#L25

```solidity
File: contracts/contract/MinipoolManager.sol

/// @audit (valid but excluded finding)
116:  		address owner = getAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".owner")));

/// @audit (valid but excluded finding)
130:  		address assignedMultisig = getAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".multisigAddr")));

/// @audit (valid but excluded finding)
153:  		bytes32 statusKey = keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status"));

/// @audit (valid but excluded finding)
246:  			setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".initialStartTime")), 0);

/// @audit (valid but excluded finding)
250:  			setUint(keccak256(abi.encodePacked("minipool.index", nodeID)), uint256(minipoolIndex + 1));

/// @audit (valid but excluded finding)
251:  			setAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".nodeID")), nodeID);

/// @audit (valid but excluded finding)
256:  		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Prelaunch));

/// @audit (valid but excluded finding)
257:  		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".duration")), duration);

/// @audit (valid but excluded finding)
258:  		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".delegationFee")), delegationFee);

/// @audit (valid but excluded finding)
259:  		setAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".owner")), msg.sender);

/// @audit (valid but excluded finding)
260:  		setAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".multisigAddr")), multisig);

/// @audit (valid but excluded finding)
261:  		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpInitialAmt")), msg.value);

/// @audit (valid but excluded finding)
262:  		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpAmt")), msg.value);

/// @audit (valid but excluded finding)
263:  		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")), avaxAssignmentRequest);

/// @audit (valid but excluded finding)
291:  		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Finished));

/// @audit (valid but excluded finding)
293:  		uint256 avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpAmt")));

/// @audit (valid but excluded finding)
294:  		uint256 avaxNodeOpRewardAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpRewardAmt")));

/// @audit (valid but excluded finding)
317:  		uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));

/// @audit (valid but excluded finding)
328:  		uint256 avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpAmt")));

/// @audit (valid but excluded finding)
329:  		uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));

/// @audit (valid but excluded finding)
335:  		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Launched));

/// @audit (valid but excluded finding)
360:  		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Staking));

/// @audit (valid but excluded finding)
361:  		setBytes32(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".txID")), txID);

/// @audit (valid but excluded finding)
362:  		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".startTime")), startTime);

/// @audit (valid but excluded finding)
365:  		uint256 initialStartTime = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".initialStartTime")));

/// @audit (valid but excluded finding)
367:  			setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".initialStartTime")), startTime);

/// @audit (valid but excluded finding)
370:  		address owner = getAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".owner")));

/// @audit (valid but excluded finding)
371:  		uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));

/// @audit (valid but excluded finding)
393:  		uint256 startTime = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".startTime")));

/// @audit (valid but excluded finding)
398:  		uint256 avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpAmt")));

/// @audit (valid but excluded finding)
399:  		uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));

/// @audit (valid but excluded finding)
405:  		address owner = getAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".owner")));

/// @audit (valid but excluded finding)
407:  		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Withdrawable));

/// @audit (valid but excluded finding)
408:  		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".endTime")), endTime);

/// @audit (valid but excluded finding)
409:  		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxTotalRewardAmt")), avaxTotalRewardAmt);

/// @audit (valid but excluded finding)
420:  		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpRewardAmt")), avaxNodeOpRewardAmt);

/// @audit (valid but excluded finding)
421:  		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerRewardAmt")), avaxLiquidStakerRewardAmt);

/// @audit (valid but excluded finding)
451:  		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpAmt")), compoundedAvaxNodeOpAmt);

/// @audit (valid but excluded finding)
452:  		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")), compoundedAvaxNodeOpAmt);

/// @audit (valid but excluded finding)
475:  		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Prelaunch));

/// @audit (valid but excluded finding)
488:  		address owner = getAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".owner")));

/// @audit (valid but excluded finding)
489:  		uint256 avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpAmt")));

/// @audit (valid but excluded finding)
490:  		uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));

/// @audit (valid but excluded finding)
496:  		setBytes32(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".errorCode")), errorCode);

/// @audit (valid but excluded finding)
497:  		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Error));

/// @audit (valid but excluded finding)
498:  		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxTotalRewardAmt")), 0);

/// @audit (valid but excluded finding)
499:  		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpRewardAmt")), 0);

/// @audit (valid but excluded finding)
500:  		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerRewardAmt")), 0);

/// @audit (valid but excluded finding)
522:  		setBytes32(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".errorCode")), errorCode);

/// @audit (valid but excluded finding)
531:  		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Finished));

/// @audit (valid but excluded finding)
567:  		return int256(getUint(keccak256(abi.encodePacked("minipool.index", nodeID)))) - 1;

/// @audit (valid but excluded finding)
582:  		mp.nodeID = getAddress(keccak256(abi.encodePacked("minipool.item", index, ".nodeID")));

/// @audit (valid but excluded finding)
583:  		mp.status = getUint(keccak256(abi.encodePacked("minipool.item", index, ".status")));

/// @audit (valid but excluded finding)
584:  		mp.duration = getUint(keccak256(abi.encodePacked("minipool.item", index, ".duration")));

/// @audit (valid but excluded finding)
585:  		mp.delegationFee = getUint(keccak256(abi.encodePacked("minipool.item", index, ".delegationFee")));

/// @audit (valid but excluded finding)
586:  		mp.owner = getAddress(keccak256(abi.encodePacked("minipool.item", index, ".owner")));

/// @audit (valid but excluded finding)
587:  		mp.multisigAddr = getAddress(keccak256(abi.encodePacked("minipool.item", index, ".multisigAddr")));

/// @audit (valid but excluded finding)
588:  		mp.avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxNodeOpAmt")));

/// @audit (valid but excluded finding)
589:  		mp.avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxLiquidStakerAmt")));

/// @audit (valid but excluded finding)
590:  		mp.txID = getBytes32(keccak256(abi.encodePacked("minipool.item", index, ".txID")));

/// @audit (valid but excluded finding)
591:  		mp.initialStartTime = getUint(keccak256(abi.encodePacked("minipool.item", index, ".initialStartTime")));

/// @audit (valid but excluded finding)
592:  		mp.startTime = getUint(keccak256(abi.encodePacked("minipool.item", index, ".startTime")));

/// @audit (valid but excluded finding)
593:  		mp.endTime = getUint(keccak256(abi.encodePacked("minipool.item", index, ".endTime")));

/// @audit (valid but excluded finding)
594:  		mp.avaxTotalRewardAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxTotalRewardAmt")));

/// @audit (valid but excluded finding)
595:  		mp.errorCode = getBytes32(keccak256(abi.encodePacked("minipool.item", index, ".errorCode")));

/// @audit (valid but excluded finding)
596:  		mp.avaxNodeOpInitialAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxNodeOpInitialAmt")));

/// @audit (valid but excluded finding)
597:  		mp.avaxNodeOpRewardAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxNodeOpRewardAmt")));

/// @audit (valid but excluded finding)
598:  		mp.avaxLiquidStakerRewardAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxLiquidStakerRewardAmt")));

/// @audit (valid but excluded finding)
599:  		mp.ggpSlashAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".ggpSlashAmt")));

/// @audit (valid but excluded finding)
648:  		setUint(keccak256(abi.encodePacked("minipool.item", index, ".status")), uint256(MinipoolStatus.Canceled));

/// @audit (valid but excluded finding)
650:  		address owner = getAddress(keccak256(abi.encodePacked("minipool.item", index, ".owner")));

/// @audit (valid but excluded finding)
651:  		uint256 avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxNodeOpAmt")));

/// @audit (valid but excluded finding)
652:  		uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxLiquidStakerAmt")));

/// @audit (valid but excluded finding)
671:  		address nodeID = getAddress(keccak256(abi.encodePacked("minipool.item", index, ".nodeID")));

/// @audit (valid but excluded finding)
672:  		address owner = getAddress(keccak256(abi.encodePacked("minipool.item", index, ".owner")));

/// @audit (valid but excluded finding)
673:  		uint256 duration = getUint(keccak256(abi.encodePacked("minipool.item", index, ".duration")));

/// @audit (valid but excluded finding)
674:  		uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxLiquidStakerAmt")));

/// @audit (valid but excluded finding)
677:  		setUint(keccak256(abi.encodePacked("minipool.item", index, ".ggpSlashAmt")), slashGGPAmt);

/// @audit (valid but excluded finding)
688:  		setBytes32(keccak256(abi.encodePacked("minipool.item", index, ".txID")), 0);

/// @audit (valid but excluded finding)
689:  		setUint(keccak256(abi.encodePacked("minipool.item", index, ".startTime")), 0);

/// @audit (valid but excluded finding)
690:  		setUint(keccak256(abi.encodePacked("minipool.item", index, ".endTime")), 0);

/// @audit (valid but excluded finding)
691:  		setUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxTotalRewardAmt")), 0);

/// @audit (valid but excluded finding)
692:  		setUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxNodeOpRewardAmt")), 0);

/// @audit (valid but excluded finding)
693:  		setUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxLiquidStakerRewardAmt")), 0);

/// @audit (valid but excluded finding)
694:  		setUint(keccak256(abi.encodePacked("minipool.item", index, ".ggpSlashAmt")), 0);

/// @audit (valid but excluded finding)
695:  		setBytes32(keccak256(abi.encodePacked("minipool.item", index, ".errorCode")), 0);

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L116

```solidity
File: contracts/contract/MultisigManager.sol

/// @audit (valid but excluded finding)
45:   		setAddress(keccak256(abi.encodePacked("multisig.item", index, ".address")), addr);

/// @audit (valid but excluded finding)
48:   		setUint(keccak256(abi.encodePacked("multisig.index", addr)), index + 1);

/// @audit (valid but excluded finding)
61:   		setBool(keccak256(abi.encodePacked("multisig.item", multisigIndex, ".enabled")), true);

/// @audit (valid but excluded finding)
74:   		setBool(keccak256(abi.encodePacked("multisig.item", multisigIndex, ".enabled")), false);

/// @audit (valid but excluded finding)
97:   		return int256(getUint(keccak256(abi.encodePacked("multisig.index", addr)))) - 1;

/// @audit (valid but excluded finding)
110:  		addr = getAddress(keccak256(abi.encodePacked("multisig.item", index, ".address")));

/// @audit (valid but excluded finding)
111:  		enabled = (addr != address(0)) && getBool(keccak256(abi.encodePacked("multisig.item", index, ".enabled")));

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MultisigManager.sol#L45

```solidity
File: contracts/contract/ProtocolDAO.sol

/// @audit (valid but excluded finding)
62:   		return getBool(keccak256(abi.encodePacked("contract.paused", contractName)));

/// @audit (valid but excluded finding)
68:   		setBool(keccak256(abi.encodePacked("contract.paused", contractName)), true);

/// @audit (valid but excluded finding)
74:   		setBool(keccak256(abi.encodePacked("contract.paused", contractName)), false);

/// @audit (valid but excluded finding)
103:  		return getUint(keccak256(abi.encodePacked("ProtocolDAO.ClaimingContractPct.", claimingContract)));

/// @audit (valid but excluded finding)
108:  		setUint(keccak256(abi.encodePacked("ProtocolDAO.ClaimingContractPct.", claimingContract)), decimal);

/// @audit (valid but excluded finding)
191:  		setBool(keccak256(abi.encodePacked("contract.exists", addr)), true);

/// @audit (valid but excluded finding)
192:  		setAddress(keccak256(abi.encodePacked("contract.address", name)), addr);

/// @audit (valid but excluded finding)
193:  		setString(keccak256(abi.encodePacked("contract.name", addr)), name);

/// @audit (valid but excluded finding)
200:  		deleteBool(keccak256(abi.encodePacked("contract.exists", addr)));

/// @audit (valid but excluded finding)
201:  		deleteAddress(keccak256(abi.encodePacked("contract.address", name)));

/// @audit (valid but excluded finding)
202:  		deleteString(keccak256(abi.encodePacked("contract.name", addr)));

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ProtocolDAO.sol#L62

```solidity
File: contracts/contract/Staking.sol

/// @audit (valid but excluded finding)
82:   		return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpStaked")));

/// @audit (valid but excluded finding)
89:   		addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpStaked")), amount);

/// @audit (valid but excluded finding)
96:   		subUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpStaked")), amount);

/// @audit (valid but excluded finding)
105:  		return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxStaked")));

/// @audit (valid but excluded finding)
112:  		addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxStaked")), amount);

/// @audit (valid but excluded finding)
119:  		subUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxStaked")), amount);

/// @audit (valid but excluded finding)
128:  		return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssigned")));

/// @audit (valid but excluded finding)
135:  		addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssigned")), amount);

/// @audit (valid but excluded finding)
142:  		subUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssigned")), amount);

/// @audit (valid but excluded finding)
151:  		return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssignedHighWater")));

/// @audit (valid but excluded finding)
158:  		addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssignedHighWater")), amount);

/// @audit (valid but excluded finding)
165:  		uint256 currAVAXAssigned = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssigned")));

/// @audit (valid but excluded finding)
166:  		setUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssignedHighWater")), currAVAXAssigned);

/// @audit (valid but excluded finding)
175:  		return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".minipoolCount")));

/// @audit (valid but excluded finding)
182:  		addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".minipoolCount")), 1);

/// @audit (valid but excluded finding)
189:  		subUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".minipoolCount")), 1);

/// @audit (valid but excluded finding)
198:  		return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".rewardsStartTime")));

/// @audit (valid but excluded finding)
210:  		setUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".rewardsStartTime")), time);

/// @audit (valid but excluded finding)
219:  		return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpRewards")));

/// @audit (valid but excluded finding)
226:  		addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpRewards")), amount);

/// @audit (valid but excluded finding)
233:  		subUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpRewards")), amount);

/// @audit (valid but excluded finding)
242:  		return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".lastRewardsCycleCompleted")));

/// @audit (valid but excluded finding)
250:  		setUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".lastRewardsCycleCompleted")), cycleNumber);

/// @audit (valid but excluded finding)
350:  			setUint(keccak256(abi.encodePacked("staker.index", stakerAddr)), uint256(stakerIndex + 1));

/// @audit (valid but excluded finding)
351:  			setAddress(keccak256(abi.encodePacked("staker.item", stakerIndex, ".stakerAddr")), stakerAddr);

/// @audit (valid but excluded finding)
399:  		return int256(getUint(keccak256(abi.encodePacked("staker.index", stakerAddr)))) - 1;

/// @audit (valid but excluded finding)
406:  		staker.ggpStaked = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpStaked")));

/// @audit (valid but excluded finding)
407:  		staker.avaxAssigned = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssigned")));

/// @audit (valid but excluded finding)
408:  		staker.avaxStaked = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxStaked")));

/// @audit (valid but excluded finding)
409:  		staker.stakerAddr = getAddress(keccak256(abi.encodePacked("staker.item", stakerIndex, ".stakerAddr")));

/// @audit (valid but excluded finding)
410:  		staker.minipoolCount = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".minipoolCount")));

/// @audit (valid but excluded finding)
411:  		staker.rewardsStartTime = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".rewardsStartTime")));

/// @audit (valid but excluded finding)
412:  		staker.ggpRewards = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpRewards")));

/// @audit (valid but excluded finding)
413:  		staker.lastRewardsCycleCompleted = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".lastRewardsCycleCompleted")));

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L82

```solidity
File: contracts/contract/Storage.sol

/// @audit (valid but excluded finding)
29:   		if (booleanStorage[keccak256(abi.encodePacked("contract.exists", msg.sender))] == false && msg.sender != guardian) {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L29

```solidity
File: contracts/contract/tokens/TokenggAVAX.sol

/// @audit (valid but excluded finding)
59:   		if (amt > 0 && getBool(keccak256(abi.encodePacked("contract.paused", "TokenggAVAX")))) {

/// @audit (valid but excluded finding)
207:  		if (getBool(keccak256(abi.encodePacked("contract.paused", "TokenggAVAX")))) {

/// @audit (valid but excluded finding)
217:  		if (getBool(keccak256(abi.encodePacked("contract.paused", "TokenggAVAX")))) {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L59

```solidity
File: contracts/contract/Vault.sol

/// @audit (valid but excluded finding)
124:  		bytes32 contractKey = keccak256(abi.encodePacked(networkContractName, address(tokenContract)));

/// @audit (valid but excluded finding)
179:  		bytes32 contractKeyTo = keccak256(abi.encodePacked(networkContractName, tokenAddress));

/// @audit (valid but excluded finding)
201:  		return tokenBalances[keccak256(abi.encodePacked(networkContractName, tokenAddress))];

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L124

## Non-critical Issues

### [N&#x2011;01]  Return values of `approve()` not checked
Not all `IERC20` implementations `revert()` when there's a failure in `approve()`. The function signature has a `boolean` return value and they indicate errors that way instead. By not checking the return value, operations that should have marked as failed, may potentially go through without actually approving anything

*There are 2 instances of this issue:*

```solidity
File: contracts/contract/ClaimNodeOp.sol

/// @audit (valid but excluded finding)
105:  			ggp.approve(address(staking), restakeAmt);

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimNodeOp.sol#L105

```solidity
File: contracts/contract/Staking.sol

/// @audit (valid but excluded finding)
342:  		ggp.approve(address(vault), amount);

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L342

### [N&#x2011;02]  `public` functions not called by the contract should be declared `external` instead
Contracts [are allowed](https://docs.soliditylang.org/en/latest/contracts.html#function-overriding) to override their parents' functions and change the visibility from `external` to `public`.

*There are 54 instances of this issue:*

```solidity
File: contracts/contract/ClaimNodeOp.sol

/// @audit (valid but excluded finding)
40:   	function setRewardsCycleTotal(uint256 amount) public onlySpecificRegisteredContract("RewardsPool", msg.sender) {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimNodeOp.sol#L40

```solidity
File: contracts/contract/MinipoolManager.sol

/// @audit (valid but excluded finding)
541:  	function getTotalAVAXLiquidStakerAmt() public view returns (uint256) {

/// @audit (valid but excluded finding)
572:  	function getMinipoolByNodeID(address nodeID) public view returns (Minipool memory mp) {

/// @audit (valid but excluded finding)
607   	function getMinipools(
608   		MinipoolStatus status,
609   		uint256 offset,
610   		uint256 limit
611:  	) public view returns (Minipool[] memory minipools) {

/// @audit (valid but excluded finding)
634:  	function getMinipoolCount() public view returns (uint256) {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L541

```solidity
File: contracts/contract/MultisigManager.sol

/// @audit (valid but excluded finding)
102:  	function getCount() public view returns (uint256) {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MultisigManager.sol#L102

```solidity
File: contracts/contract/ProtocolDAO.sol

/// @audit (valid but excluded finding)
61:   	function getContractPaused(string memory contractName) public view returns (bool) {

/// @audit (valid but excluded finding)
67:   	function pauseContract(string memory contractName) public onlySpecificRegisteredContract("Ocyticus", msg.sender) {

/// @audit (valid but excluded finding)
73:   	function resumeContract(string memory contractName) public onlySpecificRegisteredContract("Ocyticus", msg.sender) {

/// @audit (valid but excluded finding)
81:   	function getRewardsEligibilityMinSeconds() public view returns (uint256) {

/// @audit (valid but excluded finding)
86:   	function getRewardsCycleSeconds() public view returns (uint256) {

/// @audit (valid but excluded finding)
91:   	function getTotalGGPCirculatingSupply() public view returns (uint256) {

/// @audit (valid but excluded finding)
96:   	function setTotalGGPCirculatingSupply(uint256 amount) public onlySpecificRegisteredContract("RewardsPool", msg.sender) {

/// @audit (valid but excluded finding)
102:  	function getClaimingContractPct(string memory claimingContract) public view returns (uint256) {

/// @audit (valid but excluded finding)
107:  	function setClaimingContractPct(string memory claimingContract, uint256 decimal) public onlyGuardian valueNotGreaterThanOne(decimal) {

/// @audit (valid but excluded finding)
123:  	function getInflationIntervalSeconds() public view returns (uint256) {

/// @audit (valid but excluded finding)
130:  	function getMinipoolMinAVAXStakingAmt() public view returns (uint256) {

/// @audit (valid but excluded finding)
135:  	function getMinipoolNodeCommissionFeePct() public view returns (uint256) {

/// @audit (valid but excluded finding)
140:  	function getMinipoolMaxAVAXAssignment() public view returns (uint256) {

/// @audit (valid but excluded finding)
145:  	function getMinipoolMinAVAXAssignment() public view returns (uint256) {

/// @audit (valid but excluded finding)
150:  	function getMinipoolCancelMoratoriumSeconds() public view returns (uint256) {

/// @audit (valid but excluded finding)
156:  	function setExpectedAVAXRewardsRate(uint256 rate) public onlyMultisig valueNotGreaterThanOne(rate) {

/// @audit (valid but excluded finding)
161:  	function getExpectedAVAXRewardsRate() public view returns (uint256) {

/// @audit (valid but excluded finding)
168:  	function getMaxCollateralizationRatio() public view returns (uint256) {

/// @audit (valid but excluded finding)
173:  	function getMinCollateralizationRatio() public view returns (uint256) {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ProtocolDAO.sol#L61

```solidity
File: contracts/contract/RewardsPool.sol

/// @audit (valid but excluded finding)
105:  	function getRewardsCycleCount() public view returns (uint256) {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L105

```solidity
File: contracts/contract/Staking.sol

/// @audit (valid but excluded finding)
66:   	function getTotalGGPStake() public view returns (uint256) {

/// @audit (valid but excluded finding)
103:  	function getAVAXStake(address stakerAddr) public view returns (uint256) {

/// @audit (valid but excluded finding)
110:  	function increaseAVAXStake(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

/// @audit (valid but excluded finding)
117:  	function decreaseAVAXStake(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

/// @audit (valid but excluded finding)
133:  	function increaseAVAXAssigned(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

/// @audit (valid but excluded finding)
140:  	function decreaseAVAXAssigned(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

/// @audit (valid but excluded finding)
156:  	function increaseAVAXAssignedHighWater(address stakerAddr, uint256 amount) public onlyRegisteredNetworkContract {

/// @audit (valid but excluded finding)
163:  	function resetAVAXAssignedHighWater(address stakerAddr) public onlyRegisteredNetworkContract {

/// @audit (valid but excluded finding)
173:  	function getMinipoolCount(address stakerAddr) public view returns (uint256) {

/// @audit (valid but excluded finding)
180:  	function increaseMinipoolCount(address stakerAddr) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

/// @audit (valid but excluded finding)
187:  	function decreaseMinipoolCount(address stakerAddr) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

/// @audit (valid but excluded finding)
196:  	function getRewardsStartTime(address stakerAddr) public view returns (uint256) {

/// @audit (valid but excluded finding)
204:  	function setRewardsStartTime(address stakerAddr, uint256 time) public onlyRegisteredNetworkContract {

/// @audit (valid but excluded finding)
217:  	function getGGPRewards(address stakerAddr) public view returns (uint256) {

/// @audit (valid but excluded finding)
224:  	function increaseGGPRewards(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {

/// @audit (valid but excluded finding)
231:  	function decreaseGGPRewards(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {

/// @audit (valid but excluded finding)
240:  	function getLastRewardsCycleCompleted(address stakerAddr) public view returns (uint256) {

/// @audit (valid but excluded finding)
248:  	function setLastRewardsCycleCompleted(address stakerAddr, uint256 cycleNumber) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {

/// @audit (valid but excluded finding)
256:  	function getMinimumGGPStake(address stakerAddr) public view returns (uint256) {

/// @audit (valid but excluded finding)
328:  	function restakeGGP(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {

/// @audit (valid but excluded finding)
379:  	function slashGGP(address stakerAddr, uint256 ggpAmt) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L66

```solidity
File: contracts/contract/tokens/TokenggAVAX.sol

/// @audit (valid but excluded finding)
72:   	function initialize(Storage storageAddress, ERC20 asset) public initializer {

/// @audit (valid but excluded finding)
88    	function syncRewards() public {
89:   		uint32 timestamp = block.timestamp.safeCastTo32();

/// @audit (valid but excluded finding)
142:  	function depositFromStaking(uint256 baseAmt, uint256 rewardAmt) public payable onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

/// @audit (valid but excluded finding)
153:  	function withdrawForStaking(uint256 assets) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

/// @audit (valid but excluded finding)
166:  	function depositAVAX() public payable returns (uint256 shares) {

/// @audit (valid but excluded finding)
180:  	function withdrawAVAX(uint256 assets) public returns (uint256 shares) {

/// @audit (valid but excluded finding)
191:  	function redeemAVAX(uint256 shares) public returns (uint256 assets) {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L72

### [N&#x2011;03]  Event is missing `indexed` fields
Index event fields make the field more quickly accessible [to off-chain tools](https://ethereum.stackexchange.com/questions/40396/can-somebody-please-explain-the-concept-of-event-indexing) that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each `event` should use three `indexed` fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

*There are 26 instances of this issue:*

```solidity
File: contracts/contract/ClaimNodeOp.sol

/// @audit (valid but excluded finding)
25:   	event GGPRewardsClaimed(address indexed to, uint256 amount);

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimNodeOp.sol#L25

```solidity
File: contracts/contract/ClaimProtocolDAO.sol

/// @audit (valid but excluded finding)
13:   	event GGPTokensSentByDAOProtocol(string invoiceID, address indexed from, address indexed to, uint256 amount);

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimProtocolDAO.sol#L13

```solidity
File: contracts/contract/MinipoolManager.sol

/// @audit (valid but excluded finding)
75:   	event GGPSlashed(address indexed nodeID, uint256 ggp);

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L75

```solidity
File: contracts/contract/MultisigManager.sol

/// @audit (valid but excluded finding)
23:   	event DisabledMultisig(address indexed multisig, address actor);

/// @audit (valid but excluded finding)
24:   	event EnabledMultisig(address indexed multisig, address actor);

/// @audit (valid but excluded finding)
25:   	event RegisteredMultisig(address indexed multisig, address actor);

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MultisigManager.sol#L23

```solidity
File: contracts/contract/Oracle.sol

/// @audit (valid but excluded finding)
21:   	event GGPPriceUpdated(uint256 indexed price, uint256 timestamp);

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Oracle.sol#L21

```solidity
File: contracts/contract/RewardsPool.sol

/// @audit (valid but excluded finding)
24:   	event GGPInflated(uint256 newTokens);

/// @audit (valid but excluded finding)
25:   	event NewRewardsCycleStarted(uint256 totalRewardsAmt);

/// @audit (valid but excluded finding)
26:   	event ClaimNodeOpRewardsTransfered(uint256 value);

/// @audit (valid but excluded finding)
27:   	event ProtocolDAORewardsTransfered(uint256 value);

/// @audit (valid but excluded finding)
28:   	event MultisigRewardsTransfered(uint256 value);

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L24

```solidity
File: contracts/contract/Staking.sol

/// @audit (valid but excluded finding)
40:   	event GGPStaked(address indexed from, uint256 amount);

/// @audit (valid but excluded finding)
41:   	event GGPWithdrawn(address indexed to, uint256 amount);

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L40

```solidity
File: contracts/contract/Storage.sol

/// @audit (valid but excluded finding)
12:   	event GuardianChanged(address oldGuardian, address newGuardian);

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L12

```solidity
File: contracts/contract/tokens/TokenggAVAX.sol

/// @audit (valid but excluded finding)
36:   	event NewRewardsCycle(uint256 indexed cycleEnd, uint256 rewardsAmt);

/// @audit (valid but excluded finding)
37:   	event WithdrawnForStaking(address indexed caller, uint256 assets);

/// @audit (valid but excluded finding)
38:   	event DepositedFromStaking(address indexed caller, uint256 baseAmt, uint256 rewardsAmt);

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L36

```solidity
File: contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol

/// @audit (valid but excluded finding)
15:   	event Transfer(address indexed from, address indexed to, uint256 amount);

/// @audit (valid but excluded finding)
17:   	event Approval(address indexed owner, address indexed spender, uint256 amount);

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L15

```solidity
File: contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol

/// @audit (valid but excluded finding)
19:   	event Deposit(address indexed caller, address indexed owner, uint256 assets, uint256 shares);

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L19

```solidity
File: contracts/contract/Vault.sol

/// @audit (valid but excluded finding)
30:   	event AVAXDeposited(string indexed by, uint256 amount);

/// @audit (valid but excluded finding)
31:   	event AVAXTransfer(string indexed from, string indexed to, uint256 amount);

/// @audit (valid but excluded finding)
32:   	event AVAXWithdrawn(string indexed by, uint256 amount);

/// @audit (valid but excluded finding)
33:   	event TokenDeposited(bytes32 indexed by, address indexed tokenAddress, uint256 amount);

/// @audit (valid but excluded finding)
35:   	event TokenWithdrawn(bytes32 indexed by, address indexed tokenAddress, uint256 amount);

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L30


