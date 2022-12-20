

### [G01] State variables only set in the constructor should be declared `immutable`

#### Impact
Avoids a Gusset (20000 gas)
#### Findings:
```
2022-12-gogopool/contracts/contract/BaseAbstract.sol::19 => uint8 public version;
2022-12-gogopool/contracts/contract/Storage.sol::24 => address private guardian;
2022-12-gogopool/contracts/contract/Storage.sol::25 => address public newGuardian;
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::41 => uint32 public lastSync;
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::44 => uint32 public rewardsCycleLength;
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::47 => uint32 public rewardsCycleEnd;
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::50 => uint192 public lastRewardsAmt;
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::53 => uint256 public totalReleasedAssets;
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::56 => uint256 public stakingTotalAssets;
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol::23 => string public name;
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol::25 => string public symbol;
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol::27 => uint8 public decimals;
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol::33 => uint256 public totalSupply;
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol::27 => ERC20 public asset;
```




### [G02] State variables can be packed into fewer storage slots

#### Impact
If variables occupying the same slot are both written the same 
function or by the constructor avoids a separate Gsset (20000 gas). 
Reads of the variables are also cheaper
#### Findings:
```
2022-12-gogopool/contracts/contract/Storage.sol::25 => address public newGuardian;
```

### [G03] `<array>.length` should not be looked up in every loop of a `for` loop

#### Impact
calculate the array length. Storage array length checks incur an extra 
Gwarmaccess (100 gas) PER-LOOP.
#### Findings:
```
2022-12-gogopool/contracts/contract/RewardsPool.sol::230 => for (uint256 i = 0; i < enabledMultisigs.length; i++) {
```




### [G04] `++i/i++` should be `unchecked{++i}`/`unchecked{++i}` when it is not possible for them to overflow, as is the case when used in `for` and `while` loops



