### 1st Bug
Don't Initialize Variables with Default Value

#### Impact
Issue Information: 
Uninitialized variables are assigned with the types default value.

Explicitly initializing a variable with it's default value costs unnecessary gas.

Example
🤦 Bad:

uint256 x = 0;
bool y = false;
🚀 Good:

uint256 x;
bool y;

#### Findings:
```
2022-12-gogopool/lib/sol-utils/src/dependencies/Strings.sol::46 => uint256 length = 0;
2022-12-gogopool/lib/solmate/src/tokens/ERC1155.sol::93 => for (uint256 i = 0; i < ids.length; ) {
2022-12-gogopool/lib/solmate/src/tokens/ERC1155.sol::131 => for (uint256 i = 0; i < owners.length; ++i) {
2022-12-gogopool/lib/solmate/src/tokens/ERC1155.sol::181 => for (uint256 i = 0; i < idsLength; ) {
2022-12-gogopool/lib/solmate/src/tokens/ERC1155.sol::211 => for (uint256 i = 0; i < idsLength; ) {
2022-12-gogopool/scripts/finalize-minipools.s.sol::22 => for (uint256 i = 0; i < minipools.length; i++) {
```
#### Tools used
Manual code review

### 2nd Bug
Cache Array Length Outside of Loop

#### Impact
Issue Information: 
Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.

Example
🤦 Bad:

for (uint256 i = 0; i < array.length; i++) {
    // invariant: array's length is not changed
}
🚀 Good:

uint256 len = array.length
for (uint256 i = 0; i < len; i++) {
    // invariant: array's length is not changed
}

#### Findings:
```
2022-12-gogopool/contracts/contract/BaseAbstract.sol::101 => if (bytes(contractName).length == 0) {
2022-12-gogopool/contracts/contract/MinipoolManager.sol::554 => /// @param duration The length of validation in seconds
2022-12-gogopool/contracts/contract/RewardsPool.sol::167 => // This will always 'mint' (release) new tokens if the rewards cycle length requirement is met
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::43 => /// @notice the maximum length of a rewards cycle
```
#### Tools used
Manual code review


### 3rd Bug
Use immutable for OpenZeppelin AccessControl's Roles Declarations

#### Impact
Issue Information: 
⚡️ Only valid for solidity versions <0.6.12 ⚡️

Access roles marked as constant results in computing the keccak256 operation each time the variable is used because assigned operations for constant variables are re-evaluated every time.

Changing the variables to immutable results in computing the hash only once on deployment, leading to gas savings.

Example
🤦 Bad:

bytes32 public constant GOVERNOR_ROLE = keccak256("GOVERNOR_ROLE");
🚀 Good:

bytes32 public immutable GOVERNOR_ROLE = keccak256("GOVERNOR_ROLE");

