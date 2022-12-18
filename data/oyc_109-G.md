## [G-01] Don't Initialize Variables with Default Value

Uninitialized variables are assigned with the types default value. Explicitly initializing a variable with it's default value costs unnecesary gas.

```
2022-12-gogopool/contracts/contract/MinipoolManager.sol::618 => uint256 total = 0;
2022-12-gogopool/contracts/contract/MultisigManager.sol::84 => for (uint256 i = 0; i < total; i++) {
2022-12-gogopool/contracts/contract/Ocyticus.sol::61 => for (uint256 i = 0; i < count; i++) {
2022-12-gogopool/contracts/contract/RewardsPool.sol::74 => for (uint256 i = 0; i < inflationIntervalsElapsed; i++) {
2022-12-gogopool/contracts/contract/RewardsPool.sol::141 => uint256 contractRewardsTotal = 0;
2022-12-gogopool/contracts/contract/RewardsPool.sol::215 => for (uint256 i = 0; i < count; i++) {
2022-12-gogopool/contracts/contract/RewardsPool.sol::230 => for (uint256 i = 0; i < enabledMultisigs.length; i++) {
2022-12-gogopool/contracts/contract/Staking.sol::427 => uint256 total = 0;
```

## [G-02] Cache Array Length Outside of Loop

Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.

```
2022-12-gogopool/contracts/contract/RewardsPool.sol::230 => for (uint256 i = 0; i < enabledMultisigs.length; i++) {
```

## [G-03] Use Shift Right/Left instead of Division/Multiplication if possible

A division/multiplication by any number x being a power of 2 can be calculated by shifting log2(x) to the right/left.

While the DIV opcode uses 5 gas, the SHR opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

```
2022-12-gogopool/contracts/contract/MinipoolManager.sol::413 => uint256 avaxHalfRewards = avaxTotalRewardAmt / 2;
```

## [G-04] Use calldata instead of memory

Use calldata instead of memory for function parameters saves gas if the function argument is only read.

```
2022-12-gogopool/contracts/contract/BaseAbstract.sol::90 => function getContractAddress(string memory contractName) internal view returns (address) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::61 => function getContractPaused(string memory contractName) public view returns (bool) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::102 => function getClaimingContractPct(string memory claimingContract) public view returns (uint256) {
2022-12-gogopool/contracts/contract/RewardsPool.sol::134 => function getClaimingContractDistribution(string memory claimingContract) public view returns (uint256) {
2022-12-gogopool/contracts/contract/Vault.sol::193 => function balanceOf(string memory networkContractName) external view returns (uint256) {
2022-12-gogopool/contracts/contract/Vault.sol::200 => function balanceOfToken(string memory networkContractName, ERC20 tokenAddress) external view returns (uint256) {
```

## [G-05] Using private rather than public for constants, saves gas

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

```
2022-12-gogopool/contracts/contract/ClaimNodeOp.sol::27 => ERC20 public immutable ggp;
2022-12-gogopool/contracts/contract/MinipoolManager.sol::78 => ERC20 public immutable ggp;
2022-12-gogopool/contracts/contract/MinipoolManager.sol::79 => TokenggAVAX public immutable ggAVAX;
2022-12-gogopool/contracts/contract/Staking.sol::58 => ERC20 public immutable ggp;
```

## [G-06] Functions guaranteed to revert when called by normal users can be marked payable

If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

