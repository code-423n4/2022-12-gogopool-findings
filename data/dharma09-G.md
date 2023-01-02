Gas Optimizations

1. Use `immutable` variables can save gas 
2. Splitting `require()` statements that use && saves gas - (saves 8 gas per &&)
3. Comparing to a constant (`true` or `false`) is a bit more expensive
4. Should be `unchecked` due to ternary operator

**[G-01] Use `immutable` variables can save gas** 

*Note: suggested optimation, save a decent amount of gas without compromising readability.*

```solidity
/ERC20Upgradeable.sol
23 string public name;
24
25 string public symbol;
```

In `ERC20.sol`, `name` and `symbol` will never change, use immutable variable instead of storage variable can save gas.****

**[G-02] Splitting require() statements that use && saves gas - (saves 8 gas per &&)**

Instead of using the && operator in a single require statement to check multiple conditions,using multiple require statements with 1 condition per require statement will save 8 GAS per &&The gas difference would only be realized if the revert condition is realized(met).

```solidity
/ERC20Upgradeable.sol
154 require(recoveredAddress != address(0) && recoveredAddress == owner, "INVALID_SIGNER");
```

**Proof of code**

**following tests were carried out in remix with both optimization turned on and off**

```solidity
function multiple (uint a) public pure returns (uint){
	require ( a > 1 && a < 5, "Initialized");
	return  a + 2;
}
```

**Execution cost**21617 with optimization and using &&21976 without optimization and using &&

After splitting the require statement

```solidity
function multiple(uint a) public pure returns (uint){
	require (a > 1 ,"Initialized");
	require (a < 5 , "Initialized");
	return a + 2;
}
```

**Execution cost** 21609 with optimization and split require21968 without optimization and using split require

**[G-03] Comparing to a constant (`true` or `false`) is a bit more expensive**

Comparing to a constant (`true` or `false`) is a bit more expensive than directly checking the returned boolean value.I suggest using `if(directValue)` instead of `if(directValue == true)` and `if(!directValue)` instead of `if(directValue == false)` 

```solidity
/BaseAbstract.sol
25: if (getBool(keccak256(abi.encodePacked("contract.exists", msg.sender))) == false) {

/Storage.sol
29: if (booleanStorage[keccak256(abi.encodePacked("contract.exists", msg.sender))] == false && msg.sender != guardian) {
```

**[G-04] Should be `unchecked` due to ternary operator**
```solidity
/TokenggAVAX.sol
212: return assets > avail ? avail : assets;
222: return shares > avail ? avail : shares;
```