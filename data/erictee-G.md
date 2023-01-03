### [G-01] ```abi.encode()``` is less efficient than ```abi.encodePacked()```


#### Impact
Consider using ```abi.encodePacked()``` instead for efficieny.


#### Findings:
```
contract/tokens/upgradeable/ERC20Upgradeable.sol:L138							abi.encode(

contract/tokens/upgradeable/ERC20Upgradeable.sol:L169				abi.encode(

```

### [G-02] Use custom errors rather than ```revert()```/```require()``` string to save gas


#### Impact
Custom errors are available from solidity version 0.8.4, it saves around 50 gas each time they are hit by avoiding having to allocate and store the revert string.


#### Findings:
```
contract/tokens/upgradeable/ERC4626Upgradeable.sol:L44		require((shares = previewDeposit(assets)) != 0, "ZERO_SHARES");

contract/tokens/upgradeable/ERC4626Upgradeable.sol:L103		require((assets = previewRedeem(shares)) != 0, "ZERO_ASSETS");

contract/tokens/upgradeable/ERC20Upgradeable.sol:L127		require(deadline >= block.timestamp, "PERMIT_DEADLINE_EXPIRED");

contract/tokens/upgradeable/ERC20Upgradeable.sol:L154			require(recoveredAddress != address(0) && recoveredAddress == owner, "INVALID_SIGNER");

```

### [G-03] Empty blocks should be removed or emit something


#### Impact
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.


#### Findings:
```
contract/MinipoolManager.sol:L106	function receiveWithdrawalAVAX() external payable {}

contract/tokens/TokenggAVAX.sol:L255	function _authorizeUpgrade(address newImplementation) internal override onlyGuardian {}

contract/tokens/upgradeable/ERC4626Upgradeable.sol:L176	function beforeWithdraw(uint256 assets, uint256 shares) internal virtual {}

contract/tokens/upgradeable/ERC4626Upgradeable.sol:L178	function afterDeposit(uint256 assets, uint256 shares) internal virtual {}

```

### [G-04] Use a more recent version of solidity.


#### Impact
Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining 
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads 
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings 
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value.


#### Findings:
```
contract/tokens/upgradeable/ERC4626Upgradeable.sol:L2      pragma solidity >=0.8.0;

contract/tokens/upgradeable/ERC20Upgradeable.sol:L2      pragma solidity >=0.8.0;

```

### [G-05] Functions guaranteed to revert when called by normal users can be declared as payable.


#### Impact
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.