```
2022-12-gogopool/contracts/contract/ClaimNodeOp.sol::40 => function setRewardsCycleTotal(uint256 amount) public onlySpecificRegisteredContract("RewardsPool", msg.sender) {
2022-12-gogopool/contracts/contract/ClaimNodeOp.sol::56 => function calculateAndDistributeRewards(address stakerAddr, uint256 totalEligibleGGPStaked) external onlyMultisig {
2022-12-gogopool/contracts/contract/MultisigManager.sol::35 => function registerMultisig(address addr) external onlyGuardian {
2022-12-gogopool/contracts/contract/MultisigManager.sol::55 => function enableMultisig(address addr) external onlyGuardian {
2022-12-gogopool/contracts/contract/Ocyticus.sol::27 => function addDefender(address defender) external onlyGuardian {
2022-12-gogopool/contracts/contract/Ocyticus.sol::32 => function removeDefender(address defender) external onlyGuardian {
2022-12-gogopool/contracts/contract/Ocyticus.sol::37 => function pauseEverything() external onlyDefender {
2022-12-gogopool/contracts/contract/Ocyticus.sol::47 => function resumeEverything() external onlyDefender {
2022-12-gogopool/contracts/contract/Ocyticus.sol::55 => function disableAllMultisigs() public onlyDefender {
2022-12-gogopool/contracts/contract/Oracle.sol::28 => function setOneInch(address addr) external onlyGuardian {
2022-12-gogopool/contracts/contract/Oracle.sol::57 => function setGGPPriceInAVAX(uint256 price, uint256 timestamp) external onlyMultisig {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::23 => function initialize() external onlyGuardian {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::67 => function pauseContract(string memory contractName) public onlySpecificRegisteredContract("Ocyticus", msg.sender) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::73 => function resumeContract(string memory contractName) public onlySpecificRegisteredContract("Ocyticus", msg.sender) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::96 => function setTotalGGPCirculatingSupply(uint256 amount) public onlySpecificRegisteredContract("RewardsPool", msg.sender) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::107 => function setClaimingContractPct(string memory claimingContract, uint256 decimal) public onlyGuardian valueNotGreaterThanOne(decimal) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::156 => function setExpectedAVAXRewardsRate(uint256 rate) public onlyMultisig valueNotGreaterThanOne(rate) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::190 => function registerContract(address addr, string memory name) public onlyGuardian {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::198 => function unregisterContract(address addr) public onlyGuardian {
2022-12-gogopool/contracts/contract/RewardsPool.sol::34 => function initialize() external onlyGuardian {
2022-12-gogopool/contracts/contract/Staking.sol::110 => function increaseAVAXStake(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
2022-12-gogopool/contracts/contract/Staking.sol::117 => function decreaseAVAXStake(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
2022-12-gogopool/contracts/contract/Staking.sol::133 => function increaseAVAXAssigned(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
2022-12-gogopool/contracts/contract/Staking.sol::140 => function decreaseAVAXAssigned(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
2022-12-gogopool/contracts/contract/Staking.sol::156 => function increaseAVAXAssignedHighWater(address stakerAddr, uint256 amount) public onlyRegisteredNetworkContract {
2022-12-gogopool/contracts/contract/Staking.sol::163 => function resetAVAXAssignedHighWater(address stakerAddr) public onlyRegisteredNetworkContract {
2022-12-gogopool/contracts/contract/Staking.sol::180 => function increaseMinipoolCount(address stakerAddr) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
2022-12-gogopool/contracts/contract/Staking.sol::187 => function decreaseMinipoolCount(address stakerAddr) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
2022-12-gogopool/contracts/contract/Staking.sol::204 => function setRewardsStartTime(address stakerAddr, uint256 time) public onlyRegisteredNetworkContract {
2022-12-gogopool/contracts/contract/Staking.sol::224 => function increaseGGPRewards(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {
2022-12-gogopool/contracts/contract/Staking.sol::231 => function decreaseGGPRewards(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {
2022-12-gogopool/contracts/contract/Staking.sol::248 => function setLastRewardsCycleCompleted(address stakerAddr, uint256 cycleNumber) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {
2022-12-gogopool/contracts/contract/Staking.sol::328 => function restakeGGP(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {
2022-12-gogopool/contracts/contract/Staking.sol::379 => function slashGGP(address stakerAddr, uint256 ggpAmt) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
2022-12-gogopool/contracts/contract/Storage.sol::104 => function setAddress(bytes32 key, address value) external onlyRegisteredNetworkContract {
2022-12-gogopool/contracts/contract/Storage.sol::108 => function setBool(bytes32 key, bool value) external onlyRegisteredNetworkContract {
2022-12-gogopool/contracts/contract/Storage.sol::112 => function setBytes(bytes32 key, bytes calldata value) external onlyRegisteredNetworkContract {
2022-12-gogopool/contracts/contract/Storage.sol::116 => function setBytes32(bytes32 key, bytes32 value) external onlyRegisteredNetworkContract {
2022-12-gogopool/contracts/contract/Storage.sol::120 => function setInt(bytes32 key, int256 value) external onlyRegisteredNetworkContract {
2022-12-gogopool/contracts/contract/Storage.sol::124 => function setString(bytes32 key, string calldata value) external onlyRegisteredNetworkContract {
2022-12-gogopool/contracts/contract/Storage.sol::128 => function setUint(bytes32 key, uint256 value) external onlyRegisteredNetworkContract {
2022-12-gogopool/contracts/contract/Storage.sol::136 => function deleteAddress(bytes32 key) external onlyRegisteredNetworkContract {
2022-12-gogopool/contracts/contract/Storage.sol::140 => function deleteBool(bytes32 key) external onlyRegisteredNetworkContract {
2022-12-gogopool/contracts/contract/Storage.sol::144 => function deleteBytes(bytes32 key) external onlyRegisteredNetworkContract {
2022-12-gogopool/contracts/contract/Storage.sol::148 => function deleteBytes32(bytes32 key) external onlyRegisteredNetworkContract {
2022-12-gogopool/contracts/contract/Storage.sol::152 => function deleteInt(bytes32 key) external onlyRegisteredNetworkContract {
2022-12-gogopool/contracts/contract/Storage.sol::156 => function deleteString(bytes32 key) external onlyRegisteredNetworkContract {
2022-12-gogopool/contracts/contract/Storage.sol::160 => function deleteUint(bytes32 key) external onlyRegisteredNetworkContract {
2022-12-gogopool/contracts/contract/Storage.sol::170 => function addUint(bytes32 key, uint256 amount) external onlyRegisteredNetworkContract {
2022-12-gogopool/contracts/contract/Storage.sol::176 => function subUint(bytes32 key, uint256 amount) external onlyRegisteredNetworkContract {
2022-12-gogopool/contracts/contract/Vault.sol::61 => function withdrawAVAX(uint256 amount) external onlyRegisteredNetworkContract nonReentrant {
2022-12-gogopool/contracts/contract/Vault.sol::84 => function transferAVAX(string memory toContractName, uint256 amount) external onlyRegisteredNetworkContract {
2022-12-gogopool/contracts/contract/Vault.sol::204 => function addAllowedToken(address tokenAddress) external onlyGuardian {
2022-12-gogopool/contracts/contract/Vault.sol::208 => function removeAllowedToken(address tokenAddress) external onlyGuardian {
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::153 => function withdrawForStaking(uint256 assets) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
```

