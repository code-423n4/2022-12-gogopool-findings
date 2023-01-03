## Table of Contents
- Should Use Unchecked Block where Over/Underflow is not Possible
- Minimize the Number of SLOADs by Caching State Variable
- Defined Variables Used Only Once
- Redundant Use of Variable
- X = X + Y costs less gass than X += Y
- Use require instead of &&
- Use Bit Shifting Instead of Multiplication/Division of 2
- Boolean Comparisons
- Using Elements Smaller than 32 bytes (256 bits) Might Use More Gas
- Empty Blocks Should Emit Something or be Removed
- Use Custom Errors to Save Gas

&ensp;
### Should Use Unchecked Block where Over/Underflow is not Possible

#### Issue
Since Solidity 0.8.0, all arithmetic operations revert on overflow and underflow by default.
However in places where overflow and underflow is not possible, it is better to use unchecked block to reduce the gas usage.
Reference: https://docs.soliditylang.org/en/v0.8.15/control-structures.html#checked-or-unchecked-arithmetic

#### PoC
1. ClaimNodeOp.sol:claimAndRestake()
```solidity
Wrap line 102 with unchecked since underflow is not possible due to line 95 check
 95:		if (claimAmt > ggpRewards) {
102:		uint256 restakeAmt = ggpRewards - claimAmt;
```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimNodeOp.sol#L95-L102

2. MinipoolManager.sol:getMinipools()
```solidity
Wrap line 617 with unchecked since underflow is not possible due to line 613
613:		uint256 max = offset + limit;
617:		minipools = new Minipool[](max - offset);
```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L613-L617

3. TokenggAVAX.sol:depositFromStaking()
```solidity
Wrap line 149 with unchecked since underflow is not possible due to line 144 check
144:		if (totalAmt != (baseAmt + rewardAmt) || baseAmt > stakingTotalAssets) {
149:		stakingTotalAssets -= baseAmt;
```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L144-L149

#### Mitigation
Wrap the code with uncheck like described in above PoC. 

&ensp;
### Minimize the Number of SLOADs by Caching State Variable

#### Issue
SLOADs cost 100 gas where MLOADs/MSTOREs cost only 3 gas.
Whenever function reads storage value more than once, it should be cached to save gas.

#### PoC

1. Cache `rewardsCycleLength` of TokenggAVAX.sol:initialize()
2 SLOADs to 1 SLOAD, 1 MSTORE and 2 MLOAD
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L78

2. Cache `rewardsCycleLength` of TokenggAVAX.sol:syncRewards()
3 SLOADs to 1 SLOAD, 1 MSTORE and 3 MLOAD
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L102

#### Mitigation
When certain state variable is read more than once, cache it to local variable to save gas.

&ensp;
### Defined Variables Used Only Once

#### Issue
Certain variables is defined even though they are used only once.
Remove these unnecessary variables to save gas.
For cases where it will reduce the readability, one can use comments to help describe
what the code is doing.

#### PoC
1. Remove `assignedMultisig` variable of onlyValidMultisig() of MinipoolManager.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L130-L131
Mitigation:
Delete line 130 and replace line 131 like shown below
```solidity
		if (msg.sender != getAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".multisigAddr")))) {
```

2. Remove `statusKey` variable of requireValidStateTransition() of MinipoolManager.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L153-L154
Mitigation:
Delete line 153 and replace line 154 like shown below
```solidity
		MinipoolStatus currentStatus = MinipoolStatus(getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status"))));
```

3. Remove `ratio` variable of createMinipool() of MinipoolManager.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L229-L230
Mitigation:
Delete line 229 and replace line 230 like shown below
```solidity
		if (staking.getCollateralizationRatio(msg.sender) < dao.getMinCollateralizationRatio()) {
```

4. Remove `multisigManager` variable of createMinipool() of MinipoolManager.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L235-L236
Mitigation:
Delete line 235 and replace line 236 like shown below
```solidity
		address multisig = MultisigManager(getContractAddress("MultisigManager")).requireNextActiveMultisig();
```

5. Remove `vault` variable of createMinipool() of MinipoolManager.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L267-L268
Mitigation:
Delete line 267 and replace line 268 like shown below
```solidity
		Vault(getContractAddress("Vault")).depositAVAX{value: msg.value}();
```

6. Remove `staking` variable of cancelMinipool() of MinipoolManager.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L274-L279
Mitigation:
Delete line 274 and replace line 279 like shown below
```solidity
		if (block.timestamp - Staking(getContractAddress("Staking")).getRewardsStartTime(msg.sender) < dao.getMinipoolCancelMoratoriumSeconds()) {
```