#### Findings:
```
contract/ClaimNodeOp.sol:L40	function setRewardsCycleTotal(uint256 amount) public onlySpecificRegisteredContract("RewardsPool", msg.sender) {

contract/ClaimNodeOp.sol:L56	function calculateAndDistributeRewards(address stakerAddr, uint256 totalEligibleGGPStaked) external onlyMultisig {

contract/MultisigManager.sol:L35	function registerMultisig(address addr) external onlyGuardian {

contract/MultisigManager.sol:L55	function enableMultisig(address addr) external onlyGuardian {

contract/Vault.sol:L61	function withdrawAVAX(uint256 amount) external onlyRegisteredNetworkContract nonReentrant {

contract/Vault.sol:L84	function transferAVAX(string memory toContractName, uint256 amount) external onlyRegisteredNetworkContract {

contract/Vault.sol:L204	function addAllowedToken(address tokenAddress) external onlyGuardian {

contract/Vault.sol:L208	function removeAllowedToken(address tokenAddress) external onlyGuardian {

contract/Staking.sol:L110	function increaseAVAXStake(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

contract/Staking.sol:L117	function decreaseAVAXStake(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

contract/Staking.sol:L133	function increaseAVAXAssigned(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

contract/Staking.sol:L140	function decreaseAVAXAssigned(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

contract/Staking.sol:L156	function increaseAVAXAssignedHighWater(address stakerAddr, uint256 amount) public onlyRegisteredNetworkContract {

contract/Staking.sol:L163	function resetAVAXAssignedHighWater(address stakerAddr) public onlyRegisteredNetworkContract {

contract/Staking.sol:L180	function increaseMinipoolCount(address stakerAddr) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

contract/Staking.sol:L187	function decreaseMinipoolCount(address stakerAddr) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

contract/Staking.sol:L204	function setRewardsStartTime(address stakerAddr, uint256 time) public onlyRegisteredNetworkContract {

contract/Staking.sol:L224	function increaseGGPRewards(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {

contract/Staking.sol:L231	function decreaseGGPRewards(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {

contract/Staking.sol:L248	function setLastRewardsCycleCompleted(address stakerAddr, uint256 cycleNumber) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {

contract/Staking.sol:L328	function restakeGGP(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {

contract/Staking.sol:L379	function slashGGP(address stakerAddr, uint256 ggpAmt) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

contract/RewardsPool.sol:L34	function initialize() external onlyGuardian {

contract/BaseUpgradeable.sol:L10	function __BaseUpgradeable_init(Storage gogoStorageAddress) internal onlyInitializing {

contract/ProtocolDAO.sol:L23	function initialize() external onlyGuardian {

contract/ProtocolDAO.sol:L67	function pauseContract(string memory contractName) public onlySpecificRegisteredContract("Ocyticus", msg.sender) {

contract/ProtocolDAO.sol:L73	function resumeContract(string memory contractName) public onlySpecificRegisteredContract("Ocyticus", msg.sender) {

contract/ProtocolDAO.sol:L96	function setTotalGGPCirculatingSupply(uint256 amount) public onlySpecificRegisteredContract("RewardsPool", msg.sender) {

contract/ProtocolDAO.sol:L107	function setClaimingContractPct(string memory claimingContract, uint256 decimal) public onlyGuardian valueNotGreaterThanOne(decimal) {

contract/ProtocolDAO.sol:L156	function setExpectedAVAXRewardsRate(uint256 rate) public onlyMultisig valueNotGreaterThanOne(rate) {

contract/ProtocolDAO.sol:L190	function registerContract(address addr, string memory name) public onlyGuardian {

contract/ProtocolDAO.sol:L198	function unregisterContract(address addr) public onlyGuardian {

contract/Ocyticus.sol:L27	function addDefender(address defender) external onlyGuardian {

contract/Ocyticus.sol:L32	function removeDefender(address defender) external onlyGuardian {

contract/Ocyticus.sol:L37	function pauseEverything() external onlyDefender {

contract/Ocyticus.sol:L47	function resumeEverything() external onlyDefender {

contract/Ocyticus.sol:L55	function disableAllMultisigs() public onlyDefender {

contract/Oracle.sol:L28	function setOneInch(address addr) external onlyGuardian {

contract/Oracle.sol:L57	function setGGPPriceInAVAX(uint256 price, uint256 timestamp) external onlyMultisig {

contract/Storage.sol:L104	function setAddress(bytes32 key, address value) external onlyRegisteredNetworkContract {

contract/Storage.sol:L108	function setBool(bytes32 key, bool value) external onlyRegisteredNetworkContract {

contract/Storage.sol:L112	function setBytes(bytes32 key, bytes calldata value) external onlyRegisteredNetworkContract {

contract/Storage.sol:L116	function setBytes32(bytes32 key, bytes32 value) external onlyRegisteredNetworkContract {

contract/Storage.sol:L120	function setInt(bytes32 key, int256 value) external onlyRegisteredNetworkContract {

contract/Storage.sol:L124	function setString(bytes32 key, string calldata value) external onlyRegisteredNetworkContract {

contract/Storage.sol:L128	function setUint(bytes32 key, uint256 value) external onlyRegisteredNetworkContract {

contract/Storage.sol:L136	function deleteAddress(bytes32 key) external onlyRegisteredNetworkContract {

contract/Storage.sol:L140	function deleteBool(bytes32 key) external onlyRegisteredNetworkContract {

contract/Storage.sol:L144	function deleteBytes(bytes32 key) external onlyRegisteredNetworkContract {

contract/Storage.sol:L148	function deleteBytes32(bytes32 key) external onlyRegisteredNetworkContract {

contract/Storage.sol:L152	function deleteInt(bytes32 key) external onlyRegisteredNetworkContract {

contract/Storage.sol:L156	function deleteString(bytes32 key) external onlyRegisteredNetworkContract {

contract/Storage.sol:L160	function deleteUint(bytes32 key) external onlyRegisteredNetworkContract {

contract/Storage.sol:L170	function addUint(bytes32 key, uint256 amount) external onlyRegisteredNetworkContract {

contract/Storage.sol:L176	function subUint(bytes32 key, uint256 amount) external onlyRegisteredNetworkContract {

contract/tokens/TokenggAVAX.sol:L153	function withdrawForStaking(uint256 assets) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

contract/tokens/TokenggAVAX.sol:L255	function _authorizeUpgrade(address newImplementation) internal override onlyGuardian {}

```

### [G-06] Splitting ```require()``` statements that use && saves gas.


#### Impact
Consider splitting the ```require()``` statements to save gas.


#### Findings:
```
contract/tokens/upgradeable/ERC20Upgradeable.sol:L154			require(recoveredAddress != address(0) && recoveredAddress == owner, "INVALID_SIGNER");

```

### [G-07] ```++i```/```i++``` should be ```unchecked{++i}```/```unchecked{i++}``` when it is not possible for them to overflow, as is the case when used in for- and while-loops.


#### Impact
The ```unchecked``` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop.


#### Findings:
```
contract/MultisigManager.sol:L84		for (uint256 i = 0; i < total; i++) {

contract/Staking.sol:L428		for (uint256 i = offset; i < max; i++) {

contract/RewardsPool.sol:L74		for (uint256 i = 0; i < inflationIntervalsElapsed; i++) {

contract/RewardsPool.sol:L215		for (uint256 i = 0; i < count; i++) {

contract/RewardsPool.sol:L230		for (uint256 i = 0; i < enabledMultisigs.length; i++) {

contract/Ocyticus.sol:L61		for (uint256 i = 0; i < count; i++) {

contract/MinipoolManager.sol:L619		for (uint256 i = offset; i < max; i++) {

```