## [G-07] Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

```
2022-12-gogopool/contracts/contract/BaseAbstract.sol::19 => uint8 public version;
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::41 => uint32 public lastSync;
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::44 => uint32 public rewardsCycleLength;
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::47 => uint32 public rewardsCycleEnd;
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::89 => uint32 timestamp = block.timestamp.safeCastTo32();
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::102 => uint32 nextRewardsCycleEnd = ((timestamp + rewardsCycleLength) / rewardsCycleLength) * rewardsCycleLength;
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::117 => uint32 rewardsCycleEnd_ = rewardsCycleEnd;
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::118 => uint32 lastSync_ = lastSync;
```

## [G-08] Using bools for storage incurs overhead

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.
Use uint256(1) and uint256(2) for true/false instead

```
2022-12-gogopool/contracts/contract/Ocyticus.sol::13 => mapping(address => bool) public defenders;
2022-12-gogopool/contracts/contract/Storage.sol::16 => mapping(bytes32 => bool) private booleanStorage;
2022-12-gogopool/contracts/contract/Vault.sol::39 => mapping(address => bool) private allowedTokens;
```

## [G-09] ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, for example when used in for- and while-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop

```
2022-12-gogopool/contracts/contract/MinipoolManager.sol::619 => for (uint256 i = offset; i < max; i++) {
2022-12-gogopool/contracts/contract/MinipoolManager.sol::623 => total++;
2022-12-gogopool/contracts/contract/MultisigManager.sol::84 => for (uint256 i = 0; i < total; i++) {
2022-12-gogopool/contracts/contract/Ocyticus.sol::61 => for (uint256 i = 0; i < count; i++) {
2022-12-gogopool/contracts/contract/RewardsPool.sol::74 => for (uint256 i = 0; i < inflationIntervalsElapsed; i++) {
2022-12-gogopool/contracts/contract/RewardsPool.sol::215 => for (uint256 i = 0; i < count; i++) {
2022-12-gogopool/contracts/contract/RewardsPool.sol::219 => enabledCount++;
2022-12-gogopool/contracts/contract/RewardsPool.sol::230 => for (uint256 i = 0; i < enabledMultisigs.length; i++) {
2022-12-gogopool/contracts/contract/Staking.sol::428 => for (uint256 i = offset; i < max; i++) {
2022-12-gogopool/contracts/contract/Staking.sol::431 => total++;
```

