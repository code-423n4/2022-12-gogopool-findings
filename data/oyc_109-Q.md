

## [L-01] Use of Block.timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

```
2022-12-gogopool/contracts/contract/ClaimNodeOp.sol::49 => uint256 elapsedSecs = (block.timestamp - rewardsStartTime);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::226 => staking.setRewardsStartTime(msg.sender, block.timestamp);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::279 => if (block.timestamp - staking.getRewardsStartTime(msg.sender) < dao.getMinipoolCancelMoratoriumSeconds()) {
2022-12-gogopool/contracts/contract/MinipoolManager.sol::356 => if (startTime > block.timestamp) {
2022-12-gogopool/contracts/contract/MinipoolManager.sol::394 => if (endTime <= startTime || endTime > block.timestamp) {
2022-12-gogopool/contracts/contract/Oracle.sol::13 => Oracle.GGPTimestamp = block.timestamp of last update to GGP price
2022-12-gogopool/contracts/contract/Oracle.sol::40 => timestamp = block.timestamp;
2022-12-gogopool/contracts/contract/Oracle.sol::59 => if (timestamp < lastTimestamp || timestamp > block.timestamp) {
2022-12-gogopool/contracts/contract/RewardsPool.sol::40 => setUint(keccak256("RewardsPool.RewardsCycleStartTime"), block.timestamp);
2022-12-gogopool/contracts/contract/RewardsPool.sol::41 => setUint(keccak256("RewardsPool.InflationIntervalStartTime"), block.timestamp);
2022-12-gogopool/contracts/contract/RewardsPool.sol::60 => return (block.timestamp - startTime) / dao.getInflationIntervalSeconds();
2022-12-gogopool/contracts/contract/RewardsPool.sol::84 => uint256 inflationIntervalElapsedSeconds = (block.timestamp - getInflationIntervalStartTime());
2022-12-gogopool/contracts/contract/RewardsPool.sol::128 => return (block.timestamp - startTime) / dao.getRewardsCycleSeconds();
2022-12-gogopool/contracts/contract/RewardsPool.sol::164 => setUint(keccak256("RewardsPool.RewardsCycleStartTime"), block.timestamp);
2022-12-gogopool/contracts/contract/Staking.sol::206 => if (time > block.timestamp) {
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::78 => rewardsCycleEnd = (block.timestamp.safeCastTo32() / rewardsCycleLength) * rewardsCycleLength;
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::89 => uint32 timestamp = block.timestamp.safeCastTo32();
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::120 => if (block.timestamp >= rewardsCycleEnd_) {
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::128 => uint256 unlockedRewards = (lastRewardsAmt_ * (block.timestamp - lastSync_)) / (rewardsCycleEnd_ - lastSync_);
```

## [L-02] Open TODOs

Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

```
2022-12-gogopool/contracts/contract/BaseAbstract.sol::6 => // TODO remove this when dev is complete
2022-12-gogopool/contracts/contract/MinipoolManager.sol::412 => // TODO Revisit this logic if we ever allow unequal matched funds
2022-12-gogopool/contracts/contract/Staking.sol::203 => // TODO cant use onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) since we also call from increaseMinipoolCount. Wat do?
```

## [L-03] abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()

