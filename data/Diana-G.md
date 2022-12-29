## G-01 x += y COSTS MORE GAS THAN x = x + y FOR STATE VARIABLES (SAME APPLIES FOR x -= y , x = x - y)

Using the addition operator instead of plus-equals saves **113 gas**

_There are 12 instances of this issue_

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol

```
File: contract/tokens/TokenggAVAX.sol

149: stakingTotalAssets -= baseAmt;
160: stakingTotalAssets += assets;
245: totalReleasedAssets -= amount;
252: totalReleasedAssets += amount;
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol

```
File: contract/tokens/upgradeable/ERC20Upgradeable.sol

79: balanceOf[msg.sender] -= amount;
84: balanceOf[to] += amount;
101: balanceOf[from] -= amount;
106: balanceOf[to] += amount;
184: totalSupply += amount;
189: balanceOf[to] += amount;
196: balanceOf[from] -= amount;
201: totalSupply -= amount;
```

-------

## G-02 INCREMENTS CAN BE UNCHECKED

In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline

Prior to Solidity 0.8.0, arithmetic operations would always wrap in case of under- or overflow leading to widespread use of libraries that introduce additional checks.

Since Solidity 0.8.0, all arithmetic operations revert on over- and underflow by default, thus making the use of these libraries unnecessary.

To obtain the previous behaviour, an unchecked block can be used. It saves **30-40 gas** per loop.

_There are 7 instances of this issue_

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol

```
File: contract/MinipoolManager.sol

619: for (uint256 i = offset; i < max; i++) {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol

```
File: contract/MultisigManager.sol

84: for (uint256 i = 0; i < total; i++) {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol

```
File: contract/Ocyticus.sol

61: for (uint256 i = 0; i < count; i++) {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol

```
File: contract/RewardsPool.sol

74: for (uint256 i = 0; i < inflationIntervalsElapsed; i++) {
215: for (uint256 i = 0; i < count; i++) {
230: for (uint256 i = 0; i < enabledMultisigs.length; i++) {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol

```
File: contract/Staking.sol

428: for (uint256 i = offset; i < max; i++)
```

-----

## G-03 INTERNAL FUNCTIONS ONLY CALLED ONCE CAN BE INLINED TO SAVE GAS

Not inlining costs **20 to 40 gas** because of two extra `JUMP` instructions and additional stack operations needed for function calls.

_There are 14 instances of this issue:_

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol

```
File: contract/BaseAbstract.sol

123: function getInt(bytes32 key) internal view returns (int256) {
143: function setBytes(bytes32 key, bytes memory value) internal
151: function setInt(bytes32 key, int256 value) internal {
171: function deleteBytes(bytes32 key) internal {
175: function deleteBytes32(bytes32 key) internal {
179: function deleteInt(bytes32 key) internal {
183: function deleteUint(bytes32 key) internal {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseUpgradeable.sol

```
File: contract/BaseUpgradeable.sol

10: function __BaseUpgradeable_init(Storage gogoStorageAddress) internal onlyInitializing {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol

```
File: contract/BaseUpgradeable.sol

82: function inflate() internal {
110: function increaseRewardsCycleCount() internal {
203: function distributeMultisigAllotment(
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol

```
File: contract/Staking.sol

87: function increaseGGPStake(address stakerAddr, uint256 amount) internal {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol

```
File: contract/tokens/upgradeable/ERC20Upgradeable.sol

53: function __ERC20Upgradeable_init(
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol

```
File: contract/tokens/upgradeable/ERC4626Upgradeable.sol

29: function __ERC4626Upgradeable_init(
```

-----

## G-04 DON'T COMPARE BOOLEAN EXPRESSIONS TO BOOLEAN LITERALS

Comparing to a constant (`true` or `false`) is a bit more expensive than directly checking the returned boolean value. I suggest using `if(!directValue)` instead of `if(directValue == false)`

`if (<x> == true)` => `if (<x>)`, `if (<x> == false)` => `if (!<x>)`

_There are 3 instances of this issue:_

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol

```
File: contract/BaseAbstract.sol

25: if (getBool(keccak256(abi.encodePacked("contract.exists", msg.sender))) == false) {
74: if (enabled == false) {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol

```
File: contract/Storage.sol

29: if (booleanStorage[keccak256(abi.encodePacked("contract.exists", msg.sender))] == false && msg.sender != guardian) {
```

------

## G-05 SPLITTING REQURE() STATEMENTS THAT USE && SAVES GAS

See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper by **3 gas**

_There is 1 instance of this issue_

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol

```
File: contract/tokens/upgradeable/ERC20Upgradeable.sol

154: require(recoveredAddress != address(0) && recoveredAddress == owner, "INVALID_SIGNER");
```

-----

## G-06 USAGE OF UINTS OR INTS SMALLER THAN 32 BYPTES INCURS OVERHEAD

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html) Each operation involving a `uint8` costs an extra [**22-28 gas**](https://gist.github.com/IllIllI000/9388d20c70f9a4632eb3ca7836f54977) (depending on whether the other operand is also a variable of type `uint8`) as compared to ones involving `uint256`, due to the compiler having to clear the higher bits of the memory word before operating on the `uint8`, as well as the associated stack operations of doing so. Use a larger size then downcast where needed

_There are 14 instances of this issue:_

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol

```
File: contract/BaseAbstract.sol

19: uint8 public version;
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol

```
File: contract/tokens/TokenggAVAX.sol

41: uint32 public lastSync;
44: uint32 public rewardsCycleLength;
47: uint32 public rewardsCycleEnd;
50: uint192 public lastRewardsAmt;
89: uint32 timestamp = block.timestamp.safeCastTo32();
95: uint192 lastRewardsAmt_ = lastRewardsAmt
102: uint32 nextRewardsCycleEnd = ((timestamp + rewardsCycleLength) / rewardsCycleLength) * rewardsCycleLength;
116: uint192 lastRewardsAmt_ = lastRewardsAmt;
117: uint32 rewardsCycleEnd_ = rewardsCycleEnd;
118: uint32 lastSync_ = lastSync;
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol

```
File: contract/tokens/upgradeable/ERC20Upgradeable.sol

27: uint8 public decimals;
56: uint8 _decimals
123: uint8 v,
```

-------

## G-07 DIVISION BY 2 SHOULD USE BIT SHIFTING

 `<x> / 2` is the same as `<x> >> 1`. The `DIV` opcode costS 5 gas, whereas `SHL` and `SHR` only cost 3 gas.

While the compiler uses the `SHR` opcode to accomplish both, the version that uses division incurs an overhead of [**20 gas**](https://gist.github.com/IllIllI000/ec0e4e6c4f52a6bca158f137a3afd4ff) due to `JUMP`s to and from a compiler utility function that introduces checks which can be avoided by using `unchecked {}` around the division by two.

_There is 1 instance of this issue_

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol

```
File: contract/MinipoolManager.sol

413: uint256 avaxHalfRewards = avaxTotalRewardAmt / 2;
```

-----

## G-08 USING BOTH NAMED RETURNS AND A RETURN STATEMENT ISN'T NECESSARY

Removing unused named returns variables can reduce gas usage (MSTOREs/MLOADs) and improve code clarity. To save gas and improve code quality: consider using only one of those. Each MLOAD and MSTORE costs `3 additional gas`

_There are 2 instances of this issue:_

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol

```
File: contract/MinipoolManager.sol

572: function getMinipoolByNodeID(address nodeID) public view returns (Minipool memory mp) {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol

```
File: contract/RewardsPool.sol

66: function getInflationAmt() public view returns (uint256 currentTotalSupply, uint256 newTotalSupply) {
```

----