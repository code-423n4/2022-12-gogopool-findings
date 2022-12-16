# Gas Optimizations Report

|         | Issue                                                                                                                              | Instances |
| ------- | :--------------------------------------------------------------------------------------------------------------------------------- | :-------: |
| [G-001] | Don't compare boolean expressions to boolean literals                                                                              |     3     |
| [G-002] | Splitting require() statements that use `&&` saves gas                                                                             |     1     |
| [G-004] | `storage` pointer to a structure is cheaper than copying each value of the structure into `memory`, same for `array` and `mapping` |     1     |
| [G-004] | Functions guaranteed to revert when called by normal users can be marked payable                                                   |    59     |
| [G-005] | Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate                 |     1     |
| [G-006] | Replace modifier with function                                                                                                     |     7     |
| [G-007] | Use named returns for local variables where it is possible.                                                                        |     7     |

## [G-001] Don't compare boolean expressions to boolean literals

### Impact

`if (<x> == true) => if (<x>), if (<x> == false) => if (!<x>)`

### Findings

Total:3

[contracts/contract/BaseAbstract.sol#L74](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/BaseAbstract.sol#L74)

```solidity
74:    if (enabled == false) {
```

[contracts/contract/BaseAbstract.sol#L25](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/BaseAbstract.sol#L25)

```solidity
25:    if (getBool(keccak256(abi.encodePacked("contract.exists", msg.sender))) == false) {
```

[contracts/contract/Storage.sol#L29](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Storage.sol#L29)

```solidity
29:    if (booleanStorage[keccak256(abi.encodePacked("contract.exists", msg.sender))] == false && msg.sender != guardian) {
```

## [G-002] Splitting require() statements that use `&&` saves gas

### Impact

When using `&&` in a `require()` statement, the entire statement will be evaluated before the transaction reverts. If the first condition is false, the second condition will not be evaluated. This means that if the first condition is false, the second condition will not be evaluated, which is a waste of gas. Splitting the `require()` statement into two separate statements will save gas.

### Findings

Total:1

[contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L154](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L154)

```solidity
154:    require(recoveredAddress != address(0) && recoveredAddress == owner, "INVALID_SIGNER");
```

### Recommendation

Same as description

## [G-003] `storage` pointer to a structure is cheaper than copying each value of the structure into `memory`, same for `array` and `mapping`

### Impact

It may not be obvious, but every time you copy a storage `struct/array/mapping` to a `memory` variable, you are literally copying each member by reading it from `storage`, which is expensive. And when you use the `storage` keyword, you are just storing a pointer to the storage, which is much cheaper.

### Findings

Total:1

[contracts/contract/RewardsPool.sol#L212](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/RewardsPool.sol#L212)

```solidity
212:    address[] memory enabledMultisigs = new address[](count);
```

### Recommendation

Using `storage` instead of `memory`

## [G-004] Functions guaranteed to revert when called by normal users can be marked payable

### Impact

If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.
The extra opcodes avoided are `CALLVALUE(2)`,`DUP1(3)`,`ISZERO(3)`,`PUSH2(3)`,`JUMPI(10)`,`PUSH1(3)`,`DUP1(3)`,`REVERT(0)`,`JUMPDEST(1)`,`POP(2)`, which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

### Findings

Total:59

[contracts/contract/RewardsPool.sol#L34](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/RewardsPool.sol#L34)

```solidity
34:    function initialize() external onlyGuardian {
```

[contracts/contract/MultisigManager.sol#L55](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/MultisigManager.sol#L55)

```solidity
55:    function enableMultisig(address addr) external onlyGuardian {
```

[contracts/contract/MultisigManager.sol#L35](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/MultisigManager.sol#L35)

```solidity
35:    function registerMultisig(address addr) external onlyGuardian {
```

[contracts/contract/Oracle.sol#L57](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Oracle.sol#L57)

```solidity
57:    function setGGPPriceInAVAX(uint256 price, uint256 timestamp) external onlyMultisig {
```

[contracts/contract/Oracle.sol#L28](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Oracle.sol#L28)

```solidity
28:    function setOneInch(address addr) external onlyGuardian {
```

[contracts/contract/Vault.sol#L208](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Vault.sol#L208)

```solidity
208:    function removeAllowedToken(address tokenAddress) external onlyGuardian {
```

[contracts/contract/Vault.sol#L170](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Vault.sol#L170)

```solidity
170:    ) external onlyRegisteredNetworkContract {
```

[contracts/contract/Vault.sol#L84](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Vault.sol#L84)

```solidity
84:    function transferAVAX(string memory toContractName, uint256 amount) external onlyRegisteredNetworkContract {
```

[contracts/contract/Vault.sol#L141](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Vault.sol#L141)

```solidity
141:    ) external onlyRegisteredNetworkContract nonReentrant {
```

[contracts/contract/Vault.sol#L204](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Vault.sol#L204)

```solidity
204:    function addAllowedToken(address tokenAddress) external onlyGuardian {
```

[contracts/contract/Vault.sol#L61](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Vault.sol#L61)

```solidity
61:    function withdrawAVAX(uint256 amount) external onlyRegisteredNetworkContract nonReentrant {
```

[contracts/contract/Storage.sol#L152](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Storage.sol#L152)

```solidity
152:    function deleteInt(bytes32 key) external onlyRegisteredNetworkContract {
```

[contracts/contract/Storage.sol#L124](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Storage.sol#L124)

```solidity
124:    function setString(bytes32 key, string calldata value) external onlyRegisteredNetworkContract {
```

[contracts/contract/Storage.sol#L104](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Storage.sol#L104)

```solidity
104:    function setAddress(bytes32 key, address value) external onlyRegisteredNetworkContract {
```

[contracts/contract/Storage.sol#L108](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Storage.sol#L108)

```solidity
108:    function setBool(bytes32 key, bool value) external onlyRegisteredNetworkContract {
```

[contracts/contract/Storage.sol#L116](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Storage.sol#L116)

```solidity
116:    function setBytes32(bytes32 key, bytes32 value) external onlyRegisteredNetworkContract {
```

[contracts/contract/Storage.sol#L170](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Storage.sol#L170)

```solidity
170:    function addUint(bytes32 key, uint256 amount) external onlyRegisteredNetworkContract {
```

[contracts/contract/Storage.sol#L128](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Storage.sol#L128)

```solidity
128:    function setUint(bytes32 key, uint256 value) external onlyRegisteredNetworkContract {
```

[contracts/contract/Storage.sol#L144](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Storage.sol#L144)

```solidity
144:    function deleteBytes(bytes32 key) external onlyRegisteredNetworkContract {
```

[contracts/contract/Storage.sol#L160](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Storage.sol#L160)

```solidity
160:    function deleteUint(bytes32 key) external onlyRegisteredNetworkContract {
```

[contracts/contract/Storage.sol#L120](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Storage.sol#L120)

```solidity
120:    function setInt(bytes32 key, int256 value) external onlyRegisteredNetworkContract {
```

[contracts/contract/Storage.sol#L148](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Storage.sol#L148)

```solidity
148:    function deleteBytes32(bytes32 key) external onlyRegisteredNetworkContract {
```

[contracts/contract/Storage.sol#L136](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Storage.sol#L136)

```solidity
136:    function deleteAddress(bytes32 key) external onlyRegisteredNetworkContract {
```

[contracts/contract/Storage.sol#L156](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Storage.sol#L156)

```solidity
156:    function deleteString(bytes32 key) external onlyRegisteredNetworkContract {
```

[contracts/contract/Storage.sol#L176](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Storage.sol#L176)

```solidity
176:    function subUint(bytes32 key, uint256 amount) external onlyRegisteredNetworkContract {
```

[contracts/contract/Storage.sol#L112](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Storage.sol#L112)

```solidity
112:    function setBytes(bytes32 key, bytes calldata value) external onlyRegisteredNetworkContract {
```

[contracts/contract/Storage.sol#L140](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Storage.sol#L140)

```solidity
140:    function deleteBool(bytes32 key) external onlyRegisteredNetworkContract {
```

[contracts/contract/ProtocolDAO.sol#L198](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/ProtocolDAO.sol#L198)

```solidity
198:    function unregisterContract(address addr) public onlyGuardian {
```

[contracts/contract/ProtocolDAO.sol#L73](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/ProtocolDAO.sol#L73)

```solidity
73:    function resumeContract(string memory contractName) public onlySpecificRegisteredContract("Ocyticus", msg.sender) {
```

[contracts/contract/ProtocolDAO.sol#L23](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/ProtocolDAO.sol#L23)

```solidity
23:    function initialize() external onlyGuardian {
```

[contracts/contract/ProtocolDAO.sol#L156](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/ProtocolDAO.sol#L156)

```solidity
156:    function setExpectedAVAXRewardsRate(uint256 rate) public onlyMultisig valueNotGreaterThanOne(rate) {
```

[contracts/contract/ProtocolDAO.sol#L190](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/ProtocolDAO.sol#L190)

```solidity
190:    function registerContract(address addr, string memory name) public onlyGuardian {
```

[contracts/contract/ProtocolDAO.sol#L107](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/ProtocolDAO.sol#L107)

```solidity
107:    function setClaimingContractPct(string memory claimingContract, uint256 decimal) public onlyGuardian valueNotGreaterThanOne(decimal) {
```

[contracts/contract/ProtocolDAO.sol#L96](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/ProtocolDAO.sol#L96)

```solidity
96:    function setTotalGGPCirculatingSupply(uint256 amount) public onlySpecificRegisteredContract("RewardsPool", msg.sender) {
```

[contracts/contract/ProtocolDAO.sol#L213](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/ProtocolDAO.sol#L213)

```solidity
213:    ) external onlyGuardian {
```

[contracts/contract/ProtocolDAO.sol#L67](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/ProtocolDAO.sol#L67)

```solidity
67:    function pauseContract(string memory contractName) public onlySpecificRegisteredContract("Ocyticus", msg.sender) {
```

[contracts/contract/Ocyticus.sol#L27](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Ocyticus.sol#L27)

```solidity
27:    function addDefender(address defender) external onlyGuardian {
```

[contracts/contract/Ocyticus.sol#L32](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Ocyticus.sol#L32)

```solidity
32:    function removeDefender(address defender) external onlyGuardian {
```

[contracts/contract/Ocyticus.sol#L55](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Ocyticus.sol#L55)

```solidity
55:    function disableAllMultisigs() public onlyDefender {
```

[contracts/contract/Ocyticus.sol#L37](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Ocyticus.sol#L37)

```solidity
37:    function pauseEverything() external onlyDefender {
```

[contracts/contract/Ocyticus.sol#L47](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Ocyticus.sol#L47)

```solidity
47:    function resumeEverything() external onlyDefender {
```

[contracts/contract/ClaimProtocolDAO.sol#L24](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/ClaimProtocolDAO.sol#L24)

```solidity
24:    ) external onlyGuardian {
```

[contracts/contract/ClaimNodeOp.sol#L40](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/ClaimNodeOp.sol#L40)

```solidity
40:    function setRewardsCycleTotal(uint256 amount) public onlySpecificRegisteredContract("RewardsPool", msg.sender) {
```

[contracts/contract/ClaimNodeOp.sol#L56](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/ClaimNodeOp.sol#L56)

```solidity
56:    function calculateAndDistributeRewards(address stakerAddr, uint256 totalEligibleGGPStaked) external onlyMultisig {
```

[contracts/contract/Staking.sol#L140](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Staking.sol#L140)

```solidity
140:    function decreaseAVAXAssigned(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
```

[contracts/contract/Staking.sol#L231](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Staking.sol#L231)

```solidity
231:    function decreaseGGPRewards(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {
```

[contracts/contract/Staking.sol#L204](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Staking.sol#L204)

```solidity
204:    function setRewardsStartTime(address stakerAddr, uint256 time) public onlyRegisteredNetworkContract {
```

[contracts/contract/Staking.sol#L328](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Staking.sol#L328)

```solidity
328:    function restakeGGP(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {
```

[contracts/contract/Staking.sol#L163](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Staking.sol#L163)

```solidity
163:    function resetAVAXAssignedHighWater(address stakerAddr) public onlyRegisteredNetworkContract {
```

[contracts/contract/Staking.sol#L187](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Staking.sol#L187)

```solidity
187:    function decreaseMinipoolCount(address stakerAddr) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
```

[contracts/contract/Staking.sol#L110](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Staking.sol#L110)

```solidity
110:    function increaseAVAXStake(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
```

[contracts/contract/Staking.sol#L248](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Staking.sol#L248)

```solidity
248:    function setLastRewardsCycleCompleted(address stakerAddr, uint256 cycleNumber) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {
```

[contracts/contract/Staking.sol#L156](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Staking.sol#L156)

```solidity
156:    function increaseAVAXAssignedHighWater(address stakerAddr, uint256 amount) public onlyRegisteredNetworkContract {
```

[contracts/contract/Staking.sol#L133](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Staking.sol#L133)

```solidity
133:    function increaseAVAXAssigned(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
```

[contracts/contract/Staking.sol#L180](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Staking.sol#L180)

```solidity
180:    function increaseMinipoolCount(address stakerAddr) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
```

[contracts/contract/Staking.sol#L379](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Staking.sol#L379)

```solidity
379:    function slashGGP(address stakerAddr, uint256 ggpAmt) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
```

[contracts/contract/Staking.sol#L117](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Staking.sol#L117)

```solidity
117:    function decreaseAVAXStake(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
```

[contracts/contract/Staking.sol#L224](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Staking.sol#L224)

```solidity
224:    function increaseGGPRewards(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {
```

[contracts/contract/tokens/TokenggAVAX.sol#L153](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/tokens/TokenggAVAX.sol#L153)

```solidity
153:    function withdrawForStaking(uint256 assets) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
```

### Recommendation

Mark the function as payable.

## [G-005] Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate

### Impact

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to [not having to recalculate the key's keccak256 hash](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0) (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

### Findings

Total:1

[contracts/contract/Vault.sol#L38-39](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Vault.sol#L38-39)

```solidity
38:    mapping(bytes32 => uint256) private tokenBalances;
39:    	mapping(address => bool) private allowedTokens;
```

### Recommendation

Same as description

## [G-006] Replace modifier with function

### Impact

Modifiers make code more elegant, but cost more than normal functions.

### Findings

Total:7

[contracts/contract/BaseAbstract.sol#L40](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/BaseAbstract.sol#L40)

```solidity
40:    modifier guardianOrRegisteredContract() {
```

[contracts/contract/BaseAbstract.sol#L62](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/BaseAbstract.sol#L62)

```solidity
62:    modifier onlyGuardian() {
```

[contracts/contract/BaseAbstract.sol#L81](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/BaseAbstract.sol#L81)

```solidity
81:    modifier whenNotPaused() {
```

[contracts/contract/BaseAbstract.sol#L24](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/BaseAbstract.sol#L24)

```solidity
24:    modifier onlyRegisteredNetworkContract() {
```

[contracts/contract/BaseAbstract.sol#L70](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/BaseAbstract.sol#L70)

```solidity
70:    modifier onlyMultisig() {
```

[contracts/contract/Storage.sol#L28](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Storage.sol#L28)

```solidity
28:    modifier onlyRegisteredNetworkContract() {
```

[contracts/contract/Ocyticus.sol#L15](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Ocyticus.sol#L15)

```solidity
15:    modifier onlyDefender() {
```

## [G-007] Use named returns for local variables where it is possible.

### Impact

It is not necessary to have both a named return and a return statement.

### Findings

Total:7

[contracts/contract/BaseAbstract.sol#L91](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/BaseAbstract.sol#L91)

```solidity
91:    address contractAddress = getAddress(keccak256(abi.encodePacked("contract.address", contractName)));
```

[contracts/contract/RewardsPool.sol#L141](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/RewardsPool.sol#L141)

```solidity
141:    uint256 contractRewardsTotal = 0;
```

[contracts/contract/MinipoolManager.sol#L116](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/MinipoolManager.sol#L116)

```solidity
116:    address owner = getAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".owner")));
```

[contracts/contract/MinipoolManager.sol#L128](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/MinipoolManager.sol#L128)

```solidity
128:    int256 minipoolIndex = requireValidMinipool(nodeID);
```

[contracts/contract/MinipoolManager.sol#L141](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/MinipoolManager.sol#L141)

```solidity
141:    int256 minipoolIndex = getIndexOf(nodeID);
```

[contracts/contract/MinipoolManager.sol#L314](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/MinipoolManager.sol#L314)

```solidity
314:    int256 minipoolIndex = onlyValidMultisig(nodeID);
```

[contracts/contract/Staking.sol#L388](https://github.com/code-423n4/2022-12-gogopool/tree/main//contracts/contract/Staking.sol#L388)

```solidity
388:    int256 index = getIndexOf(stakerAddr);
```