#### Findings:
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
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol::143 => nonces[owner]++,
```





### [G05] It costs more gas to initialize variables to zero than to let the default of zero be applied


#### Findings:
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




### [G06] `++i` costs less gas than `i++`, especially when it’s used in forloops (`--i`/`i--` too)


#### Findings:
```
2022-12-gogopool/contracts/contract/MinipoolManager.sol::619 => for (uint256 i = offset; i < max; i++) {
2022-12-gogopool/contracts/contract/MultisigManager.sol::84 => for (uint256 i = 0; i < total; i++) {
2022-12-gogopool/contracts/contract/Ocyticus.sol::61 => for (uint256 i = 0; i < count; i++) {
2022-12-gogopool/contracts/contract/RewardsPool.sol::74 => for (uint256 i = 0; i < inflationIntervalsElapsed; i++) {
2022-12-gogopool/contracts/contract/RewardsPool.sol::215 => for (uint256 i = 0; i < count; i++) {
2022-12-gogopool/contracts/contract/RewardsPool.sol::230 => for (uint256 i = 0; i < enabledMultisigs.length; i++) {
2022-12-gogopool/contracts/contract/Staking.sol::428 => for (uint256 i = offset; i < max; i++) {
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol::143 => nonces[owner]++,
```




### [G07] Splitting `require()` statements that use `&&` Cost gas


#### Findings:
```
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol::154 => require(recoveredAddress != address(0) && recoveredAddress == owner, "INVALID_SIGNER");
```




### [G08] Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead

#### Impact
> When using elements that are smaller than 32 bytes, your 
contract’s gas usage may be higher. This is because the EVM operates on 
32 bytes at a time. Therefore, if the element is smaller than that, the 
EVM must use more operations in order to reduce the size of the element 
from 32 bytes to the desired size.
> 

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html)
Use a larger size then downcast where needed
#### Findings:
```
2022-12-gogopool/contracts/contract/BaseAbstract.sol::19 => uint8 public version;
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::41 => uint32 public lastSync;
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::44 => uint32 public rewardsCycleLength;
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::47 => uint32 public rewardsCycleEnd;
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::50 => uint192 public lastRewardsAmt;
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::89 => uint32 timestamp = block.timestamp.safeCastTo32();
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::95 => uint192 lastRewardsAmt_ = lastRewardsAmt;
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::102 => uint32 nextRewardsCycleEnd = ((timestamp + rewardsCycleLength) / rewardsCycleLength) * rewardsCycleLength;
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::116 => uint192 lastRewardsAmt_ = lastRewardsAmt;
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::117 => uint32 rewardsCycleEnd_ = rewardsCycleEnd;
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::118 => uint32 lastSync_ = lastSync;
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol::27 => uint8 public decimals;
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol::56 => uint8 _decimals
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol::123 => uint8 v,
```




### [G09] Duplicated `require()`/`revert()` checks should be refactored to a modifier or function


#### Findings:
```
2022-12-gogopool/contracts/contract/BaseAbstract.sol::26 => revert InvalidOrOutdatedContract();
2022-12-gogopool/contracts/contract/BaseAbstract.sol::45 => revert MustBeGuardianOrValidContract();
2022-12-gogopool/contracts/contract/BaseAbstract.sol::102 => revert ContractNotFound();
2022-12-gogopool/contracts/contract/ClaimNodeOp.sol::96 => revert InvalidAmount();
2022-12-gogopool/contracts/contract/MinipoolManager.sol::231 => revert InsufficientGGPCollateralization();
2022-12-gogopool/contracts/contract/MinipoolManager.sol::493 => revert InvalidAmount();
2022-12-gogopool/contracts/contract/MultisigManager.sol::58 => revert MultisigNotFound();
2022-12-gogopool/contracts/contract/Oracle.sol::49 => revert InvalidGGPPrice();
2022-12-gogopool/contracts/contract/Vault.sol::49 => revert InvalidAmount();
2022-12-gogopool/contracts/contract/Vault.sol::72 => revert InsufficientContractBalance();
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::194 => revert ZeroAssets();
```


### [G10] Use custom errors rather than `revert()`/`require()` strings to save deployment gas


#### Findings:
```
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol::127 => require(deadline >= block.timestamp, "PERMIT_DEADLINE_EXPIRED");
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol::154 => require(recoveredAddress != address(0) && recoveredAddress == owner, "INVALID_SIGNER");
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol::44 => require((shares = previewDeposit(assets)) != 0, "ZERO_SHARES");
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol::103 => require((assets = previewRedeem(shares)) != 0, "ZERO_ASSETS");
```




### [G11] Functions guaranteed to revert when called by normal users can be marked `payable`

#### Impact
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.
#### Findings:
```
2022-12-gogopool/contracts/contract/BaseUpgradeable.sol::10 => function __BaseUpgradeable_init(Storage gogoStorageAddress) internal onlyInitializing {
2022-12-gogopool/contracts/contract/ClaimNodeOp.sol::40 => function setRewardsCycleTotal(uint256 amount) public onlySpecificRegisteredContract("RewardsPool", msg.sender) {
2022-12-gogopool/contracts/contract/ClaimNodeOp.sol::56 => function calculateAndDistributeRewards(address stakerAddr, uint256 totalEligibleGGPStaked) external onlyMultisig {
2022-12-gogopool/contracts/contract/MinipoolManager.sol::115 => function onlyOwner(int256 minipoolIndex) private view returns (address) {
2022-12-gogopool/contracts/contract/MinipoolManager.sol::127 => function onlyValidMultisig(address nodeID) private view returns (int256) {
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
2022-12-gogopool/contracts/contract/Vault.sol::46 => function depositAVAX() external payable onlyRegisteredNetworkContract {
2022-12-gogopool/contracts/contract/Vault.sol::61 => function withdrawAVAX(uint256 amount) external onlyRegisteredNetworkContract nonReentrant {
2022-12-gogopool/contracts/contract/Vault.sol::84 => function transferAVAX(string memory toContractName, uint256 amount) external onlyRegisteredNetworkContract {
2022-12-gogopool/contracts/contract/Vault.sol::204 => function addAllowedToken(address tokenAddress) external onlyGuardian {
2022-12-gogopool/contracts/contract/Vault.sol::208 => function removeAllowedToken(address tokenAddress) external onlyGuardian {
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::142 => function depositFromStaking(uint256 baseAmt, uint256 rewardAmt) public payable onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::153 => function withdrawForStaking(uint256 assets) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::255 => function _authorizeUpgrade(address newImplementation) internal override onlyGuardian {}
```


### [G12] Bitshift for divide by 2

#### Impact
When multiplying or dividing by a power of two, it is cheaper to bitshift than to use standard math operations.
#### Findings:
```
2022-12-gogopool/contracts/contract/MinipoolManager.sol::413 => uint256 avaxHalfRewards = avaxTotalRewardAmt / 2;
```



### [G13] Use `calldata` instead of `memory` for function parameters

#### Impact
Use calldata instead of memory for function parameters. Having function arguments use calldata instead of memory can save gas.
#### Findings:
```
2022-12-gogopool/contracts/contract/BaseAbstract.sol::90 => function getContractAddress(string memory contractName) internal view returns (address) {
2022-12-gogopool/contracts/contract/BaseAbstract.sol::143 => function setBytes(bytes32 key, bytes memory value) internal {
2022-12-gogopool/contracts/contract/BaseAbstract.sol::159 => function setString(bytes32 key, string memory value) internal {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::61 => function getContractPaused(string memory contractName) public view returns (bool) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::67 => function pauseContract(string memory contractName) public onlySpecificRegisteredContract("Ocyticus", msg.sender) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::73 => function resumeContract(string memory contractName) public onlySpecificRegisteredContract("Ocyticus", msg.sender) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::102 => function getClaimingContractPct(string memory claimingContract) public view returns (uint256) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::107 => function setClaimingContractPct(string memory claimingContract, uint256 decimal) public onlyGuardian valueNotGreaterThanOne(decimal) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::190 => function registerContract(address addr, string memory name) public onlyGuardian {
2022-12-gogopool/contracts/contract/RewardsPool.sol::134 => function getClaimingContractDistribution(string memory claimingContract) public view returns (uint256) {
2022-12-gogopool/contracts/contract/Vault.sol::84 => function transferAVAX(string memory toContractName, uint256 amount) external onlyRegisteredNetworkContract {
2022-12-gogopool/contracts/contract/Vault.sol::193 => function balanceOf(string memory networkContractName) external view returns (uint256) {
2022-12-gogopool/contracts/contract/Vault.sol::200 => function balanceOfToken(string memory networkContractName, ERC20 tokenAddress) external view returns (uint256) {
```




### [G14] Amounts should be checked for 0 before calling a transfer

#### Impact
Checking non-zero transfer values can avoid an expensive external call and save gas.
#### Findings:
```
2022-12-gogopool/contracts/contract/Vault.sol::159 => tokenContract.safeTransfer(withdrawalAddress, amount);
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol::88 => asset.safeTransfer(receiver, assets);
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol::111 => asset.safeTransfer(receiver, assets);
```




### [G15] Multiple `address` mappings can be combined into a single `mapping` of an `address` to a `struct`, where appropriate

#### Impact
Saves a storage slot for the mapping. Depending on the circumstances 
and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. 
Reads and subsequent writes can also be cheaper when a function requires
 both values and they both fit in the same storage slot
#### Findings:
```
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol::47 => mapping(address => uint256) public nonces;
```



### [G16] Using `bools` for storage incurs overhead



#### Findings:
```
2022-12-gogopool/contracts/contract/Ocyticus.sol::13 => mapping(address => bool) public defenders;
2022-12-gogopool/contracts/contract/Storage.sol::16 => mapping(bytes32 => bool) private booleanStorage;
2022-12-gogopool/contracts/contract/Vault.sol::39 => mapping(address => bool) private allowedTokens;
```


### [G17] Using `private` rather than `public` for constants, saves gas

#### Impact
If needed, the value can be read from the verified contract source 
code.
#### Findings:
```
2022-12-gogopool/contracts/contract/MultisigManager.sol::27 => uint256 public constant MULTISIG_LIMIT = 10;
```




### [G18] Usage of `assert()` instead of `require()`


#### Findings:
```
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::83 => assert(msg.sender == address(asset));
```




### [G19] Empty blocks should be removed or emit something

#### Impact
The code should be refactored such that they no longer exist, or the 
block should do something useful, such as emitting an event or 
reverting. If the contract is meant to be extended, the contract should 
be abstract and the function signatures be added without 
any default implementation. If the block is an empty if-statement block 
to avoid doing subsequent checks in the else-if/else conditions, the 
else-if/else conditions should be nested under the negation of the 
if-statement, because they involve different classes of checks, which 
may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})
#### Findings:
```
2022-12-gogopool/contracts/contract/MinipoolManager.sol::106 => function receiveWithdrawalAVAX() external payable {}
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::255 => function _authorizeUpgrade(address newImplementation) internal override onlyGuardian {}
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol::176 => function beforeWithdraw(uint256 assets, uint256 shares) internal virtual {}
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol::178 => function afterDeposit(uint256 assets, uint256 shares) internal virtual {}
```



### [G20] `abi.encode()` is less efficient than abi.encodePacked()



#### Findings:
```
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol::138 => abi.encode(
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol::169 => abi.encode(
```




### [G21] Don’t compare boolean expressions to boolean literals

#### Impact
`if (<x> == true)` => `if (<x>)`, `if (<x> == false)` => `if (!<x>)`
#### Findings:
```
2022-12-gogopool/contracts/contract/BaseAbstract.sol::25 => if (getBool(keccak256(abi.encodePacked("contract.exists", msg.sender))) == false) {
2022-12-gogopool/contracts/contract/BaseAbstract.sol::74 => if (enabled == false) {
2022-12-gogopool/contracts/contract/Storage.sol::29 => if (booleanStorage[keccak256(abi.encodePacked("contract.exists", msg.sender))] == false && msg.sender != guardian) {
```




### [G22] Multiplication/division by two should use bit shifting

#### Impact
<x> * 2 is equivalent to <x> << 1 and <x> / 2 is the same as <x> >> 1. The MUL and DIV opcodes cost 5 gas, whereas SHL and SHR only cost 3 gas
#### Findings:
```
2022-12-gogopool/contracts/contract/MinipoolManager.sol::413 => uint256 avaxHalfRewards = avaxTotalRewardAmt / 2;
```




### [G23] Optimize names to save gas

#### Impact
public/external function names and public member variable names can be optimized to save gas. See [this](https://gist.github.com/IllIllI000/a5d8b486a8259f9f77891a919febd1a9)
 link for an example of how it works. Below are the interfaces/abstract 
contracts that can be optimized so that the most frequently-called 
functions use the least amount of gas possible during method lookup. 
Method IDs that have two leading zero bytes can save 128 gas each during deployment, and renaming functions to have lower method IDs will save 22 gas per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)
#### Findings:
```
2022-12-gogopool/contracts/contract/Base.sol::7 => abstract contract Base is BaseAbstract {
2022-12-gogopool/contracts/contract/BaseAbstract.sol::11 => abstract contract BaseAbstract {
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol::10 => abstract contract ERC20Upgradeable is Initializable {
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol::11 => abstract contract ERC4626Upgradeable is Initializable, ERC20Upgradeable {
```