Use abi.encode() instead which will pad items to 32 bytes, which will prevent hash collisions (e.g. abi.encodePacked(0x123,0x456) => 0x123456 => abi.encodePacked(0x1,0x23456), but abi.encode(0x123,0x456) => 0x0...1230...456). Unless there is a compelling reason, abi.encode should be preferred. If there is only one argument to abi.encodePacked() it can often be cast to bytes() or bytes32() instead.

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
2022-12-gogopool/contracts/contract/MinipoolManager.sol::116 => address owner = getAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".owner")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::130 => address assignedMultisig = getAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".multisigAddr")));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::153 => bytes32 statusKey = keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status"));
2022-12-gogopool/contracts/contract/MinipoolManager.sol::246 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".initialStartTime")), 0);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::250 => setUint(keccak256(abi.encodePacked("minipool.index", nodeID)), uint256(minipoolIndex + 1));
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
2022-12-gogopool/contracts/contract/MinipoolManager.sol::522 => setBytes32(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".errorCode")), errorCode);
2022-12-gogopool/contracts/contract/MinipoolManager.sol::531 => setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Finished));
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
2022-12-gogopool/contracts/contract/MultisigManager.sol::45 => setAddress(keccak256(abi.encodePacked("multisig.item", index, ".address")), addr);
2022-12-gogopool/contracts/contract/MultisigManager.sol::48 => setUint(keccak256(abi.encodePacked("multisig.index", addr)), index + 1);
2022-12-gogopool/contracts/contract/MultisigManager.sol::61 => setBool(keccak256(abi.encodePacked("multisig.item", multisigIndex, ".enabled")), true);
2022-12-gogopool/contracts/contract/MultisigManager.sol::74 => setBool(keccak256(abi.encodePacked("multisig.item", multisigIndex, ".enabled")), false);
2022-12-gogopool/contracts/contract/MultisigManager.sol::97 => return int256(getUint(keccak256(abi.encodePacked("multisig.index", addr)))) - 1;
2022-12-gogopool/contracts/contract/MultisigManager.sol::110 => addr = getAddress(keccak256(abi.encodePacked("multisig.item", index, ".address")));
2022-12-gogopool/contracts/contract/MultisigManager.sol::111 => enabled = (addr != address(0)) && getBool(keccak256(abi.encodePacked("multisig.item", index, ".enabled")));
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::62 => return getBool(keccak256(abi.encodePacked("contract.paused", contractName)));
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::68 => setBool(keccak256(abi.encodePacked("contract.paused", contractName)), true);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::74 => setBool(keccak256(abi.encodePacked("contract.paused", contractName)), false);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::103 => return getUint(keccak256(abi.encodePacked("ProtocolDAO.ClaimingContractPct.", claimingContract)));
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::108 => setUint(keccak256(abi.encodePacked("ProtocolDAO.ClaimingContractPct.", claimingContract)), decimal);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::191 => setBool(keccak256(abi.encodePacked("contract.exists", addr)), true);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::192 => setAddress(keccak256(abi.encodePacked("contract.address", name)), addr);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::193 => setString(keccak256(abi.encodePacked("contract.name", addr)), name);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::200 => deleteBool(keccak256(abi.encodePacked("contract.exists", addr)));
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::201 => deleteAddress(keccak256(abi.encodePacked("contract.address", name)));
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::202 => deleteString(keccak256(abi.encodePacked("contract.name", addr)));
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

## [L-04] require() should be used instead of assert()

require() should be used for checking error conditions on inputs and return values while assert() should be used for invariant checking

```
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::83 => assert(msg.sender == address(asset));
```

## [L-05] approve should be replaced with safeApprove or safeIncreaseAllowance() / safeDecreaseAllowance()

approve is subject to a known front-running attack. Consider using safeApprove() or safeIncreaseAllowance() or safeDecreaseAllowance() instead

```
2022-12-gogopool/contracts/contract/ClaimNodeOp.sol::105 => ggp.approve(address(staking), restakeAmt);
2022-12-gogopool/contracts/contract/Staking.sol::342 => ggp.approve(address(vault), amount);
```

## [L-06] Events not emitted for important state changes

When changing state variables events are not emitted. Emitting events allows monitoring activities with off-chain monitoring tools.

