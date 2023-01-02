# Gas report

## Author: rotcivegaf

| G-N    |Issue|Instances|
|:------:|:----|:-------:|
| [G&#x2011;01] | Don't compare boolean expressions to boolean literals | 3 |
| [G&#x2011;02] | Add `unchecked {}` for subtractions where the operands cannot underflow/overflow because of a previous `require()` or `if`-statement or not expected to underflow/overflow | 17 |
| [G&#x2011;03] | Unnecessary cast, variable | 1 |

## [G-01] Don't compare boolean expressions to boolean literals

`require(<x> == true)` => `require(<x>)`
`require(<x> == false)` => `require(!<x>)`

```solidity
File: contracts/contract/BaseAbstract.sol

25:		if (getBool(keccak256(abi.encodePacked("contract.exists", msg.sender))) == false) {

74:		if (enabled == false) {
```

```solidity
File: contracts/contract/Storage.sol

29:		if (booleanStorage[keccak256(abi.encodePacked("contract.exists", msg.sender))] == false && msg.sender != guardian) {
```

## [G-02] Add `unchecked {}` for subtractions where the operands cannot underflow/overflow because of a previous `require()` or `if`-statement or not expected to underflow/overflow

```solidity
File: contracts/contract/Vault.sol

/// @audit: impossibly overflow
 56:		avaxBalances[contractName] = avaxBalances[contractName] + msg.value;

/// @audit: checked in L71: `if (avaxBalances[contractName] < amount) {`
 75:			avaxBalances[contractName] = avaxBalances[contractName] - amount;

/// @audit: checked in L95: `if (avaxBalances[fromContractName] < amount) {`
 99:		avaxBalances[fromContractName] = avaxBalances[fromContractName] - amount;

/// @audit: impossibly overflow
100:		avaxBalances[toContractName] = avaxBalances[toContractName] + amount;

/// @audit: impossibly overflow, L128: `tokenContract.safeTransferFrom(msg.sender, address(this), amount);`
130:		tokenBalances[contractKey] = tokenBalances[contractKey] + amount;

/// @audit: checked in L151: `if (tokenBalances[contractKey] < amount) {`
155:		tokenBalances[contractKey] = tokenBalances[contractKey] - amount;

/// @audit: checked in L183: `if (tokenBalances[contractKeyFrom] < amount) {`
187:		tokenBalances[contractKeyFrom] = tokenBalances[contractKeyFrom] - amount;

/// @audit: impossibly overflow
188:		tokenBalances[contractKeyTo] = tokenBalances[contractKeyTo] + amount;
```

```solidity
File: contracts/contract/BaseAbstract.sol

/// @audit: impossibly underflow
71:		int256 multisigIndex = int256(getUint(keccak256(abi.encodePacked("multisig.index", msg.sender)))) - 1;
```

```solidity
File: contracts/contract/ClaimNodeOp.sol

/// @audit: checked in L95: `if (claimAmt > ggpRewards) {`
102:		uint256 restakeAmt = ggpRewards - claimAmt;
```

```solidity
File: contracts/contract/MinipoolManager.sol

/// @audit: checked in L401: `if (msg.value != totalAvaxAmt + avaxTotalRewardAmt) {`
400:		uint256 totalAvaxAmt = avaxNodeOpAmt + avaxLiquidStakerAmt;

413:		uint256 avaxHalfRewards = avaxTotalRewardAmt / 2;

/// @audit: impossibly underflow
567:		return int256(getUint(keccak256(abi.encodePacked("minipool.index", nodeID)))) - 1;
```

```solidity
File: contracts/contract/MultisigManager.sol

/// @audit: impossibly overflow
48:		setUint(keccak256(abi.encodePacked("multisig.index", addr)), index + 1);

/// @audit: impossibly underflow
97:		return int256(getUint(keccak256(abi.encodePacked("multisig.index", addr)))) - 1;
```

```solidity
File: contracts/contract/Staking.sol

/// @audit: impossibly overflow
 48:			setUint(keccak256(abi.encodePacked("staker.index", stakerAddr)), uint256(stakerIndex + 1));

/// @audit: impossibly overflow
431:			total++;
```

## [G-03] Unnecessary cast, variable

```solidity
File: contracts/contract/Vault.sol

/// @audit: The `tokenAddress` parameter of `withdrawToken` function is already ERC20 type
156:		// Get the toke ERC20 instance
157:		ERC20 tokenContract = ERC20(tokenAddress);
```