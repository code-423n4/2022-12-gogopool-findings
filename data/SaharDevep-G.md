# c4udit Report

## Summery

[G01] SPLITTING REQUIRE() STATEMENTS THAT USE && SAVES GAS
[G02] FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED PAYABLE
[G03] USAGE OF UINT/INT SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD
[G04] TIGHT VARIABLE PACKING
[G05] ADD UNCHECKED {} FOR SUBTRACTIONS WHERE THE OPERANDS CANNOT UNDERFLOW
[G06] WRITING ZERO WASTES GAS
[G07] SUPERFLUOUS EVENT FIELDS
[G08] USE SHIFT RIGHT/LEFT INSTEAD OF DIV/MUL IF POSSIBLE
[G09] DON’T COMPARE BOOLEAN EXPRESSIONS TO BOOLEAN LITERALS
[G10] STATE VARIABLES ONLY SET IN THE CONSTRUCTOR SHOULD BE DECLARED IMMUTABLE
[G11] STATE VARIABLES SHOULD BE CACHED IN STACK VARIABLES RATHER THAN RE-READING THEM FROM STORAGE
[G12] EMPTY BLOCKS OF CODE

## Issues found

### [G01] SPLITTING REQUIRE() STATEMENTS THAT USE && SAVES GAS

#### Findings:
```
contracts\contract\tokens\upgradeable\ERC20Upgradeable.sol::154 => require(recoveredAddress != address(0) && recoveredAddress == owner, "INVALID_SIGNER");
```
#### Tools used 
Manual


### [G02] FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED PAYABLE

#### Findings:
```
contracts\contract\MinipoolManager.sol::273 => function cancelMinipool(address nodeID) external nonReentrant {
contracts\contract\MinipoolManager.sol::287 => function withdrawMinipoolFunds(address nodeID) external nonReentrant {
```
#### Tools used 
Manual

### [G03] USAGE OF UINT/INT SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD

#### Findings:
```
contracts\contract\tokens\TokenggAVAX.sol::41 => uint32 public lastSync;
contracts\contract\tokens\TokenggAVAX.sol::44 => uint32 public rewardsCycleLength;
contracts\contract\tokens\TokenggAVAX.sol::47 => uint32 public rewardsCycleEnd;
contracts\contract\tokens\TokenggAVAX.sol::50 => uint192 public lastRewardsAmt;
```
#### Tools used 
Manual


### [G04] TIGHT VARIABLE PACKING

#### Findings:
```
contracts\contract\MinipoolManager.sol::82-104 => struct Minipool {
```

#### Mitigation:

Use This instead:

```
struct Minipool {
    int256 index;
    address nodeID;
    address owner;
    address multisigAddr;
    uint256 status;
    uint256 duration;
    uint256 delegationFee;
    uint256 avaxNodeOpAmt;
    uint256 avaxNodeOpInitialAmt;
    uint256 avaxLiquidStakerAmt;
    uint256 initialStartTime;
    uint256 startTime;
    uint256 endTime;
    uint256 avaxTotalRewardAmt;
    uint256 ggpSlashAmt;
    uint256 avaxNodeOpRewardAmt;
    uint256 avaxLiquidStakerRewardAmt;
    bytes32 txID;
    bytes32 errorCode;
}
```
#### Tools used 
Manual

### [G05] ADD UNCHECKED {} FOR SUBTRACTIONS WHERE THE OPERANDS CANNOT UNDERFLOW

#### Findings:

```
contracts\contract\MinipoolManager.sol::619 => for (uint256 i = offset; i < max; i++) {
contracts\contract\MultisigManager.sol::84 => for (uint256 i = 0; i < total; i++) {
contracts\contract\Ocyticus.sol::61 => for (uint256 i = 0; i < count; i++) {
contracts\contract\RewardsPool.sol::74 => for (uint256 i = 0; i < inflationIntervalsElapsed; i++) {
contracts\contract\RewardsPool.sol::215 => for (uint256 i = 0; i < count; i++) {
contracts\contract\RewardsPool.sol::230 => for (uint256 i = 0; i < enabledMultisigs.length; i++) {
contracts\contract\Staking.sol::428 => for (uint256 i = offset; i < max; i++) {
```
#### Tools used
Manual

