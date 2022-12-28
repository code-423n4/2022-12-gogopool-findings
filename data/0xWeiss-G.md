### 1 VARIABLE PACKING IN LINES: 

#### Impact
Issue Information: [G001]( https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L46-L53)

#### Findings:
Instead of instanciating the units as 256, find the bits that can work for the length of your variables and pack them.
Like this:
struct Staker {
		address stakerAddr;
		uint16 ggpStaked;
		uint16 avaxStaked;
		uint32 avaxAssigned;
		uint32 avaxAssignedHighWater;
		uint8 minipoolCount;
		uint8 rewardsStartTime;
		uint16 ggpRewards;
		uint16 lastRewardsCycleCompleted;
	}
As said, not sure if the bits are correct for every variable, but there can be room for improvement

### 2 INCREMENT AND DECREMENT OF VARIABLES CAN BE OPTIMIZED: 

#### Impact
Issue Information: [G002]( https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L149)

#### Findings:
gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L149 AND https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L160 
It is cheaper to write: stakingTotalAssets = stakingTotalAssets  + assets;  than stakingTotalAssets += assets;

The same optimization can be done in lines: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L245 and https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L252




### 3 FOR LOOP GAS OPTIMIZATION:

#### Findings:
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L74
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L215
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L230
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Ocyticus.sol#L61
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L428-L432
It is cheaper to add unchecked when it does not over/underflow
Just like this:

BEFORE:
	for (uint256 i = 0; i < inflationIntervalsElapsed; i++) {
			newTotalSupply = newTotalSupply.mulWadDown(inflationRate);
		}

AFTER:
for (uint256 i = 0; i < inflationIntervalsElapsed; ) {
	newTotalSupply = newTotalSupply.mulWadDown(inflationRate);
    unchecked {   ++ i ; }        
}


### 4 DOBLE OPTIMIZATION USING UNCHEKED AND ++in front 

#### Findings:
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L619-L623 AND IN: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L428-L431

4.1 Use unchecked in the for loop in line: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L619, AND https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L428, the same as issue number 3 instantiated before.
4.2 It is cheaper to use ++total than total++ in line https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L623 AND https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L431




### Don't Initialize Variables with Default Value

#### Impact
All the cases below, it is cheaper not to initialize them to 0. Because their default value is already 0.
Issue Information: [G001](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g001---dont-initialize-variables-with-default-value)