```
2022-12-gogopool/contracts/contract/BaseAbstract.sol::135 => function setAddress(bytes32 key, address value) internal {
2022-12-gogopool/contracts/contract/BaseAbstract.sol::139 => function setBool(bytes32 key, bool value) internal {
2022-12-gogopool/contracts/contract/BaseAbstract.sol::143 => function setBytes(bytes32 key, bytes memory value) internal {
2022-12-gogopool/contracts/contract/BaseAbstract.sol::151 => function setInt(bytes32 key, int256 value) internal {
2022-12-gogopool/contracts/contract/BaseAbstract.sol::155 => function setUint(bytes32 key, uint256 value) internal {
2022-12-gogopool/contracts/contract/BaseAbstract.sol::159 => function setString(bytes32 key, string memory value) internal {
2022-12-gogopool/contracts/contract/ClaimNodeOp.sol::40 => function setRewardsCycleTotal(uint256 amount) public onlySpecificRegisteredContract("RewardsPool", msg.sender) {
2022-12-gogopool/contracts/contract/Oracle.sol::28 => function setOneInch(address addr) external onlyGuardian {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::96 => function setTotalGGPCirculatingSupply(uint256 amount) public onlySpecificRegisteredContract("RewardsPool", msg.sender) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::107 => function setClaimingContractPct(string memory claimingContract, uint256 decimal) public onlyGuardian valueNotGreaterThanOne(decimal) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::156 => function setExpectedAVAXRewardsRate(uint256 rate) public onlyMultisig valueNotGreaterThanOne(rate) {
2022-12-gogopool/contracts/contract/Staking.sol::204 => function setRewardsStartTime(address stakerAddr, uint256 time) public onlyRegisteredNetworkContract {
2022-12-gogopool/contracts/contract/Staking.sol::248 => function setLastRewardsCycleCompleted(address stakerAddr, uint256 cycleNumber) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {
2022-12-gogopool/contracts/contract/Storage.sol::41 => function setGuardian(address newAddress) external {
2022-12-gogopool/contracts/contract/Storage.sol::104 => function setAddress(bytes32 key, address value) external onlyRegisteredNetworkContract {
2022-12-gogopool/contracts/contract/Storage.sol::108 => function setBool(bytes32 key, bool value) external onlyRegisteredNetworkContract {
2022-12-gogopool/contracts/contract/Storage.sol::112 => function setBytes(bytes32 key, bytes calldata value) external onlyRegisteredNetworkContract {
2022-12-gogopool/contracts/contract/Storage.sol::120 => function setInt(bytes32 key, int256 value) external onlyRegisteredNetworkContract {
2022-12-gogopool/contracts/contract/Storage.sol::124 => function setString(bytes32 key, string calldata value) external onlyRegisteredNetworkContract {
2022-12-gogopool/contracts/contract/Storage.sol::128 => function setUint(bytes32 key, uint256 value) external onlyRegisteredNetworkContract {
```

## [L-07] _safeMint() should be used rather than _mint() wherever possible

Some tokens do not implement the ERC20 standard properly but are still accepted by most code that accepts ERC20 tokens. For example Tether (USDT)'s transfer() and transferFrom() functions do not return booleans as the specification requires, and instead have no return value. When these sorts of tokens are cast to IERC20, their function signatures do not match and therefore the calls made, revert. Use OpenZeppelin’s SafeERC20's safeTransfer()/safeTransferFrom() instead

```
2022-12-gogopool/contracts/contract/tokens/TokenGGP.sol::13 => _mint(msg.sender, TOTAL_SUPPLY);
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::176 => _mint(msg.sender, shares);
```

## [L-08] Upgradeable contract is missing a __gap[50] storage variable to allow for new storage variables in later versions

__gap is empty reserved space in storage that is recommended to be put in place in upgradeable contracts. It allows new state variables to be added in the future without compromising the storage compatibility with existing deployments

```
2022-12-gogopool/contracts/contract/BaseUpgradeable.sol::7 => import {Initializable} from "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::15 => import {Initializable} from "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
```

## [L-09] Implementation contract may not be initialized

Implementation contract does not have a constructor with the initializer modifier therefore may be uninitialized. Implementation contracts should be initialized to avoid potential griefs or exploits.

```
2022-12-gogopool/contracts/contract/BaseUpgradeable.sol::7 => import {Initializable} from "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
```

## [N-1] Event is missing indexed fields

Each event should use three indexed fields if there are three or more fields

```
2022-12-gogopool/contracts/contract/RewardsPool.sol::24 => event GGPInflated(uint256 newTokens);
2022-12-gogopool/contracts/contract/RewardsPool.sol::25 => event NewRewardsCycleStarted(uint256 totalRewardsAmt);
2022-12-gogopool/contracts/contract/RewardsPool.sol::26 => event ClaimNodeOpRewardsTransfered(uint256 value);
2022-12-gogopool/contracts/contract/RewardsPool.sol::27 => event ProtocolDAORewardsTransfered(uint256 value);
2022-12-gogopool/contracts/contract/RewardsPool.sol::28 => event MultisigRewardsTransfered(uint256 value);
2022-12-gogopool/contracts/contract/Storage.sol::12 => event GuardianChanged(address oldGuardian, address newGuardian);
```

