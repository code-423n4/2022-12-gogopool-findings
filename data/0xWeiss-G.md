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