## [G-10] <x> += <y> costs more gas than <x> = <x> + <y> for state variables

use <x> = <x> + <y> or <x> = <x> - <y> instead to save gas

```
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::149 => stakingTotalAssets -= baseAmt;
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::160 => stakingTotalAssets += assets;
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::245 => totalReleasedAssets -= amount;
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::252 => totalReleasedAssets += amount;
```

## [G-11] Prefix increments cheaper than Postfix increments

++i costs less gas than i++, especially when it's used in for-loops (--i/i-- too)
Saves 5 gas PER LOOP

```
2022-12-gogopool/contracts/contract/MinipoolManager.sol::619 => for (uint256 i = offset; i < max; i++) {
2022-12-gogopool/contracts/contract/MultisigManager.sol::84 => for (uint256 i = 0; i < total; i++) {
2022-12-gogopool/contracts/contract/Ocyticus.sol::61 => for (uint256 i = 0; i < count; i++) {
2022-12-gogopool/contracts/contract/RewardsPool.sol::74 => for (uint256 i = 0; i < inflationIntervalsElapsed; i++) {
2022-12-gogopool/contracts/contract/RewardsPool.sol::215 => for (uint256 i = 0; i < count; i++) {
2022-12-gogopool/contracts/contract/RewardsPool.sol::230 => for (uint256 i = 0; i < enabledMultisigs.length; i++) {
2022-12-gogopool/contracts/contract/Staking.sol::428 => for (uint256 i = offset; i < max; i++) {
```

## [G-12] Use bytes32 instead of string

Use bytes32 instead of string to save gas whenever possible. String is a dynamic data structure and therefore is more gas consuming then bytes32.

```
2022-12-gogopool/contracts/contract/BaseAbstract.sol::82 => string memory contractName = getContractName(address(this));
2022-12-gogopool/contracts/contract/BaseAbstract.sol::100 => string memory contractName = getString(keccak256(abi.encodePacked("contract.name", contractAddress)));
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::199 => string memory name = getContractName(addr);
2022-12-gogopool/contracts/contract/Storage.sol::125 => stringStorage[key] = value;
2022-12-gogopool/contracts/contract/Vault.sol::52 => string memory contractName = getContractName(msg.sender);
2022-12-gogopool/contracts/contract/Vault.sol::67 => string memory contractName = getContractName(msg.sender);
2022-12-gogopool/contracts/contract/Vault.sol::91 => string memory fromContractName = getContractName(msg.sender);
```

## [G-13] Usage of assert() instead of require()

Between solc 0.4.10 and 0.8.0, require() used REVERT (0xfd) opcode which refunded remaining gas on failure while assert() used INVALID (0xfe) opcode which consumed all the supplied gas.
require() should be used for checking error conditions on inputs and return values while assert() should be used for invariant checking (properly functioning code should never reach a failing assert statement, unless there is a bug in your contract you should fix).

```
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::83 => assert(msg.sender == address(asset));
```

## [G-14] Public functions not called by the contract should be declared external instead

Contracts are allowed to override their parents' functions and change the visibility from external to public and can save gas by doing so.