7. Remove `dao` variable of cancelMinipool() of MinipoolManager.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L275-L279
Mitigation:
Delete line 275 and replace line 279 like shown below
```solidity
		if (block.timestamp - staking.getRewardsStartTime(msg.sender) < ProtocolDAO(getContractAddress("ProtocolDAO")).getMinipoolCancelMoratoriumSeconds()) {
```

8. Remove `avaxNodeOpRewardAmt` variable of withdrawMinipoolFunds() of MinipoolManager.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L294-L295
Mitigation:
Delete line 294 and replace line 295 like shown below
```solidity
		uint256 totalAvaxAmt = avaxNodeOpAmt + getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpRewardAmt")));
```

9. Remove `staking` variable of withdrawMinipoolFunds() of MinipoolManager.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L297-L298
Mitigation:
Delete line 297 and replace line 298 like shown below
```solidity
		Staking(getContractAddress("Staking")).decreaseAVAXStake(owner, avaxNodeOpAmt);
```

10. Remove `vault` variable of withdrawMinipoolFunds() of MinipoolManager.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L300-L301
Mitigation:
Delete line 300 and replace line 301 like shown below
```solidity
		Vault(getContractAddress("Vault")).withdrawAVAX(totalAvaxAmt);
```

11. Remove `avaxLiquidStakerAmt` variable of canClaimAndInitiateStaking() of MinipoolManager.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L317-L318
Mitigation:
Delete line 317 and replace line 318 like shown below
```solidity
		return getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt"))) <= ggAVAX.amountAvailableForStaking();
```

12. Remove `vault` variable of claimAndInitiateStaking() of MinipoolManager.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L338-L339
Mitigation:
Delete line 338 and replace line 339 like shown below
```solidity
		Vault(getContractAddress("Vault")).withdrawAVAX(avaxNodeOpAmt);
```

13. Remove `totalAvaxAmt` variable of claimAndInitiateStaking() of MinipoolManager.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L341-L342
Mitigation:
Delete line 341 and replace line 342 like shown below
```solidity
		msg.sender.safeTransferETH(avaxNodeOpAmt + avaxLiquidStakerAmt);
```

14. Remove `initialStartTime` variable of recordStakingStart() of MinipoolManager.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L365-L366
Mitigation:
Delete line 365 and replace line 366 like shown below
```solidity
		if (getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".initialStartTime"))) == 0) {
```

15. Remove `avaxLiquidStakerAmt` variable of recordStakingStart() of MinipoolManager.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L371-L374
Mitigation:
Delete line 371 and replace line 374 like shown below
```solidity
			staking.increaseAVAXAssignedHighWater(owner, getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt"))));
```

16. Remove `startTime` variable of recordStakingEnd() of MinipoolManager.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L393-L394
Mitigation:
Delete line 393 and replace line 394 like shown below
```solidity
		if (endTime <= getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".startTime"))) || endTime > block.timestamp) {
```

17. Remove `totalAvaxAmt` variable of recordStakingEnd() of MinipoolManager.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L400-L401
Mitigation:
Delete line 400 and replace line 401 like shown below
```solidity
		if (msg.value != avaxNodeOpAmt + avaxLiquidStakerAmt + avaxTotalRewardAmt) {
```

18. Remove `dao` variable of recordStakingEnd() of MinipoolManager.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L416-L417
Mitigation:
Delete line 416 and replace line 417 like shown below
```solidity
		uint256 avaxLiquidStakerRewardAmt = avaxHalfRewards - avaxHalfRewards.mulWadDown(ProtocolDAO(getContractAddress("ProtocolDAO")).getMinipoolNodeCommissionFeePct());
```

19. Remove `vault` variable of recordStakingEnd() of MinipoolManager.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L429-L430
Mitigation:
Delete line 429 and replace line 430 like shown below
```solidity
		Vault(getContractAddress("Vault")).depositAVAX{value: avaxNodeOpAmt + avaxNodeOpRewardAmt}();
```

20. Remove `dao` variable of recreateMinipool() of MinipoolManager.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L467-L469
Mitigation:
Delete line 467 and replace line 469 like shown below
```solidity
		if (ratio < ProtocolDAO(getContractAddress("ProtocolDAO")).getMinCollateralizationRatio()) {
```

21. Remove `ratio` variable of recreateMinipool() of MinipoolManager.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L468-L469
Mitigation:
Delete line 468 and replace line 469 like shown below
```solidity
		if (staking.getCollateralizationRatio(mp.owner) < dao.getMinCollateralizationRatio()) {
```