## [N-2] Missing NatSpec

Code should include NatSpec

```
2022-12-gogopool/contracts/contract/BaseUpgradeable.sol::1 => // SPDX-License-Identifier: GPL-3.0-only
2022-12-gogopool/contracts/contract/tokens/TokenGGP.sol::1 => // SPDX-License-Identifier: GPL-3.0-only
```

## [N-3] Missing event for critical parameter change

Emitting events after sensitive changes take place, to facilitate tracking and notify off-chain clients following changes to the contract.

```
2022-12-gogopool/contracts/contract/BaseAbstract.sol::135 => function setAddress(bytes32 key, address value) internal {
2022-12-gogopool/contracts/contract/BaseAbstract.sol::139 => function setBool(bytes32 key, bool value) internal {
2022-12-gogopool/contracts/contract/BaseAbstract.sol::143 => function setBytes(bytes32 key, bytes memory value) internal {
2022-12-gogopool/contracts/contract/BaseAbstract.sol::151 => function setInt(bytes32 key, int256 value) internal {
2022-12-gogopool/contracts/contract/BaseAbstract.sol::155 => function setUint(bytes32 key, uint256 value) internal {
2022-12-gogopool/contracts/contract/BaseAbstract.sol::159 => function setString(bytes32 key, string memory value) internal {
2022-12-gogopool/contracts/contract/ClaimNodeOp.sol::40 => function setRewardsCycleTotal(uint256 amount) public onlySpecificRegisteredContract("RewardsPool", msg.sender) {
2022-12-gogopool/contracts/contract/Oracle.sol::28 => function setOneInch(address addr) external onlyGuardian {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::96 => function setTotalGGPCirculatingSupply(uint256 amount) public onlySpecificRegisteredContract("RewardsPool", msg.sender) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::107 => function setClaimingContractPct(string memory claimingContract, uint256 decimal) public onlyGuardian valueNotGreaterThanOne(decimal) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::156 => function setExpectedAVAXRewardsRate(uint256 rate) public onlyMultisig valueNotGreaterThanOne(rate) {
2022-12-gogopool/contracts/contract/Staking.sol::204 => function setRewardsStartTime(address stakerAddr, uint256 time) public onlyRegisteredNetworkContract {
2022-12-gogopool/contracts/contract/Staking.sol::248 => function setLastRewardsCycleCompleted(address stakerAddr, uint256 cycleNumber) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {
2022-12-gogopool/contracts/contract/Storage.sol::41 => function setGuardian(address newAddress) external {
2022-12-gogopool/contracts/contract/Storage.sol::104 => function setAddress(bytes32 key, address value) external onlyRegisteredNetworkContract {
2022-12-gogopool/contracts/contract/Storage.sol::108 => function setBool(bytes32 key, bool value) external onlyRegisteredNetworkContract {
2022-12-gogopool/contracts/contract/Storage.sol::112 => function setBytes(bytes32 key, bytes calldata value) external onlyRegisteredNetworkContract {
2022-12-gogopool/contracts/contract/Storage.sol::120 => function setInt(bytes32 key, int256 value) external onlyRegisteredNetworkContract {
2022-12-gogopool/contracts/contract/Storage.sol::124 => function setString(bytes32 key, string calldata value) external onlyRegisteredNetworkContract {
2022-12-gogopool/contracts/contract/Storage.sol::128 => function setUint(bytes32 key, uint256 value) external onlyRegisteredNetworkContract {
```

## [N-4] Lines are too long

Usually lines in source code are limited to 80 characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length

```
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::41 => setUint(keccak256("ProtocolDAO.InflationIntervalRate"), 1000133680617113500); // 5% annual calculated on a daily interval - Calculate in js example: let dailyInflation = web3.utils.toBN((1 + 0.05) ** (1 / (365)) * 1e18);
```