```
2022-12-gogopool/contracts/contract/ClaimNodeOp.sol::40 => function setRewardsCycleTotal(uint256 amount) public onlySpecificRegisteredContract("RewardsPool", msg.sender) {
2022-12-gogopool/contracts/contract/MinipoolManager.sol::541 => function getTotalAVAXLiquidStakerAmt() public view returns (uint256) {
2022-12-gogopool/contracts/contract/MinipoolManager.sol::572 => function getMinipoolByNodeID(address nodeID) public view returns (Minipool memory mp) {
2022-12-gogopool/contracts/contract/MinipoolManager.sol::634 => function getMinipoolCount() public view returns (uint256) {
2022-12-gogopool/contracts/contract/MultisigManager.sol::102 => function getCount() public view returns (uint256) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::61 => function getContractPaused(string memory contractName) public view returns (bool) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::67 => function pauseContract(string memory contractName) public onlySpecificRegisteredContract("Ocyticus", msg.sender) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::73 => function resumeContract(string memory contractName) public onlySpecificRegisteredContract("Ocyticus", msg.sender) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::81 => function getRewardsEligibilityMinSeconds() public view returns (uint256) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::86 => function getRewardsCycleSeconds() public view returns (uint256) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::91 => function getTotalGGPCirculatingSupply() public view returns (uint256) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::96 => function setTotalGGPCirculatingSupply(uint256 amount) public onlySpecificRegisteredContract("RewardsPool", msg.sender) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::102 => function getClaimingContractPct(string memory claimingContract) public view returns (uint256) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::107 => function setClaimingContractPct(string memory claimingContract, uint256 decimal) public onlyGuardian valueNotGreaterThanOne(decimal) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::123 => function getInflationIntervalSeconds() public view returns (uint256) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::130 => function getMinipoolMinAVAXStakingAmt() public view returns (uint256) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::135 => function getMinipoolNodeCommissionFeePct() public view returns (uint256) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::140 => function getMinipoolMaxAVAXAssignment() public view returns (uint256) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::145 => function getMinipoolMinAVAXAssignment() public view returns (uint256) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::150 => function getMinipoolCancelMoratoriumSeconds() public view returns (uint256) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::156 => function setExpectedAVAXRewardsRate(uint256 rate) public onlyMultisig valueNotGreaterThanOne(rate) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::161 => function getExpectedAVAXRewardsRate() public view returns (uint256) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::168 => function getMaxCollateralizationRatio() public view returns (uint256) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::173 => function getMinCollateralizationRatio() public view returns (uint256) {
2022-12-gogopool/contracts/contract/RewardsPool.sol::105 => function getRewardsCycleCount() public view returns (uint256) {
2022-12-gogopool/contracts/contract/Staking.sol::66 => function getTotalGGPStake() public view returns (uint256) {
2022-12-gogopool/contracts/contract/Staking.sol::103 => function getAVAXStake(address stakerAddr) public view returns (uint256) {
2022-12-gogopool/contracts/contract/Staking.sol::110 => function increaseAVAXStake(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
2022-12-gogopool/contracts/contract/Staking.sol::117 => function decreaseAVAXStake(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
2022-12-gogopool/contracts/contract/Staking.sol::133 => function increaseAVAXAssigned(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
2022-12-gogopool/contracts/contract/Staking.sol::140 => function decreaseAVAXAssigned(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
2022-12-gogopool/contracts/contract/Staking.sol::156 => function increaseAVAXAssignedHighWater(address stakerAddr, uint256 amount) public onlyRegisteredNetworkContract {
2022-12-gogopool/contracts/contract/Staking.sol::163 => function resetAVAXAssignedHighWater(address stakerAddr) public onlyRegisteredNetworkContract {
2022-12-gogopool/contracts/contract/Staking.sol::173 => function getMinipoolCount(address stakerAddr) public view returns (uint256) {
2022-12-gogopool/contracts/contract/Staking.sol::180 => function increaseMinipoolCount(address stakerAddr) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
2022-12-gogopool/contracts/contract/Staking.sol::187 => function decreaseMinipoolCount(address stakerAddr) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
2022-12-gogopool/contracts/contract/Staking.sol::196 => function getRewardsStartTime(address stakerAddr) public view returns (uint256) {
2022-12-gogopool/contracts/contract/Staking.sol::204 => function setRewardsStartTime(address stakerAddr, uint256 time) public onlyRegisteredNetworkContract {
2022-12-gogopool/contracts/contract/Staking.sol::217 => function getGGPRewards(address stakerAddr) public view returns (uint256) {
2022-12-gogopool/contracts/contract/Staking.sol::224 => function increaseGGPRewards(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {
2022-12-gogopool/contracts/contract/Staking.sol::231 => function decreaseGGPRewards(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {
2022-12-gogopool/contracts/contract/Staking.sol::240 => function getLastRewardsCycleCompleted(address stakerAddr) public view returns (uint256) {
2022-12-gogopool/contracts/contract/Staking.sol::248 => function setLastRewardsCycleCompleted(address stakerAddr, uint256 cycleNumber) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {
2022-12-gogopool/contracts/contract/Staking.sol::256 => function getMinimumGGPStake(address stakerAddr) public view returns (uint256) {
2022-12-gogopool/contracts/contract/Staking.sol::328 => function restakeGGP(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {
2022-12-gogopool/contracts/contract/Staking.sol::379 => function slashGGP(address stakerAddr, uint256 ggpAmt) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::72 => function initialize(Storage storageAddress, ERC20 asset) public initializer {
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::88 => function syncRewards() public {
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::142 => function depositFromStaking(uint256 baseAmt, uint256 rewardAmt) public payable onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::153 => function withdrawForStaking(uint256 assets) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::166 => function depositAVAX() public payable returns (uint256 shares) {
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::180 => function withdrawAVAX(uint256 assets) public returns (uint256 shares) {
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::191 => function redeemAVAX(uint256 shares) public returns (uint256 assets) {
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::206 => function maxWithdraw(address _owner) public view override returns (uint256) {
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::216 => function maxRedeem(address _owner) public view override returns (uint256) {
```