22. Remove `owner` variable of recordStakingError() of MinipoolManager.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L488-L510
Mitigation:
Delete line 488 and replace line 510 like shown below
```solidity
		staking.decreaseAVAXAssigned(getAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".owner"))), avaxLiquidStakerAmt);
```

23. Remove `vault` variable of recordStakingError() of MinipoolManager.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L503-L504
Mitigation:
Delete line 503 and replace line 504 like shown below
```solidity
		Vault(getContractAddress("Vault")).depositAVAX{value: avaxNodeOpAmt}();
```

24. Remove `staking` variable of recordStakingError() of MinipoolManager.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L509-L510
Mitigation:
Delete line 509 and replace line 510 like shown below
```solidity
		Staking(getContractAddress("Staking")).decreaseAVAXAssigned(owner, avaxLiquidStakerAmt);
```

25. Remove `oracle` variable of calculateGGPSlashAmt() of MinipoolManager.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L548-L549
Mitigation:
Delete line 548 and replace line 549 like shown below
```solidity
		(uint256 ggpPriceInAvax, ) = Oracle(getContractAddress("Oracle")).getGGPPriceInAVAX();
```

26. Remove `dao` variable of getExpectedAVAXRewardsAmt() of MinipoolManager.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L558-L559
Mitigation:
Delete line 558 and replace line 559 like shown below
```solidity
		uint256 rate = ProtocolDAO(getContractAddress("ProtocolDAO")).getExpectedAVAXRewardsRate();
```

27. Remove `rate` variable of getExpectedAVAXRewardsAmt() of MinipoolManager.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L559-L560
Mitigation:
Delete line 559 and replace line 560 like shown below
```solidity
		return (avaxAmt.mulWadDown(dao.getExpectedAVAXRewardsRate()) * duration) / 365 days;
```

28. Remove `index` variable of getMinipoolByNodeID() of MinipoolManager.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L573-L574
Mitigation:
Delete line 573 and replace line 574 like shown below
```solidity
		return getMinipool(getIndexOf(nodeID));
```

29. Remove `avaxLiquidStakerAmt` variable of _cancelMinipoolAndReturnFunds() of MinipoolManager.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L652-L656
Mitigation:
Delete line 652 and replace line 656 like shown below
```solidity
		staking.decreaseAVAXAssigned(owner, getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxLiquidStakerAmt"))));
```

30. Remove `vault` variable of _cancelMinipoolAndReturnFunds() of MinipoolManager.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L662-L663
Mitigation:
Delete line 662 and replace line 663 like shown below
```solidity
		Vault(getContractAddress("Vault")).withdrawAVAX(avaxNodeOpAmt);
```

31. Remove `staking` variable of slash() of MinipoolManager.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L681-L682
Mitigation:
Delete line 681 and replace line 682 like shown below
```solidity
		Staking(getContractAddress("Staking")).slashGGP(owner, slashGGPAmt);
```

32. Remove `vault` variable of getTotalGGPStake() of Staking.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L67-L68
Mitigation:
Delete line 67 and replace line 68 like shown below
```solidity
		return Vault(getContractAddress("Vault")).balanceOfToken("Staking", ggp);
```

33. Remove `stakerIndex` variable of getGGPStake() of Staking.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L81-L82
Mitigation:
Delete line 81 and replace line 82 like shown below
```solidity
		return getUint(keccak256(abi.encodePacked("staker.item", requireValidStaker(stakerAddr), ".ggpStaked")));
```

34. Remove `stakerIndex` variable of increaseGGPStake() of Staking.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L88-L89
Mitigation:
Delete line 88 and replace line 89 like shown below
```solidity
		addUint(keccak256(abi.encodePacked("staker.item", requireValidStaker(stakerAddr), ".ggpStaked")), amount);
```

35. Remove `stakerIndex` variable of decreaseGGPStake() of Staking.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L95-L96
Mitigation:
Delete line 95 and replace line 96 like shown below
```solidity
		subUint(keccak256(abi.encodePacked("staker.item", requireValidStaker(stakerAddr), ".ggpStaked")), amount);
```

36. Remove `stakerIndex` variable of getAVAXStake() of Staking.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L104-L105
Mitigation:
Delete line 104 and replace line 105 like shown below
```solidity
		return getUint(keccak256(abi.encodePacked("staker.item", requireValidStaker(stakerAddr), ".avaxStaked")));
```

37. Remove `stakerIndex` variable of increaseAVAXStake() of Staking.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L111-L112
Mitigation:
Delete line 111 and replace line 112 like shown below
```solidity
		addUint(keccak256(abi.encodePacked("staker.item", requireValidStaker(stakerAddr), ".avaxStaked")), amount);
```

