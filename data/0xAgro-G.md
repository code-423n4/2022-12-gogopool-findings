# Gas Report
## Finding Summary
||Issue|Instances|Gas Savings (from provided tests)|
|-|-|-|-|
|[G-01]|`uint256` Iterator Checked Each Iteration|7|-269091
|[G-02]|`uint<size>` Used Rather Than `uint256`|1|-140427
|[G-03]|&& In If Statement(s)|3|-21899
|[G-04]|`unchecked` Not Used on Overflow / Underflow Proof Arithmetic|4|-13632
|[G-05]|`onlyOwner` Can Be Checked Earlier|1|-6533
|[G-06]|Explicit `true`/`false` Check(s) Used|2|-5039
|[G-07]|`x += y` Used|3|-2888
|[G-08]|Bit Shift(s) Not Used For MUL/DIV/MOD of Powers of 2|1|-1056
|[G-09]|`x -= y` Used|3|-687

### [G-01] `uint256` Iterator Checked Each Iteration

A `uint256` iterator will not overflow before the check variable overflows. Unchecking the iterator increment saves gas.
**Example**
```solidity
//From
for (uint256 i; i < len; ++i) {
	//Do Something
}
//To
for (uint256 i; i < len;) {
	//Do Something
	unchecked { ++i; }
}
```

#### Findings:

*/contracts/contract/MinipoolManager.sol*
Links: [619](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L619).
```solidity
619:	for (uint256 i = offset; i < max; i++) {
```

*/contracts/contract/MultisigManager.sol*
Links: [84](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L84).
```solidity
84:	for (uint256 i = 0; i < total; i++) {
```

*/contracts/contract/Ocyticus.sol*
Links: [61](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L61).
```solidity
61:	for (uint256 i = 0; i < count; i++) {
```

*/contracts/contract/RewardsPool.sol*
Links: [74](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L74), [215](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L215), [230](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L230).
```solidity
74:	for (uint256 i = 0; i < inflationIntervalsElapsed; i++) {
215:	for (uint256 i = 0; i < count; i++) {
230:	for (uint256 i = 0; i < enabledMultisigs.length; i++) {
```

### [G-02] `uint<size>` Used Rather Than `uint256`

The EVM deals with 32 bytes at a time. For `uint<size>`s less than 32 bytes the EVM performs more operations as a result of the needed size reduction from `uint256` to `uint<size>`. Consider using `uint256`.

#### Findings:

*/contracts/contract/BaseAbstract.sol*
Links: [19](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L19).
```solidity
19:	uint8 public version;
```

### [G-03] && In If Statement(s)

Seperating if statements saves gas.
**Example**
```solidity
//From
if (a != HIGH && b != LOW) {
	//Do Something
}
//To
if (a != HIGH) {
	if (b != LOW) {
		//Do Something
	}
}
```

#### Findings:

*/contracts/contract/RewardsPool.sol*
Links: [142](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L142).
```solidity
142:	if (claimContractPct > 0 && currentCycleRewardsTotal > 0) {
```

*/contracts/contract/Storage.sol*
Links: [29](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L29).
```solidity
29:	if (booleanStorage[keccak256(abi.encodePacked("contract.exists", msg.sender))] == false && msg.sender != guardian) {
```

### [G-04] `unchecked` Not Used on Overflow / Underflow Proof Arithmetic

Modern Solidity will automatically revert if a value overflows/underflows. More computation is used in order to force an overflow/underflow revert by default. When overflows/underflows are not possible (likely due to an earlier check), code can be placed inside an `unchecked` brace to save gas.

```solidity
unchecked {
	//Code
}
```

The findings below display the first line which allows the second line to go `unchecked`. ONLY the second line is suggested to be put in an `unchecked` brace.

#### Findings:

*/contracts/contract/Vault.sol*
Links: [71/75](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L71-L75), [95/99](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L95-L99), [151/155](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L151-L155), [183/187](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L183-L187).
```solidity
71:	if (avaxBalances[contractName] < amount) { revert InsufficientContractBalance(); }
75:	avaxBalances[contractName] = avaxBalances[contractName] - amount;
```
```solidity
95:	if (avaxBalances[fromContractName] < amount) { revert InsufficientContractBalance(); }
99:	avaxBalances[fromContractName] = avaxBalances[fromContractName] - amount;
```
```solidity
151:	if (tokenBalances[contractKey] < amount) { revert InsufficientContractBalance(); }
155:	tokenBalances[contractKey] = tokenBalances[contractKey] - amount;
```
```solidity
183:	if (tokenBalances[contractKeyFrom] < amount) { revert InsufficientContractBalance(); }
187:	tokenBalances[contractKeyFrom] = tokenBalances[contractKeyFrom] - amount;
```

### [G-05] `onlyOwner` Can Be Checked Earlier

`onlyOwner` function (acts similar to a modifier) can be checked at the beginning of the associated function rather than after other computation.

*/contracts/contract/MinipoolManager.sol*
Links: [276-277](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L276-L277).
```solidity
276:	int256 index = requireValidMinipool(nodeID);
277:	onlyOwner(index);
```

### [G-06] Explicit `true`/`false` Check(s) Used

Explicit `false` checks are more expensive than using `!`.

#### Findings:

*/contracts/contract/BaseAbstract.sol*
Links: [25](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L25), [74](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L74).
```solidity
25:	if (getBool(keccak256(abi.encodePacked("contract.exists", msg.sender))) == false) {
74:	if (enabled == false) {
```
**Suggested Change**
```solidity
25:	if (!getBool(keccak256(abi.encodePacked("contract.exists", msg.sender)))) {
74:	if (!enabled) {
```

*/contracts/contract/Storage.sol*
Links: [29](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L29).
```solidity
29:	if (booleanStorage[keccak256(abi.encodePacked("contract.exists", msg.sender))] == false && msg.sender != guardian) {
```
**Suggested Change**
```solidity
29:	if (!booleanStorage[keccak256(abi.encodePacked("contract.exists", msg.sender))] && msg.sender != guardian) {
```

### [G-07] `x += y` Used

`x = x + y` is cheaper than `x += y` in some cases like below.

#### Findings:

*/contracts/contract/tokens/TokenggAVAX.sol*
Links: [160](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L160), [252](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L252).
```solidity
160:	stakingTotalAssets += assets;
252:	totalReleasedAssets += amount;
```

### [G-08] Bit Shift(s) Not Used For MUL/DIV/MOD of Powers of 2

Bitwise operations usually save on gas. Expressions that deal with unsigned integers, a power of 2 and the operations MUL/DIV/MOD can often be re-written with bitwise shifts.

#### Findings:

*/contracts/contract/MinipoolManager.sol*
Links: [413](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L413).
```solidity
413:	uint256 avaxHalfRewards = avaxTotalRewardAmt / 2;
```
**Suggested Change**
```solidity
413:	uint256 avaxHalfRewards = avaxTotalRewardAmt >> 1;
```

### [G-09] `x -= y` Used

`x = x - y` is cheaper than `x -= y` in some cases like below.

#### Findings:

*/contracts/contract/tokens/TokenggAVAX.sol*
Links: [149](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L149), [245](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L245).
```solidity
149:	stakingTotalAssets -= baseAmt;
245:	totalReleasedAssets -= amount;
```