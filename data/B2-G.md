##  Multiple `key` mappings can be combined into a single mapping of `key` to a struct, where appropriate.

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot

```
	mapping(bytes32 => address) private addressStorage;
	mapping(bytes32 => bool) private booleanStorage;
	mapping(bytes32 => bytes) private bytesStorage;
	mapping(bytes32 => bytes32) private bytes32Storage;
	mapping(bytes32 => int256) private intStorage;
	mapping(bytes32 => string) private stringStorage;
	mapping(bytes32 => uint256) private uintStorage;
```
>File: contract/Storage.sol [(line 15-21)](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L15-L21)


```
	mapping(address => uint256) public balanceOf;

	mapping(address => mapping(address => uint256)) public allowance;

	mapping(address => uint256) public nonces;

```
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L35-L37 with
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L47

## Splitting `require()` statements that use `&&` saves gas

Instead of using the `&&` operator in a single require statement to check multiple conditions, I suggest using multiple require statements with 1 condition per require statement (saving 3 gas per `&`):


```
			require(recoveredAddress != address(0) && recoveredAddress == owner, "INVALID_SIGNER");

```
>File: contract/tokens/upgradeable/ERC20Upgradeable.sol [(line 154)](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L154) 


## `internal` functions only called once can be `inlined` to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.


```
	function afterDeposit(

```
>File: contract/tokens/TokenggAVAX.sol [(line 248)](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L248)


```
	function increaseGGPStake(address stakerAddr, uint256 amount) internal {

```
>File: contract/Staking.sol [(line 87)](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L87)
```
	Other instances of the issue are:
```
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L203
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L110
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L99

## Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.


```
	uint8 public version;
```
>File: contract/BaseAbstract.sol [(line 19)](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L19)


```
	uint8 public decimals;
```
>File: contract/tokens/upgradeable/ERC20Upgradeable.sol [(line 27)](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L27)

```
	Other instances of the issue are:
```
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L41-L47


## UNUSED NAMED RETURNS

Using both named returns and a return statement isn’t necessary. Removing one of those can improve code clarity:

```
		return (currentTotalSupply, newTotalSupply);
```
>File: contract/RewardsPool.sol [(line 77)](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L77)



```
		return getMinipool(index);
```
>File: contract/MinipoolManager.sol [(line 574)](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L574)


## Use `unchecked` to save gas

```
		uint256 restakeAmt = ggpRewards - claimAmt;

```
>File: contract/ClaimNodeOp.sol [(line 102)](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L102) 

```
				total++;

```
>File: contract/MinipoolManager.sol [(line 623)](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L623) 

## Don't compare boolean expressions to boolean literals

```
		if (getBool(keccak256(abi.encodePacked("contract.exists", msg.sender))) == false) {

```
>File: contract/BaseAbstract.sol [(line 25)](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L25) 



```
		if (enabled == false) {

```
>File: contract/BaseAbstract.sol [(line 74)](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L74) 

## abi.encode() is less efficient than abi.encodePacked()

```
							abi.encode(
```
>File: contract/tokens/upgradeable/ERC20Upgradeable.sol [(line 138)](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L138)

```
				abi.encode(
```
>File: contract/tokens/upgradeable/ERC20Upgradeable.sol [(line 169)](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L169)


## Cache external call

* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L61
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L65
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L68