38. Remove `stakerIndex` variable of decreaseAVAXStake() of Staking.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L118-L119
Mitigation:
Delete line 118 and replace line 119 like shown below
```solidity
		subUint(keccak256(abi.encodePacked("staker.item", requireValidStaker(stakerAddr), ".avaxStaked")), amount);
```

39. Remove `stakerIndex` variable of getAVAXAssigned() of Staking.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L127-L128
Mitigation:
Delete line 127 and replace line 128 like shown below
```solidity
		return getUint(keccak256(abi.encodePacked("staker.item", getIndexOf(stakerAddr), ".avaxAssigned")));
```

40. Remove `stakerIndex` variable of increaseAVAXAssigned() of Staking.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L134-L135
Mitigation:
Delete line 134 and replace line 135 like shown below
```solidity
		addUint(keccak256(abi.encodePacked("staker.item", requireValidStaker(stakerAddr), ".avaxAssigned")), amount);
```

41. Remove `stakerIndex` variable of decreaseAVAXAssigned() of Staking.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L141-L142
Mitigation:
Delete line 141 and replace line 142 like shown below
```solidity
		subUint(keccak256(abi.encodePacked("staker.item", requireValidStaker(stakerAddr), ".avaxAssigned")), amount);
```

42. Remove `stakerIndex` variable of getAVAXAssignedHighWater() of Staking.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L150-L151
Mitigation:
Delete line 150 and replace line 151 like shown below
```solidity
		return getUint(keccak256(abi.encodePacked("staker.item", getIndexOf(stakerAddr), ".avaxAssignedHighWater")));
```

43. Remove `stakerIndex` variable of increaseAVAXAssignedHighWater() of Staking.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L157-L158
Mitigation:
Delete line 157 and replace line 158 like shown below
```solidity
		addUint(keccak256(abi.encodePacked("staker.item", requireValidStaker(stakerAddr), ".avaxAssignedHighWater")), amount);
```

44. Remove `stakerIndex` variable of getMinipoolCount() of Staking.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L174-L175
Mitigation:
Delete line 174 and replace line 175 like shown below
```solidity
		return getUint(keccak256(abi.encodePacked("staker.item", getIndexOf(stakerAddr), ".minipoolCount")));
```

45. Remove `stakerIndex` variable of increaseMinipoolCount() of Staking.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L181-L182
Mitigation:
Delete line 181 and replace line 182 like shown below
```solidity
		addUint(keccak256(abi.encodePacked("staker.item", requireValidStaker(stakerAddr), ".minipoolCount")), 1);
```

46. Remove `stakerIndex` variable of decreaseMinipoolCount() of Staking.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L188-L189
Mitigation:
Delete line 188 and replace line 189 like shown below
```solidity
		subUint(keccak256(abi.encodePacked("staker.item", requireValidStaker(stakerAddr), ".minipoolCount")), 1);
```

47. Remove `stakingTotalAssets_` variable of syncRewards() of TokenggAVAX.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L97-L99
Mitigation:
Delete line 97 and replace line 99 like shown below
```solidity
		uint256 nextRewardsAmt = (asset.balanceOf(address(this)) + stakingTotalAssets) - totalReleasedAssets_ - lastRewardsAmt_;
```

#### Mitigation
Don't define variable that is used only once.
Details are listed on above PoC.

&ensp;
### Redundant Use of Variable

#### Issue
Below has redundant use of variables. 
Instead of defining a new variable, emit the event first and then update the variable.

#### PoC
1. confirmGuardian function of Storage.sol
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L56-L66
Change it to
```solidity
		emit GuardianChanged(guardian, newGuardian);
		guardian = newGuardian;
		delete newGuardian;
```

#### Mitigation
Instead of defining a new variable, emit the event first and then update the variable like shown in above PoC.

&ensp;
### X = X + Y costs less gass than X += Y

#### Issue
X = X + Y costs less gass than X += Y for state variables

#### PoC
Total of 4 instances found.
```
./TokenggAVAX.sol:160:		stakingTotalAssets += assets;
./TokenggAVAX.sol:252:		totalReleasedAssets += amount;
./TokenggAVAX.sol:149:		stakingTotalAssets -= baseAmt;
./TokenggAVAX.sol:245:		totalReleasedAssets -= amount;
```

&ensp;
### Use require instead of &&

#### Issue
When there are multiple conditions in require statement, break down the require statement into
multiple require statements instead of using && can save gas.

