# c4udit Report

## Summery
[L01] SOLIDITY PRAGMA VERSIONING SHOULD BE EXACTLY THE SAME IN ALL CONTRACTS
[L02] USE A MORE RECENT VERSION OF SOLIDITY
[L03] SHADOWED VARIABLE
[L04] USE REQUIRE INSTEAD OF ASSERT
[L05] USE _SAFEMINT INSTEAD OF _MINT
[L06] ADD GAP TO RESERVE SPACE ON UPGRADEBLE CONTRACTS TO ADD NEW VARIABLES
[N01] COMMENTED CODE
[N02] INCORRECT FUNCTION ORDER
[N03] VARIABLE NAMES THAT CONSIST OF ALL CAPITAL LETTERS SHOULD BE RESERVED FOR CONSTANT/IMMUTABLE VARIABLES
[N04] NATSPEC IS MISSING
[N05] FUNCTION NAME SHOULD BE WRITTEN IN MIXED CASE
[N06] THE NONREENTRANT MODIFIER SHOULD OCCUR BEFORE ALL OTHER MODIFIERS

## Issues found

### [L01] SOLIDITY PRAGMA VERSIONING SHOULD BE EXACTLY THE SAME IN ALL CONTRACTS

#### Findings:
The following files have different pragma versions from other files.

```
contracts\contract\tokens\upgradeable\ERC20Upgradeable.sol::2 => pragma solidity >=0.8.0;
contracts\contract\tokens\ERC20.sol::2 => pragma solidity >=0.8.0;
```
#### Tools used 
Manual

### [L02] USE A MORE RECENT VERSION OF SOLIDITY

#### Impact 
Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions.
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings

#### Findings:
```
contracts\contract\tokens\upgradeable\ERC20Upgradeable.sol::2 => pragma solidity >=0.8.0;
contracts\contract\tokens\ERC20.sol::2 => pragma solidity >=0.8.0;
```
#### Tools used 
Manual

### [L03] SHADOWED VARIABLE

#### Impact
 shadowing local variables is naming conventions found in two or more variables that are similar. Although they do not pose any immediate risk to the contract, incorrect usage of the variables is possible and can cause serious issues if the developer does not pay close attention.

#### Impact
The asset cariable is being shadowed.

#### Findings:
```
contracts\contract\tokens\TokenggAVAX.sol::73 => __ERC4626Upgradeable_init(asset, "GoGoPool Liquid Staking Token", "ggAVAX");
```
#### Tools used 
Manual

### [L04] USE REQUIRE INSTEAD OF ASSERT

#### Findings:
```
contracts\contract\tokens\TokenggAVAX.sol::83 => assert(msg.sender == address(asset));
```
#### Tools used 
Manual

### [L05] USE _SAFEMINT INSTEAD OF _MINT

#### Findings:
```
contracts\contract\tokens\TokenGGP.sol::83 => _mint(msg.sender, TOTAL_SUPPLY);
contracts\contract\tokens\upgradeable\ERC4626Upgradeable.sol::49 => _mint(receiver, shares);
contracts\contract\tokens\upgradeable\ERC4626Upgradeable.sol::62 => _mint(receiver, shares); 
```
#### Tools used 
Manual

### [L06] ADD GAP TO RESERVE SPACE ON UPGRADEBLE CONTRACTS TO ADD NEW VARIABLES

#### Impact
For upgradeable contracts, there must be storage gap to "allow developers to freely add new state variables in the future without compromising the storage compatibility with existing deployments" (quote OpenZeppelin). Otherwise it may be very difficult to write new implementation code. Without storage gap, the variable in child contract might be overwritten by the upgraded base contract if new variables are added to the base contract. This could have unintended and very serious consequences to the child contracts, potentially causing loss of user fund or cause the contract to malfunction completely.

#### Findings:
```
contracts\contract\BaseUpgradeable.sol::9 => contract BaseUpgradeable is Initializable, BaseAbstract {
contracts\contract\tokens\upgradeable\ERC20Upgradeable.sol::10 => abstract contract ERC20Upgradeable is Initializable {
contracts\contract\tokens\upgradeable\ERC4626Upgradeable.sol::11 => abstract contract ERC4626Upgradeable is Initializable, ERC20Upgradeable {
```

### Mitigation
Recommend adding appropriate storage gap at the end of upgradeable contracts such as the below. Please reference OpenZeppelin upgradeable contract templates.
Check https://docs.openzeppelin.com/contracts/3.x/upgradeable for more details.

```
uint256[50] private __gap;
```

#### Tools used 
Manual

### [N01] COMMENTED CODE

#### Findings:
```
contracts\contract\MinipoolManager.sol::21-54 => /*
contracts\contract\MultisigManager.sol::7-14 => /*
contracts\contract\Oracle.sol::9-14 => /*
contracts\contract\Staking.sol::15-27 => /*
```
#### Tools used 
Manual

### [N02] INCORRECT FUNCTION ORDER   

#### Impact

Function order should be like this:
```
	constructor
	receive function (if exists)
	fallback function (if exists)
	external
	public
	internal
	private
```

#### Findings:
```
contracts\contract\MinipoolManager.sol 
```
#### Tools used 
Manual


### [N03] VARIABLE NAMES THAT CONSIST OF ALL CAPITAL LETTERS SHOULD BE RESERVED FOR CONSTANT/IMMUTABLE VARIABLES

#### Findings:
```
contracts\contract\tokens\upgradeable\ERC20Upgradeable.sol::43 => uint256 internal INITIAL_CHAIN_ID;
contracts\contract\tokens\upgradeable\ERC20Upgradeable.sol::45 => bytes32 internal INITIAL_DOMAIN_SEPARATOR;
```
#### Tools used 
Manual

### [N04] NATSPEC IS MISSING

#### Findings:
```
contracts\contract\tokens\TokenggAVAX.sol::72 => function initialize(Storage storageAddress, ERC20 asset) public initializer {
contracts\contract\tokens\upgradeable\ERC20Upgradeable.sol::118 => function permit(
```
#### Tools used 
Manual

### [N05] FUNCTION NAME SHOULD BE WRITTEN IN MIXED CASE

#### Findings:
```
contracts\contract\tokens\upgradeable\ERC20Upgradeable.sol::162 => function DOMAIN_SEPARATOR() public view virtual returns (bytes32) {
```
#### Tools used 
Manual

### [N06] THE NONREENTRANT MODIFIER SHOULD OCCUR BEFORE ALL OTHER MODIFIERS

#### Findings:
```
contracts\contract\Vault.sol::61 => function withdrawAVAX(uint256 amount) external onlyRegisteredNetworkContract nonReentrant {
contracts\contract\Vault.sol::141 => ) external onlyRegisteredNetworkContract nonReentrant {
```
#### Tools used 
Manual