#### Findings:
```
examples/contracts/contract/MinipoolManager.sol::618 => uint256 total = 0;
examples/contracts/contract/MultisigManager.sol::84 => for (uint256 i = 0; i < total; i++) {
examples/contracts/contract/Ocyticus.sol::61 => for (uint256 i = 0; i < count; i++) {
examples/contracts/contract/RewardsPool.sol::74 => for (uint256 i = 0; i < inflationIntervalsElapsed; i++) {
examples/contracts/contract/RewardsPool.sol::141 => uint256 contractRewardsTotal = 0;
examples/contracts/contract/RewardsPool.sol::215 => for (uint256 i = 0; i < count; i++) {
examples/contracts/contract/RewardsPool.sol::230 => for (uint256 i = 0; i < enabledMultisigs.length; i++) {
examples/contracts/contract/Staking.sol::427 => uint256 total = 0;
examples/contracts/contract/utils/Multicall.sol::18 => for (uint256 i = 0; i < calls.length; i++) {
examples/contracts/contract/utils/Multicall3.sol::46 => for (uint256 i = 0; i < length; ) {
examples/contracts/contract/utils/Multicall3.sol::66 => for (uint256 i = 0; i < length; ) {
examples/contracts/contract/utils/Multicall3.sol::122 => for (uint256 i = 0; i < length; ) {
examples/contracts/contract/utils/Multicall3.sol::156 => for (uint256 i = 0; i < length; ) {
examples/contracts/contract/utils/RialtoSimulator.sol::90 => uint256 rewards = 0;
examples/contracts/contract/utils/RialtoSimulator.sol::102 => uint256 totalEligibleStakedGGP = 0;
examples/contracts/contract/utils/RialtoSimulator.sol::104 => for (uint256 i = 0; i < allStakers.length; i++) {
examples/contracts/contract/utils/RialtoSimulator.sol::115 => for (uint256 i = 0; i < allStakers.length; i++) {
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Cache Array Length Outside of Loop

#### Impact
Issue Information: [G002](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g002---cache-array-length-outside-of-loop)

#### Findings:
```
examples/contracts/contract/BaseAbstract.sol::101 => if (bytes(contractName).length == 0) {
examples/contracts/contract/MinipoolManager.sol::554 => /// @param duration The length of validation in seconds
examples/contracts/contract/RewardsPool.sol::167 => // This will always 'mint' (release) new tokens if the rewards cycle length requirement is met
examples/contracts/contract/RewardsPool.sol::230 => for (uint256 i = 0; i < enabledMultisigs.length; i++) {
examples/contracts/contract/tokens/TokenggAVAX.sol::43 => /// @notice the maximum length of a rewards cycle
examples/contracts/contract/utils/Multicall.sol::17 => returnData = new bytes[](calls.length);
examples/contracts/contract/utils/Multicall.sol::18 => for (uint256 i = 0; i < calls.length; i++) {
examples/contracts/contract/utils/Multicall3.sol::43 => uint256 length = calls.length;
examples/contracts/contract/utils/Multicall3.sol::44 => returnData = new bytes[](length);
examples/contracts/contract/utils/Multicall3.sol::46 => for (uint256 i = 0; i < length; ) {
examples/contracts/contract/utils/Multicall3.sol::63 => uint256 length = calls.length;
examples/contracts/contract/utils/Multicall3.sol::64 => returnData = new Result[](length);
examples/contracts/contract/utils/Multicall3.sol::66 => for (uint256 i = 0; i < length; ) {
examples/contracts/contract/utils/Multicall3.sol::119 => uint256 length = calls.length;
examples/contracts/contract/utils/Multicall3.sol::120 => returnData = new Result[](length);
examples/contracts/contract/utils/Multicall3.sol::122 => for (uint256 i = 0; i < length; ) {
examples/contracts/contract/utils/Multicall3.sol::134 => // set length of revert string
examples/contracts/contract/utils/Multicall3.sol::153 => uint256 length = calls.length;
examples/contracts/contract/utils/Multicall3.sol::154 => returnData = new Result[](length);
examples/contracts/contract/utils/Multicall3.sol::156 => for (uint256 i = 0; i < length; ) {
examples/contracts/contract/utils/Multicall3.sol::174 => // set length of revert string
examples/contracts/contract/utils/RialtoSimulator.sol::104 => for (uint256 i = 0; i < allStakers.length; i++) {
examples/contracts/contract/utils/RialtoSimulator.sol::115 => for (uint256 i = 0; i < allStakers.length; i++) {
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Use != 0 instead of > 0 for Unsigned Integer Comparison

#### Impact
Issue Information: [G003](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g003---use--0-instead-of--0-for-unsigned-integer-comparison)

#### Findings:
```
examples/contracts/contract/ClaimNodeOp.sol::103 => if (restakeAmt > 0) {
examples/contracts/contract/ClaimNodeOp.sol::109 => if (claimAmt > 0) {
examples/contracts/contract/RewardsPool.sol::142 => if (claimContractPct > 0 && currentCycleRewardsTotal > 0) {
examples/contracts/contract/RewardsPool.sol::152 => return getRewardsCyclesElapsed() > 0 && getInflationIntervalsElapsed() > 0;
examples/contracts/contract/RewardsPool.sol::181 => if (daoClaimContractAllotment > 0) {
examples/contracts/contract/RewardsPool.sol::186 => if (multisigClaimContractAllotment > 0) {
examples/contracts/contract/RewardsPool.sol::191 => if (nopClaimContractAllotment > 0) {
examples/contracts/contract/tokens/TokenggAVAX.sol::59 => if (amt > 0 && getBool(keccak256(abi.encodePacked("contract.paused", "TokenggAVAX")))) {
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Use immutable for OpenZeppelin AccessControl's Roles Declarations

#### Impact
Issue Information: [G006](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g006---use-immutable-for-openzeppelin-accesscontrols-roles-declarations)

#### Findings:
```
examples/contracts/contract/BaseAbstract.sol::25 => if (getBool(keccak256(abi.encodePacked("contract.exists", msg.sender))) == false) {
examples/contracts/contract/BaseAbstract.sol::33 => if (contractAddress != getAddress(keccak256(abi.encodePacked("contract.address", contractName)))) {
examples/contracts/contract/BaseAbstract.sol::41 => bool isContract = getBool(keccak256(abi.encodePacked("contract.exists", msg.sender)));
examples/contracts/contract/BaseAbstract.sol::52 => bool isContract = contractAddress == getAddress(keccak256(abi.encodePacked("contract.address", contractName)));
examples/contracts/contract/BaseAbstract.sol::71 => int256 multisigIndex = int256(getUint(keccak256(abi.encodePacked("multisig.index", msg.sender)))) - 1;
examples/contracts/contract/BaseAbstract.sol::72 => address addr = getAddress(keccak256(abi.encodePacked("multisig.item", multisigIndex, ".address")));
examples/contracts/contract/BaseAbstract.sol::73 => bool enabled = (addr != address(0)) && getBool(keccak256(abi.encodePacked("multisig.item", multisigIndex, ".enabled")));
examples/contracts/contract/BaseAbstract.sol::83 => if (getBool(keccak256(abi.encodePacked("contract.paused", contractName)))) {
examples/contracts/contract/BaseAbstract.sol::91 => address contractAddress = getAddress(keccak256(abi.encodePacked("contract.address", contractName)));
examples/contracts/contract/BaseAbstract.sol::100 => string memory contractName = getString(keccak256(abi.encodePacked("contract.name", contractAddress)));
examples/contracts/contract/ClaimNodeOp.sol::36 => return getUint(keccak256("NOPClaim.RewardsCycleTotal"));
examples/contracts/contract/ClaimNodeOp.sol::41 => setUint(keccak256("NOPClaim.RewardsCycleTotal"), amount);
examples/contracts/contract/MinipoolManager.sol::116 => address owner = getAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".owner")));
examples/contracts/contract/MinipoolManager.sol::130 => address assignedMultisig = getAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".multisigAddr")));
examples/contracts/contract/MinipoolManager.sol::153 => bytes32 statusKey = keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status"));
examples/contracts/contract/MinipoolManager.sol::246 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".initialStartTime")), 0);
examples/contracts/contract/MinipoolManager.sol::248 => minipoolIndex = int256(getUint(keccak256("minipool.count")));
examples/contracts/contract/MinipoolManager.sol::250 => setUint(keccak256(abi.encodePacked("minipool.index", nodeID)), uint256(minipoolIndex + 1));
examples/contracts/contract/MinipoolManager.sol::251 => setAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".nodeID")), nodeID);
examples/contracts/contract/MinipoolManager.sol::252 => addUint(keccak256("minipool.count"), 1);
examples/contracts/contract/MinipoolManager.sol::256 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Prelaunch));
examples/contracts/contract/MinipoolManager.sol::257 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".duration")), duration);
examples/contracts/contract/MinipoolManager.sol::258 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".delegationFee")), delegationFee);
examples/contracts/contract/MinipoolManager.sol::259 => setAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".owner")), msg.sender);
examples/contracts/contract/MinipoolManager.sol::260 => setAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".multisigAddr")), multisig);
examples/contracts/contract/MinipoolManager.sol::261 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpInitialAmt")), msg.value);
examples/contracts/contract/MinipoolManager.sol::262 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpAmt")), msg.value);
examples/contracts/contract/MinipoolManager.sol::263 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")), avaxAssignmentRequest);
examples/contracts/contract/MinipoolManager.sol::291 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Finished));
examples/contracts/contract/MinipoolManager.sol::293 => uint256 avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpAmt")));
examples/contracts/contract/MinipoolManager.sol::294 => uint256 avaxNodeOpRewardAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpRewardAmt")));
examples/contracts/contract/MinipoolManager.sol::317 => uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));
examples/contracts/contract/MinipoolManager.sol::328 => uint256 avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpAmt")));
examples/contracts/contract/MinipoolManager.sol::329 => uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));
examples/contracts/contract/MinipoolManager.sol::333 => addUint(keccak256("MinipoolManager.TotalAVAXLiquidStakerAmt"), avaxLiquidStakerAmt);
examples/contracts/contract/MinipoolManager.sol::335 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Launched));
examples/contracts/contract/MinipoolManager.sol::360 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Staking));
examples/contracts/contract/MinipoolManager.sol::361 => setBytes32(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".txID")), txID);
examples/contracts/contract/MinipoolManager.sol::362 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".startTime")), startTime);
examples/contracts/contract/MinipoolManager.sol::365 => uint256 initialStartTime = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".initialStartTime")));
examples/contracts/contract/MinipoolManager.sol::367 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".initialStartTime")), startTime);
examples/contracts/contract/MinipoolManager.sol::370 => address owner = getAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".owner")));
examples/contracts/contract/MinipoolManager.sol::371 => uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));
examples/contracts/contract/MinipoolManager.sol::393 => uint256 startTime = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".startTime")));
examples/contracts/contract/MinipoolManager.sol::398 => uint256 avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpAmt")));
examples/contracts/contract/MinipoolManager.sol::399 => uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));
examples/contracts/contract/MinipoolManager.sol::405 => address owner = getAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".owner")));
examples/contracts/contract/MinipoolManager.sol::407 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Withdrawable));
examples/contracts/contract/MinipoolManager.sol::408 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".endTime")), endTime);
examples/contracts/contract/MinipoolManager.sol::409 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxTotalRewardAmt")), avaxTotalRewardAmt);
examples/contracts/contract/MinipoolManager.sol::420 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpRewardAmt")), avaxNodeOpRewardAmt);
examples/contracts/contract/MinipoolManager.sol::421 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerRewardAmt")), avaxLiquidStakerRewardAmt);
examples/contracts/contract/MinipoolManager.sol::433 => subUint(keccak256("MinipoolManager.TotalAVAXLiquidStakerAmt"), avaxLiquidStakerAmt);
examples/contracts/contract/MinipoolManager.sol::451 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpAmt")), compoundedAvaxNodeOpAmt);
examples/contracts/contract/MinipoolManager.sol::452 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")), compoundedAvaxNodeOpAmt);
examples/contracts/contract/MinipoolManager.sol::475 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Prelaunch));
examples/contracts/contract/MinipoolManager.sol::488 => address owner = getAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".owner")));
examples/contracts/contract/MinipoolManager.sol::489 => uint256 avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpAmt")));
examples/contracts/contract/MinipoolManager.sol::490 => uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));
examples/contracts/contract/MinipoolManager.sol::496 => setBytes32(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".errorCode")), errorCode);
examples/contracts/contract/MinipoolManager.sol::497 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Error));
examples/contracts/contract/MinipoolManager.sol::498 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxTotalRewardAmt")), 0);
examples/contracts/contract/MinipoolManager.sol::499 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpRewardAmt")), 0);
examples/contracts/contract/MinipoolManager.sol::500 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerRewardAmt")), 0);
examples/contracts/contract/MinipoolManager.sol::512 => subUint(keccak256("MinipoolManager.TotalAVAXLiquidStakerAmt"), avaxLiquidStakerAmt);
examples/contracts/contract/MinipoolManager.sol::522 => setBytes32(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".errorCode")), errorCode);
examples/contracts/contract/MinipoolManager.sol::531 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Finished));
examples/contracts/contract/MinipoolManager.sol::542 => return getUint(keccak256("MinipoolManager.TotalAVAXLiquidStakerAmt"));
examples/contracts/contract/MinipoolManager.sol::567 => return int256(getUint(keccak256(abi.encodePacked("minipool.index", nodeID)))) - 1;
examples/contracts/contract/MinipoolManager.sol::582 => mp.nodeID = getAddress(keccak256(abi.encodePacked("minipool.item", index, ".nodeID")));
examples/contracts/contract/MinipoolManager.sol::583 => mp.status = getUint(keccak256(abi.encodePacked("minipool.item", index, ".status")));
examples/contracts/contract/MinipoolManager.sol::584 => mp.duration = getUint(keccak256(abi.encodePacked("minipool.item", index, ".duration")));
examples/contracts/contract/MinipoolManager.sol::585 => mp.delegationFee = getUint(keccak256(abi.encodePacked("minipool.item", index, ".delegationFee")));
examples/contracts/contract/MinipoolManager.sol::586 => mp.owner = getAddress(keccak256(abi.encodePacked("minipool.item", index, ".owner")));
examples/contracts/contract/MinipoolManager.sol::587 => mp.multisigAddr = getAddress(keccak256(abi.encodePacked("minipool.item", index, ".multisigAddr")));
examples/contracts/contract/MinipoolManager.sol::588 => mp.avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxNodeOpAmt")));
examples/contracts/contract/MinipoolManager.sol::589 => mp.avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxLiquidStakerAmt")));
examples/contracts/contract/MinipoolManager.sol::590 => mp.txID = getBytes32(keccak256(abi.encodePacked("minipool.item", index, ".txID")));
examples/contracts/contract/MinipoolManager.sol::591 => mp.initialStartTime = getUint(keccak256(abi.encodePacked("minipool.item", index, ".initialStartTime")));
examples/contracts/contract/MinipoolManager.sol::592 => mp.startTime = getUint(keccak256(abi.encodePacked("minipool.item", index, ".startTime")));
examples/contracts/contract/MinipoolManager.sol::593 => mp.endTime = getUint(keccak256(abi.encodePacked("minipool.item", index, ".endTime")));
examples/contracts/contract/MinipoolManager.sol::594 => mp.avaxTotalRewardAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxTotalRewardAmt")));
examples/contracts/contract/MinipoolManager.sol::595 => mp.errorCode = getBytes32(keccak256(abi.encodePacked("minipool.item", index, ".errorCode")));
examples/contracts/contract/MinipoolManager.sol::596 => mp.avaxNodeOpInitialAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxNodeOpInitialAmt")));
examples/contracts/contract/MinipoolManager.sol::597 => mp.avaxNodeOpRewardAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxNodeOpRewardAmt")));
examples/contracts/contract/MinipoolManager.sol::598 => mp.avaxLiquidStakerRewardAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxLiquidStakerRewardAmt")));
examples/contracts/contract/MinipoolManager.sol::599 => mp.ggpSlashAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".ggpSlashAmt")));
examples/contracts/contract/MinipoolManager.sol::612 => uint256 totalMinipools = getUint(keccak256("minipool.count"));
examples/contracts/contract/MinipoolManager.sol::635 => return getUint(keccak256("minipool.count"));
examples/contracts/contract/MinipoolManager.sol::648 => setUint(keccak256(abi.encodePacked("minipool.item", index, ".status")), uint256(MinipoolStatus.Canceled));
examples/contracts/contract/MinipoolManager.sol::650 => address owner = getAddress(keccak256(abi.encodePacked("minipool.item", index, ".owner")));
examples/contracts/contract/MinipoolManager.sol::651 => uint256 avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxNodeOpAmt")));
examples/contracts/contract/MinipoolManager.sol::652 => uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxLiquidStakerAmt")));
examples/contracts/contract/MinipoolManager.sol::671 => address nodeID = getAddress(keccak256(abi.encodePacked("minipool.item", index, ".nodeID")));
examples/contracts/contract/MinipoolManager.sol::672 => address owner = getAddress(keccak256(abi.encodePacked("minipool.item", index, ".owner")));
examples/contracts/contract/MinipoolManager.sol::673 => uint256 duration = getUint(keccak256(abi.encodePacked("minipool.item", index, ".duration")));
examples/contracts/contract/MinipoolManager.sol::674 => uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxLiquidStakerAmt")));
examples/contracts/contract/MinipoolManager.sol::677 => setUint(keccak256(abi.encodePacked("minipool.item", index, ".ggpSlashAmt")), slashGGPAmt);
examples/contracts/contract/MinipoolManager.sol::688 => setBytes32(keccak256(abi.encodePacked("minipool.item", index, ".txID")), 0);
examples/contracts/contract/MinipoolManager.sol::689 => setUint(keccak256(abi.encodePacked("minipool.item", index, ".startTime")), 0);
examples/contracts/contract/MinipoolManager.sol::690 => setUint(keccak256(abi.encodePacked("minipool.item", index, ".endTime")), 0);
examples/contracts/contract/MinipoolManager.sol::691 => setUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxTotalRewardAmt")), 0);
examples/contracts/contract/MinipoolManager.sol::692 => setUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxNodeOpRewardAmt")), 0);
examples/contracts/contract/MinipoolManager.sol::693 => setUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxLiquidStakerRewardAmt")), 0);
examples/contracts/contract/MinipoolManager.sol::694 => setUint(keccak256(abi.encodePacked("minipool.item", index, ".ggpSlashAmt")), 0);
examples/contracts/contract/MinipoolManager.sol::695 => setBytes32(keccak256(abi.encodePacked("minipool.item", index, ".errorCode")), 0);
examples/contracts/contract/MultisigManager.sol::40 => uint256 index = getUint(keccak256("multisig.count"));
examples/contracts/contract/MultisigManager.sol::45 => setAddress(keccak256(abi.encodePacked("multisig.item", index, ".address")), addr);
examples/contracts/contract/MultisigManager.sol::48 => setUint(keccak256(abi.encodePacked("multisig.index", addr)), index + 1);
examples/contracts/contract/MultisigManager.sol::49 => addUint(keccak256("multisig.count"), 1);
examples/contracts/contract/MultisigManager.sol::61 => setBool(keccak256(abi.encodePacked("multisig.item", multisigIndex, ".enabled")), true);
examples/contracts/contract/MultisigManager.sol::74 => setBool(keccak256(abi.encodePacked("multisig.item", multisigIndex, ".enabled")), false);
examples/contracts/contract/MultisigManager.sol::81 => uint256 total = getUint(keccak256("multisig.count"));
examples/contracts/contract/MultisigManager.sol::97 => return int256(getUint(keccak256(abi.encodePacked("multisig.index", addr)))) - 1;
examples/contracts/contract/MultisigManager.sol::103 => return getUint(keccak256("multisig.count"));
examples/contracts/contract/MultisigManager.sol::110 => addr = getAddress(keccak256(abi.encodePacked("multisig.item", index, ".address")));
examples/contracts/contract/MultisigManager.sol::111 => enabled = (addr != address(0)) && getBool(keccak256(abi.encodePacked("multisig.item", index, ".enabled")));
examples/contracts/contract/Oracle.sol::29 => setAddress(keccak256("Oracle.OneInch"), addr);
examples/contracts/contract/Oracle.sol::38 => IOneInch oneinch = IOneInch(getAddress(keccak256("Oracle.OneInch")));
examples/contracts/contract/Oracle.sol::47 => price = getUint(keccak256("Oracle.GGPPriceInAVAX"));
examples/contracts/contract/Oracle.sol::51 => timestamp = getUint(keccak256("Oracle.GGPTimestamp"));
examples/contracts/contract/Oracle.sol::58 => uint256 lastTimestamp = getUint(keccak256("Oracle.GGPTimestamp"));
examples/contracts/contract/Oracle.sol::65 => setUint(keccak256("Oracle.GGPPriceInAVAX"), price);
examples/contracts/contract/Oracle.sol::66 => setUint(keccak256("Oracle.GGPTimestamp"), timestamp);
examples/contracts/contract/ProtocolDAO.sol::24 => if (getBool(keccak256("ProtocolDAO.initialized"))) {
examples/contracts/contract/ProtocolDAO.sol::27 => setBool(keccak256("ProtocolDAO.initialized"), true);
examples/contracts/contract/ProtocolDAO.sol::30 => setUint(keccak256("ProtocolDAO.RewardsEligibilityMinSeconds"), 14 days);
examples/contracts/contract/ProtocolDAO.sol::33 => setUint(keccak256("ProtocolDAO.RewardsCycleSeconds"), 28 days); // The time in which a claim period will span in seconds - 28 days by default
examples/contracts/contract/ProtocolDAO.sol::34 => setUint(keccak256("ProtocolDAO.TotalGGPCirculatingSupply"), 18_000_000 ether);
examples/contracts/contract/ProtocolDAO.sol::35 => setUint(keccak256("ProtocolDAO.ClaimingContractPct.ClaimMultisig"), 0.20 ether);
examples/contracts/contract/ProtocolDAO.sol::36 => setUint(keccak256("ProtocolDAO.ClaimingContractPct.ClaimNodeOp"), 0.70 ether);
examples/contracts/contract/ProtocolDAO.sol::37 => setUint(keccak256("ProtocolDAO.ClaimingContractPct.ClaimProtocolDAO"), 0.10 ether);
examples/contracts/contract/ProtocolDAO.sol::40 => setUint(keccak256("ProtocolDAO.InflationIntervalSeconds"), 1 days);
examples/contracts/contract/ProtocolDAO.sol::41 => setUint(keccak256("ProtocolDAO.InflationIntervalRate"), 1000133680617113500); // 5% annual calculated on a daily interval - Calculate in js example: let dailyInflation = web3.utils.toBN((1 + 0.05) ** (1 / (365)) * 1e18);
examples/contracts/contract/ProtocolDAO.sol::44 => setUint(keccak256("ProtocolDAO.TargetGGAVAXReserveRate"), 0.1 ether); // 10% collateral held in reserve
examples/contracts/contract/ProtocolDAO.sol::47 => setUint(keccak256("ProtocolDAO.MinipoolMinAVAXStakingAmt"), 2_000 ether);
examples/contracts/contract/ProtocolDAO.sol::48 => setUint(keccak256("ProtocolDAO.MinipoolNodeCommissionFeePct"), 0.15 ether);
examples/contracts/contract/ProtocolDAO.sol::49 => setUint(keccak256("ProtocolDAO.MinipoolMaxAVAXAssignment"), 10_000 ether);
examples/contracts/contract/ProtocolDAO.sol::50 => setUint(keccak256("ProtocolDAO.MinipoolMinAVAXAssignment"), 1_000 ether);
examples/contracts/contract/ProtocolDAO.sol::51 => setUint(keccak256("ProtocolDAO.ExpectedAVAXRewardsRate"), 0.1 ether); // Annual rate as pct of 1 avax
examples/contracts/contract/ProtocolDAO.sol::52 => setUint(keccak256("ProtocolDAO.MinipoolCancelMoratoriumSeconds"), 5 days);
examples/contracts/contract/ProtocolDAO.sol::55 => setUint(keccak256("ProtocolDAO.MaxCollateralizationRatio"), 1.5 ether);
examples/contracts/contract/ProtocolDAO.sol::56 => setUint(keccak256("ProtocolDAO.MinCollateralizationRatio"), 0.1 ether);
examples/contracts/contract/ProtocolDAO.sol::62 => return getBool(keccak256(abi.encodePacked("contract.paused", contractName)));
examples/contracts/contract/ProtocolDAO.sol::68 => setBool(keccak256(abi.encodePacked("contract.paused", contractName)), true);
examples/contracts/contract/ProtocolDAO.sol::74 => setBool(keccak256(abi.encodePacked("contract.paused", contractName)), false);
examples/contracts/contract/ProtocolDAO.sol::82 => return getUint(keccak256("ProtocolDAO.RewardsEligibilityMinSeconds"));
examples/contracts/contract/ProtocolDAO.sol::87 => return getUint(keccak256("ProtocolDAO.RewardsCycleSeconds"));
examples/contracts/contract/ProtocolDAO.sol::92 => return getUint(keccak256("ProtocolDAO.TotalGGPCirculatingSupply"));
examples/contracts/contract/ProtocolDAO.sol::97 => return setUint(keccak256("ProtocolDAO.TotalGGPCirculatingSupply"), amount);
examples/contracts/contract/ProtocolDAO.sol::103 => return getUint(keccak256(abi.encodePacked("ProtocolDAO.ClaimingContractPct.", claimingContract)));
examples/contracts/contract/ProtocolDAO.sol::108 => setUint(keccak256(abi.encodePacked("ProtocolDAO.ClaimingContractPct.", claimingContract)), decimal);
examples/contracts/contract/ProtocolDAO.sol::117 => uint256 rate = getUint(keccak256("ProtocolDAO.InflationIntervalRate"));
examples/contracts/contract/ProtocolDAO.sol::124 => return getUint(keccak256("ProtocolDAO.InflationIntervalSeconds"));
examples/contracts/contract/ProtocolDAO.sol::131 => return getUint(keccak256("ProtocolDAO.MinipoolMinAVAXStakingAmt"));
examples/contracts/contract/ProtocolDAO.sol::136 => return getUint(keccak256("ProtocolDAO.MinipoolNodeCommissionFeePct"));
examples/contracts/contract/ProtocolDAO.sol::141 => return getUint(keccak256("ProtocolDAO.MinipoolMaxAVAXAssignment"));
examples/contracts/contract/ProtocolDAO.sol::146 => return getUint(keccak256("ProtocolDAO.MinipoolMinAVAXAssignment"));
examples/contracts/contract/ProtocolDAO.sol::151 => return getUint(keccak256("ProtocolDAO.MinipoolCancelMoratoriumSeconds"));
examples/contracts/contract/ProtocolDAO.sol::157 => setUint(keccak256("ProtocolDAO.ExpectedAVAXRewardsRate"), rate);
examples/contracts/contract/ProtocolDAO.sol::162 => return getUint(keccak256("ProtocolDAO.ExpectedAVAXRewardsRate"));
examples/contracts/contract/ProtocolDAO.sol::169 => return getUint(keccak256("ProtocolDAO.MaxCollateralizationRatio"));
examples/contracts/contract/ProtocolDAO.sol::174 => return getUint(keccak256("ProtocolDAO.MinCollateralizationRatio"));
examples/contracts/contract/ProtocolDAO.sol::182 => return getUint(keccak256("ProtocolDAO.TargetGGAVAXReserveRate"));
examples/contracts/contract/ProtocolDAO.sol::191 => setBool(keccak256(abi.encodePacked("contract.exists", addr)), true);
examples/contracts/contract/ProtocolDAO.sol::192 => setAddress(keccak256(abi.encodePacked("contract.address", name)), addr);
examples/contracts/contract/ProtocolDAO.sol::193 => setString(keccak256(abi.encodePacked("contract.name", addr)), name);
examples/contracts/contract/ProtocolDAO.sol::200 => deleteBool(keccak256(abi.encodePacked("contract.exists", addr)));
examples/contracts/contract/ProtocolDAO.sol::201 => deleteAddress(keccak256(abi.encodePacked("contract.address", name)));
examples/contracts/contract/ProtocolDAO.sol::202 => deleteString(keccak256(abi.encodePacked("contract.name", addr)));
examples/contracts/contract/RewardsPool.sol::35 => if (getBool(keccak256("RewardsPool.initialized"))) {
examples/contracts/contract/RewardsPool.sol::38 => setBool(keccak256("RewardsPool.initialized"), true);
examples/contracts/contract/RewardsPool.sol::40 => setUint(keccak256("RewardsPool.RewardsCycleStartTime"), block.timestamp);
examples/contracts/contract/RewardsPool.sol::41 => setUint(keccak256("RewardsPool.InflationIntervalStartTime"), block.timestamp);
examples/contracts/contract/RewardsPool.sol::49 => return getUint(keccak256("RewardsPool.InflationIntervalStartTime"));
examples/contracts/contract/RewardsPool.sol::98 => addUint(keccak256("RewardsPool.InflationIntervalStartTime"), inflationIntervalElapsedSeconds);
examples/contracts/contract/RewardsPool.sol::99 => setUint(keccak256("RewardsPool.RewardsCycleTotalAmt"), newTokens);
examples/contracts/contract/RewardsPool.sol::106 => return getUint(keccak256("RewardsPool.RewardsCycleCount"));
examples/contracts/contract/RewardsPool.sol::111 => addUint(keccak256("RewardsPool.RewardsCycleCount"), 1);
examples/contracts/contract/RewardsPool.sol::116 => return getUint(keccak256("RewardsPool.RewardsCycleStartTime"));
examples/contracts/contract/RewardsPool.sol::121 => return getUint(keccak256("RewardsPool.RewardsCycleTotalAmt"));
examples/contracts/contract/RewardsPool.sol::164 => setUint(keccak256("RewardsPool.RewardsCycleStartTime"), block.timestamp);
examples/contracts/contract/Staking.sol::73 => return getUint(keccak256("staker.count"));
examples/contracts/contract/Staking.sol::82 => return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpStaked")));
examples/contracts/contract/Staking.sol::89 => addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpStaked")), amount);
examples/contracts/contract/Staking.sol::96 => subUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpStaked")), amount);
examples/contracts/contract/Staking.sol::105 => return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxStaked")));
examples/contracts/contract/Staking.sol::112 => addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxStaked")), amount);
examples/contracts/contract/Staking.sol::119 => subUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxStaked")), amount);
examples/contracts/contract/Staking.sol::128 => return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssigned")));
examples/contracts/contract/Staking.sol::135 => addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssigned")), amount);
examples/contracts/contract/Staking.sol::142 => subUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssigned")), amount);
examples/contracts/contract/Staking.sol::151 => return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssignedHighWater")));
examples/contracts/contract/Staking.sol::158 => addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssignedHighWater")), amount);
examples/contracts/contract/Staking.sol::165 => uint256 currAVAXAssigned = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssigned")));
examples/contracts/contract/Staking.sol::166 => setUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssignedHighWater")), currAVAXAssigned);
examples/contracts/contract/Staking.sol::175 => return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".minipoolCount")));
examples/contracts/contract/Staking.sol::182 => addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".minipoolCount")), 1);
examples/contracts/contract/Staking.sol::189 => subUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".minipoolCount")), 1);
examples/contracts/contract/Staking.sol::198 => return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".rewardsStartTime")));
examples/contracts/contract/Staking.sol::210 => setUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".rewardsStartTime")), time);
examples/contracts/contract/Staking.sol::219 => return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpRewards")));
examples/contracts/contract/Staking.sol::226 => addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpRewards")), amount);
examples/contracts/contract/Staking.sol::233 => subUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpRewards")), amount);
examples/contracts/contract/Staking.sol::242 => return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".lastRewardsCycleCompleted")));
examples/contracts/contract/Staking.sol::250 => setUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".lastRewardsCycleCompleted")), cycleNumber);
examples/contracts/contract/Staking.sol::348 => stakerIndex = int256(getUint(keccak256("staker.count")));
examples/contracts/contract/Staking.sol::349 => addUint(keccak256("staker.count"), 1);
examples/contracts/contract/Staking.sol::350 => setUint(keccak256(abi.encodePacked("staker.index", stakerAddr)), uint256(stakerIndex + 1));
examples/contracts/contract/Staking.sol::351 => setAddress(keccak256(abi.encodePacked("staker.item", stakerIndex, ".stakerAddr")), stakerAddr);
examples/contracts/contract/Staking.sol::399 => return int256(getUint(keccak256(abi.encodePacked("staker.index", stakerAddr)))) - 1;
examples/contracts/contract/Staking.sol::406 => staker.ggpStaked = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpStaked")));
examples/contracts/contract/Staking.sol::407 => staker.avaxAssigned = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssigned")));
examples/contracts/contract/Staking.sol::408 => staker.avaxStaked = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxStaked")));
examples/contracts/contract/Staking.sol::409 => staker.stakerAddr = getAddress(keccak256(abi.encodePacked("staker.item", stakerIndex, ".stakerAddr")));
examples/contracts/contract/Staking.sol::410 => staker.minipoolCount = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".minipoolCount")));
examples/contracts/contract/Staking.sol::411 => staker.rewardsStartTime = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".rewardsStartTime")));
examples/contracts/contract/Staking.sol::412 => staker.ggpRewards = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpRewards")));
examples/contracts/contract/Staking.sol::413 => staker.lastRewardsCycleCompleted = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".lastRewardsCycleCompleted")));
examples/contracts/contract/Storage.sol::29 => if (booleanStorage[keccak256(abi.encodePacked("contract.exists", msg.sender))] == false && msg.sender != guardian) {
examples/contracts/contract/Vault.sol::124 => bytes32 contractKey = keccak256(abi.encodePacked(networkContractName, address(tokenContract)));
examples/contracts/contract/Vault.sol::147 => bytes32 contractKey = keccak256(abi.encodePacked(getContractName(msg.sender), tokenAddress));
examples/contracts/contract/Vault.sol::178 => bytes32 contractKeyFrom = keccak256(abi.encodePacked(getContractName(msg.sender), tokenAddress));
examples/contracts/contract/Vault.sol::179 => bytes32 contractKeyTo = keccak256(abi.encodePacked(networkContractName, tokenAddress));
examples/contracts/contract/Vault.sol::201 => return tokenBalances[keccak256(abi.encodePacked(networkContractName, tokenAddress))];
examples/contracts/contract/tokens/TokenggAVAX.sol::59 => if (amt > 0 && getBool(keccak256(abi.encodePacked("contract.paused", "TokenggAVAX")))) {
examples/contracts/contract/tokens/TokenggAVAX.sol::207 => if (getBool(keccak256(abi.encodePacked("contract.paused", "TokenggAVAX")))) {
examples/contracts/contract/tokens/TokenggAVAX.sol::217 => if (getBool(keccak256(abi.encodePacked("contract.paused", "TokenggAVAX")))) {
examples/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol::133 => keccak256(
examples/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol::137 => keccak256(
examples/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol::139 => keccak256("Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)"),
examples/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol::168 => keccak256(
examples/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol::170 => keccak256("EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)"),
examples/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol::171 => keccak256(bytes(name)),
examples/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol::172 => keccak256("1"),
examples/contracts/contract/utils/Multicall3.sol::130 => // set "Error(string)" signature: bytes32(bytes4(keccak256("Error(string)")))
examples/contracts/contract/utils/Multicall3.sol::170 => // set "Error(string)" signature: bytes32(bytes4(keccak256("Error(string)")))
examples/contracts/contract/utils/RialtoSimulator.sol::69 => bytes32 txID = keccak256(abi.encodePacked(nodeID, blockhash(block.timestamp)));
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Long Revert Strings

#### Impact
Issue Information: [G007](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g007---long-revert-strings)

#### Findings:
```
examples/contracts/contract/BaseAbstract.sol::72 => address addr = getAddress(keccak256(abi.encodePacked("multisig.item", multisigIndex, ".address")));
examples/contracts/contract/BaseAbstract.sol::73 => bool enabled = (addr != address(0)) && getBool(keccak256(abi.encodePacked("multisig.item", multisigIndex, ".enabled")));
examples/contracts/contract/BaseUpgradeable.sol::7 => import {Initializable} from "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
examples/contracts/contract/ClaimNodeOp.sol::13 => import {ERC20} from "@rari-capital/solmate/src/tokens/ERC20.sol";
examples/contracts/contract/ClaimNodeOp.sol::14 => import {FixedPointMathLib} from "@rari-capital/solmate/src/utils/FixedPointMathLib.sol";
examples/contracts/contract/MinipoolManager.sol::16 => import {ERC20} from "@rari-capital/solmate/src/mixins/ERC4626.sol";
examples/contracts/contract/MinipoolManager.sol::17 => import {FixedPointMathLib} from "@rari-capital/solmate/src/utils/FixedPointMathLib.sol";
examples/contracts/contract/MinipoolManager.sol::18 => import {ReentrancyGuard} from "@rari-capital/solmate/src/utils/ReentrancyGuard.sol";
examples/contracts/contract/MinipoolManager.sol::19 => import {SafeTransferLib} from "@rari-capital/solmate/src/utils/SafeTransferLib.sol";
examples/contracts/contract/MinipoolManager.sol::116 => address owner = getAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".owner")));
examples/contracts/contract/MinipoolManager.sol::130 => address assignedMultisig = getAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".multisigAddr")));
examples/contracts/contract/MinipoolManager.sol::153 => bytes32 statusKey = keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status"));
examples/contracts/contract/MinipoolManager.sol::246 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".initialStartTime")), 0);
examples/contracts/contract/MinipoolManager.sol::251 => setAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".nodeID")), nodeID);
examples/contracts/contract/MinipoolManager.sol::256 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Prelaunch));
examples/contracts/contract/MinipoolManager.sol::257 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".duration")), duration);
examples/contracts/contract/MinipoolManager.sol::258 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".delegationFee")), delegationFee);
examples/contracts/contract/MinipoolManager.sol::259 => setAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".owner")), msg.sender);
examples/contracts/contract/MinipoolManager.sol::260 => setAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".multisigAddr")), multisig);
examples/contracts/contract/MinipoolManager.sol::261 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpInitialAmt")), msg.value);
examples/contracts/contract/MinipoolManager.sol::262 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpAmt")), msg.value);
examples/contracts/contract/MinipoolManager.sol::263 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")), avaxAssignmentRequest);
examples/contracts/contract/MinipoolManager.sol::291 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Finished));
examples/contracts/contract/MinipoolManager.sol::293 => uint256 avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpAmt")));
examples/contracts/contract/MinipoolManager.sol::294 => uint256 avaxNodeOpRewardAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpRewardAmt")));
examples/contracts/contract/MinipoolManager.sol::317 => uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));
examples/contracts/contract/MinipoolManager.sol::328 => uint256 avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpAmt")));
examples/contracts/contract/MinipoolManager.sol::329 => uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));
examples/contracts/contract/MinipoolManager.sol::333 => addUint(keccak256("MinipoolManager.TotalAVAXLiquidStakerAmt"), avaxLiquidStakerAmt);
examples/contracts/contract/MinipoolManager.sol::335 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Launched));
examples/contracts/contract/MinipoolManager.sol::360 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Staking));
examples/contracts/contract/MinipoolManager.sol::361 => setBytes32(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".txID")), txID);
examples/contracts/contract/MinipoolManager.sol::362 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".startTime")), startTime);
examples/contracts/contract/MinipoolManager.sol::365 => uint256 initialStartTime = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".initialStartTime")));
examples/contracts/contract/MinipoolManager.sol::367 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".initialStartTime")), startTime);
examples/contracts/contract/MinipoolManager.sol::370 => address owner = getAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".owner")));
examples/contracts/contract/MinipoolManager.sol::371 => uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));
examples/contracts/contract/MinipoolManager.sol::393 => uint256 startTime = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".startTime")));
examples/contracts/contract/MinipoolManager.sol::398 => uint256 avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpAmt")));
examples/contracts/contract/MinipoolManager.sol::399 => uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));
examples/contracts/contract/MinipoolManager.sol::405 => address owner = getAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".owner")));
examples/contracts/contract/MinipoolManager.sol::407 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Withdrawable));
examples/contracts/contract/MinipoolManager.sol::408 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".endTime")), endTime);
examples/contracts/contract/MinipoolManager.sol::409 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxTotalRewardAmt")), avaxTotalRewardAmt);
examples/contracts/contract/MinipoolManager.sol::420 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpRewardAmt")), avaxNodeOpRewardAmt);
examples/contracts/contract/MinipoolManager.sol::421 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerRewardAmt")), avaxLiquidStakerRewardAmt);
examples/contracts/contract/MinipoolManager.sol::433 => subUint(keccak256("MinipoolManager.TotalAVAXLiquidStakerAmt"), avaxLiquidStakerAmt);
examples/contracts/contract/MinipoolManager.sol::451 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpAmt")), compoundedAvaxNodeOpAmt);
examples/contracts/contract/MinipoolManager.sol::452 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")), compoundedAvaxNodeOpAmt);
examples/contracts/contract/MinipoolManager.sol::475 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Prelaunch));
examples/contracts/contract/MinipoolManager.sol::488 => address owner = getAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".owner")));
examples/contracts/contract/MinipoolManager.sol::489 => uint256 avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpAmt")));
examples/contracts/contract/MinipoolManager.sol::490 => uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));
examples/contracts/contract/MinipoolManager.sol::496 => setBytes32(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".errorCode")), errorCode);
examples/contracts/contract/MinipoolManager.sol::497 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Error));
examples/contracts/contract/MinipoolManager.sol::498 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxTotalRewardAmt")), 0);
examples/contracts/contract/MinipoolManager.sol::499 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpRewardAmt")), 0);
examples/contracts/contract/MinipoolManager.sol::500 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerRewardAmt")), 0);
examples/contracts/contract/MinipoolManager.sol::512 => subUint(keccak256("MinipoolManager.TotalAVAXLiquidStakerAmt"), avaxLiquidStakerAmt);
examples/contracts/contract/MinipoolManager.sol::522 => setBytes32(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".errorCode")), errorCode);
examples/contracts/contract/MinipoolManager.sol::531 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Finished));
examples/contracts/contract/MinipoolManager.sol::542 => return getUint(keccak256("MinipoolManager.TotalAVAXLiquidStakerAmt"));
examples/contracts/contract/MinipoolManager.sol::584 => mp.duration = getUint(keccak256(abi.encodePacked("minipool.item", index, ".duration")));
examples/contracts/contract/MinipoolManager.sol::585 => mp.delegationFee = getUint(keccak256(abi.encodePacked("minipool.item", index, ".delegationFee")));
examples/contracts/contract/MinipoolManager.sol::587 => mp.multisigAddr = getAddress(keccak256(abi.encodePacked("minipool.item", index, ".multisigAddr")));
examples/contracts/contract/MinipoolManager.sol::588 => mp.avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxNodeOpAmt")));
examples/contracts/contract/MinipoolManager.sol::589 => mp.avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxLiquidStakerAmt")));
examples/contracts/contract/MinipoolManager.sol::591 => mp.initialStartTime = getUint(keccak256(abi.encodePacked("minipool.item", index, ".initialStartTime")));
examples/contracts/contract/MinipoolManager.sol::592 => mp.startTime = getUint(keccak256(abi.encodePacked("minipool.item", index, ".startTime")));
examples/contracts/contract/MinipoolManager.sol::594 => mp.avaxTotalRewardAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxTotalRewardAmt")));
examples/contracts/contract/MinipoolManager.sol::595 => mp.errorCode = getBytes32(keccak256(abi.encodePacked("minipool.item", index, ".errorCode")));
examples/contracts/contract/MinipoolManager.sol::596 => mp.avaxNodeOpInitialAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxNodeOpInitialAmt")));
examples/contracts/contract/MinipoolManager.sol::597 => mp.avaxNodeOpRewardAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxNodeOpRewardAmt")));
examples/contracts/contract/MinipoolManager.sol::598 => mp.avaxLiquidStakerRewardAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxLiquidStakerRewardAmt")));
examples/contracts/contract/MinipoolManager.sol::599 => mp.ggpSlashAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".ggpSlashAmt")));
examples/contracts/contract/MinipoolManager.sol::651 => uint256 avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxNodeOpAmt")));
examples/contracts/contract/MinipoolManager.sol::652 => uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxLiquidStakerAmt")));
examples/contracts/contract/MinipoolManager.sol::673 => uint256 duration = getUint(keccak256(abi.encodePacked("minipool.item", index, ".duration")));
examples/contracts/contract/MinipoolManager.sol::674 => uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxLiquidStakerAmt")));
examples/contracts/contract/MinipoolManager.sol::677 => setUint(keccak256(abi.encodePacked("minipool.item", index, ".ggpSlashAmt")), slashGGPAmt);
examples/contracts/contract/MinipoolManager.sol::689 => setUint(keccak256(abi.encodePacked("minipool.item", index, ".startTime")), 0);
examples/contracts/contract/MinipoolManager.sol::691 => setUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxTotalRewardAmt")), 0);
examples/contracts/contract/MinipoolManager.sol::692 => setUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxNodeOpRewardAmt")), 0);
examples/contracts/contract/MinipoolManager.sol::693 => setUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxLiquidStakerRewardAmt")), 0);
examples/contracts/contract/MinipoolManager.sol::694 => setUint(keccak256(abi.encodePacked("minipool.item", index, ".ggpSlashAmt")), 0);
examples/contracts/contract/MinipoolManager.sol::695 => setBytes32(keccak256(abi.encodePacked("minipool.item", index, ".errorCode")), 0);
examples/contracts/contract/MultisigManager.sol::61 => setBool(keccak256(abi.encodePacked("multisig.item", multisigIndex, ".enabled")), true);
examples/contracts/contract/MultisigManager.sol::74 => setBool(keccak256(abi.encodePacked("multisig.item", multisigIndex, ".enabled")), false);
examples/contracts/contract/ProtocolDAO.sol::30 => setUint(keccak256("ProtocolDAO.RewardsEligibilityMinSeconds"), 14 days);
examples/contracts/contract/ProtocolDAO.sol::34 => setUint(keccak256("ProtocolDAO.TotalGGPCirculatingSupply"), 18_000_000 ether);
examples/contracts/contract/ProtocolDAO.sol::35 => setUint(keccak256("ProtocolDAO.ClaimingContractPct.ClaimMultisig"), 0.20 ether);
examples/contracts/contract/ProtocolDAO.sol::36 => setUint(keccak256("ProtocolDAO.ClaimingContractPct.ClaimNodeOp"), 0.70 ether);
examples/contracts/contract/ProtocolDAO.sol::37 => setUint(keccak256("ProtocolDAO.ClaimingContractPct.ClaimProtocolDAO"), 0.10 ether);
examples/contracts/contract/ProtocolDAO.sol::40 => setUint(keccak256("ProtocolDAO.InflationIntervalSeconds"), 1 days);
examples/contracts/contract/ProtocolDAO.sol::41 => setUint(keccak256("ProtocolDAO.InflationIntervalRate"), 1000133680617113500); // 5% annual calculated on a daily interval - Calculate in js example: let dailyInflation = web3.utils.toBN((1 + 0.05) ** (1 / (365)) * 1e18);
examples/contracts/contract/ProtocolDAO.sol::44 => setUint(keccak256("ProtocolDAO.TargetGGAVAXReserveRate"), 0.1 ether); // 10% collateral held in reserve
examples/contracts/contract/ProtocolDAO.sol::47 => setUint(keccak256("ProtocolDAO.MinipoolMinAVAXStakingAmt"), 2_000 ether);
examples/contracts/contract/ProtocolDAO.sol::48 => setUint(keccak256("ProtocolDAO.MinipoolNodeCommissionFeePct"), 0.15 ether);
examples/contracts/contract/ProtocolDAO.sol::49 => setUint(keccak256("ProtocolDAO.MinipoolMaxAVAXAssignment"), 10_000 ether);
examples/contracts/contract/ProtocolDAO.sol::50 => setUint(keccak256("ProtocolDAO.MinipoolMinAVAXAssignment"), 1_000 ether);
examples/contracts/contract/ProtocolDAO.sol::51 => setUint(keccak256("ProtocolDAO.ExpectedAVAXRewardsRate"), 0.1 ether); // Annual rate as pct of 1 avax
examples/contracts/contract/ProtocolDAO.sol::52 => setUint(keccak256("ProtocolDAO.MinipoolCancelMoratoriumSeconds"), 5 days);
examples/contracts/contract/ProtocolDAO.sol::55 => setUint(keccak256("ProtocolDAO.MaxCollateralizationRatio"), 1.5 ether);
examples/contracts/contract/ProtocolDAO.sol::56 => setUint(keccak256("ProtocolDAO.MinCollateralizationRatio"), 0.1 ether);
examples/contracts/contract/ProtocolDAO.sol::82 => return getUint(keccak256("ProtocolDAO.RewardsEligibilityMinSeconds"));
examples/contracts/contract/ProtocolDAO.sol::92 => return getUint(keccak256("ProtocolDAO.TotalGGPCirculatingSupply"));
examples/contracts/contract/ProtocolDAO.sol::97 => return setUint(keccak256("ProtocolDAO.TotalGGPCirculatingSupply"), amount);
examples/contracts/contract/ProtocolDAO.sol::117 => uint256 rate = getUint(keccak256("ProtocolDAO.InflationIntervalRate"));
examples/contracts/contract/ProtocolDAO.sol::124 => return getUint(keccak256("ProtocolDAO.InflationIntervalSeconds"));
examples/contracts/contract/ProtocolDAO.sol::131 => return getUint(keccak256("ProtocolDAO.MinipoolMinAVAXStakingAmt"));
examples/contracts/contract/ProtocolDAO.sol::136 => return getUint(keccak256("ProtocolDAO.MinipoolNodeCommissionFeePct"));
examples/contracts/contract/ProtocolDAO.sol::141 => return getUint(keccak256("ProtocolDAO.MinipoolMaxAVAXAssignment"));
examples/contracts/contract/ProtocolDAO.sol::146 => return getUint(keccak256("ProtocolDAO.MinipoolMinAVAXAssignment"));
examples/contracts/contract/ProtocolDAO.sol::151 => return getUint(keccak256("ProtocolDAO.MinipoolCancelMoratoriumSeconds"));
examples/contracts/contract/ProtocolDAO.sol::157 => setUint(keccak256("ProtocolDAO.ExpectedAVAXRewardsRate"), rate);
examples/contracts/contract/ProtocolDAO.sol::162 => return getUint(keccak256("ProtocolDAO.ExpectedAVAXRewardsRate"));
examples/contracts/contract/ProtocolDAO.sol::169 => return getUint(keccak256("ProtocolDAO.MaxCollateralizationRatio"));
examples/contracts/contract/ProtocolDAO.sol::174 => return getUint(keccak256("ProtocolDAO.MinCollateralizationRatio"));
examples/contracts/contract/ProtocolDAO.sol::182 => return getUint(keccak256("ProtocolDAO.TargetGGAVAXReserveRate"));
examples/contracts/contract/RewardsPool.sol::12 => import {FixedPointMathLib} from "@rari-capital/solmate/src/utils/FixedPointMathLib.sol";
examples/contracts/contract/RewardsPool.sol::40 => setUint(keccak256("RewardsPool.RewardsCycleStartTime"), block.timestamp);
examples/contracts/contract/RewardsPool.sol::41 => setUint(keccak256("RewardsPool.InflationIntervalStartTime"), block.timestamp);
examples/contracts/contract/RewardsPool.sol::49 => return getUint(keccak256("RewardsPool.InflationIntervalStartTime"));
examples/contracts/contract/RewardsPool.sol::98 => addUint(keccak256("RewardsPool.InflationIntervalStartTime"), inflationIntervalElapsedSeconds);
examples/contracts/contract/RewardsPool.sol::116 => return getUint(keccak256("RewardsPool.RewardsCycleStartTime"));
examples/contracts/contract/RewardsPool.sol::164 => setUint(keccak256("RewardsPool.RewardsCycleStartTime"), block.timestamp);
examples/contracts/contract/Staking.sol::11 => import {ERC20} from "@rari-capital/solmate/src/mixins/ERC4626.sol";
examples/contracts/contract/Staking.sol::12 => import {FixedPointMathLib} from "@rari-capital/solmate/src/utils/FixedPointMathLib.sol";
examples/contracts/contract/Staking.sol::13 => import {SafeTransferLib} from "@rari-capital/solmate/src/utils/SafeTransferLib.sol";
examples/contracts/contract/Staking.sol::82 => return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpStaked")));
examples/contracts/contract/Staking.sol::89 => addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpStaked")), amount);
examples/contracts/contract/Staking.sol::96 => subUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpStaked")), amount);
examples/contracts/contract/Staking.sol::105 => return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxStaked")));
examples/contracts/contract/Staking.sol::112 => addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxStaked")), amount);
examples/contracts/contract/Staking.sol::119 => subUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxStaked")), amount);
examples/contracts/contract/Staking.sol::128 => return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssigned")));
examples/contracts/contract/Staking.sol::135 => addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssigned")), amount);
examples/contracts/contract/Staking.sol::142 => subUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssigned")), amount);
examples/contracts/contract/Staking.sol::151 => return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssignedHighWater")));
examples/contracts/contract/Staking.sol::158 => addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssignedHighWater")), amount);
examples/contracts/contract/Staking.sol::165 => uint256 currAVAXAssigned = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssigned")));
examples/contracts/contract/Staking.sol::166 => setUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssignedHighWater")), currAVAXAssigned);
examples/contracts/contract/Staking.sol::175 => return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".minipoolCount")));
examples/contracts/contract/Staking.sol::182 => addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".minipoolCount")), 1);
examples/contracts/contract/Staking.sol::189 => subUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".minipoolCount")), 1);
examples/contracts/contract/Staking.sol::198 => return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".rewardsStartTime")));
examples/contracts/contract/Staking.sol::210 => setUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".rewardsStartTime")), time);
examples/contracts/contract/Staking.sol::219 => return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpRewards")));
examples/contracts/contract/Staking.sol::226 => addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpRewards")), amount);
examples/contracts/contract/Staking.sol::233 => subUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpRewards")), amount);
examples/contracts/contract/Staking.sol::242 => return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".lastRewardsCycleCompleted")));
examples/contracts/contract/Staking.sol::250 => setUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".lastRewardsCycleCompleted")), cycleNumber);
examples/contracts/contract/Staking.sol::351 => setAddress(keccak256(abi.encodePacked("staker.item", stakerIndex, ".stakerAddr")), stakerAddr);
examples/contracts/contract/Staking.sol::406 => staker.ggpStaked = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpStaked")));
examples/contracts/contract/Staking.sol::407 => staker.avaxAssigned = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssigned")));
examples/contracts/contract/Staking.sol::408 => staker.avaxStaked = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxStaked")));
examples/contracts/contract/Staking.sol::409 => staker.stakerAddr = getAddress(keccak256(abi.encodePacked("staker.item", stakerIndex, ".stakerAddr")));
examples/contracts/contract/Staking.sol::410 => staker.minipoolCount = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".minipoolCount")));
examples/contracts/contract/Staking.sol::411 => staker.rewardsStartTime = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".rewardsStartTime")));
examples/contracts/contract/Staking.sol::412 => staker.ggpRewards = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpRewards")));
examples/contracts/contract/Staking.sol::413 => staker.lastRewardsCycleCompleted = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".lastRewardsCycleCompleted")));
examples/contracts/contract/Vault.sol::10 => import {ERC20} from "@rari-capital/solmate/src/tokens/ERC20.sol";
examples/contracts/contract/Vault.sol::11 => import {ReentrancyGuard} from "@rari-capital/solmate/src/utils/ReentrancyGuard.sol";
examples/contracts/contract/Vault.sol::12 => import {SafeTransferLib} from "@rari-capital/solmate/src/utils/SafeTransferLib.sol";
examples/contracts/contract/tokens/TokenGGP.sol::4 => import {ERC20} from "@rari-capital/solmate/src/tokens/ERC20.sol";
examples/contracts/contract/tokens/TokenggAVAX.sol::7 => import {ERC20Upgradeable} from "./upgradeable/ERC20Upgradeable.sol";
examples/contracts/contract/tokens/TokenggAVAX.sol::8 => import {ERC4626Upgradeable} from "./upgradeable/ERC4626Upgradeable.sol";
examples/contracts/contract/tokens/TokenggAVAX.sol::15 => import {Initializable} from "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
examples/contracts/contract/tokens/TokenggAVAX.sol::16 => import {UUPSUpgradeable} from "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";
examples/contracts/contract/tokens/TokenggAVAX.sol::18 => import {ERC20} from "@rari-capital/solmate/src/mixins/ERC4626.sol";
examples/contracts/contract/tokens/TokenggAVAX.sol::19 => import {FixedPointMathLib} from "@rari-capital/solmate/src/utils/FixedPointMathLib.sol";
examples/contracts/contract/tokens/TokenggAVAX.sol::20 => import {SafeCastLib} from "@rari-capital/solmate/src/utils/SafeCastLib.sol";
examples/contracts/contract/tokens/TokenggAVAX.sol::21 => import {SafeTransferLib} from "@rari-capital/solmate/src/utils/SafeTransferLib.sol";
examples/contracts/contract/tokens/TokenggAVAX.sol::73 => __ERC4626Upgradeable_init(asset, "GoGoPool Liquid Staking Token", "ggAVAX");
examples/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol::4 => import {Initializable} from "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
examples/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol::139 => keccak256("Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)"),
examples/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol::170 => keccak256("EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)"),
examples/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol::6 => import {ERC20} from "@rari-capital/solmate/src/tokens/ERC20.sol";
examples/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol::7 => import {FixedPointMathLib} from "@rari-capital/solmate/src/utils/FixedPointMathLib.sol";
examples/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol::8 => import {SafeTransferLib} from "@rari-capital/solmate/src/utils/SafeTransferLib.sol";
examples/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol::9 => import {Initializable} from "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
examples/contracts/contract/utils/Multicall3.sol::130 => // set "Error(string)" signature: bytes32(bytes4(keccak256("Error(string)")))
examples/contracts/contract/utils/Multicall3.sol::170 => // set "Error(string)" signature: bytes32(bytes4(keccak256("Error(string)")))
examples/contracts/contract/utils/OneInchMock.sol::4 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
examples/contracts/contract/utils/WAVAX.sol::4 => import {ERC20} from "@rari-capital/solmate/src/tokens/ERC20.sol";
examples/contracts/contract/utils/WAVAX.sol::5 => import {SafeTransferLib} from "@rari-capital/solmate/src/utils/SafeTransferLib.sol";
examples/contracts/interface/IOneInch.sol::4 => import "@rari-capital/solmate/src/tokens/ERC20.sol";
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Use Shift Right/Left instead of Division/Multiplication if possible

#### Impact
Issue Information: [G008](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md/#g008---use-shift-rightleft-instead-of-divisionmultiplication-if-possible)

#### Findings:
```
examples/contracts/contract/MinipoolManager.sol::413 => uint256 avaxHalfRewards = avaxTotalRewardAmt / 2;
examples/contracts/contract/utils/RialtoSimulator.sol::109 => effectiveGGPStaked = staking.getEffectiveGGPStaked(allStakers[i].stakerAddr) / 2;
examples/contracts/contract/utils/RialtoSimulator.sol::119 => nopClaim.calculateAndDistributeRewards(allStakers[i].stakerAddr, (totalEligibleStakedGGP * 2));
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)
