# GAS
## increments can be unchecked in loops
### Summary
In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.

### Github Permalinks
* [RewardsPool.sol#Line74](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L74)

* [RewardsPool.sol#Line215](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L215)

* [Ocyticus.sol#Line61](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L61)

* [Staking.sol#Line428](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L428)


### Mitigation
  The code should change to:
```
    for (uint256 i; i < numIterations;) {
      // ...
      unchecked { ++i; }
    }
    //The risk of overflow is inexistent for a uint256 here.
```


## Variables should not be initialized as zero.
### Summary 
It cost more gas to initialize a variable to zero than to let the default of zero be applied to it by solidity.

###  Github
* [RewardsPool.sol#Line141](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L141)

* [Staking.sol#Line427](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L427)

### Mitigation 
 Uinitialize the variable to save some gas on deployment.

``` 
  uint256 contractRewardsTotal;
```

## Using `private` rather than `public`  for constant variables, saves gas.

### Summary 
If needed, the values can be read from the verified contract source code. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table.

### Github permalinks
* [MultisigManager.sol#Line27](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L27)

### Mitigation
Use `private` instead.
``` 
uint256 private constant MULTISIG_LIMIT = 10;
```

## Use `calldata` instead of `memory` for external function parameters.
### Summary
When calling an external function , the entire `memory` area is copied to the contract being called.
This can be gas expensive , especially if the `memory` area is large. Using `calldata` instead of  `memory` can reduce these
cost, since only the data that is actually needed is copied to the contract being called. Calldata is read-only , cant be modified.
The variable `invoiceID` is only read and not modified hence, its best to use calldata. 

### Github link
* [ClaimProtocolDAO.sol#Line20](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimProtocolDAO.sol#L20)

### Mitigation
Use `calldata` instead of  `memory`.
``` 
function spend(
		string calldata invoiceID,
		address recipientAddress,
		uint256 amount
	) external onlyGuardian {
		///......
	}
```
## Don't compare boolean expressions to boolean literals
### Summary 
Negation(!) is a better optimized implementation of applying == false. It requires less gas and also the recommended approach to compute the negation. In other words: `x == false` and `!x` compute the same result, but `!x` costs less gas.

### Github link.

* [Storage.sol#Line#29](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L29)
```
modifier onlyRegisteredNetworkContract() {
	if (booleanStorage[keccak256(abi.encodePacked("contract.exists", msg.sender))] == false && msg.sender != guardian) {
```

* [BaseAbstract.sol#Line74](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L74)
```
   if (enabled == false) {
```
### Mitigation
Negate (!) the first condition.
``` 
modifier onlyRegisteredNetworkContract() {
	if (!(booleanStorage[keccak256(abi.encodePacked("contract.exists", msg.sender))]) && msg.sender != guardian) {
		revert InvalidOrOutdatedContract();
	}
	_;
	}
```
## Public functions not called by the contracts should be made external.
## Scope
* [ClaimNodeOp.sol#Line40](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L40)

```
 function setRewardsCycleTotal(uint256 amount) public onlySpecificRegisteredContract("RewardsPool", msg.sender) {
```
* [MinipoolManager.sol#Line541](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L541)
```
function getTotalAVAXLiquidStakerAmt() public view returns (uint256) {
```
* [MinipoolManager.sol#Line572](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L572)

```
function getMinipoolByNodeID(address nodeID) public view returns (Minipool memory mp) {
```
* [MinipoolManager.sol#Line607](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L607)
```
function getMinipools(
		MinipoolStatus status,
		uint256 offset,
		uint256 limit
	) public view returns (Minipool[] memory minipools) {
```
* [MinipoolManager.sol#Line634](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L634)
```
function getMinipoolCount() public view returns (uint256) {
```

* [MultisigManager.sol#Line102](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L102)
```
function getCount() public view returns (uint256) {
```
* [ProtocolDAO.sol#Line61](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L61)
```
function getContractPaused(string memory contractName) public view returns (bool) {
```
* [ProtocolDAO.sol#Line67](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L67)
```
function pauseContract(string memory contractName) public onlySpecificRegisteredContract("Ocyticus", msg.sender) {
```

* [ProtocolDAO.sol#Line73](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L731)
```
function resumeContract(string memory contractName) public onlySpecificRegisteredContract("Ocyticus", msg.sender) {
```