#### Findings:
```
2022-12-gogopool/contracts/contract/BaseAbstract.sol::25 => if (getBool(keccak256(abi.encodePacked("contract.exists", msg.sender))) == false) {
2022-12-gogopool/contracts/contract/BaseAbstract.sol::33 => if (contractAddress != getAddress(keccak256(abi.encodePacked("contract.address", contractName)))) {
2022-12-gogopool/contracts/contract/BaseAbstract.sol::41 => bool isContract = getBool(keccak256(abi.encodePacked("contract.exists", msg.sender)));
2022-12-gogopool/contracts/contract/BaseAbstract.sol::52 => bool isContract = contractAddress == getAddress(keccak256(abi.encodePacked("contract.address", contractName)));
2022-12-gogopool/contracts/contract/BaseAbstract.sol::71 => int256 multisigIndex = int256(getUint(keccak256(abi.encodePacked("multisig.index", msg.sender)))) - 1;
2022-12-gogopool/contracts/contract/BaseAbstract.sol::72 => address addr = getAddress(keccak256(abi.encodePacked("multisig.item", multisigIndex, ".address")));
2022-12-gogopool/contracts/contract/BaseAbstract.sol::73 => bool enabled = (addr != address(0)) && getBool(keccak256(abi.encodePacked("multisig.item", multisigIndex, ".enabled")));
2022-12-gogopool/contracts/contract/BaseAbstract.sol::83 => if (getBool(keccak256(abi.encodePacked("contract.paused", contractName)))) {
2022-12-gogopool/contracts/contract/BaseAbstract.sol::91 => address contractAddress = getAddress(keccak256(abi.encodePacked("contract.address", contractName)));
2022-12-gogopool/contracts/contract/BaseAbstract.sol::100 => string memory contractName = getString(keccak256(abi.encodePacked("contract.name", contractAddress)));
2022-12-gogopool/contracts/contract/ClaimNodeOp.sol::36 => return getUint(keccak256("NOPClaim.RewardsCycleTotal"));
2022-12-gogopool/contracts/contract/ClaimNodeOp.sol::41 => setUint(keccak256("NOPClaim.RewardsCycleTotal"), amount);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::116 => address owner = getAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".owner")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::130 => address assignedMultisig = getAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".multisigAddr")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::153 => bytes32 statusKey = keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status"));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::246 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".initialStartTime")), 0);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::248 => minipoolIndex = int256(getUint(keccak256("minipool.count")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::250 => setUint(keccak256(abi.encodePacked("minipool.index", nodeID)), uint256(minipoolIndex + 1));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::251 => setAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".nodeID")), nodeID);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::252 => addUint(keccak256("minipool.count"), 1);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::256 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Prelaunch));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::257 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".duration")), duration);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::258 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".delegationFee")), delegationFee);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::259 => setAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".owner")), msg.sender);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::260 => setAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".multisigAddr")), multisig);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::261 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpInitialAmt")), msg.value);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::262 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpAmt")), msg.value);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::263 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")), avaxAssignmentRequest);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::291 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Finished));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::293 => uint256 avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpAmt")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::294 => uint256 avaxNodeOpRewardAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpRewardAmt")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::317 => uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::328 => uint256 avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpAmt")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::329 => uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::333 => addUint(keccak256("MinipoolManager.TotalAVAXLiquidStakerAmt"), avaxLiquidStakerAmt);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::335 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Launched));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::360 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Staking));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::361 => setBytes32(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".txID")), txID);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::362 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".startTime")), startTime);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::365 => uint256 initialStartTime = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".initialStartTime")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::367 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".initialStartTime")), startTime);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::370 => address owner = getAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".owner")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::371 => uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::393 => uint256 startTime = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".startTime")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::398 => uint256 avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpAmt")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::399 => uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::405 => address owner = getAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".owner")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::407 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Withdrawable));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::408 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".endTime")), endTime);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::409 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxTotalRewardAmt")), avaxTotalRewardAmt);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::420 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpRewardAmt")), avaxNodeOpRewardAmt);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::421 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerRewardAmt")), avaxLiquidStakerRewardAmt);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::433 => subUint(keccak256("MinipoolManager.TotalAVAXLiquidStakerAmt"), avaxLiquidStakerAmt);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::451 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpAmt")), compoundedAvaxNodeOpAmt);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::452 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")), compoundedAvaxNodeOpAmt);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::475 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Prelaunch));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::488 => address owner = getAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".owner")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::489 => uint256 avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpAmt")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::490 => uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::496 => setBytes32(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".errorCode")), errorCode);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::497 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Error));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::498 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxTotalRewardAmt")), 0);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::499 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpRewardAmt")), 0);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::500 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerRewardAmt")), 0);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::512 => subUint(keccak256("MinipoolManager.TotalAVAXLiquidStakerAmt"), avaxLiquidStakerAmt);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::522 => setBytes32(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".errorCode")), errorCode);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::531 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Finished));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::542 => return getUint(keccak256("MinipoolManager.TotalAVAXLiquidStakerAmt"));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::567 => return int256(getUint(keccak256(abi.encodePacked("minipool.index", nodeID)))) - 1;
2022-12-gogopool/contracts/contract/MinipoolManager.sol::582 => mp.nodeID = getAddress(keccak256(abi.encodePacked("minipool.item", index, ".nodeID")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::583 => mp.status = getUint(keccak256(abi.encodePacked("minipool.item", index, ".status")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::584 => mp.duration = getUint(keccak256(abi.encodePacked("minipool.item", index, ".duration")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::585 => mp.delegationFee = getUint(keccak256(abi.encodePacked("minipool.item", index, ".delegationFee")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::586 => mp.owner = getAddress(keccak256(abi.encodePacked("minipool.item", index, ".owner")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::587 => mp.multisigAddr = getAddress(keccak256(abi.encodePacked("minipool.item", index, ".multisigAddr")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::588 => mp.avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxNodeOpAmt")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::589 => mp.avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxLiquidStakerAmt")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::590 => mp.txID = getBytes32(keccak256(abi.encodePacked("minipool.item", index, ".txID")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::591 => mp.initialStartTime = getUint(keccak256(abi.encodePacked("minipool.item", index, ".initialStartTime")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::592 => mp.startTime = getUint(keccak256(abi.encodePacked("minipool.item", index, ".startTime")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::593 => mp.endTime = getUint(keccak256(abi.encodePacked("minipool.item", index, ".endTime")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::594 => mp.avaxTotalRewardAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxTotalRewardAmt")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::595 => mp.errorCode = getBytes32(keccak256(abi.encodePacked("minipool.item", index, ".errorCode")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::596 => mp.avaxNodeOpInitialAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxNodeOpInitialAmt")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::597 => mp.avaxNodeOpRewardAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxNodeOpRewardAmt")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::598 => mp.avaxLiquidStakerRewardAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxLiquidStakerRewardAmt")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::599 => mp.ggpSlashAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".ggpSlashAmt")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::612 => uint256 totalMinipools = getUint(keccak256("minipool.count"));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::635 => return getUint(keccak256("minipool.count"));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::648 => setUint(keccak256(abi.encodePacked("minipool.item", index, ".status")), uint256(MinipoolStatus.Canceled));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::650 => address owner = getAddress(keccak256(abi.encodePacked("minipool.item", index, ".owner")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::651 => uint256 avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxNodeOpAmt")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::652 => uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxLiquidStakerAmt")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::671 => address nodeID = getAddress(keccak256(abi.encodePacked("minipool.item", index, ".nodeID")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::672 => address owner = getAddress(keccak256(abi.encodePacked("minipool.item", index, ".owner")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::673 => uint256 duration = getUint(keccak256(abi.encodePacked("minipool.item", index, ".duration")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::674 => uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxLiquidStakerAmt")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::677 => setUint(keccak256(abi.encodePacked("minipool.item", index, ".ggpSlashAmt")), slashGGPAmt);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::688 => setBytes32(keccak256(abi.encodePacked("minipool.item", index, ".txID")), 0);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::689 => setUint(keccak256(abi.encodePacked("minipool.item", index, ".startTime")), 0);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::690 => setUint(keccak256(abi.encodePacked("minipool.item", index, ".endTime")), 0);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::691 => setUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxTotalRewardAmt")), 0);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::692 => setUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxNodeOpRewardAmt")), 0);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::693 => setUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxLiquidStakerRewardAmt")), 0);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::694 => setUint(keccak256(abi.encodePacked("minipool.item", index, ".ggpSlashAmt")), 0);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::695 => setBytes32(keccak256(abi.encodePacked("minipool.item", index, ".errorCode")), 0);
2022-12-gogopool/contracts/contract/MultisigManager.sol::40 => uint256 index = getUint(keccak256("multisig.count"));
2022-12-gogopool/contracts/contract/MultisigManager.sol::45 => setAddress(keccak256(abi.encodePacked("multisig.item", index, ".address")), addr);
2022-12-gogopool/contracts/contract/MultisigManager.sol::48 => setUint(keccak256(abi.encodePacked("multisig.index", addr)), index + 1);
2022-12-gogopool/contracts/contract/MultisigManager.sol::49 => addUint(keccak256("multisig.count"), 1);
2022-12-gogopool/contracts/contract/MultisigManager.sol::61 => setBool(keccak256(abi.encodePacked("multisig.item", multisigIndex, ".enabled")), true);
2022-12-gogopool/contracts/contract/MultisigManager.sol::74 => setBool(keccak256(abi.encodePacked("multisig.item", multisigIndex, ".enabled")), false);
2022-12-gogopool/contracts/contract/MultisigManager.sol::81 => uint256 total = getUint(keccak256("multisig.count"));
2022-12-gogopool/contracts/contract/MultisigManager.sol::97 => return int256(getUint(keccak256(abi.encodePacked("multisig.index", addr)))) - 1;
2022-12-gogopool/contracts/contract/MultisigManager.sol::103 => return getUint(keccak256("multisig.count"));
2022-12-gogopool/contracts/contract/MultisigManager.sol::110 => addr = getAddress(keccak256(abi.encodePacked("multisig.item", index, ".address")));
2022-12-gogopool/contracts/contract/MultisigManager.sol::111 => enabled = (addr != address(0)) && getBool(keccak256(abi.encodePacked("multisig.item", index, ".enabled")));
2022-12-gogopool/contracts/contract/Oracle.sol::29 => setAddress(keccak256("Oracle.OneInch"), addr);
2022-12-gogopool/contracts/contract/Oracle.sol::38 => IOneInch oneinch = IOneInch(getAddress(keccak256("Oracle.OneInch")));
2022-12-gogopool/contracts/contract/Oracle.sol::47 => price = getUint(keccak256("Oracle.GGPPriceInAVAX"));
2022-12-gogopool/contracts/contract/Oracle.sol::51 => timestamp = getUint(keccak256("Oracle.GGPTimestamp"));
2022-12-gogopool/contracts/contract/Oracle.sol::58 => uint256 lastTimestamp = getUint(keccak256("Oracle.GGPTimestamp"));
2022-12-gogopool/contracts/contract/Oracle.sol::65 => setUint(keccak256("Oracle.GGPPriceInAVAX"), price);
2022-12-gogopool/contracts/contract/Oracle.sol::66 => setUint(keccak256("Oracle.GGPTimestamp"), timestamp);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::24 => if (getBool(keccak256("ProtocolDAO.initialized"))) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::27 => setBool(keccak256("ProtocolDAO.initialized"), true);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::30 => setUint(keccak256("ProtocolDAO.RewardsEligibilityMinSeconds"), 14 days);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::33 => setUint(keccak256("ProtocolDAO.RewardsCycleSeconds"), 28 days); // The time in which a claim period will span in seconds - 28 days by default
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::34 => setUint(keccak256("ProtocolDAO.TotalGGPCirculatingSupply"), 18_000_000 ether);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::35 => setUint(keccak256("ProtocolDAO.ClaimingContractPct.ClaimMultisig"), 0.20 ether);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::36 => setUint(keccak256("ProtocolDAO.ClaimingContractPct.ClaimNodeOp"), 0.70 ether);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::37 => setUint(keccak256("ProtocolDAO.ClaimingContractPct.ClaimProtocolDAO"), 0.10 ether);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::40 => setUint(keccak256("ProtocolDAO.InflationIntervalSeconds"), 1 days);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::41 => setUint(keccak256("ProtocolDAO.InflationIntervalRate"), 1000133680617113500); // 5% annual calculated on a daily interval - Calculate in js example: let dailyInflation = web3.utils.toBN((1 + 0.05) ** (1 / (365)) * 1e18);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::44 => setUint(keccak256("ProtocolDAO.TargetGGAVAXReserveRate"), 0.1 ether); // 10% collateral held in reserve
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::47 => setUint(keccak256("ProtocolDAO.MinipoolMinAVAXStakingAmt"), 2_000 ether);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::48 => setUint(keccak256("ProtocolDAO.MinipoolNodeCommissionFeePct"), 0.15 ether);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::49 => setUint(keccak256("ProtocolDAO.MinipoolMaxAVAXAssignment"), 10_000 ether);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::50 => setUint(keccak256("ProtocolDAO.MinipoolMinAVAXAssignment"), 1_000 ether);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::51 => setUint(keccak256("ProtocolDAO.ExpectedAVAXRewardsRate"), 0.1 ether); // Annual rate as pct of 1 avax
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::52 => setUint(keccak256("ProtocolDAO.MinipoolCancelMoratoriumSeconds"), 5 days);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::55 => setUint(keccak256("ProtocolDAO.MaxCollateralizationRatio"), 1.5 ether);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::56 => setUint(keccak256("ProtocolDAO.MinCollateralizationRatio"), 0.1 ether);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::62 => return getBool(keccak256(abi.encodePacked("contract.paused", contractName)));
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::68 => setBool(keccak256(abi.encodePacked("contract.paused", contractName)), true);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::74 => setBool(keccak256(abi.encodePacked("contract.paused", contractName)), false);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::82 => return getUint(keccak256("ProtocolDAO.RewardsEligibilityMinSeconds"));
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::87 => return getUint(keccak256("ProtocolDAO.RewardsCycleSeconds"));
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::92 => return getUint(keccak256("ProtocolDAO.TotalGGPCirculatingSupply"));
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::97 => return setUint(keccak256("ProtocolDAO.TotalGGPCirculatingSupply"), amount);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::103 => return getUint(keccak256(abi.encodePacked("ProtocolDAO.ClaimingContractPct.", claimingContract)));
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::108 => setUint(keccak256(abi.encodePacked("ProtocolDAO.ClaimingContractPct.", claimingContract)), decimal);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::117 => uint256 rate = getUint(keccak256("ProtocolDAO.InflationIntervalRate"));
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::124 => return getUint(keccak256("ProtocolDAO.InflationIntervalSeconds"));
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::131 => return getUint(keccak256("ProtocolDAO.MinipoolMinAVAXStakingAmt"));
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::136 => return getUint(keccak256("ProtocolDAO.MinipoolNodeCommissionFeePct"));
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::141 => return getUint(keccak256("ProtocolDAO.MinipoolMaxAVAXAssignment"));
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::146 => return getUint(keccak256("ProtocolDAO.MinipoolMinAVAXAssignment"));
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::151 => return getUint(keccak256("ProtocolDAO.MinipoolCancelMoratoriumSeconds"));
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::157 => setUint(keccak256("ProtocolDAO.ExpectedAVAXRewardsRate"), rate);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::162 => return getUint(keccak256("ProtocolDAO.ExpectedAVAXRewardsRate"));
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::169 => return getUint(keccak256("ProtocolDAO.MaxCollateralizationRatio"));
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::174 => return getUint(keccak256("ProtocolDAO.MinCollateralizationRatio"));
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::182 => return getUint(keccak256("ProtocolDAO.TargetGGAVAXReserveRate"));
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::191 => setBool(keccak256(abi.encodePacked("contract.exists", addr)), true);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::192 => setAddress(keccak256(abi.encodePacked("contract.address", name)), addr);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::193 => setString(keccak256(abi.encodePacked("contract.name", addr)), name);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::200 => deleteBool(keccak256(abi.encodePacked("contract.exists", addr)));
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::201 => deleteAddress(keccak256(abi.encodePacked("contract.address", name)));
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::202 => deleteString(keccak256(abi.encodePacked("contract.name", addr)));
2022-12-gogopool/contracts/contract/RewardsPool.sol::35 => if (getBool(keccak256("RewardsPool.initialized"))) {
2022-12-gogopool/contracts/contract/RewardsPool.sol::38 => setBool(keccak256("RewardsPool.initialized"), true);
2022-12-gogopool/contracts/contract/RewardsPool.sol::40 => setUint(keccak256("RewardsPool.RewardsCycleStartTime"), block.timestamp);
2022-12-gogopool/contracts/contract/RewardsPool.sol::41 => setUint(keccak256("RewardsPool.InflationIntervalStartTime"), block.timestamp);
2022-12-gogopool/contracts/contract/RewardsPool.sol::49 => return getUint(keccak256("RewardsPool.InflationIntervalStartTime"));
2022-12-gogopool/contracts/contract/RewardsPool.sol::98 => addUint(keccak256("RewardsPool.InflationIntervalStartTime"), inflationIntervalElapsedSeconds);
2022-12-gogopool/contracts/contract/RewardsPool.sol::99 => setUint(keccak256("RewardsPool.RewardsCycleTotalAmt"), newTokens);
2022-12-gogopool/contracts/contract/RewardsPool.sol::106 => return getUint(keccak256("RewardsPool.RewardsCycleCount"));
2022-12-gogopool/contracts/contract/RewardsPool.sol::111 => addUint(keccak256("RewardsPool.RewardsCycleCount"), 1);
2022-12-gogopool/contracts/contract/RewardsPool.sol::116 => return getUint(keccak256("RewardsPool.RewardsCycleStartTime"));
2022-12-gogopool/contracts/contract/RewardsPool.sol::121 => return getUint(keccak256("RewardsPool.RewardsCycleTotalAmt"));
2022-12-gogopool/contracts/contract/RewardsPool.sol::164 => setUint(keccak256("RewardsPool.RewardsCycleStartTime"), block.timestamp);
2022-12-gogopool/contracts/contract/Staking.sol::73 => return getUint(keccak256("staker.count"));
2022-12-gogopool/contracts/contract/Staking.sol::82 => return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpStaked")));
2022-12-gogopool/contracts/contract/Staking.sol::89 => addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpStaked")), amount);
2022-12-gogopool/contracts/contract/Staking.sol::96 => subUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpStaked")), amount);
2022-12-gogopool/contracts/contract/Staking.sol::105 => return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxStaked")));
2022-12-gogopool/contracts/contract/Staking.sol::112 => addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxStaked")), amount);
2022-12-gogopool/contracts/contract/Staking.sol::119 => subUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxStaked")), amount);
2022-12-gogopool/contracts/contract/Staking.sol::128 => return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssigned")));
2022-12-gogopool/contracts/contract/Staking.sol::135 => addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssigned")), amount);
2022-12-gogopool/contracts/contract/Staking.sol::142 => subUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssigned")), amount);
2022-12-gogopool/contracts/contract/Staking.sol::151 => return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssignedHighWater")));
2022-12-gogopool/contracts/contract/Staking.sol::158 => addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssignedHighWater")), amount);
2022-12-gogopool/contracts/contract/Staking.sol::165 => uint256 currAVAXAssigned = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssigned")));
2022-12-gogopool/contracts/contract/Staking.sol::166 => setUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssignedHighWater")), currAVAXAssigned);
2022-12-gogopool/contracts/contract/Staking.sol::175 => return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".minipoolCount")));
2022-12-gogopool/contracts/contract/Staking.sol::182 => addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".minipoolCount")), 1);
2022-12-gogopool/contracts/contract/Staking.sol::189 => subUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".minipoolCount")), 1);
2022-12-gogopool/contracts/contract/Staking.sol::198 => return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".rewardsStartTime")));
2022-12-gogopool/contracts/contract/Staking.sol::210 => setUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".rewardsStartTime")), time);
2022-12-gogopool/contracts/contract/Staking.sol::219 => return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpRewards")));
2022-12-gogopool/contracts/contract/Staking.sol::226 => addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpRewards")), amount);
2022-12-gogopool/contracts/contract/Staking.sol::233 => subUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpRewards")), amount);
2022-12-gogopool/contracts/contract/Staking.sol::242 => return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".lastRewardsCycleCompleted")));
2022-12-gogopool/contracts/contract/Staking.sol::250 => setUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".lastRewardsCycleCompleted")), cycleNumber);
2022-12-gogopool/contracts/contract/Staking.sol::348 => stakerIndex = int256(getUint(keccak256("staker.count")));
2022-12-gogopool/contracts/contract/Staking.sol::349 => addUint(keccak256("staker.count"), 1);
2022-12-gogopool/contracts/contract/Staking.sol::350 => setUint(keccak256(abi.encodePacked("staker.index", stakerAddr)), uint256(stakerIndex + 1));
2022-12-gogopool/contracts/contract/Staking.sol::351 => setAddress(keccak256(abi.encodePacked("staker.item", stakerIndex, ".stakerAddr")), stakerAddr);
2022-12-gogopool/contracts/contract/Staking.sol::399 => return int256(getUint(keccak256(abi.encodePacked("staker.index", stakerAddr)))) - 1;
2022-12-gogopool/contracts/contract/Staking.sol::406 => staker.ggpStaked = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpStaked")));
2022-12-gogopool/contracts/contract/Staking.sol::407 => staker.avaxAssigned = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssigned")));
2022-12-gogopool/contracts/contract/Staking.sol::408 => staker.avaxStaked = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxStaked")));
2022-12-gogopool/contracts/contract/Staking.sol::409 => staker.stakerAddr = getAddress(keccak256(abi.encodePacked("staker.item", stakerIndex, ".stakerAddr")));
2022-12-gogopool/contracts/contract/Staking.sol::410 => staker.minipoolCount = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".minipoolCount")));
2022-12-gogopool/contracts/contract/Staking.sol::411 => staker.rewardsStartTime = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".rewardsStartTime")));
2022-12-gogopool/contracts/contract/Staking.sol::412 => staker.ggpRewards = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpRewards")));
2022-12-gogopool/contracts/contract/Staking.sol::413 => staker.lastRewardsCycleCompleted = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".lastRewardsCycleCompleted")));
2022-12-gogopool/contracts/contract/Storage.sol::29 => if (booleanStorage[keccak256(abi.encodePacked("contract.exists", msg.sender))] == false && msg.sender != guardian) {
2022-12-gogopool/contracts/contract/Vault.sol::124 => bytes32 contractKey = keccak256(abi.encodePacked(networkContractName, address(tokenContract)));
2022-12-gogopool/contracts/contract/Vault.sol::147 => bytes32 contractKey = keccak256(abi.encodePacked(getContractName(msg.sender), tokenAddress));
2022-12-gogopool/contracts/contract/Vault.sol::178 => bytes32 contractKeyFrom = keccak256(abi.encodePacked(getContractName(msg.sender), tokenAddress));
2022-12-gogopool/contracts/contract/Vault.sol::179 => bytes32 contractKeyTo = keccak256(abi.encodePacked(networkContractName, tokenAddress));
2022-12-gogopool/contracts/contract/Vault.sol::201 => return tokenBalances[keccak256(abi.encodePacked(networkContractName, tokenAddress))];
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::59 => if (amt > 0 && getBool(keccak256(abi.encodePacked("contract.paused", "TokenggAVAX")))) {
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::207 => if (getBool(keccak256(abi.encodePacked("contract.paused", "TokenggAVAX")))) {
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::217 => if (getBool(keccak256(abi.encodePacked("contract.paused", "TokenggAVAX")))) {
```
#### Tools used
Manual code review