## [G-15] internal functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

```
2022-12-gogopool/contracts/contract/BaseAbstract.sol::90 => function getContractAddress(string memory contractName) internal view returns (address) {
2022-12-gogopool/contracts/contract/BaseAbstract.sol::99 => function getContractName(address contractAddress) internal view returns (string memory) {
2022-12-gogopool/contracts/contract/BaseAbstract.sol::115 => function getBytes(bytes32 key) internal view returns (bytes memory) {
2022-12-gogopool/contracts/contract/BaseAbstract.sol::119 => function getBytes32(bytes32 key) internal view returns (bytes32) {
2022-12-gogopool/contracts/contract/BaseAbstract.sol::123 => function getInt(bytes32 key) internal view returns (int256) {
2022-12-gogopool/contracts/contract/BaseAbstract.sol::135 => function setAddress(bytes32 key, address value) internal {
2022-12-gogopool/contracts/contract/BaseAbstract.sol::139 => function setBool(bytes32 key, bool value) internal {
2022-12-gogopool/contracts/contract/BaseAbstract.sol::143 => function setBytes(bytes32 key, bytes memory value) internal {
2022-12-gogopool/contracts/contract/BaseAbstract.sol::147 => function setBytes32(bytes32 key, bytes32 value) internal {
2022-12-gogopool/contracts/contract/BaseAbstract.sol::151 => function setInt(bytes32 key, int256 value) internal {
2022-12-gogopool/contracts/contract/BaseAbstract.sol::155 => function setUint(bytes32 key, uint256 value) internal {
2022-12-gogopool/contracts/contract/BaseAbstract.sol::159 => function setString(bytes32 key, string memory value) internal {
2022-12-gogopool/contracts/contract/BaseAbstract.sol::163 => function deleteAddress(bytes32 key) internal {
2022-12-gogopool/contracts/contract/BaseAbstract.sol::167 => function deleteBool(bytes32 key) internal {
2022-12-gogopool/contracts/contract/BaseAbstract.sol::171 => function deleteBytes(bytes32 key) internal {
2022-12-gogopool/contracts/contract/BaseAbstract.sol::175 => function deleteBytes32(bytes32 key) internal {
2022-12-gogopool/contracts/contract/BaseAbstract.sol::179 => function deleteInt(bytes32 key) internal {
2022-12-gogopool/contracts/contract/BaseAbstract.sol::183 => function deleteUint(bytes32 key) internal {
2022-12-gogopool/contracts/contract/BaseAbstract.sol::187 => function deleteString(bytes32 key) internal {
2022-12-gogopool/contracts/contract/BaseAbstract.sol::191 => function addUint(bytes32 key, uint256 amount) internal {
2022-12-gogopool/contracts/contract/BaseAbstract.sol::195 => function subUint(bytes32 key, uint256 amount) internal {
2022-12-gogopool/contracts/contract/BaseUpgradeable.sol::10 => function __BaseUpgradeable_init(Storage gogoStorageAddress) internal onlyInitializing {
2022-12-gogopool/contracts/contract/RewardsPool.sol::82 => function inflate() internal {
2022-12-gogopool/contracts/contract/RewardsPool.sol::110 => function increaseRewardsCycleCount() internal {
2022-12-gogopool/contracts/contract/Staking.sol::87 => function increaseGGPStake(address stakerAddr, uint256 amount) internal {
```

## [G-16] internal functions not called by the contract should be removed to save deployment gas

If the functions are required by an interface, the contract should inherit from that interface and use the override keyword

```
2022-12-gogopool/contracts/contract/BaseAbstract.sol::90 => function getContractAddress(string memory contractName) internal view returns (address) {
2022-12-gogopool/contracts/contract/BaseUpgradeable.sol::10 => function __BaseUpgradeable_init(Storage gogoStorageAddress) internal onlyInitializing {
```