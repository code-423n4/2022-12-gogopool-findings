## Summary<a name="Summary">

### Gas Optimizations
| |Issue|Contexts|Estimated Gas Saved|
|-|:-|:-|:-:|
| [GAS&#x2011;1](#GAS&#x2011;1) | Multiple Address Mappings Can Be Combined Into A Single Mapping Of An Address To A Struct, Where Appropriate | 2 | - |
| [GAS&#x2011;2](#GAS&#x2011;2) | Empty Blocks Should Be Removed Or Emit Something | 4 | - |
| [GAS&#x2011;3](#GAS&#x2011;3) | `require()` Should Be Used Instead Of `assert()` | 1 | - |
| [GAS&#x2011;4](#GAS&#x2011;4) | `++i`/`i++` Should Be `unchecked{++i}`/`unchecked{i++}` When It Is Not Possible For Them To Overflow, As Is The Case When Used In For- And While-loops | 7 | 245 |
| [GAS&#x2011;5](#GAS&#x2011;5) | Splitting `require()` Statements That Use `&&` Saves Gas | 1 | 9 |
| [GAS&#x2011;6](#GAS&#x2011;6) | `abi.encode()` is less efficient than `abi.encodepacked()` | 2 | 200 |
| [GAS&#x2011;7](#GAS&#x2011;7) | Multiplication/division By Two Should Use Bit Shifting | 1 | - |
| [GAS&#x2011;8](#GAS&#x2011;8) | Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead | 18 | - |
| [GAS&#x2011;9](#GAS&#x2011;9) | Use of Custom Errors Instead of String | 4 | - |
| [GAS&#x2011;10](#GAS&#x2011;10) | Optimize names to save gas | 18 | 396 |
| [GAS&#x2011;11](#GAS&#x2011;11) | Using fixed bytes is cheaper than using `string` | 1 | - |
| [GAS&#x2011;12](#GAS&#x2011;12) | Superfluous event fields | 1 | - |
| [GAS&#x2011;13](#GAS&#x2011;13) | `internal` functions only called once can be inlined to save gas | 4 | - |
| [GAS&#x2011;14](#GAS&#x2011;14) | Setting the `constructor` to `payable` | 14 | 182 |
| [GAS&#x2011;15](#GAS&#x2011;15) | Functions guaranteed to revert when called by normal users can be marked `payable` | 46 | 966 |
| [GAS&#x2011;16](#GAS&#x2011;16) | Using `unchecked` blocks to save gas | 7 | 952 |
| [GAS&#x2011;17](#GAS&#x2011;17) | Use solidity version 0.8.17 to gain some gas boost | 2 | 176 |
| [GAS&#x2011;18](#GAS&#x2011;18) | Struct packing | 2 | - |

Total: 136 contexts over 18 issues

## Gas Optimizations



### <a href="#Summary">[GAS&#x2011;1]</a><a name="GAS&#x2011;1"> Multiple Mappings Can Be Combined Into A Single Mapping Of A Type To A Struct, Where Appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot.


#### <ins>Proof Of Concept</ins>


```solidity
35: mapping(address => uint256) public balanceOf;
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L35

```solidity
47: mapping(address => uint256) public nonces;
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L47

i.e. Can use the following struct:

```solidity
struct erc20Struct {
    uint256 balanceOf;
    uint256 nonces;
}
```


```solidity
	mapping(bytes32 => address) private addressStorage;
	mapping(bytes32 => bool) private booleanStorage;
	mapping(bytes32 => bytes) private bytesStorage;
	mapping(bytes32 => bytes32) private bytes32Storage;
	mapping(bytes32 => int256) private intStorage;
	mapping(bytes32 => string) private stringStorage;
	mapping(bytes32 => uint256) private uintStorage;
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L15-L21

i.e. Can use the following struct:

```solidity
struct storageStruct {
	address addressStorage;
	bool booleanStorage;
	bytes bytesStorage;
	bytes32 bytes32Storage;
	int256 intStorage;
	string stringStorage;
	uint256 uint256;
}
```




### <a href="#Summary">[GAS&#x2011;2]</a><a name="GAS&#x2011;2"> Empty Blocks Should Be Removed Or Emit Something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})

#### <ins>Proof Of Concept</ins>

```solidity
106: function receiveWithdrawalAVAX() external payable {}
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L106

```solidity
255: function _authorizeUpgrade(address newImplementation) internal override onlyGuardian {}
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L255

```solidity
176: function beforeWithdraw(uint256 assets, uint256 shares) internal virtual {}
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L176

```solidity
178: function afterDeposit(uint256 assets, uint256 shares) internal virtual {}
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L178






### <a href="#Summary">[GAS&#x2011;3]</a><a name="GAS&#x2011;3"> `require()` Should Be Used Instead Of `assert()`

#### <ins>Proof Of Concept</ins>


```solidity
83: assert(msg.sender == address(asset));
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L83






### <a href="#Summary">[GAS&#x2011;4]</a><a name="GAS&#x2011;4"> `++i`/`i++` Should Be `unchecked{++i}`/`unchecked{i++}` When It Is Not Possible For Them To Overflow, As Is The Case When Used In For- And While-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas PER LOOP

#### <ins>Proof Of Concept</ins>


```solidity
619: for (uint256 i = offset; i < max; i++) {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L619

```solidity
84: for (uint256 i = 0; i < total; i++) {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MultisigManager.sol#L84

```solidity
61: for (uint256 i = 0; i < count; i++) {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Ocyticus.sol#L61

```solidity
74: for (uint256 i = 0; i < inflationIntervalsElapsed; i++) {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/RewardsPool.sol#L74

```solidity
215: for (uint256 i = 0; i < count; i++) {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/RewardsPool.sol#L215

```solidity
230: for (uint256 i = 0; i < enabledMultisigs.length; i++) {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/RewardsPool.sol#L230

```solidity
428: for (uint256 i = offset; i < max; i++) {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L428





### <a href="#Summary">[GAS&#x2011;5]</a><a name="GAS&#x2011;5"> Splitting `require()` statements that use `&&` saves gas

Instead of using operator `&&` on a single `require`. Using a two `require` can save more gas.

i.e.
for `require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf");` use:

```
	require(version == 1);
	require(_bytecodeHash[1] == bytes1(0));
```

#### <ins>Proof Of Concept</ins>

```solidity
154: require(recoveredAddress != address(0) && recoveredAddress == owner, "INVALID_SIGNER");
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L154





### <a href="#Summary">[GAS&#x2011;6]</a><a name="GAS&#x2011;6"> `abi.encode()` is less efficient than `abi.encodepacked()`

See for more information: https://github.com/ConnorBlockchain/Solidity-Encode-Gas-Comparison 

#### <ins>Proof Of Concept</ins>


```solidity
138: abi.encode(
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L138





### <a href="#Summary">[GAS&#x2011;7]</a><a name="GAS&#x2011;7"> Multiplication/division By Two Should Use Bit Shifting

<x> * 2 is equivalent to <x> << 1 and <x> / 2 is the same as <x> >> 1. The MUL and DIV opcodes cost 5 gas, whereas SHL and SHR only cost 3 gas

#### <ins>Proof Of Concept</ins>


```solidity
413: uint256 avaxHalfRewards = avaxTotalRewardAmt / 2;
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L413








### <a href="#Summary">[GAS&#x2011;8]</a><a name="GAS&#x2011;8"> Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract's gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html
Each operation involving a `uint8` costs an extra 22-28 gas (depending on whether the other operand is also a variable of type `uint8`) as compared to ones involving `uint256`, due to the compiler having to clear the higher bits of the memory word before operating on the `uint8`, as well as the associated stack operations of doing so. Use a larger size then downcast where needed

#### <ins>Proof Of Concept</ins>


```solidity
19: uint8 public version;
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/BaseAbstract.sol#L19

```solidity
129: uintStorage[key] = value;
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L129

```solidity
171: uintStorage[key] = uintStorage[key] + amount;
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L171

```solidity
177: uintStorage[key] = uintStorage[key] - amount;
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L177

```solidity
41: uint32 public lastSync;
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L41

```solidity
44: uint32 public rewardsCycleLength;
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L44

```solidity
47: uint32 public rewardsCycleEnd;
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L47

```solidity
50: uint192 public lastRewardsAmt;
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L50

```solidity
89: uint32 timestamp = block.timestamp.safeCastTo32();
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L89

```solidity
116: uint192 lastRewardsAmt_ = lastRewardsAmt;
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L116

```solidity
102: uint32 nextRewardsCycleEnd = ((timestamp + rewardsCycleLength) / rewardsCycleLength) * rewardsCycleLength;
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L102

```solidity
117: uint32 rewardsCycleEnd_ = rewardsCycleEnd;
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L117

```solidity
118: uint32 lastSync_ = lastSync;
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L118

```solidity
27: uint8 public decimals;
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L27






### <a href="#Summary">[GAS&#x2011;9]</a><a name="GAS&#x2011;9"> Use of Custom Errors Instead of String

To save some gas the use of custom errors leads to cheaper deploy time cost and run time cost. The run time cost is only relevant when the revert condition is met.

#### <ins>Proof Of Concept</ins>


```solidity
154: require(recoveredAddress != address(0) && recoveredAddress == owner, "INVALID_SIGNER");
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L154

```solidity
103: require((assets = previewRedeem(shares)) != 0, "ZERO_ASSETS");
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L103



#### <ins>Recommended Mitigation Steps</ins>

Use Custom Errors instead of strings.



### <a href="#Summary">[GAS&#x2011;10]</a><a name="GAS&#x2011;10"> Optimize names to save gas

Contracts most called functions could simply save gas by function ordering via Method ID. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because 22 gas are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions. 

See more <a href="https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92">here</a>

#### <ins>Proof Of Concept</ins>

```solidity
File: ./Projects/gogopool/code-423n4/2022-12-gogopool/contracts/contract/Base.sol
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Base.sol

```solidity
File: ./Projects/gogopool/code-423n4/2022-12-gogopool/contracts/contract/BaseAbstract.sol
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/BaseAbstract.sol

```solidity
File: ./Projects/gogopool/code-423n4/2022-12-gogopool/contracts/contract/BaseUpgradeable.sol
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/BaseUpgradeable.sol

```solidity
File: ./Projects/gogopool/code-423n4/2022-12-gogopool/contracts/contract/ClaimNodeOp.sol
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ClaimNodeOp.sol

```solidity
File: ./Projects/gogopool/code-423n4/2022-12-gogopool/contracts/contract/ClaimProtocolDAO.sol
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ClaimProtocolDAO.sol

```solidity
File: ./Projects/gogopool/code-423n4/2022-12-gogopool/contracts/contract/MinipoolManager.sol
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol

```solidity
File: ./Projects/gogopool/code-423n4/2022-12-gogopool/contracts/contract/MultisigManager.sol
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MultisigManager.sol

```solidity
File: ./Projects/gogopool/code-423n4/2022-12-gogopool/contracts/contract/Ocyticus.sol
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Ocyticus.sol

```solidity
File: ./Projects/gogopool/code-423n4/2022-12-gogopool/contracts/contract/Oracle.sol
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Oracle.sol

```solidity
File: ./Projects/gogopool/code-423n4/2022-12-gogopool/contracts/contract/ProtocolDAO.sol
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ProtocolDAO.sol

```solidity
File: ./Projects/gogopool/code-423n4/2022-12-gogopool/contracts/contract/RewardsPool.sol
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/RewardsPool.sol

```solidity
File: ./Projects/gogopool/code-423n4/2022-12-gogopool/contracts/contract/Staking.sol
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol

```solidity
File: ./Projects/gogopool/code-423n4/2022-12-gogopool/contracts/contract/Storage.sol
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol

```solidity
File: ./Projects/gogopool/code-423n4/2022-12-gogopool/contracts/contract/Vault.sol
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Vault.sol

```solidity
File: ./Projects/gogopool/code-423n4/2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol

```solidity
File: ./Projects/gogopool/code-423n4/2022-12-gogopool/contracts/contract/tokens/TokenGGP.sol
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenGGP.sol

```solidity
File: ./Projects/gogopool/code-423n4/2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol

```solidity
File: ./Projects/gogopool/code-423n4/2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol



#### <ins>Recommended Mitigation Steps</ins>
Find a lower method ID name for the most called functions for example Call() vs. Call1() is cheaper by 22 gas
For example, the function IDs in the Gauge.sol contract will be the most used; A lower method ID may be given.





### <a href="#Summary">[GAS&#x2011;11]</a><a name="GAS&#x2011;11"> Using fixed bytes is cheaper than using `string`

As a rule of thumb, use `bytes` for arbitrary-length raw byte data and string for arbitrary-length `string` (UTF-8) data. If you can limit the length to a certain number of bytes, always use one of `bytes1` to `bytes32` because they are much cheaper.

#### <ins>Proof Of Concept</ins>


```solidity
100: string memory contractName = getString(keccak256(abi.encodePacked("contract.name", contractAddress)));
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/BaseAbstract.sol#L100






### <a href="#Summary">[GAS&#x2011;12]</a><a name="GAS&#x2011;12"> Superfluous event fields

`block.number` and `block.timestamp` are added to the event information by default, so adding them manually will waste additional gas.

#### <ins>Proof Of Concept</ins>


```solidity
21: event GGPPriceUpdated(uint256 indexed price, uint256 timestamp);
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Oracle.sol#L21





### <a href="#Summary">[GAS&#x2011;13]</a><a name="GAS&#x2011;13"> `internal` functions only called once can be inlined to save gas

#### <ins>Proof Of Concept</ins>

```solidity
82: function inflate

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/RewardsPool.sol#L82

```solidity
110: function increaseRewardsCycleCount

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/RewardsPool.sol#L110

```solidity
203: function distributeMultisigAllotment

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/RewardsPool.sol#L203

```solidity
87: function increaseGGPStake

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L87





### <a href="#Summary">[GAS&#x2011;14]</a><a name="GAS&#x2011;14"> Setting the `constructor` to `payable`

Saves ~13 gas per instance

#### <ins>Proof Of Concept</ins>

```solidity
9: constructor(Storage _gogoStorageAddress)
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Base.sol#L9

```solidity
29: constructor(Storage storageAddress, ERC20 ggp_) Base(storageAddress)
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ClaimNodeOp.sol#L29

```solidity
15: constructor(Storage storageAddress) Base(storageAddress)
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ClaimProtocolDAO.sol#L15

```solidity
177: constructor(
		Storage storageAddress,
		ERC20 ggp_,
		TokenggAVAX ggAVAX_
	) Base(storageAddress)
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L177

```solidity
29: constructor(Storage storageAddress) Base(storageAddress)
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MultisigManager.sol#L29

```solidity
22: constructor(Storage storageAddress) Base(storageAddress)
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Ocyticus.sol#L22

```solidity
23: constructor(Storage storageAddress) Base(storageAddress)
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Oracle.sol#L23

```solidity
19: constructor(Storage storageAddress) Base(storageAddress)
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ProtocolDAO.sol#L19

```solidity
30: constructor(Storage storageAddress) Base(storageAddress)
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/RewardsPool.sol#L30

```solidity
60: constructor(Storage storageAddress, ERC20 ggp_) Base(storageAddress)
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L60

```solidity
35: constructor()
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L35

```solidity
41: constructor(Storage storageAddress) Base(storageAddress)
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Vault.sol#L41

```solidity
66: constructor()
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L66

```solidity
12: constructor() ERC20("GoGoPool Protocol", "GGP", 18)
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenGGP.sol#L12





### <a href="#Summary">[GAS&#x2011;15]</a><a name="GAS&#x2011;15"> Functions guaranteed to revert when called by normal users can be marked `payable`

If a function modifier or require such as onlyOwner/onlyX is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2), DUP1(3), ISZERO(3), PUSH2(3), JUMPI(10), PUSH1(3), DUP1(3), REVERT(0), JUMPDEST(1), POP(2) which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

#### <ins>Proof Of Concept</ins>

```solidity
40: function setRewardsCycleTotal(uint256 amount) public onlySpecificRegisteredContract("RewardsPool", msg.sender) {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ClaimNodeOp.sol#L40

```solidity
56: function calculateAndDistributeRewards(address stakerAddr, uint256 totalEligibleGGPStaked) external onlyMultisig {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ClaimNodeOp.sol#L56

```solidity
20: function spend(
		string memory invoiceID,
		address recipientAddress,
		uint256 amount
	) external onlyGuardian {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ClaimProtocolDAO.sol#L20

```solidity
35: function registerMultisig(address addr) external onlyGuardian {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MultisigManager.sol#L35

```solidity
55: function enableMultisig(address addr) external onlyGuardian {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MultisigManager.sol#L55

```solidity
27: function addDefender(address defender) external onlyGuardian {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Ocyticus.sol#L27

```solidity
32: function removeDefender(address defender) external onlyGuardian {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Ocyticus.sol#L32

```solidity
37: function pauseEverything() external onlyDefender {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Ocyticus.sol#L37

```solidity
47: function resumeEverything() external onlyDefender {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Ocyticus.sol#L47

```solidity
55: function disableAllMultisigs() public onlyDefender {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Ocyticus.sol#L55

```solidity
28: function setOneInch(address addr) external onlyGuardian {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Oracle.sol#L28

```solidity
57: function setGGPPriceInAVAX(uint256 price, uint256 timestamp) external onlyMultisig {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Oracle.sol#L57

```solidity
23: function initialize() external onlyGuardian {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ProtocolDAO.sol#L23

```solidity
67: function pauseContract(string memory contractName) public onlySpecificRegisteredContract("Ocyticus", msg.sender) {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ProtocolDAO.sol#L67

```solidity
73: function resumeContract(string memory contractName) public onlySpecificRegisteredContract("Ocyticus", msg.sender) {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ProtocolDAO.sol#L73

```solidity
96: function setTotalGGPCirculatingSupply(uint256 amount) public onlySpecificRegisteredContract("RewardsPool", msg.sender) {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ProtocolDAO.sol#L96

```solidity
107: function setClaimingContractPct(string memory claimingContract, uint256 decimal) public onlyGuardian valueNotGreaterThanOne(decimal) {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ProtocolDAO.sol#L107

```solidity
156: function setExpectedAVAXRewardsRate(uint256 rate) public onlyMultisig valueNotGreaterThanOne(rate) {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ProtocolDAO.sol#L156

```solidity
190: function registerContract(address addr, string memory name) public onlyGuardian {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ProtocolDAO.sol#L190

```solidity
198: function unregisterContract(address addr) public onlyGuardian {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ProtocolDAO.sol#L198

```solidity
209: function upgradeExistingContract(
		address newAddr,
		string memory newName,
		address existingAddr
	) external onlyGuardian {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ProtocolDAO.sol#L209

```solidity
34: function initialize() external onlyGuardian {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/RewardsPool.sol#L34

```solidity
110: function increaseAVAXStake(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L110

```solidity
117: function decreaseAVAXStake(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L117

```solidity
133: function increaseAVAXAssigned(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
156: function increaseAVAXAssigned(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L133

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L156



```solidity
140: function decreaseAVAXAssigned(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L140

```solidity
156: function increaseAVAXAssignedHighWater(address stakerAddr, uint256 amount) public onlyRegisteredNetworkContract {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L156

```solidity
163: function resetAVAXAssignedHighWater(address stakerAddr) public onlyRegisteredNetworkContract {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L163

```solidity
180: function increaseMinipoolCount(address stakerAddr) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L180

```solidity
187: function decreaseMinipoolCount(address stakerAddr) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L187

```solidity
204: function setRewardsStartTime(address stakerAddr, uint256 time) public onlyRegisteredNetworkContract {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L204

```solidity
224: function increaseGGPRewards(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L224

```solidity
231: function decreaseGGPRewards(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L231

```solidity
248: function setLastRewardsCycleCompleted(address stakerAddr, uint256 cycleNumber) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L248

```solidity
328: function restakeGGP(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L328

```solidity
379: function slashGGP(address stakerAddr, uint256 ggpAmt) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L379

```solidity
104: function setAddress(bytes32 key, address value) external onlyRegisteredNetworkContract {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L104

```solidity
136: function deleteAddress(bytes32 key) external onlyRegisteredNetworkContract {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L136

```solidity
170: function addUint(bytes32 key, uint256 amount) external onlyRegisteredNetworkContract {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L170

```solidity
176: function subUint(bytes32 key, uint256 amount) external onlyRegisteredNetworkContract {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L176

```solidity
46: function depositAVAX() external payable onlyRegisteredNetworkContract {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Vault.sol#L46

```solidity
61: function withdrawAVAX(uint256 amount) external onlyRegisteredNetworkContract nonReentrant {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Vault.sol#L61

```solidity
84: function transferAVAX(string memory toContractName, uint256 amount) external onlyRegisteredNetworkContract {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Vault.sol#L84

```solidity
137: function withdrawToken(
		address withdrawalAddress,
		ERC20 tokenAddress,
		uint256 amount
	) external onlyRegisteredNetworkContract nonReentrant {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Vault.sol#L137

```solidity
166: function transferToken(
		string memory networkContractName,
		ERC20 tokenAddress,
		uint256 amount
	) external onlyRegisteredNetworkContract {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Vault.sol#L166



#### <ins>Recommended Mitigation Steps</ins>
Functions guaranteed to revert when called by normal users can be marked payable.



### <a href="#Summary">[GAS&#x2011;16]</a><a name="GAS&#x2011;16"> Using `unchecked` blocks to save gas

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an `unchecked` block

#### <ins>Proof Of Concept</ins>

```solidity
279: if (block.timestamp - staking.getRewardsStartTime(msg.sender) < dao.getMinipoolCancelMoratoriumSeconds()) {

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L279

```solidity
417: uint256 avaxLiquidStakerRewardAmt = avaxHalfRewards - avaxHalfRewards.mulWadDown(dao.getMinipoolNodeCommissionFeePct());
418: uint256 avaxNodeOpRewardAmt = avaxTotalRewardAmt - avaxLiquidStakerRewardAmt;

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L417-L418

```solidity
79: if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;
99: if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;
79: if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;
99: if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L79

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L99

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L79

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L99







### <a href="#Summary">[GAS&#x2011;17]</a><a name="GAS&#x2011;17"> Use solidity version 0.8.17 to gain some gas boost

Upgrade to the latest solidity version 0.8.17 to get additional gas savings.

#### <ins>Proof Of Concept</ins>


```solidity
pragma solidity >=0.8.0;
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L2

```solidity
pragma solidity >=0.8.0;
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L2


### <a href="#Summary">[GAS&#x2011;18]</a><a name="GAS&#x2011;18"> Struct packing

#### <ins>Proof Of Concept</ins>

```solidity
struct Minipool {
		int256 index;
		address nodeID;
		uint256 status;
		uint256 duration;
		uint256 delegationFee;
		address owner;
		address multisigAddr;
		uint256 avaxNodeOpAmt;
		uint256 avaxNodeOpInitialAmt;
		uint256 avaxLiquidStakerAmt;
		// Submitted by the Rialto Oracle
		bytes32 txID;
		uint256 initialStartTime;
		uint256 startTime;
		uint256 endTime;
		uint256 avaxTotalRewardAmt;
		bytes32 errorCode;
		// Calculated in recordStakingEnd
		uint256 ggpSlashAmt;
		uint256 avaxNodeOpRewardAmt;
		uint256 avaxLiquidStakerRewardAmt;
	}
```

Since `initialStartTime`, `startTime` and `endTime` are unlikely to ever reach max uint256 then it is possible to set them as `uint64`, saving 3 gas slots.

```solidity
struct Minipool {
		int256 index;
		address nodeID;
		uint64 initialStartTime;
		uint256 status;
		uint256 duration;
		uint256 delegationFee;
		address owner;
		uint64 startTime;
		address multisigAddr;
		uint64 endTime;
		uint256 avaxNodeOpAmt;
		uint256 avaxNodeOpInitialAmt;
		uint256 avaxLiquidStakerAmt;
		// Submitted by the Rialto Oracle
		bytes32 txID;
		uint256 avaxTotalRewardAmt;
		bytes32 errorCode;
		// Calculated in recordStakingEnd
		uint256 ggpSlashAmt;
		uint256 avaxNodeOpRewardAmt;
		uint256 avaxLiquidStakerRewardAmt;
	}
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L82-L104


Same applies to here:
```solidity
struct Staker {
		address stakerAddr;
		uint256 ggpStaked;
		uint256 avaxStaked;
		uint256 avaxAssigned;
		uint256 avaxAssignedHighWater;
		uint256 minipoolCount;
		uint256 rewardsStartTime;
		uint256 ggpRewards;
		uint256 lastRewardsCycleCompleted;
	}
```

Saving 1 gas slot by changing to:

```solidity
struct Staker {
		address stakerAddr;
		uint64 rewardsStartTime;
		uint256 ggpStaked;
		uint256 avaxStaked;
		uint256 avaxAssigned;
		uint256 avaxAssignedHighWater;
		uint256 minipoolCount;
		uint256 ggpRewards;
		uint256 lastRewardsCycleCompleted;
	}
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L44-L54