### 4th Bug
Long Revert Strings

#### Impact
Issue Information: 
Shortening revert strings to fit in 32 bytes will decrease gas costs for deployment and gas costs when the revert condition has been met.

If the contract(s) in scope allow using Solidity >=0.8.4, consider using Custom Errors as they are more gas efficient while allowing developers to describe the error in detail using NatSpec.

Example
🤦 Bad:

require(condition, "UniswapV3: The reentrancy guard. A transaction cannot re-enter the pool mid-swap");
🚀 Good (with shorter string):

// TODO: Provide link to a reference of error codes
require(condition, "LOK");
🚀 Good (with custom errors):

/// @notice A transaction cannot re-enter the pool mid-swap.
error NoReentrancy();

// ...

if (!condition) {
    revert NoReentrancy();
}

#### Findings:
```
2022-12-gogopool/contracts/contract/BaseAbstract.sol::72 => address addr = getAddress(keccak256(abi.encodePacked("multisig.item", multisigIndex, ".address")));
2022-12-gogopool/contracts/contract/BaseAbstract.sol::73 => bool enabled = (addr != address(0)) && getBool(keccak256(abi.encodePacked("multisig.item", multisigIndex, ".enabled")));
2022-12-gogopool/contracts/contract/BaseUpgradeable.sol::7 => import {Initializable} from "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
2022-12-gogopool/contracts/contract/ClaimNodeOp.sol::13 => import {ERC20} from "@rari-capital/solmate/src/tokens/ERC20.sol";
2022-12-gogopool/contracts/contract/ClaimNodeOp.sol::14 => import {FixedPointMathLib} from "@rari-capital/solmate/src/utils/FixedPointMathLib.sol";
2022-12-gogopool/contracts/contract/MinipoolManager.sol::16 => import {ERC20} from "@rari-capital/solmate/src/mixins/ERC4626.sol";
2022-12-gogopool/contracts/contract/MinipoolManager.sol::17 => import {FixedPointMathLib} from "@rari-capital/solmate/src/utils/FixedPointMathLib.sol";
2022-12-gogopool/contracts/contract/MinipoolManager.sol::18 => import {ReentrancyGuard} from "@rari-capital/solmate/src/utils/ReentrancyGuard.sol";
2022-12-gogopool/contracts/contract/MinipoolManager.sol::19 => import {SafeTransferLib} from "@rari-capital/solmate/src/utils/SafeTransferLib.sol";
2022-12-gogopool/contracts/contract/MinipoolManager.sol::116 => address owner = getAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".owner")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::130 => address assignedMultisig = getAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".multisigAddr")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::153 => bytes32 statusKey = keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status"));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::246 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".initialStartTime")), 0);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::251 => setAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".nodeID")), nodeID);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::256 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Prelaunch));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::257 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".duration")), duration);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::258 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".delegationFee")), delegationFee);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::259 => setAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".owner")), msg.sender);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::260 => setAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".multisigAddr")), multisig);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::261 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpInitialAmt")), msg.value);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::262 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpAmt")), msg.value);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::263 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")), avaxAssignmentRequest);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::291 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Finished));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::293 => uint256 avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpAmt")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::294 => uint256 avaxNodeOpRewardAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpRewardAmt")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::317 => uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::328 => uint256 avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpAmt")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::329 => uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::333 => addUint(keccak256("MinipoolManager.TotalAVAXLiquidStakerAmt"), avaxLiquidStakerAmt);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::335 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Launched));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::360 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Staking));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::361 => setBytes32(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".txID")), txID);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::362 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".startTime")), startTime);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::365 => uint256 initialStartTime = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".initialStartTime")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::367 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".initialStartTime")), startTime);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::370 => address owner = getAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".owner")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::371 => uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::393 => uint256 startTime = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".startTime")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::398 => uint256 avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpAmt")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::399 => uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::405 => address owner = getAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".owner")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::407 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Withdrawable));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::408 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".endTime")), endTime);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::409 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxTotalRewardAmt")), avaxTotalRewardAmt);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::420 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpRewardAmt")), avaxNodeOpRewardAmt);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::421 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerRewardAmt")), avaxLiquidStakerRewardAmt);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::433 => subUint(keccak256("MinipoolManager.TotalAVAXLiquidStakerAmt"), avaxLiquidStakerAmt);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::451 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpAmt")), compoundedAvaxNodeOpAmt);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::452 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")), compoundedAvaxNodeOpAmt);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::475 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Prelaunch));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::488 => address owner = getAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".owner")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::489 => uint256 avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpAmt")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::490 => uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::496 => setBytes32(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".errorCode")), errorCode);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::497 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Error));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::498 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxTotalRewardAmt")), 0);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::499 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpRewardAmt")), 0);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::500 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerRewardAmt")), 0);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::512 => subUint(keccak256("MinipoolManager.TotalAVAXLiquidStakerAmt"), avaxLiquidStakerAmt);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::522 => setBytes32(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".errorCode")), errorCode);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::531 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Finished));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::542 => return getUint(keccak256("MinipoolManager.TotalAVAXLiquidStakerAmt"));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::584 => mp.duration = getUint(keccak256(abi.encodePacked("minipool.item", index, ".duration")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::585 => mp.delegationFee = getUint(keccak256(abi.encodePacked("minipool.item", index, ".delegationFee")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::587 => mp.multisigAddr = getAddress(keccak256(abi.encodePacked("minipool.item", index, ".multisigAddr")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::588 => mp.avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxNodeOpAmt")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::589 => mp.avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxLiquidStakerAmt")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::591 => mp.initialStartTime = getUint(keccak256(abi.encodePacked("minipool.item", index, ".initialStartTime")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::592 => mp.startTime = getUint(keccak256(abi.encodePacked("minipool.item", index, ".startTime")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::594 => mp.avaxTotalRewardAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxTotalRewardAmt")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::595 => mp.errorCode = getBytes32(keccak256(abi.encodePacked("minipool.item", index, ".errorCode")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::596 => mp.avaxNodeOpInitialAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxNodeOpInitialAmt")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::597 => mp.avaxNodeOpRewardAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxNodeOpRewardAmt")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::598 => mp.avaxLiquidStakerRewardAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxLiquidStakerRewardAmt")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::599 => mp.ggpSlashAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".ggpSlashAmt")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::651 => uint256 avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxNodeOpAmt")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::652 => uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxLiquidStakerAmt")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::673 => uint256 duration = getUint(keccak256(abi.encodePacked("minipool.item", index, ".duration")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::674 => uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxLiquidStakerAmt")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::677 => setUint(keccak256(abi.encodePacked("minipool.item", index, ".ggpSlashAmt")), slashGGPAmt);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::689 => setUint(keccak256(abi.encodePacked("minipool.item", index, ".startTime")), 0);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::691 => setUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxTotalRewardAmt")), 0);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::692 => setUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxNodeOpRewardAmt")), 0);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::693 => setUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxLiquidStakerRewardAmt")), 0);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::694 => setUint(keccak256(abi.encodePacked("minipool.item", index, ".ggpSlashAmt")), 0);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::695 => setBytes32(keccak256(abi.encodePacked("minipool.item", index, ".errorCode")), 0);
2022-12-gogopool/contracts/contract/MultisigManager.sol::61 => setBool(keccak256(abi.encodePacked("multisig.item", multisigIndex, ".enabled")), true);
2022-12-gogopool/contracts/contract/MultisigManager.sol::74 => setBool(keccak256(abi.encodePacked("multisig.item", multisigIndex, ".enabled")), false);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::30 => setUint(keccak256("ProtocolDAO.RewardsEligibilityMinSeconds"), 14 days);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::34 => setUint(keccak256("ProtocolDAO.TotalGGPCirculatingSupply"), 18_000_000 ether);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::35 => setUint(keccak256("ProtocolDAO.ClaimingContractPct.ClaimMultisig"), 0.20 ether);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::36 => setUint(keccak256("ProtocolDAO.ClaimingContractPct.ClaimNodeOp"), 0.70 ether);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::37 => setUint(keccak256("ProtocolDAO.ClaimingContractPct.ClaimProtocolDAO"), 0.10 ether);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::40 => setUint(keccak256("ProtocolDAO.InflationIntervalSeconds"), 1 days);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::41 => setUint(keccak256("ProtocolDAO.InflationIntervalRate"), 1000133680617113500); // 5% annual calculated on a daily interval - Calculate in js example: let dailyInflation = web3.utils.toBN((1 + 0.05) ** (1 / (365)) * 1e18);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::44 => setUint(keccak256("ProtocolDAO.TargetGGAVAXReserveRate"), 0.1 ether); // 10% collateral held in reserve
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::47 => setUint(keccak256("ProtocolDAO.MinipoolMinAVAXStakingAmt"), 2_000 ether);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::48 => setUint(keccak256("ProtocolDAO.MinipoolNodeCommissionFeePct"), 0.15 ether);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::49 => setUint(keccak256("ProtocolDAO.MinipoolMaxAVAXAssignment"), 10_000 ether);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::50 => setUint(keccak256("ProtocolDAO.MinipoolMinAVAXAssignment"), 1_000 ether);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::51 => setUint(keccak256("ProtocolDAO.ExpectedAVAXRewardsRate"), 0.1 ether); // Annual rate as pct of 1 avax
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::52 => setUint(keccak256("ProtocolDAO.MinipoolCancelMoratoriumSeconds"), 5 days);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::55 => setUint(keccak256("ProtocolDAO.MaxCollateralizationRatio"), 1.5 ether);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::56 => setUint(keccak256("ProtocolDAO.MinCollateralizationRatio"), 0.1 ether);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::82 => return getUint(keccak256("ProtocolDAO.RewardsEligibilityMinSeconds"));
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::92 => return getUint(keccak256("ProtocolDAO.TotalGGPCirculatingSupply"));
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::97 => return setUint(keccak256("ProtocolDAO.TotalGGPCirculatingSupply"), amount);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::117 => uint256 rate = getUint(keccak256("ProtocolDAO.InflationIntervalRate"));
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::124 => return getUint(keccak256("ProtocolDAO.InflationIntervalSeconds"));
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::131 => return getUint(keccak256("ProtocolDAO.MinipoolMinAVAXStakingAmt"));
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::136 => return getUint(keccak256("ProtocolDAO.MinipoolNodeCommissionFeePct"));
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::141 => return getUint(keccak256("ProtocolDAO.MinipoolMaxAVAXAssignment"));
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::146 => return getUint(keccak256("ProtocolDAO.MinipoolMinAVAXAssignment"));
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::151 => return getUint(keccak256("ProtocolDAO.MinipoolCancelMoratoriumSeconds"));
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::157 => setUint(keccak256("ProtocolDAO.ExpectedAVAXRewardsRate"), rate);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::162 => return getUint(keccak256("ProtocolDAO.ExpectedAVAXRewardsRate"));
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::169 => return getUint(keccak256("ProtocolDAO.MaxCollateralizationRatio"));
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::174 => return getUint(keccak256("ProtocolDAO.MinCollateralizationRatio"));
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::182 => return getUint(keccak256("ProtocolDAO.TargetGGAVAXReserveRate"));
2022-12-gogopool/contracts/contract/RewardsPool.sol::12 => import {FixedPointMathLib} from "@rari-capital/solmate/src/utils/FixedPointMathLib.sol";
2022-12-gogopool/contracts/contract/RewardsPool.sol::40 => setUint(keccak256("RewardsPool.RewardsCycleStartTime"), block.timestamp);
2022-12-gogopool/contracts/contract/RewardsPool.sol::41 => setUint(keccak256("RewardsPool.InflationIntervalStartTime"), block.timestamp);
2022-12-gogopool/contracts/contract/RewardsPool.sol::49 => return getUint(keccak256("RewardsPool.InflationIntervalStartTime"));
2022-12-gogopool/contracts/contract/RewardsPool.sol::98 => addUint(keccak256("RewardsPool.InflationIntervalStartTime"), inflationIntervalElapsedSeconds);
2022-12-gogopool/contracts/contract/RewardsPool.sol::116 => return getUint(keccak256("RewardsPool.RewardsCycleStartTime"));
2022-12-gogopool/contracts/contract/RewardsPool.sol::164 => setUint(keccak256("RewardsPool.RewardsCycleStartTime"), block.timestamp);
2022-12-gogopool/contracts/contract/Staking.sol::11 => import {ERC20} from "@rari-capital/solmate/src/mixins/ERC4626.sol";
2022-12-gogopool/contracts/contract/Staking.sol::12 => import {FixedPointMathLib} from "@rari-capital/solmate/src/utils/FixedPointMathLib.sol";
2022-12-gogopool/contracts/contract/Staking.sol::13 => import {SafeTransferLib} from "@rari-capital/solmate/src/utils/SafeTransferLib.sol";
2022-12-gogopool/contracts/contract/Staking.sol::82 => return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpStaked")));
2022-12-gogopool/contracts/contract/Staking.sol::89 => addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpStaked")), amount);
2022-12-gogopool/contracts/contract/Staking.sol::96 => subUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpStaked")), amount);
2022-12-gogopool/contracts/contract/Staking.sol::105 => return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxStaked")));
2022-12-gogopool/contracts/contract/Staking.sol::112 => addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxStaked")), amount);
2022-12-gogopool/contracts/contract/Staking.sol::119 => subUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxStaked")), amount);
2022-12-gogopool/contracts/contract/Staking.sol::128 => return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssigned")));
2022-12-gogopool/contracts/contract/Staking.sol::135 => addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssigned")), amount);
2022-12-gogopool/contracts/contract/Staking.sol::142 => subUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssigned")), amount);
2022-12-gogopool/contracts/contract/Staking.sol::151 => return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssignedHighWater")));
2022-12-gogopool/contracts/contract/Staking.sol::158 => addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssignedHighWater")), amount);
2022-12-gogopool/contracts/contract/Staking.sol::165 => uint256 currAVAXAssigned = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssigned")));
2022-12-gogopool/contracts/contract/Staking.sol::166 => setUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssignedHighWater")), currAVAXAssigned);
2022-12-gogopool/contracts/contract/Staking.sol::175 => return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".minipoolCount")));
2022-12-gogopool/contracts/contract/Staking.sol::182 => addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".minipoolCount")), 1);
2022-12-gogopool/contracts/contract/Staking.sol::189 => subUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".minipoolCount")), 1);
2022-12-gogopool/contracts/contract/Staking.sol::198 => return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".rewardsStartTime")));
2022-12-gogopool/contracts/contract/Staking.sol::210 => setUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".rewardsStartTime")), time);
2022-12-gogopool/contracts/contract/Staking.sol::219 => return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpRewards")));
2022-12-gogopool/contracts/contract/Staking.sol::226 => addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpRewards")), amount);
2022-12-gogopool/contracts/contract/Staking.sol::233 => subUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpRewards")), amount);
2022-12-gogopool/contracts/contract/Staking.sol::242 => return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".lastRewardsCycleCompleted")));
2022-12-gogopool/contracts/contract/Staking.sol::250 => setUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".lastRewardsCycleCompleted")), cycleNumber);
2022-12-gogopool/contracts/contract/Staking.sol::351 => setAddress(keccak256(abi.encodePacked("staker.item", stakerIndex, ".stakerAddr")), stakerAddr);
2022-12-gogopool/contracts/contract/Staking.sol::406 => staker.ggpStaked = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpStaked")));
2022-12-gogopool/contracts/contract/Staking.sol::407 => staker.avaxAssigned = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssigned")));
2022-12-gogopool/contracts/contract/Staking.sol::408 => staker.avaxStaked = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxStaked")));
2022-12-gogopool/contracts/contract/Staking.sol::409 => staker.stakerAddr = getAddress(keccak256(abi.encodePacked("staker.item", stakerIndex, ".stakerAddr")));
2022-12-gogopool/contracts/contract/Staking.sol::410 => staker.minipoolCount = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".minipoolCount")));
2022-12-gogopool/contracts/contract/Staking.sol::411 => staker.rewardsStartTime = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".rewardsStartTime")));
2022-12-gogopool/contracts/contract/Staking.sol::412 => staker.ggpRewards = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpRewards")));
2022-12-gogopool/contracts/contract/Staking.sol::413 => staker.lastRewardsCycleCompleted = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".lastRewardsCycleCompleted")));
2022-12-gogopool/contracts/contract/Vault.sol::10 => import {ERC20} from "@rari-capital/solmate/src/tokens/ERC20.sol";
2022-12-gogopool/contracts/contract/Vault.sol::11 => import {ReentrancyGuard} from "@rari-capital/solmate/src/utils/ReentrancyGuard.sol";
2022-12-gogopool/contracts/contract/Vault.sol::12 => import {SafeTransferLib} from "@rari-capital/solmate/src/utils/SafeTransferLib.sol";
2022-12-gogopool/contracts/contract/tokens/TokenGGP.sol::4 => import {ERC20} from "@rari-capital/solmate/src/tokens/ERC20.sol";
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::7 => import {ERC20Upgradeable} from "./upgradeable/ERC20Upgradeable.sol";
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::8 => import {ERC4626Upgradeable} from "./upgradeable/ERC4626Upgradeable.sol";
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::15 => import {Initializable} from "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::16 => import {UUPSUpgradeable} from "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::18 => import {ERC20} from "@rari-capital/solmate/src/mixins/ERC4626.sol";
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::19 => import {FixedPointMathLib} from "@rari-capital/solmate/src/utils/FixedPointMathLib.sol";
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::20 => import {SafeCastLib} from "@rari-capital/solmate/src/utils/SafeCastLib.sol";
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::21 => import {SafeTransferLib} from "@rari-capital/solmate/src/utils/SafeTransferLib.sol";
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::73 => __ERC4626Upgradeable_init(asset, "GoGoPool Liquid Staking Token", "ggAVAX");
```
#### Tools used
Manual code review

### 5th Bug
Use Shift Right/Left instead of Division/Multiplication if possible

#### Impact
Issue Information: 
A division/multiplication by any number x being a power of 2 can be calculated by shifting log2(x) to the right/left.

While the DIV opcode uses 5 gas, the SHR opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

Example
🤦 Bad:

uint256 b = a / 2;
uint256 c = a / 4;
uint256 d = a * 8;
🚀 Good:

uint256 b = a >> 1;
uint256 c = a >> 2;
uint256 d = a << 3;

#### Findings:
```
2022-12-gogopool/contracts/contract/MinipoolManager.sol::413 => uint256 avaxHalfRewards = avaxTotalRewardAmt / 2;
```
#### Tools used
Manual code review


### 6th Bug
USING BOOLS FOR STORAGE INCURS OVERHEAD

#### Impact:
when changing from false to true it costs Gwarmaccess (100 gas) for the extra SLOAD

### Description:
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from false to true, after having been true in the past.

#### Findings:
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L61

####Tools Used
Manual code review