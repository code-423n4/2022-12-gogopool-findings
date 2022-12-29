### G-01 ++I or I++ SHOULD BE UNCHECKED{++I} or UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS

*Number of Instances Identified: 7*

The `unchecked` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves **30-40 gas [per loop](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)**.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol

```
619: for (uint256 i = offset; i < max; i++) {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol

```
84: for (uint256 i = 0; i < total; i++) {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol

```
61: for (uint256 i = 0; i < count; i++) {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol

```
74: for (uint256 i = 0; i < inflationIntervalsElapsed; i++) {
215: for (uint256 i = 0; i < count; i++) {
230: for (uint256 i = 0; i < enabledMultisigs.length; i++) {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol

```
428: for (uint256 i = offset; i < max; i++) {
```


### G-02 USING BOTH NAMED RETURNS AND A RETURN STATEMENT ISNT NECESSARY

*Number of Instances Identified: 2*

Removing unused named returns variables can reduce gas usage (MSTOREs/MLOADs) and improve code clarity. To save gas and improve code quality: consider using only one of those.
Each MLOAD and MSTORE costs `3 additional gas`

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol

```
572: function getMinipoolByNodeID(address nodeID) public view returns (Minipool memory mp) {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol

```
66: function getInflationAmt() public view returns (uint256 currentTotalSupply, uint256 newTotalSupply) {
```


### G-03 X += Y COSTS MORE GAS THAN X = X + Y FOR STATE VARIABLES

*Number of Instances Identified: 12*

Using the addition operator instead of plus-equals saves **[113 gas]([https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8)**

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol

```
149: stakingTotalAssets -= baseAmt;
160: stakingTotalAssets += assets;
245: totalReleasedAssets -= amount;
252: totalReleasedAssets += amount;
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol

```
79: balanceOf[msg.sender] -= amount;
84: balanceOf[to] += amount;
101: balanceOf[from] -= amount;
106: balanceOf[to] += amount;
184: totalSupply += amount;
189: balanceOf[to] += amount;
196: balanceOf[from] -= amount;
201: totalSupply -= amount;
```

### G-04 INTERNAL FUNCTIONS ONLY CALLED ONCE CAN BE INLINED TO SAVE GAS

*Number of Instances Identified: 11*

Not inlining costs **20 to 40 gas** because of two extra `JUMP` instructions and additional stack operations needed for function calls.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol

```
123: function getInt(bytes32 key) internal view returns (int256) {
143: function setBytes(bytes32 key, bytes memory value) internal {
151: function setInt(bytes32 key, int256 value) internal  {
171: function deleteBytes(bytes32 key) internal {
175: function deleteBytes32(bytes32 key) internal {
179: function deleteInt(bytes32 key) internal {
183: function deleteUint(bytes32 key) internal {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseUpgradeable.sol

```
10: function __BaseUpgradeable_init(Storage gogoStorageAddress) internal onlyInitializing {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol

```
87: function increaseGGPStake(address stakerAddr, uint256 amount) internal {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol

```
53: function __ERC20Upgradeable_init() internal
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol

```
29: function __ERC4626Upgradeable_init() internal
```


### G-05 SPLITTING REQUIRE() STATEMENTS THAT USE && SAVES GAS

*Number of Instances Identified: 1*

See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper by **3 gas**

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol

```
154: require(recoveredAddress != address(0) && recoveredAddress == owner, "INVALID_SIGNER");
```


### G-06 DONT COMPARE BOOLEAN EXPRESSIONS TO BOOLEAN LITERALS

*Number of Instances Identified: 3*

`if (<x> == true)` => `if (<x>)`, `if (<x> == false)` => `if (!<x>)`

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol

```
25: if (getBool(keccak256(abi.encodePacked("contract.exists", msg.sender))) == false) {
74: if (enabled == false) {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol

```
29: if (booleanStorage[keccak256(abi.encodePacked("contract.exists", msg.sender))] == false && msg.sender != guardian) {
```


### G-07 MULTIPLICATION or DIVISION BY TWO SHOULD USE BIT SHIFTING

*Number of Instances Identified: 1*

`<x> * 2` is equivalent to `<x> << 1` and `<x> / 2` is the same as `<x> >> 1`. The `MUL` and `DIV` opcodes cost 5 gas, whereas `SHL` and `SHR` only cost 3 gas

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol

```
413: uint256 avaxHalfRewards = avaxTotalRewardAmt / 2;
```

### G-08 USAGE OF UINTS or INTS SMALLER THAN 32 BYTES - 256 BITS INCURS OVERHEAD

*Number of Instances Identified: 4*

> When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html) Use a larger size then downcast where needed


https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol

```
19: uint8 public version;
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol

```
102: uint32 nextRewardsCycleEnd
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol

```
27: uint8 public decimals;
56: uint8 _decimals
```