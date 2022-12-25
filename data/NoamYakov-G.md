
## Summary

### Gas Optimizations
| |Issue|Instances|Total Gas Saved|
|-|:-|:-:|:-:|
| [G&#x2011;01] | State variables can be packed into fewer storage slots | 1 |  - |
| [G&#x2011;02] | Use a more recent version of solidity | 2 |  - |
| [G&#x2011;03] | `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops | 10 |  600 |
| [G&#x2011;04] | `internal` functions only called once can be inlined to save gas | 4 |  80 |
| [G&#x2011;05] | Functions guaranteed to revert when called by normal users can be marked `payable` | 62 |  1302 |
| [G&#x2011;06] | Optimize names to save gas | 13 |  286 |
| [G&#x2011;07] | Use custom errors rather than `revert()`/`require()` strings to save gas | 4 |  200 |
| [G&#x2011;08] | Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement | 7 |  595 |
| [G&#x2011;09] | Multiple accesses of a mapping/array should use a local variable cache | 4 |  168 |
| [G&#x2011;10] | State variables should be cached in stack variables rather than re-reading them from storage | 11 |  1067 |
| [G&#x2011;11] | Modification of `getX()`, `setX()`, `deleteX()`, `addX()` and `subX()` in `BaseAbstract.sol` increases gas savings in [G&#x2011;10] | 116 |  11252 |
| [G&#x2011;12] | `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables (`-=` too) | 12 |  1356 |
| [G&#x2011;13] | Division by two should use bit shifting | 1 |  20 |
| [G&#x2011;14] | `keccak256()` should only need to be called on a specific string literal once | 74 |  3108 |
| [G&#x2011;15] | The result of function calls should be cached rather than re-calling the function | 2 |  200 |
| [G&#x2011;16] | Don't compare boolean expressions to boolean literals | 3 |  27 |
| [G&#x2011;17] | Splitting `require()` statements that use `&&` saves gas | 1 |  3 |
| [G&#x2011;18] | Stack variable used as a cheaper cache for a state variable is only used once | 3 | 9 |

Total: 330 instances over 18 issues with **20273 gas** saved.

Gas totals use lower bounds of ranges and count two iterations of each `for`-loop. All values above are runtime, not deployment, values; deployment values are listed in the individual issue descriptions.

## Gas Optimizations

### [G&#x2011;01]  State variables can be packed into fewer storage slots
If variables occupying the same slot are both written by the same function or by the constructor, avoids a separate Gsset (**20000 gas**). Reads of the variables can also be cheaper.

*There is 1 instance of this issue:*

```solidity
File: contracts\contract\tokens\TokenggAVAX.sol

/// @audit currently, `lastSync`, `rewardsCycleLength` and `rewardsCycleEnd` are
/// 	stored in a single storage slot and `lastRewardsAmt` is in another one.
/// 	Since all of these state variables except for `rewardsCycleLength` are
/// 	being set together (in `syncRewards()`), I suggest to reorder them so
/// 	that they will use the same storage slot and `rewardsCycleLength` will
/// 	use a different one.
40  	/// @notice the effective start of the current cycle
41  	uint32 public lastSync;
42  
43  	/// @notice the maximum length of a rewards cycle
44  	uint32 public rewardsCycleLength;
45  
46  	/// @notice the end of the current cycle. Will always be evenly divisible by `rewardsCycleLength`.
47  	uint32 public rewardsCycleEnd;
48  
49  	/// @notice the amount of rewards distributed in a the most recent cycle.
50  	uint192 public lastRewardsAmt;
```

### [G&#x2011;02]  Use a more recent version of solidity
Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining.
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads.
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than `revert()/require()` strings.
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value.

*There are 2 instances of this issue:*

```solidity
File: contracts\contract\tokens\upgradeable\ERC20Upgradeable.sol

2      pragma solidity >=0.8.0;
```

```solidity
File: contracts\contract\tokens\upgradeable\ERC4626Upgradeable.sol

2      pragma solidity >=0.8.0;
```

### [G&#x2011;03]  `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops
The `unchecked` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves **30-40 gas [per loop](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)**.

*There are 10 instances of this issue:*

```solidity
File: contracts\contract\MinipoolManager.sol

619 		for (uint256 i = offset; i < max; i++) {

/// @audit inside `for`-loop
623 				total++;
```

```solidity
File: contracts\contract\MultisigManager.sol

84  		for (uint256 i = 0; i < total; i++) {
```

```solidity
File: contracts\contract\Ocyticus.sol

61  		for (uint256 i = 0; i < count; i++) {
```

```solidity
File: contracts\contract\RewardsPool.sol

74  		for (uint256 i = 0; i < inflationIntervalsElapsed; i++) {

215 		for (uint256 i = 0; i < count; i++) {

/// @audit inside `for`-loop
219 				enabledCount++;

230 		for (uint256 i = 0; i < enabledMultisigs.length; i++) {
```

```solidity
File: contracts\contract\Staking.sol

428 		for (uint256 i = offset; i < max; i++) {

/// @audit inside `for`-loop
431 			total++;
```

### [G&#x2011;04]  `internal` functions only called once can be inlined to save gas
Not inlining costs **20 to 40 gas** because of two extra `JUMP` instructions and additional stack operations needed for function calls.

*There are 4 instances of this issue:*

```solidity
File: contracts\contract\RewardsPool.sol

82  	function inflate() internal {

110 	function increaseRewardsCycleCount() internal {

203 	function distributeMultisigAllotment(
204 		uint256 allotment,
205 		Vault vault,
206 		TokenGGP ggp
207 	) internal {
```

```solidity
File: contracts\contract\Staking.sol

87  	function increaseGGPStake(address stakerAddr, uint256 amount) internal {
```

### [G&#x2011;05]  Functions guaranteed to revert when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are 
`CALLVALUE`(2),`DUP1`(3),`ISZERO`(3),`PUSH2`(3),`JUMPI`(10),`PUSH1`(3),`DUP1`(3),`REVERT`(0),`JUMPDEST`(1),`POP`(2), which costs an average of about **21 gas per call** to the function, in addition to the extra deployment cost.

*There are 62 instances of this issue:*

```solidity
File: contracts\contract\ClaimNodeOp.sol

40  	function setRewardsCycleTotal(uint256 amount) public onlySpecificRegisteredContract("RewardsPool", msg.sender) {

56  	function calculateAndDistributeRewards(address stakerAddr, uint256 totalEligibleGGPStaked) external onlyMultisig {
```

```solidity
File: contracts\contract\Ocyticus.sol

27  	function addDefender(address defender) external onlyGuardian {

32  	function removeDefender(address defender) external onlyGuardian {

37  	function pauseEverything() external onlyDefender {

47  	function resumeEverything() external onlyDefender {

55  	function disableAllMultisigs() public onlyDefender {
```

```solidity
File: contracts\contract\ProtocolDAO.sol

23  	function initialize() external onlyGuardian {

67  	function pauseContract(string memory contractName) public onlySpecificRegisteredContract("Ocyticus", msg.sender) {

73  	function resumeContract(string memory contractName) public onlySpecificRegisteredContract("Ocyticus", msg.sender) {

96  	function setTotalGGPCirculatingSupply(uint256 amount) public onlySpecificRegisteredContract("RewardsPool", msg.sender) {

107 	function setClaimingContractPct(string memory claimingContract, uint256 decimal) public onlyGuardian valueNotGreaterThanOne(decimal) {

156 	function setExpectedAVAXRewardsRate(uint256 rate) public onlyMultisig valueNotGreaterThanOne(rate) {

190 	function registerContract(address addr, string memory name) public onlyGuardian {

198 	function unregisterContract(address addr) public onlyGuardian {

209 	function upgradeExistingContract(
210 		address newAddr,
211 		string memory newName,
212 		address existingAddr
213 	) external onlyGuardian {
```

```solidity
File: contracts\contract\Staking.sol

110 	function increaseAVAXStake(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

117 	function decreaseAVAXStake(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

133 	function increaseAVAXAssigned(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

140 	function decreaseAVAXAssigned(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

156 	function increaseAVAXAssignedHighWater(address stakerAddr, uint256 amount) public onlyRegisteredNetworkContract {

163 	function resetAVAXAssignedHighWater(address stakerAddr) public onlyRegisteredNetworkContract {

180 	function increaseMinipoolCount(address stakerAddr) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

187 	function decreaseMinipoolCount(address stakerAddr) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

204 	function setRewardsStartTime(address stakerAddr, uint256 time) public onlyRegisteredNetworkContract {

224 	function increaseGGPRewards(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {

231 	function decreaseGGPRewards(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {

248 	function setLastRewardsCycleCompleted(address stakerAddr, uint256 cycleNumber) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {

328 	function restakeGGP(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {

379 	function slashGGP(address stakerAddr, uint256 ggpAmt) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
```

```solidity
File: contracts\contract\tokens\TokenggAVAX.sol

153 	function withdrawForStaking(uint256 assets) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
```

```solidity
File: contracts\contract\ClaimProtocolDAO.sol

20  	function spend(
21  		string memory invoiceID,
22  		address recipientAddress,
23  		uint256 amount
24  	) external onlyGuardian {
```

```solidity
File: contracts\contract\MultisigManager.sol

35  	function registerMultisig(address addr) external onlyGuardian {

55  	function enableMultisig(address addr) external onlyGuardian {

68  	function disableMultisig(address addr) external guardianOrSpecificRegisteredContract("Ocyticus", msg.sender) {
```

```solidity
File: contracts\contract\Oracle.sol

28  	function setOneInch(address addr) external onlyGuardian {

57  	function setGGPPriceInAVAX(uint256 price, uint256 timestamp) external onlyMultisig {
```

```solidity
File: contracts\contract\RewardsPool.sol

34  	function initialize() external onlyGuardian {
```

```solidity
File: contracts\contract\Storage.sol

104 	function setAddress(bytes32 key, address value) external onlyRegisteredNetworkContract {

108 	function setBool(bytes32 key, bool value) external onlyRegisteredNetworkContract {

112 	function setBytes(bytes32 key, bytes calldata value) external onlyRegisteredNetworkContract {

116 	function setBytes32(bytes32 key, bytes32 value) external onlyRegisteredNetworkContract {

120 	function setInt(bytes32 key, int256 value) external onlyRegisteredNetworkContract {

124 	function setString(bytes32 key, string calldata value) external onlyRegisteredNetworkContract {

128 	function setUint(bytes32 key, uint256 value) external onlyRegisteredNetworkContract {

136 	function deleteAddress(bytes32 key) external onlyRegisteredNetworkContract {

140 	function deleteBool(bytes32 key) external onlyRegisteredNetworkContract {

144 	function deleteBytes(bytes32 key) external onlyRegisteredNetworkContract {

148 	function deleteBytes32(bytes32 key) external onlyRegisteredNetworkContract {

152 	function deleteInt(bytes32 key) external onlyRegisteredNetworkContract {

156 	function deleteString(bytes32 key) external onlyRegisteredNetworkContract {

160 	function deleteUint(bytes32 key) external onlyRegisteredNetworkContract {

170 	function addUint(bytes32 key, uint256 amount) external onlyRegisteredNetworkContract {

176 	function subUint(bytes32 key, uint256 amount) external onlyRegisteredNetworkContract {
```

```solidity
File: contracts\contract\Vault.sol

46  	function depositAVAX() external payable onlyRegisteredNetworkContract {

61  	function withdrawAVAX(uint256 amount) external onlyRegisteredNetworkContract nonReentrant {

84  	function transferAVAX(string memory toContractName, uint256 amount) external onlyRegisteredNetworkContract {

108 	function depositToken(
109 		string memory networkContractName,
110 		ERC20 tokenContract,
111 		uint256 amount
112 	) external guardianOrRegisteredContract {

137 	function withdrawToken(
138 		address withdrawalAddress,
139 		ERC20 tokenAddress,
140 		uint256 amount
141 	) external onlyRegisteredNetworkContract nonReentrant {

166 	function transferToken(
167 		string memory networkContractName,
168 		ERC20 tokenAddress,
169 		uint256 amount
170 	) external onlyRegisteredNetworkContract {

204 	function addAllowedToken(address tokenAddress) external onlyGuardian {

208 	function removeAllowedToken(address tokenAddress) external onlyGuardian {
```

### [G&#x2011;06]  Optimize names to save gas
`public`/`external` function names and `public` member variable names can be optimized to save gas. See [this](https://gist.github.com/IllIllI000/a5d8b486a8259f9f77891a919febd1a9) link for an example of how it works. Below are the interfaces/abstract contracts that can be optimized so that the most frequently-called functions use the least amount of gas possible during method lookup. Method IDs that have two leading zero bytes can save **128 gas** each during deployment, and renaming functions to have lower method IDs will save **22 gas** per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92).

*There are 13 instances of this issue:*

```solidity
File: contracts\contract\BaseAbstract.sol

/// @audit version
11  abstract contract BaseAbstract {
```

```solidity
File: contracts\contract\ClaimNodeOp.sol

/// @audit ggp, getRewardsCycleTotal(), setRewardsCycleTotal(), isEligible(),
///     calculateAndDistributeRewards(), claimAndRestake()
17  contract ClaimNodeOp is Base {
```

```solidity
File: contracts\contract\ClaimProtocolDAO.sol

/// @audit spend()
10  contract ClaimProtocolDAO is Base {
```

```solidity
File: contracts\contract\MinipoolManager.sol

/// @audit ggp, ggAVAX, receiveWithdrawalAVAX(), createMinipool(),
///     cancelMinipool(), withdrawMinipoolFunds(), canClaimAndInitiateStaking(),
///     claimAndInitiateStaking(), recordStakingStart(), recordStakingEnd(),
///     recreateMinipool(), recordStakingError(), cancelMinipoolByMultisig(),
///     finishFailedMinipoolByMultisig(), getTotalAVAXLiquidStakerAmt(),
///     calculateGGPSlashAmt(), getExpectedAVAXRewardsAmt(), getIndexOf(),
///     getMinipoolByNodeID(), getMinipool(), getMinipools(),
///     getMinipoolCount()
contract MinipoolManager is Base, ReentrancyGuard, IWithdrawer {
```

```solidity
File: contracts\contract\MultisigManager.sol

/// @audit MULTISIG_LIMIT, registerMultisig(), enableMultisig(),
///     disableMultisig(), requireNextActiveMultisig(), getIndexOf(),
///     getCount(), getMultisig()
17  contract MultisigManager is Base {
```

```solidity
File: contracts\contract\Ocyticus.sol

/// @audit defenders, addDefender(), removeDefender(), pauseEverything(),
///     resumeEverything(), disableAllMultisigs()
10  contract Ocyticus is Base {
```

```solidity
File: contracts\contract\Oracle.sol

/// @audit setOneInch(), getGGPPriceInAVAXFromOneInch(), getGGPPriceInAVAX(),
///     setGGPPriceInAVAX()
17  contract Oracle is Base {
```

```solidity
File: contracts\contract\ProtocolDAO.sol

/// @audit initialize(), getContractPaused(), pauseContract(), resumeContract(),
///     getRewardsEligibilityMinSeconds(), getRewardsCycleSeconds(),
///     getTotalGGPCirculatingSupply(), setTotalGGPCirculatingSupply(),
///     getClaimingContractPct(), setClaimingContractPct(),
///     getInflationIntervalRate(), getInflationIntervalSeconds(),
///     getMinipoolMinAVAXStakingAmt(), getMinipoolNodeCommissionFeePct(),
///     getMinipoolMaxAVAXAssignment(), getMinipoolMinAVAXAssignment(),
///     getMinipoolCancelMoratoriumSeconds(), setExpectedAVAXRewardsRate(),
///     getExpectedAVAXRewardsRate(), getMaxCollateralizationRatio(),
///     getMinCollateralizationRatio(), getTargetGGAVAXReserveRate(),
///     registerContract(), unregisterContract(), upgradeExistingContract()
9   contract ProtocolDAO is Base {
```

```solidity
File: contracts\contract\RewardsPool.sol

/// @audit initialize(), getInflationIntervalStartTime(),
///     getInflationIntervalsElapsed(), getInflationAmt(),
///     getRewardsCycleCount(), getRewardsCycleStartTime(),
///     getRewardsCycleTotalAmt(), getRewardsCyclesElapsed(),
///     getClaimingContractDistribution(), canStartRewardsCycle(),
///     startRewardsCycle()
15  contract RewardsPool is Base {
```

```solidity
File: contracts\contract\Staking.sol

/// @audit ggp, getTotalGGPStake(), getStakerCount(), getGGPStake(),
///     getAVAXStake(), increaseAVAXStake(), decreaseAVAXStake(),
///     getAVAXAssigned(), increaseAVAXAssigned(), decreaseAVAXAssigned(),
///     getAVAXAssignedHighWater(), increaseAVAXAssignedHighWater(),
///     resetAVAXAssignedHighWater(), getMinipoolCount(),
///     increaseMinipoolCount(), decreaseMinipoolCount(), getRewardsStartTime(),
///     setRewardsStartTime(), getGGPRewards(), increaseGGPRewards(),
///     decreaseGGPRewards(), getLastRewardsCycleCompleted(),
///     setLastRewardsCycleCompleted(), getMinimumGGPStake(),
///     getCollateralizationRatio(), getEffectiveRewardsRatio(),
///     getEffectiveGGPStaked(), stakeGGP(), restakeGGP(), withdrawGGP(),
///     slashGGP(), requireValidStaker(), getIndexOf(), getStaker(),
///     getStakers()
30  contract Staking is Base {
```

```solidity
File: contracts\contract\Storage.sol

/// @audit newGuardian, setGuardian(), getGuardian(), confirmGuardian(),
///     getAddress(), getBool(), getBytes(), getBytes32(), getInt(),
///     getString(), getUint(), setAddress(), setBool(), setBytes(),
///     setBytes32(), setInt(), setString(), setUint(), deleteAddress(),
///     deleteBool(), deleteBytes(), deleteBytes32(), deleteInt(),
///     deleteString(), deleteUint(), addUint(), subUint()
7   contract Storage {
```

```solidity
File: contracts\contract\Vault.sol

/// @audit depositAVAX(), withdrawAVAX(), transferAVAX(), depositToken(),
///     withdrawToken(), transferToken(), balanceOf(), balanceOfToken(),
///     addAllowedToken(), removeAllowedToken()
19  contract Vault is Base, ReentrancyGuard {
```

```solidity
File: contracts\contract\tokens\TokenggAVAX.sol

/// @audit lastSync, rewardsCycleLength, rewardsCycleEnd, lastRewardsAmt,
///     totalReleasedAssets, stakingTotalAssets, initialize(), syncRewards(),
///     totalAssets(), amountAvailableForStaking(), depositFromStaking(),
///     withdrawForStaking(), depositAVAX(), withdrawAVAX(), redeemAVAX()
24  contract TokenggAVAX is Initializable, ERC4626Upgradeable, UUPSUpgradeable, BaseUpgradeable {
```

### [G&#x2011;07]  Use custom errors rather than `revert()`/`require()` strings to save gas
Custom errors are available from solidity version 0.8.4. Custom errors save [**~50 gas**](https://gist.github.com/IllIllI000/ad1bd0d29a0101b25e57c293b4b0c746) each time they're hit by [avoiding having to allocate and store the revert string](https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth). Not defining the strings also save deployment gas.

*There are 4 instances of this issue:*

```solidity
File: contracts\contract\tokens\upgradeable\ERC20Upgradeable.sol

127 		require(deadline >= block.timestamp, "PERMIT_DEADLINE_EXPIRED");

154 			require(recoveredAddress != address(0) && recoveredAddress == owner, "INVALID_SIGNER");
```

```solidity
File: contracts\contract\tokens\upgradeable\ERC4626Upgradeable.sol

44  		require((shares = previewDeposit(assets)) != 0, "ZERO_SHARES");

103 		require((assets = previewRedeem(shares)) != 0, "ZERO_ASSETS");
```

### [G&#x2011;08]  Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement
`require(a <= b); x = b - a` => `require(a <= b); unchecked { x = b - a }`.

*There are 7 instances of this issue:*

```solidity
File: contracts\contract\ClaimNodeOp.sol

/// @audit `if`-condition on line 95
102 		uint256 restakeAmt = ggpRewards - claimAmt;
```

```solidity
File: contracts\contract\MinipoolManager.sol

/// @audit `avaxLiquidStakerRewardAmt` <= `avaxHalfRewards` =
///     `avaxTotalRewardAmt / 2` <= `avaxTotalRewardAmt` by definition
418 		uint256 avaxNodeOpRewardAmt = avaxTotalRewardAmt - avaxLiquidStakerRewardAmt;
```

```solidity
File: contracts\contract\Vault.sol

/// @audit `if`-condition on line 71
75  		avaxBalances[contractName] = avaxBalances[contractName] - amount;

/// @audit `if`-condition on line 95
99  		avaxBalances[fromContractName] = avaxBalances[fromContractName] - amount;

/// @audit `if`-condition on line 151
155 		tokenBalances[contractKey] = tokenBalances[contractKey] - amount;

/// @audit `if`-condition on line 183
187 		tokenBalances[contractKeyFrom] = tokenBalances[contractKeyFrom] - amount;
```

```solidity
File: contracts\contract\tokens\TokenggAVAX.sol

/// @audit `if`-condition on line 144
149 		stakingTotalAssets -= baseAmt;
```

### [G&#x2011;09]  Multiple accesses of a mapping/array should use a local variable cache
The instances below point to the second+ access of a value inside a mapping/array, within a function. Caching a mapping's value in a local `storage` or `calldata` variable when the value is accessed [multiple times](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0), saves **~42 gas per access** due to not having to recalculate the key's keccak256 hash (Gkeccak256 - **30 gas**) and that calculation's associated stack operations. Caching an array's struct avoids recalculating the array offsets into memory/calldata.

*There are 4 instances of this issue:*

```solidity
File: contracts\contract\Vault.sol

/// @audit `avaxBalances[contractName]` on line 71
75  		avaxBalances[contractName] = avaxBalances[contractName] - amount;

/// @audit `avaxBalances[fromContractName]` on line 95
99  		avaxBalances[fromContractName] = avaxBalances[fromContractName] - amount;

/// @audit `tokenBalances[contractKey]` on line 151
155 		tokenBalances[contractKey] = tokenBalances[contractKey] - amount;

/// @audit `tokenBalances[contractKeyFrom]` on line 183
187 		tokenBalances[contractKeyFrom] = tokenBalances[contractKeyFrom] - amount;
```

### [G&#x2011;10]  State variables should be cached in stack variables rather than re-reading them from storage
The instances below point to the second+ access of a state variable within a function. Caching of a state variable replaces each Gwarmaccess (**100 gas**) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

*There are 11 instances of this issue:*

```solidity
File: contracts\contract\tokens\TokenggAVAX.sol

/// @audit `rewardsCycleLength` was set to `14 days` on line 76
78  		rewardsCycleEnd = (block.timestamp.safeCastTo32() / rewardsCycleLength) * rewardsCycleLength;

/// @audit `rewardsCycleLength` was set to `14 days` on line 76
78  		rewardsCycleEnd = (block.timestamp.safeCastTo32() / rewardsCycleLength) * rewardsCycleLength;

/// @audit `rewardsCycleLength` on line 102
102 		uint32 nextRewardsCycleEnd = ((timestamp + rewardsCycleLength) / rewardsCycleLength) * rewardsCycleLength;

/// @audit `rewardsCycleLength` on line 102
102 		uint32 nextRewardsCycleEnd = ((timestamp + rewardsCycleLength) / rewardsCycleLength) * rewardsCycleLength;

/// @audit `stakingTotalAssets` on line 144
149 		stakingTotalAssets -= baseAmt;
```

```solidity
File: contracts\contract\Storage.sol

/// @audit `newGuardian` on line 57
63  		guardian = newGuardian;

/// @audit `guardian` was set to `newGuardian` on line 63
65  		emit GuardianChanged(oldGuardian, guardian);
```

```solidity
File: contracts\contract\Vault.sol

/// @audit `avaxBalances[contractName]` on line 71
75  		avaxBalances[contractName] = avaxBalances[contractName] - amount;

/// @audit `avaxBalances[fromContractName]` on line 95
99  		avaxBalances[fromContractName] = avaxBalances[fromContractName] - amount;

/// @audit `tokenBalances[contractKey]` on line 151
155 		tokenBalances[contractKey] = tokenBalances[contractKey] - amount;

/// @audit `tokenBalances[contractKeyFrom]` on line 183
187 		tokenBalances[contractKeyFrom] = tokenBalances[contractKeyFrom] - amount;
```

### [G&#x2011;11]  Modification of `getX()`, `setX()`, `deleteX()`, `addX()` and `subX()` in `BaseAbstract.sol` increases gas savings in [G&#x2011;10]
Modify the `getX()`, `setX()`, `deleteX()`, `addX()` and `subX()` functions in `BaseAbstract.sol` to receive the address of the `Storage` contract as an argument. This modification will create dozens more fixable instances of [G&#x2011;10], listed below.

*There are 116 instances of this issue:*

```solidity
File: contracts\contract\MinipoolManager.sol

/// @audit `gogoStorage` on line 248
250 			setUint(keccak256(abi.encodePacked("minipool.index", nodeID)), uint256(minipoolIndex + 1));

/// @audit `gogoStorage` on line 248
251 			setAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".nodeID")), nodeID);

/// @audit `gogoStorage` on line 248
252 			addUint(keccak256("minipool.count"), 1);

/// @audit `gogoStorage` on line 246 / 248
256 		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Prelaunch));

/// @audit `gogoStorage` on line 246 / 248
257 		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".duration")), duration);

/// @audit `gogoStorage` on line 246 / 248
258 		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".delegationFee")), delegationFee);

/// @audit `gogoStorage` on line 246 / 248
259 		setAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".owner")), msg.sender);

/// @audit `gogoStorage` on line 246 / 248
260 		setAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".multisigAddr")), multisig);

/// @audit `gogoStorage` on line 246 / 248
261 		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpInitialAmt")), msg.value);

/// @audit `gogoStorage` on line 246 / 248
262 		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpAmt")), msg.value);

/// @audit `gogoStorage` on line 246 / 248
263 		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")), avaxAssignmentRequest);

/// @audit `gogoStorage` on line 291
293 		uint256 avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpAmt")));

/// @audit `gogoStorage` on line 291
294 		uint256 avaxNodeOpRewardAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpRewardAmt")));

/// @audit `gogoStorage` on line 328
329 		uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));

/// @audit `gogoStorage` on line 328
333 		addUint(keccak256("MinipoolManager.TotalAVAXLiquidStakerAmt"), avaxLiquidStakerAmt);

/// @audit `gogoStorage` on line 328
335 		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Launched));

/// @audit `gogoStorage` on line 360
361 		setBytes32(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".txID")), txID);

/// @audit `gogoStorage` on line 360
362 		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".startTime")), startTime);

/// @audit `gogoStorage` on line 360
365 		uint256 initialStartTime = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".initialStartTime")));

/// @audit `gogoStorage` on line 360
367 			setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".initialStartTime")), startTime);

/// @audit `gogoStorage` on line 360
370 		address owner = getAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".owner")));

/// @audit `gogoStorage` on line 360
371 		uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));

/// @audit `gogoStorage` on line 393
398 		uint256 avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpAmt")));

/// @audit `gogoStorage` on line 393
399 		uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));

/// @audit `gogoStorage` on line 393
405 		address owner = getAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".owner")));

/// @audit `gogoStorage` on line 393
407 		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Withdrawable));

/// @audit `gogoStorage` on line 393
408 		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".endTime")), endTime);

/// @audit `gogoStorage` on line 393
409 		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxTotalRewardAmt")), avaxTotalRewardAmt);

/// @audit `gogoStorage` on line 393
420 		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpRewardAmt")), avaxNodeOpRewardAmt);

/// @audit `gogoStorage` on line 393
421 		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerRewardAmt")), avaxLiquidStakerRewardAmt);

/// @audit `gogoStorage` on line 393
433 		subUint(keccak256("MinipoolManager.TotalAVAXLiquidStakerAmt"), avaxLiquidStakerAmt);

/// @audit `gogoStorage` on line 451
452 		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")), compoundedAvaxNodeOpAmt);

/// @audit `gogoStorage` on line 451
475 		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Prelaunch));

/// @audit `gogoStorage` on line 488
489 		uint256 avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpAmt")));

/// @audit `gogoStorage` on line 488
490 		uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));

/// @audit `gogoStorage` on line 488
496 		setBytes32(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".errorCode")), errorCode);

/// @audit `gogoStorage` on line 488
497 		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Error));

/// @audit `gogoStorage` on line 488
498 		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxTotalRewardAmt")), 0);

/// @audit `gogoStorage` on line 488
499 		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpRewardAmt")), 0);

/// @audit `gogoStorage` on line 488
500 		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerRewardAmt")), 0);

/// @audit `gogoStorage` on line 488
512 		subUint(keccak256("MinipoolManager.TotalAVAXLiquidStakerAmt"), avaxLiquidStakerAmt);

/// @audit `gogoStorage` on line 582
583 		mp.status = getUint(keccak256(abi.encodePacked("minipool.item", index, ".status")));

/// @audit `gogoStorage` on line 582
584 		mp.duration = getUint(keccak256(abi.encodePacked("minipool.item", index, ".duration")));

/// @audit `gogoStorage` on line 582
585 		mp.delegationFee = getUint(keccak256(abi.encodePacked("minipool.item", index, ".delegationFee")));

/// @audit `gogoStorage` on line 582
586 		mp.owner = getAddress(keccak256(abi.encodePacked("minipool.item", index, ".owner")));

/// @audit `gogoStorage` on line 582
587 		mp.multisigAddr = getAddress(keccak256(abi.encodePacked("minipool.item", index, ".multisigAddr")));

/// @audit `gogoStorage` on line 582
588 		mp.avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxNodeOpAmt")));

/// @audit `gogoStorage` on line 582
589 		mp.avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxLiquidStakerAmt")));

/// @audit `gogoStorage` on line 582
590 		mp.txID = getBytes32(keccak256(abi.encodePacked("minipool.item", index, ".txID")));

/// @audit `gogoStorage` on line 582
591 		mp.initialStartTime = getUint(keccak256(abi.encodePacked("minipool.item", index, ".initialStartTime")));

/// @audit `gogoStorage` on line 582
592 		mp.startTime = getUint(keccak256(abi.encodePacked("minipool.item", index, ".startTime")));

/// @audit `gogoStorage` on line 582
593 		mp.endTime = getUint(keccak256(abi.encodePacked("minipool.item", index, ".endTime")));

/// @audit `gogoStorage` on line 582
594 		mp.avaxTotalRewardAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxTotalRewardAmt")));

/// @audit `gogoStorage` on line 582
595 		mp.errorCode = getBytes32(keccak256(abi.encodePacked("minipool.item", index, ".errorCode")));

/// @audit `gogoStorage` on line 582
596 		mp.avaxNodeOpInitialAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxNodeOpInitialAmt")));

/// @audit `gogoStorage` on line 582
597 		mp.avaxNodeOpRewardAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxNodeOpRewardAmt")));

/// @audit `gogoStorage` on line 582
598 		mp.avaxLiquidStakerRewardAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxLiquidStakerRewardAmt")));

/// @audit `gogoStorage` on line 582
599 		mp.ggpSlashAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".ggpSlashAmt")));

/// @audit `gogoStorage` on line 648
650 		address owner = getAddress(keccak256(abi.encodePacked("minipool.item", index, ".owner")));

/// @audit `gogoStorage` on line 648
651 		uint256 avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxNodeOpAmt")));

/// @audit `gogoStorage` on line 648
652 		uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxLiquidStakerAmt")));

/// @audit `gogoStorage` on line 671
672 		address owner = getAddress(keccak256(abi.encodePacked("minipool.item", index, ".owner")));

/// @audit `gogoStorage` on line 671
673 		uint256 duration = getUint(keccak256(abi.encodePacked("minipool.item", index, ".duration")));

/// @audit `gogoStorage` on line 671
674 		uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxLiquidStakerAmt")));

/// @audit `gogoStorage` on line 671
677 		setUint(keccak256(abi.encodePacked("minipool.item", index, ".ggpSlashAmt")), slashGGPAmt);

/// @audit `gogoStorage` on line 688
689 		setUint(keccak256(abi.encodePacked("minipool.item", index, ".startTime")), 0);

/// @audit `gogoStorage` on line 688
690 		setUint(keccak256(abi.encodePacked("minipool.item", index, ".endTime")), 0);

/// @audit `gogoStorage` on line 688
691 		setUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxTotalRewardAmt")), 0);

/// @audit `gogoStorage` on line 688
692 		setUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxNodeOpRewardAmt")), 0);

/// @audit `gogoStorage` on line 688
693 		setUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxLiquidStakerRewardAmt")), 0);

/// @audit `gogoStorage` on line 688
694 		setUint(keccak256(abi.encodePacked("minipool.item", index, ".ggpSlashAmt")), 0);

/// @audit `gogoStorage` on line 688
695 		setBytes32(keccak256(abi.encodePacked("minipool.item", index, ".errorCode")), 0);
```

```solidity
File: contracts\contract\MultisigManager.sol

/// @audit `gogoStorage` on line 40
45  		setAddress(keccak256(abi.encodePacked("multisig.item", index, ".address")), addr);

/// @audit `gogoStorage` on line 40
48  		setUint(keccak256(abi.encodePacked("multisig.index", addr)), index + 1);

/// @audit `gogoStorage` on line 40
49  		addUint(keccak256("multisig.count"), 1);

/// @audit `gogoStorage` on line 110
111 		enabled = (addr != address(0)) && getBool(keccak256(abi.encodePacked("multisig.item", index, ".enabled")));
```

```solidity
File: contracts\contract\Oracle.sol

/// @audit `gogoStorage` on line 47
51  		timestamp = getUint(keccak256("Oracle.GGPTimestamp"));

/// @audit `gogoStorage` on line 58
65  		setUint(keccak256("Oracle.GGPPriceInAVAX"), price);

/// @audit `gogoStorage` on line 58
66  		setUint(keccak256("Oracle.GGPTimestamp"), timestamp);
```

```solidity
File: contracts\contract\ProtocolDAO.sol

/// @audit `gogoStorage` on line 24
27  		setBool(keccak256("ProtocolDAO.initialized"), true);

/// @audit `gogoStorage` on line 24
30  		setUint(keccak256("ProtocolDAO.RewardsEligibilityMinSeconds"), 14 days);

/// @audit `gogoStorage` on line 24
33  		setUint(keccak256("ProtocolDAO.RewardsCycleSeconds"), 28 days); // The time in which a claim period will span in seconds - 28 days by default

/// @audit `gogoStorage` on line 24
34  		setUint(keccak256("ProtocolDAO.TotalGGPCirculatingSupply"), 18_000_000 ether);

/// @audit `gogoStorage` on line 24
35  		setUint(keccak256("ProtocolDAO.ClaimingContractPct.ClaimMultisig"), 0.20 ether);

/// @audit `gogoStorage` on line 24
36  		setUint(keccak256("ProtocolDAO.ClaimingContractPct.ClaimNodeOp"), 0.70 ether);

/// @audit `gogoStorage` on line 24
37  		setUint(keccak256("ProtocolDAO.ClaimingContractPct.ClaimProtocolDAO"), 0.10 ether);

/// @audit `gogoStorage` on line 24
40  		setUint(keccak256("ProtocolDAO.InflationIntervalSeconds"), 1 days);

/// @audit `gogoStorage` on line 24
41  		setUint(keccak256("ProtocolDAO.InflationIntervalRate"), 1000133680617113500); // 5% annual calculated on a daily interval - Calculate in js example: let dailyInflation = web3.utils.toBN((1 + 0.05) ** (1 / (365)) * 1e18);

/// @audit `gogoStorage` on line 24
44  		setUint(keccak256("ProtocolDAO.TargetGGAVAXReserveRate"), 0.1 ether); // 10% collateral held in reserve

/// @audit `gogoStorage` on line 24
47  		setUint(keccak256("ProtocolDAO.MinipoolMinAVAXStakingAmt"), 2_000 ether);

/// @audit `gogoStorage` on line 24
48  		setUint(keccak256("ProtocolDAO.MinipoolNodeCommissionFeePct"), 0.15 ether);

/// @audit `gogoStorage` on line 24
49  		setUint(keccak256("ProtocolDAO.MinipoolMaxAVAXAssignment"), 10_000 ether);

/// @audit `gogoStorage` on line 24
50  		setUint(keccak256("ProtocolDAO.MinipoolMinAVAXAssignment"), 1_000 ether);

/// @audit `gogoStorage` on line 24
51  		setUint(keccak256("ProtocolDAO.ExpectedAVAXRewardsRate"), 0.1 ether); // Annual rate as pct of 1 avax

/// @audit `gogoStorage` on line 24
52  		setUint(keccak256("ProtocolDAO.MinipoolCancelMoratoriumSeconds"), 5 days);

/// @audit `gogoStorage` on line 24
55  		setUint(keccak256("ProtocolDAO.MaxCollateralizationRatio"), 1.5 ether);

/// @audit `gogoStorage` on line 24
56  		setUint(keccak256("ProtocolDAO.MinCollateralizationRatio"), 0.1 ether);

/// @audit `gogoStorage` on line 191
192 		setAddress(keccak256(abi.encodePacked("contract.address", name)), addr);

/// @audit `gogoStorage` on line 191
193 		setString(keccak256(abi.encodePacked("contract.name", addr)), name);

/// @audit `gogoStorage` on line 200
201 		deleteAddress(keccak256(abi.encodePacked("contract.address", name)));

/// @audit `gogoStorage` on line 200
202 		deleteString(keccak256(abi.encodePacked("contract.name", addr)));
```

```solidity
File: contracts\contract\RewardsPool.sol

/// @audit `gogoStorage` on line 35
38  		setBool(keccak256("RewardsPool.initialized"), true);

/// @audit `gogoStorage` on line 35
40  		setUint(keccak256("RewardsPool.RewardsCycleStartTime"), block.timestamp);

/// @audit `gogoStorage` on line 35
41  		setUint(keccak256("RewardsPool.InflationIntervalStartTime"), block.timestamp);

/// @audit `gogoStorage` on line 98
99  		setUint(keccak256("RewardsPool.RewardsCycleTotalAmt"), newTokens);
```

```solidity
File: contracts\contract\Staking.sol

/// @audit `gogoStorage` on line 165
166 		setUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssignedHighWater")), currAVAXAssigned);

/// @audit `gogoStorage` on line 348
349 			addUint(keccak256("staker.count"), 1);

/// @audit `gogoStorage` on line 348
350 			setUint(keccak256(abi.encodePacked("staker.index", stakerAddr)), uint256(stakerIndex + 1));

/// @audit `gogoStorage` on line 348
351 			setAddress(keccak256(abi.encodePacked("staker.item", stakerIndex, ".stakerAddr")), stakerAddr);

/// @audit `gogoStorage` on line 406
407 		staker.avaxAssigned = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssigned")));

/// @audit `gogoStorage` on line 406
408 		staker.avaxStaked = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxStaked")));

/// @audit `gogoStorage` on line 406
409 		staker.stakerAddr = getAddress(keccak256(abi.encodePacked("staker.item", stakerIndex, ".stakerAddr")));

/// @audit `gogoStorage` on line 406
410 		staker.minipoolCount = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".minipoolCount")));

/// @audit `gogoStorage` on line 406
411 		staker.rewardsStartTime = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".rewardsStartTime")));

/// @audit `gogoStorage` on line 406
412 		staker.ggpRewards = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpRewards")));

/// @audit `gogoStorage` on line 406
413 		staker.lastRewardsCycleCompleted = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".lastRewardsCycleCompleted")));
```

### [G&#x2011;12]  `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables (`-=` too)
Using the addition operator instead of plus-equals saves **[113 gas](https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8)**. Subtructions act the same way.

*There are 12 instances of this issue:*

```solidity
File: contracts\contract\tokens\TokenggAVAX.sol

149 		stakingTotalAssets -= baseAmt;

160 		stakingTotalAssets += assets;

245 		totalReleasedAssets -= amount;

252 		totalReleasedAssets += amount;
```

```solidity
File: contracts\contract\tokens\upgradeable\ERC20Upgradeable.sol

79  		balanceOf[msg.sender] -= amount;

84  			balanceOf[to] += amount;

101 		balanceOf[from] -= amount;

106   			balanceOf[to] += amount;

184   		totalSupply += amount;

189 			balanceOf[to] += amount;

196 		balanceOf[from] -= amount;

201 			totalSupply -= amount;
```


### [G&#x2011;13]  Division by two should use bit shifting
`<x> / 2` is the same as `<x> >> 1`. While the compiler uses the `SHR` opcode to accomplish both, the version that uses division incurs an overhead of [**20 gas**](https://gist.github.com/IllIllI000/ec0e4e6c4f52a6bca158f137a3afd4ff) due to `JUMP`s to and from a compiler utility function that introduces checks which can be avoided by using `unchecked {}` around the division by two.

*There is 1 instance of this issue:*

```solidity
File: contracts\contract\MinipoolManager.sol

413 		uint256 avaxHalfRewards = avaxTotalRewardAmt / 2;
```

### [G&#x2011;14]  `keccak256()` should only need to be called on a specific string literal once
It should be saved to an immutable variable, and the variable used instead. If the hash is being used as a part of a function selector, the cast to `bytes4` should also only be done once.

*There are 74 instances of this issue:*

```solidity
File: contracts\contract\ClaimNodeOp.sol

36  		return getUint(keccak256("NOPClaim.RewardsCycleTotal"));

41  		setUint(keccak256("NOPClaim.RewardsCycleTotal"), amount);
```

```solidity
File: contracts\contract\MinipoolManager.sol

248 			minipoolIndex = int256(getUint(keccak256("minipool.count")));

252 			addUint(keccak256("minipool.count"), 1);

333 		addUint(keccak256("MinipoolManager.TotalAVAXLiquidStakerAmt"), avaxLiquidStakerAmt);

433 		subUint(keccak256("MinipoolManager.TotalAVAXLiquidStakerAmt"), avaxLiquidStakerAmt);

512 		subUint(keccak256("MinipoolManager.TotalAVAXLiquidStakerAmt"), avaxLiquidStakerAmt);

542 		return getUint(keccak256("MinipoolManager.TotalAVAXLiquidStakerAmt"));

612 		uint256 totalMinipools = getUint(keccak256("minipool.count"));

635 		return getUint(keccak256("minipool.count"));
```

```solidity
File: contracts\contract\MultisigManager.sol

40  		uint256 index = getUint(keccak256("multisig.count"));

49  		addUint(keccak256("multisig.count"), 1);

81  		uint256 total = getUint(keccak256("multisig.count"));

103 		return getUint(keccak256("multisig.count"));
```

```solidity
File: contracts\contract\Oracle.sol

29  		setAddress(keccak256("Oracle.OneInch"), addr);

38  		IOneInch oneinch = IOneInch(getAddress(keccak256("Oracle.OneInch")));

47  		price = getUint(keccak256("Oracle.GGPPriceInAVAX"));

51  		timestamp = getUint(keccak256("Oracle.GGPTimestamp"));

58  		uint256 lastTimestamp = getUint(keccak256("Oracle.GGPTimestamp"));

65  		setUint(keccak256("Oracle.GGPPriceInAVAX"), price);

66  		setUint(keccak256("Oracle.GGPTimestamp"), timestamp);
```

```solidity
File: contracts\contract\ProtocolDAO.sol

24  		if (getBool(keccak256("ProtocolDAO.initialized"))) {

27  		setBool(keccak256("ProtocolDAO.initialized"), true);

30  		setUint(keccak256("ProtocolDAO.RewardsEligibilityMinSeconds"), 14 days);

33  		setUint(keccak256("ProtocolDAO.RewardsCycleSeconds"), 28 days); // The time in which a claim period will span in seconds - 28 days by default

34  		setUint(keccak256("ProtocolDAO.TotalGGPCirculatingSupply"), 18_000_000 ether);

35  		setUint(keccak256("ProtocolDAO.ClaimingContractPct.ClaimMultisig"), 0.20 ether);

36  		setUint(keccak256("ProtocolDAO.ClaimingContractPct.ClaimNodeOp"), 0.70 ether);

37  		setUint(keccak256("ProtocolDAO.ClaimingContractPct.ClaimProtocolDAO"), 0.10 ether);

40  		setUint(keccak256("ProtocolDAO.InflationIntervalSeconds"), 1 days);

41  		setUint(keccak256("ProtocolDAO.InflationIntervalRate"), 1000133680617113500); // 5% annual calculated on a daily interval - Calculate in js example: let dailyInflation = web3.utils.toBN((1 + 0.05) ** (1 / (365)) * 1e18);

44  		setUint(keccak256("ProtocolDAO.TargetGGAVAXReserveRate"), 0.1 ether); // 10% collateral held in reserve

47  		setUint(keccak256("ProtocolDAO.MinipoolMinAVAXStakingAmt"), 2_000 ether);

48  		setUint(keccak256("ProtocolDAO.MinipoolNodeCommissionFeePct"), 0.15 ether);

49  		setUint(keccak256("ProtocolDAO.MinipoolMaxAVAXAssignment"), 10_000 ether);

50  		setUint(keccak256("ProtocolDAO.MinipoolMinAVAXAssignment"), 1_000 ether);

51  		setUint(keccak256("ProtocolDAO.ExpectedAVAXRewardsRate"), 0.1 ether); // Annual rate as pct of 1 avax

52  		setUint(keccak256("ProtocolDAO.MinipoolCancelMoratoriumSeconds"), 5 days);

55  		setUint(keccak256("ProtocolDAO.MaxCollateralizationRatio"), 1.5 ether);

56  		setUint(keccak256("ProtocolDAO.MinCollateralizationRatio"), 0.1 ether);

82  		return getUint(keccak256("ProtocolDAO.RewardsEligibilityMinSeconds"));

87  		return getUint(keccak256("ProtocolDAO.RewardsCycleSeconds"));

92  		return getUint(keccak256("ProtocolDAO.TotalGGPCirculatingSupply"));

97  		return setUint(keccak256("ProtocolDAO.TotalGGPCirculatingSupply"), amount);

117 		uint256 rate = getUint(keccak256("ProtocolDAO.InflationIntervalRate"));

124 		return getUint(keccak256("ProtocolDAO.InflationIntervalSeconds"));

131 		return getUint(keccak256("ProtocolDAO.MinipoolMinAVAXStakingAmt"));

136 		return getUint(keccak256("ProtocolDAO.MinipoolNodeCommissionFeePct"));

141 		return getUint(keccak256("ProtocolDAO.MinipoolMaxAVAXAssignment"));

146 		return getUint(keccak256("ProtocolDAO.MinipoolMinAVAXAssignment"));

151 		return getUint(keccak256("ProtocolDAO.MinipoolCancelMoratoriumSeconds"));

157 		setUint(keccak256("ProtocolDAO.ExpectedAVAXRewardsRate"), rate);

162 		return getUint(keccak256("ProtocolDAO.ExpectedAVAXRewardsRate"));

169 		return getUint(keccak256("ProtocolDAO.MaxCollateralizationRatio"));

174 		return getUint(keccak256("ProtocolDAO.MinCollateralizationRatio"));

182 		return getUint(keccak256("ProtocolDAO.TargetGGAVAXReserveRate"));
```

```solidity
File: contracts\contract\RewardsPool.sol

35  		if (getBool(keccak256("RewardsPool.initialized"))) {

38  		setBool(keccak256("RewardsPool.initialized"), true);

40  		setUint(keccak256("RewardsPool.RewardsCycleStartTime"), block.timestamp);

41  		setUint(keccak256("RewardsPool.InflationIntervalStartTime"), block.timestamp);

49  		return getUint(keccak256("RewardsPool.InflationIntervalStartTime"));

98  		addUint(keccak256("RewardsPool.InflationIntervalStartTime"), inflationIntervalElapsedSeconds);

99  		setUint(keccak256("RewardsPool.RewardsCycleTotalAmt"), newTokens);

106 		return getUint(keccak256("RewardsPool.RewardsCycleCount"));

111 		addUint(keccak256("RewardsPool.RewardsCycleCount"), 1);

116 		return getUint(keccak256("RewardsPool.RewardsCycleStartTime"));

121 		return getUint(keccak256("RewardsPool.RewardsCycleTotalAmt"));

164 		setUint(keccak256("RewardsPool.RewardsCycleStartTime"), block.timestamp);
```

```solidity
File: contracts\contract\Staking.sol

73  		return getUint(keccak256("staker.count"));

348 			stakerIndex = int256(getUint(keccak256("staker.count")));

349 			addUint(keccak256("staker.count"), 1);
```

```solidity
File: contracts\contract\tokens\upgradeable\ERC20Upgradeable.sol

139 								keccak256("Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)"),

170 					keccak256("EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)"),

172 					keccak256("1"),
```

### [G&#x2011;15]  The result of function calls should be cached rather than re-calling the function
The instances below point to the second+ call of the function within a single function. _Every_ external call made to a contract incurs at least **100 gas** of overhead.

*There are 2 instances of this issue:*

```solidity
File: contracts\contract\ClaimNodeOp.sol

/// @audit rewardsPool.getRewardsCycleCount() on line 61
65  		if (staking.getLastRewardsCycleCompleted(stakerAddr) == rewardsPool.getRewardsCycleCount()) {

/// @audit rewardsPool.getRewardsCycleCount() on line 61
68  		staking.setLastRewardsCycleCompleted(stakerAddr, rewardsPool.getRewardsCycleCount());
```

### [G&#x2011;16]  Don't compare boolean expressions to boolean literals
`if (<x> == true)` => `if (<x>)`, `if (<x> == false)` => `if (!<x>)`.

*There are 3 instances of this issue:*

```solidity
File: contracts\contract\BaseAbstract.sol

25  		if (getBool(keccak256(abi.encodePacked("contract.exists", msg.sender))) == false) {

74  		if (enabled == false) {
```

```solidity
File: contracts\contract\Storage.sol

29  		if (booleanStorage[keccak256(abi.encodePacked("contract.exists", msg.sender))] == false && msg.sender != guardian) {
```

### [G&#x2011;17]  Splitting `require()` statements that use `&&` saves gas
See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper by **3 gas**.

*There is 1 instance of this issue:*

```solidity
File: contracts\contract\tokens\upgradeable\ERC20Upgradeable.sol

154 			require(recoveredAddress != address(0) && recoveredAddress == owner, "INVALID_SIGNER");
```

### [G&#x2011;18]  Stack variable used as a cheaper cache for a state variable is only used once
If the variable is only accessed once, it's cheaper to use the state variable directly that one time, and save the **3 gas** the extra stack assignment would spend.

*There are 3 instances of this issue:*

```solidity
File: contracts\contract\tokens\TokenggAVAX.sol

97  		uint256 stakingTotalAssets_ = stakingTotalAssets;

115 		uint256 totalReleasedAssets_ = totalReleasedAssets;

116 		uint192 lastRewardsAmt_ = lastRewardsAmt;
```