### [G06] WRITING ZERO WASTES GAS

#### Findings:
```
contracts\contract\BaseAbstract.sol::101 => if (bytes(contractName).length == 0) {
contracts\contract\ClaimNodeOp.sol::61 => if (rewardsPool.getRewardsCycleCount() == 0) {
contracts\contract\ClaimNodeOp.sol::82 => if (minipoolCount == 0) {
contracts\contract\ClaimNodeOp.sol::92 => if (ggpRewards == 0) {
contracts\contract\ClaimProtocolDAO.sol::28 => if (amount == 0 || amount > vault.balanceOfToken("ClaimProtocolDAO", ggpToken)) {
contracts\contract\MinipoolManager.sol::225 => if (staking.getRewardsStartTime(msg.sender) == 0) {
contracts\contract\MinipoolManager.sol::336 => if (initialStartTime == 0) {
contracts\contract\MinipoolManager.sol::424 => if (avaxTotalRewardAmt == 0) {
contracts\contract\MinipoolManager.sol::461 => if (staking.getRewardsStartTime(mp.owner) == 0) {
contracts\contract\MinipoolManager.sol::614 => if (max > totalMinipools || limit == 0) {
```
#### Tools used
Manual

### [G07] SUPERFLUOUS EVENT FIELDS

### Impact 
For example, block.timestamp and block.number are added to event information by default so adding them manually wastes gas.

#### Findings:
```
contracts\contract\Oracle.sol::21 => event GGPPriceUpdated(uint256 indexed price, uint256 timestamp);
```

#### Tools used
Manual


### [G08] USE SHIFT RIGHT/LEFT INSTEAD OF DIV/MUL IF POSSIBLE

#### Impact
Issue Information:(https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md/#g008---use-shift-rightleft-instead-of-divisionmultiplication-if-possible)

#### Findings:
```
contracts\contract\MinipoolManager.sol::413 => uint256 avaxHalfRewards = avaxTotalRewardAmt / 2;
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)


### [G09] DON’T COMPARE BOOLEAN EXPRESSIONS TO BOOLEAN LITERALS

#### Findings:
```
contracts\contract\BaseAbstract.sol:: 25 => if (getBool(keccak256(abi.encodePacked("contract.exists", msg.sender))) == false) {
contracts\contract\BaseAbstract.sol:: 74 => if (enabled == false) {
contracts\contract\Storage.sol:: 29 => if (booleanStorage[keccak256(abi.encodePacked("contract.exists", msg.sender))] == false && msg.sender != guardian) {
contracts\contract\BaseAbstract.sol:: 74 => if (enabled == false) {
```

#### Mitigation
use if(!enabled) instead

#### Tools used 
Manual

### [G10] STATE VARIABLES ONLY SET IN THE CONSTRUCTOR SHOULD BE DECLARED IMMUTABLE

#### Findings:
```
contracts\contract\Storage.sol:: 24 => address private guardian;
```

#### Tools used 
Manual

### [G11] STATE VARIABLES SHOULD BE CACHED IN STACK VARIABLES RATHER THAN RE-READING THEM FROM STORAGE

#### Findings:
```
contracts\contract\tokens\TokenggAVAX.sol:: 102 => uint32 nextRewardsCycleEnd = ((timestamp + rewardsCycleLength) / rewardsCycleLength) * rewardsCycleLength;
```
#### Tools used 
Manual

### [G12] EMPTY BLOCKS OF CODE

#### Impact
Empty blocks should be removed or emit something

#### Findings:
```
contracts\contract\MinipoolManager.sol::106 => function receiveWithdrawalAVAX() external payable {}
contracts\contract\tokens\TokenggAVAX.sol::255 => function _authorizeUpgrade(address newImplementation) internal override onlyGuardian {}
contracts\contract\tokens\upgradeable\ERC4626Upgradeable.sol::176 => function beforeWithdraw(uint256 assets, uint256 shares) internal virtual {}
contracts\contract\tokens\upgradeable\ERC4626Upgradeable.sol::178 => function afterDeposit(uint256 assets, uint256 shares) internal virtual {}
```
#### Tools used 
Manual