#### PoC
Total of 1 instance found.
```solidity
./ERC20Upgradeable.sol:154:			require(recoveredAddress != address(0) && recoveredAddress == owner, "INVALID_SIGNER");
```

#### Mitigation
Break down into several require statement.
```solidity
require(recoveredAddress != address(0));  
require(recoveredAddress == owner, "INVALID_SIGNER");
```

&ensp;
### Use Bit Shifting Instead of Multiplication/Division of 2

#### Issue
The MUL and DIV opcodes cost 5 gas but SHL and SHR only costs 3 gas.
Since MUL/DIV and SHL/SHR result the same, use cheaper bit shifting.

#### PoC
Total of 1 instance found.
```solidity
./MinipoolManager.sol:413:		uint256 avaxHalfRewards = avaxTotalRewardAmt / 2;
```

#### Mitigation
Use bit shifting instead of multiplication/division.
Example:
```solidity
uint256 center = upper - (upper - lower) / 2; 
Good:
uint256 center = upper - (upper - lower) >> 2; 
```

&ensp;
### Boolean Comparisons

#### Issue
It is more gas expensive to compare boolean with "variable == true" or "variable == false" than 
directly checking the returned boolean value.

#### PoC
Total of 3 instances found.
```solidity
./BaseAbstract.sol:25:		if (getBool(keccak256(abi.encodePacked("contract.exists", msg.sender))) == false) {
./BaseAbstract.sol:74:		if (enabled == false) {
./Storage.sol:29:		if (booleanStorage[keccak256(abi.encodePacked("contract.exists", msg.sender))] == false && msg.sender != guardian) {
```

#### Mitigation
Simply check by returned boolean value.
Change it to
```
require(!somethinghere)
```

&ensp;
### Using Elements Smaller than 32 bytes (256 bits) Might Use More Gas

#### Issue
Since EVM operates on 32 bytes at a time, if the element is smaller than that, the EVM must use more operations
in order to reduce the elements from 32 bytes to specified size. Therefore it is more gas saving to use 32 bytes 
unless it is used in a struct or state variable in order to reduce storage slot. 

Reference: https://docs.soliditylang.org/en/v0.8.15/internals/layout_in_storage.html

#### PoC
Total of 10 instances found.
```
./BaseAbstract.sol:19:	uint8 public version;
./TokenggAVAX.sol:89:		uint32 timestamp = block.timestamp.safeCastTo32();
./TokenggAVAX.sol:95:		uint192 lastRewardsAmt_ = lastRewardsAmt;
./TokenggAVAX.sol:102:		uint32 nextRewardsCycleEnd = ((timestamp + rewardsCycleLength) / rewardsCycleLength) * rewardsCycleLength;
./TokenggAVAX.sol:116:		uint192 lastRewardsAmt_ = lastRewardsAmt;
./TokenggAVAX.sol:117:		uint32 rewardsCycleEnd_ = rewardsCycleEnd;
./TokenggAVAX.sol:118:		uint32 lastSync_ = lastSync;
./ERC20Upgradeable.sol:27:	uint8 public decimals;
./ERC20Upgradeable.sol:56:		uint8 _decimals
./ERC20Upgradeable.sol:123:		uint8 v,
```

#### Mitigation
I suggest using uint256 instead of anything smaller and downcast where needed.

&ensp;
### Empty Blocks Should Emit Something or be Removed

#### Issue
There are function with empty blocks. These should do something like emit an event or reverting.
If not it should be removed from the contract.

#### PoC
Total of 2 instances found.
```solidity
./TokenggAVAX.sol:255:	function _authorizeUpgrade(address newImplementation) internal override onlyGuardian {}
./MinipoolManager.sol:106:	function receiveWithdrawalAVAX() external payable {}
```

#### Mitigation
Add emit or revert in the function block.

&ensp;
### Use Custom Errors to Save Gas

#### Issue
Custom errors from Solidity 0.8.4 are cheaper than revert strings.
Details are explained here: https://blog.soliditylang.org/2021/04/21/custom-errors/

#### PoC
Total of 4 instances found.
```
./ERC20Upgradeable.sol:127:		require(deadline >= block.timestamp, "PERMIT_DEADLINE_EXPIRED");
./ERC20Upgradeable.sol:154:			require(recoveredAddress != address(0) && recoveredAddress == owner, "INVALID_SIGNER");
./ERC4626Upgradeable.sol:44:		require((shares = previewDeposit(assets)) != 0, "ZERO_SHARES");
./ERC4626Upgradeable.sol:103:		require((assets = previewRedeem(shares)) != 0, "ZERO_ASSETS");
```

#### Mitigation
I suggest implementing custom errors to save gas.

&ensp;