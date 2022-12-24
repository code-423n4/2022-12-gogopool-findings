## [G-1] USE SHIFT RIGHT/LEFT INSTEAD OF DIVISION/MULTIPLICATION IF POSSIBLE</p>

### Description
A division/multiplication by any number `x` being a power of 2 can be calculated by shifting `log2(x)` to the right/left.
While the `DIV` opcode uses 5 gas, the `SHR` opcode only uses 3 gas.
urthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

### ✅ Recommendation
You should change multiplication and division with powers of two to bit shift.

### 🔍 Findings:
| | |
|---|---|
|examples/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L413|[uint256 avaxHalfRewards = avaxTotalRewardAmt / 2;](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L413 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L560|[return (avaxAmt.mulWadDown(rate) * duration) / 365 days;](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L560 )|
---

## [G-2] ++i/i++ SHOULD BE `unchecked{++i}`/`unchecked{i++}` WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, FOR EXAMPLE WHEN USED IN `for` AND `while` LOOPS</p>

### Description
In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck [cannot be made inline](https://github.com/ethereum/solidity/issues/10695). 
			Example for loop:

```Solidity
for (uint i = 0; i < length; i++) {
// do something that doesn't change the value of i
}
```

In this example, the for loop post condition, i.e., `i++` involves checked arithmetic, which is not required. This is because the value of i is always strictly less than `length <= 2**256 - 1`. Therefore, the theoretical maximum value of i to enter the for-loop body `is 2**256 - 2`. This means that the `i++` in the for loop can never overflow. Regardless, the overflow checks are performed by the compiler.

Unfortunately, the Solidity optimizer is not smart enough to detect this and remove the checks. You should manually do this by:

```Solidity
for (uint i = 0; i < length; i = unchecked_inc(i)) {
	// do something that doesn't change the value of i
}

function unchecked_inc(uint i) returns (uint) {
	unchecked {
		return i + 1;
	}
}
```
Or just:

```Solidity
for (uint i = 0; i < length;) {
	// do something that doesn't change the value of i
	unchecked { i++; 	}
}
```
Note that it’s important that the call to `unchecked_inc` is inlined. This is only possible for solidity versions starting from `0.8.2`.

Gas savings: roughly speaking this can save 30-40 gas per loop iteration. For lengthy loops, this can be significant!
(This is only relevant if you are using the default solidity checked arithmetic.)


### ✅ Recommendation
Use the `unchecked` keyword

### 🔍 Findings:
| | |
|---|---|
|examples/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L619|[for (uint256 i = offset; i < max; i++) {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L619 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/MultisigManager.sol#L84|[for (uint256 i = 0; i < total; i++) {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MultisigManager.sol#L84 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/Ocyticus.sol#L61|[for (uint256 i = 0; i < count; i++) {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Ocyticus.sol#L61 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/RewardsPool.sol#L74|[for (uint256 i = 0; i < inflationIntervalsElapsed; i++) {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/RewardsPool.sol#L74 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/RewardsPool.sol#L215|[for (uint256 i = 0; i < count; i++) {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/RewardsPool.sol#L215 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/RewardsPool.sol#L230|[for (uint256 i = 0; i < enabledMultisigs.length; i++) {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/RewardsPool.sol#L230 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L428|[for (uint256 i = offset; i < max; i++) {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L428 )|
---

## [G-3] MULTIPLE ADDRESS MAPPINGS CAN BE COMBINED INTO A SINGLE MAPPING OF AN ADDRESS TO A STRUCT, WHERE APPROPRIATE</p>

### Description
Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

### ✅ Recommendation
Where appropriate, you can combine multiple address mappings into a single mapping of an address to a struct.

### 🔍 Findings:
| | |
|---|---|
|examples/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L37|[mapping(address => mapping(address => uint256)) public allowance;](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L37 )|
---

## [G-4] USING STORAGE INSTEAD OF MEMORY FOR STRUCTS/ARRAYS SAVES GAS</p>

### Description
When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read.
Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct

### ✅ Recommendation
Use storage for `struct/array`

### 🔍 Findings:
| | |
|---|---|
|examples/2022-12-gogopool/tree/main/contracts/contract/RewardsPool.sol#L212|[address[] memory enabledMultisigs = new address[](count);](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/RewardsPool.sol#L212 )|
---

## [G-5] EMPTY BLOCKS SHOULD BE REMOVED OR EMIT SOMETHING</p>

### Description
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.
If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified
			```solidity
			if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}}
			```

### ✅ Recommendation
Empty blocks should be removed or emit something (The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

### 🔍 Findings:
| | |
|---|---|
|examples/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L106|[function receiveWithdrawalAVAX() external payable {}](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L106 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L255|[function _authorizeUpgrade(address newImplementation) internal override onlyGuardian {}](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L255 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L176|[function beforeWithdraw(uint256 assets, uint256 shares) internal virtual {}](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L176 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L178|[function afterDeposit(uint256 assets, uint256 shares) internal virtual {}](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L178 )|
---

## [G-6] FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED PAYABLE</p>

### Description
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are `CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2)`, which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost. Please check out this [article](https://coinsbench.com/solidity-payable-vs-regular-functions-a-gas-usage-comparison-b4a387fe860d)

### ✅ Recommendation
You should add the keyword `payable` to those functions.

### 🔍 Findings:
| | |
|---|---|
|examples/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L115|[function onlyOwner(int256 minipoolIndex) private view returns (address) {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L115 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/MultisigManager.sol#L35|[function registerMultisig(address addr) external onlyGuardian {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MultisigManager.sol#L35 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/MultisigManager.sol#L55|[function enableMultisig(address addr) external onlyGuardian {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MultisigManager.sol#L55 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/Ocyticus.sol#L27|[function addDefender(address defender) external onlyGuardian {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Ocyticus.sol#L27 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/Ocyticus.sol#L32|[function removeDefender(address defender) external onlyGuardian {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Ocyticus.sol#L32 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/Ocyticus.sol#L37|[function pauseEverything() external onlyDefender {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Ocyticus.sol#L37 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/Ocyticus.sol#L47|[function resumeEverything() external onlyDefender {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Ocyticus.sol#L47 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/Ocyticus.sol#L55|[function disableAllMultisigs() public onlyDefender {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Ocyticus.sol#L55 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/Oracle.sol#L28|[function setOneInch(address addr) external onlyGuardian {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Oracle.sol#L28 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/Oracle.sol#L57|[function setGGPPriceInAVAX(uint256 price, uint256 timestamp) external onlyMultisig {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Oracle.sol#L57 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/ProtocolDAO.sol#L23|[function initialize() external onlyGuardian {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ProtocolDAO.sol#L23 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/ProtocolDAO.sol#L156|[function setExpectedAVAXRewardsRate(uint256 rate) public onlyMultisig valueNotGreaterThanOne(rate) {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ProtocolDAO.sol#L156 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/ProtocolDAO.sol#L190|[function registerContract(address addr, string memory name) public onlyGuardian {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ProtocolDAO.sol#L190 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/ProtocolDAO.sol#L198|[function unregisterContract(address addr) public onlyGuardian {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ProtocolDAO.sol#L198 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/RewardsPool.sol#L34|[function initialize() external onlyGuardian {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/RewardsPool.sol#L34 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L163|[function resetAVAXAssignedHighWater(address stakerAddr) public onlyRegisteredNetworkContract {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L163 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L204|[function setRewardsStartTime(address stakerAddr, uint256 time) public onlyRegisteredNetworkContract {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L204 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L104|[function setAddress(bytes32 key, address value) external onlyRegisteredNetworkContract {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L104 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L108|[function setBool(bytes32 key, bool value) external onlyRegisteredNetworkContract {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L108 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L112|[function setBytes(bytes32 key, bytes calldata value) external onlyRegisteredNetworkContract {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L112 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L116|[function setBytes32(bytes32 key, bytes32 value) external onlyRegisteredNetworkContract {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L116 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L120|[function setInt(bytes32 key, int256 value) external onlyRegisteredNetworkContract {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L120 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L124|[function setString(bytes32 key, string calldata value) external onlyRegisteredNetworkContract {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L124 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L128|[function setUint(bytes32 key, uint256 value) external onlyRegisteredNetworkContract {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L128 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L136|[function deleteAddress(bytes32 key) external onlyRegisteredNetworkContract {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L136 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L140|[function deleteBool(bytes32 key) external onlyRegisteredNetworkContract {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L140 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L144|[function deleteBytes(bytes32 key) external onlyRegisteredNetworkContract {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L144 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L148|[function deleteBytes32(bytes32 key) external onlyRegisteredNetworkContract {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L148 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L152|[function deleteInt(bytes32 key) external onlyRegisteredNetworkContract {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L152 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L156|[function deleteString(bytes32 key) external onlyRegisteredNetworkContract {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L156 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L160|[function deleteUint(bytes32 key) external onlyRegisteredNetworkContract {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L160 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L170|[function addUint(bytes32 key, uint256 amount) external onlyRegisteredNetworkContract {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L170 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L176|[function subUint(bytes32 key, uint256 amount) external onlyRegisteredNetworkContract {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L176 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/Vault.sol#L46|[function depositAVAX() external payable onlyRegisteredNetworkContract {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Vault.sol#L46 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/Vault.sol#L61|[function withdrawAVAX(uint256 amount) external onlyRegisteredNetworkContract nonReentrant {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Vault.sol#L61 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/Vault.sol#L84|[function transferAVAX(string memory toContractName, uint256 amount) external onlyRegisteredNetworkContract {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Vault.sol#L84 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/Vault.sol#L204|[function addAllowedToken(address tokenAddress) external onlyGuardian {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Vault.sol#L204 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/Vault.sol#L208|[function removeAllowedToken(address tokenAddress) external onlyGuardian {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Vault.sol#L208 )|
---

## [G-7] USE CUSTOM ERRORS RATHER THAN `REVERT()`/`REQUIRE()` STRINGS TO SAVE GAS</p>

### Description
Custom errors from Solidity `0.8.4` are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met)
			Source: [solidity blog](https://blog.soliditylang.org/2021/04/21/custom-errors/)
 			Starting from [Solidity `v0.8.4`](https://github.com/ethereum/solidity/releases/tag/v0.8.4), there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors. Until now, you could already use strings to give more information about failures (e.g.,` revert("Insufficient funds.");`), but they are rather expensive, especially when it comes to deploy cost, and it is difficult to use dynamic information in them.
			Custom errors are defined using the error statement, which can be used inside and outside of contracts (including interfaces and libraries).

### ✅ Recommendation
Consider using Custom Erros instead of strings 

### 🔍 Findings:
| | |
|---|---|
|examples/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L127|[require(deadline >= block.timestamp, "PERMIT_DEADLINE_EXPIRED");](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L127 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L154|[require(recoveredAddress != address(0) && recoveredAddress == owner, "INVALID_SIGNER");](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L154 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L44|[require((shares = previewDeposit(assets)) != 0, "ZERO_SHARES");](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L44 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L103|[require((assets = previewRedeem(shares)) != 0, "ZERO_ASSETS");](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L103 )|
---

## [G-8] USE TWO `require` STATEMENTS INSTEAD OF OPERATOR `&&` TO SAVE GAS</p>

### Description
Usage of double require will save you around 10 gas with the optimizer enabled.
See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper.
 Example:
```Solidity
contract Requires {
uint256 public gas;
			
				function check1(uint x) public {
					gas = gasleft();
					require(x == 0 && x < 1 ); // gas cost 22156
					gas -= gasleft();
				}
			
				function check2(uint x) public {
					gas = gasleft();
					require(x == 0); // gas cost 22148
					require(x < 1);
					gas -= gasleft();
	}
}
```


### ✅ Recommendation
Consider changing one `require()` statement to two `require()` to save gas

### 🔍 Findings:
| | |
|---|---|
|examples/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L154|[require(recoveredAddress != address(0) && recoveredAddress == owner, "INVALID_SIGNER");](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L154 )|
---

## [G-9] USAGE OF `uint`s/`int`s SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD</p>

### Description
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

### ✅ Recommendation
Use `uint256` instead.

### 🔍 Findings:
| | |
|---|---|
|examples/2022-12-gogopool/tree/main/contracts/contract/BaseAbstract.sol#L19|[uint8 public version;](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/BaseAbstract.sol#L19 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L41|[uint32 public lastSync;](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L41 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L44|[uint32 public rewardsCycleLength;](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L44 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L47|[uint32 public rewardsCycleEnd;](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L47 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L89|[uint32 timestamp = block.timestamp.safeCastTo32();](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L89 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L102|[uint32 nextRewardsCycleEnd = ((timestamp + rewardsCycleLength) / rewardsCycleLength) * rewardsCycleLength;](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L102 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L117|[uint32 rewardsCycleEnd_ = rewardsCycleEnd;](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L117 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L118|[uint32 lastSync_ = lastSync;](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L118 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L27|[uint8 public decimals;](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L27 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L56|[uint8 _decimals](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L56 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L123|[uint8 v,](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L123 )|
---

## [G-10] `ABI.ENCODE()` IS LESS EFFICIENT THAN `ABI.ENCODEPACKED()`</p>

### Description
`abi.encode` will apply ABI encoding rules. Therefore all elementary types are padded to 32 bytes and dynamic arrays include their length. Therefore it is possible to also decode this data again (with `abi.decode`) when the type are known.

`abi.encodePacked` will only use the only use the minimal required memory to encode the data. E.g. an address will only use 20 bytes and for dynamic arrays only the elements will be stored without length. For more info see the Solidity docs for packed mode.

For the input of the `keccak` method it is important that you can ensure that the resulting bytes of the encoding are unique. So if you always encode the same types and arrays always have the same length then there is no problem. But if you switch the parameters that you encode or encode multiple dynamic arrays you might have conflicts.
For example:
`abi.encodePacked(address(0x0000000000000000000000000000000000000001), uint(0))` encodes to the same as `abi.encodePacked(uint(0x0000000000000000000000000000000000000001000000000000000000000000), address(0))` 
and `abi.encodePacked(uint[](1,2), uint[](3))` encodes to the same as `abi.encodePacked(uint[](1), uint[](2,3))`
Therefore these examples will also generate the same hashes even so they are different inputs.
On the other hand you require less memory and therefore in most cases abi.encodePacked uses less gas than abi.encode.

### ✅ Recommendation
Use `abi.encodePacked()` where possible to save gas

### 🔍 Findings:
| | |
|---|---|
|examples/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L138|[abi.encode(](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L138 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L169|[abi.encode(](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L169 )|
---

## [G-11] INTERNAL FUNCTIONS ONLY CALLED ONCE CAN BE INLINED TO SAVE GAS</p>

### Description
Not inlining costs 20 to 40 gas because of two extra `JUMP` instructions and additional stack operations needed for function calls.
Check this out: [link](https://betterprogramming.pub/solidity-gas-optimizations-and-tricks-2bcee0f9f1f2)
Note: To sum up, when there is an internal function that is only called once, there is no point for that function to exist. 

### ✅ Recommendation
TODO - CHECK IF THIS IS REALLY THE CASE!!!!

### 🔍 Findings:
| | |
|---|---|
|examples/2022-12-gogopool/tree/main/contracts/contract/BaseAbstract.sol#L163|[function deleteAddress(bytes32 key) internal {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/BaseAbstract.sol#L163 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/BaseAbstract.sol#L167|[function deleteBool(bytes32 key) internal {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/BaseAbstract.sol#L167 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/BaseAbstract.sol#L171|[function deleteBytes(bytes32 key) internal {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/BaseAbstract.sol#L171 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/BaseAbstract.sol#L175|[function deleteBytes32(bytes32 key) internal {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/BaseAbstract.sol#L175 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/BaseAbstract.sol#L179|[function deleteInt(bytes32 key) internal {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/BaseAbstract.sol#L179 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/BaseAbstract.sol#L183|[function deleteUint(bytes32 key) internal {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/BaseAbstract.sol#L183 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/BaseAbstract.sol#L187|[function deleteString(bytes32 key) internal {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/BaseAbstract.sol#L187 )|
---
