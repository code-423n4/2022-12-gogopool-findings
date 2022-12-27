# Remove temp variables if them are used just once

If a variable is being used just once, it would be more gas efficient to remove it and use it's value.

## Where
* [ClaimNodeOp.sol#L46-L52
](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/ClaimNodeOp.sol#L46-L52)

```diff
function isEligible(address stakerAddr) external view returns (bool) {
  // staking variable is unnecessary
-  Staking staking = Staking(getContractAddress("Staking"));
-  uint256 rewardsStartTime = staking.getRewardsStartTime(stakerAddr);
+  uint256 rewardsStartTime = Staking(getContractAddress("Staking")).getRewardsStartTime(stakerAddr);

  uint256 elapsedSecs = (block.timestamp - rewardsStartTime);
   // dao variable is unnecessary
-  ProtocolDAO dao = ProtocolDAO(getContractAddress("ProtocolDAO"));
-  return (rewardsStartTime != 0 && elapsedSecs >= dao.getRewardsEligibilityMinSeconds());
+  return (rewardsStartTime != 0 && elapsedSecs >= ProtocolDAO(getContractAddress("ProtocolDAO")).getRewardsEligibilityMinSeconds());
}
```

* [MinipoolManager.sol#L235-L236
](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/MinipoolManager.sol#L235-L236)

```diff
  // Get a Rialto multisig to assign for this minipool
-  MultisigManager multisigManager = MultisigManager(getContractAddress("MultisigManager"));
-  address multisig = multisigManager.requireNextActiveMultisig();
+  address multisig = MultisigManager(getContractAddress("MultisigManager")).requireNextActiveMultisig();
```

* [MinipoolManager.sol#L267-L268
](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/MinipoolManager.sol#L267-L268)

```diff
-  Vault vault = Vault(getContractAddress("Vault"));
-  vault.depositAVAX{value: msg.value}();
+  Vault(getContractAddress("Vault")).depositAVAX{value: msg.value}();
```

* [MinipoolManager.sol#L273-L283
](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/MinipoolManager.sol#L273-L283)

```diff
function cancelMinipool(address nodeID) external nonReentrant {
-  Staking staking = Staking(getContractAddress("Staking"));
-  ProtocolDAO dao = ProtocolDAO(getContractAddress("ProtocolDAO"));
  int256 index = requireValidMinipool(nodeID);
  onlyOwner(index);
  // make sure they meet the wait period requirement
-  if (block.timestamp - staking.getRewardsStartTime(msg.sender) < dao.getMinipoolCancelMoratoriumSeconds()) {
+ if (block.timestamp - Staking(getContractAddress("Staking")).getRewardsStartTime(msg.sender) < ProtocolDAO(getContractAddress("ProtocolDAO")).getMinipoolCancelMoratoriumSeconds()) {

    revert CancellationTooEarly();
  }
  _cancelMinipoolAndReturnFunds(nodeID, index);
}
```

* [MinipoolManager.sol#L287-L303
](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/MinipoolManager.sol#L287-L303)

```diff
function withdrawMinipoolFunds(address nodeID) external nonReentrant {
  int256 minipoolIndex = requireValidMinipool(nodeID);
  address owner = onlyOwner(minipoolIndex);
  requireValidStateTransition(minipoolIndex, MinipoolStatus.Finished);
  setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Finished));

  uint256 avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpAmt")));
  uint256 avaxNodeOpRewardAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpRewardAmt")));
  uint256 totalAvaxAmt = avaxNodeOpAmt + avaxNodeOpRewardAmt;

-  Staking staking = Staking(getContractAddress("Staking"));
-  staking.decreaseAVAXStake(owner, avaxNodeOpAmt);
+  Staking(getContractAddress("Staking")).decreaseAVAXStake(owner, avaxNodeOpAmt);
-  Vault vault = Vault(getContractAddress("Vault"));
-  vault.withdrawAVAX(totalAvaxAmt);
+  Vault(getContractAddress("Vault")).withdrawAVAX(totalAvaxAmt);
  owner.safeTransferETH(totalAvaxAmt);
}
```

* [MinipoolManager.sol#L324-L343](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/MinipoolManager.sol#L324-L343)

```diff
    function claimAndInitiateStaking(address nodeID) external {
    	int256 minipoolIndex = onlyValidMultisig(nodeID);
    	requireValidStateTransition(minipoolIndex, MinipoolStatus.Launched);

    	uint256 avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpAmt")));
    	uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));

    	// Transfer funds to this contract and then send to multisig
    	ggAVAX.withdrawForStaking(avaxLiquidStakerAmt);
    	addUint(keccak256("MinipoolManager.TotalAVAXLiquidStakerAmt"), avaxLiquidStakerAmt);

    	setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Launched));
    	emit MinipoolStatusChanged(nodeID, MinipoolStatus.Launched);

-    	Vault vault = Vault(getContractAddress("Vault"));
-    	vault.withdrawAVAX(avaxNodeOpAmt);
+     Vault(getContractAddress("Vault")).withdrawAVAX(avaxNodeOpAmt);

    	uint256 totalAvaxAmt = avaxNodeOpAmt + avaxLiquidStakerAmt;
    	msg.sender.safeTransferETH(totalAvaxAmt);
    }
```

* [MinipoolManager.sol#L429-L430](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/MinipoolManager.sol#L429-L430)

```diff
  ...
- Vault vault = Vault(getContractAddress("Vault"));
- vault.depositAVAX{value: avaxNodeOpAmt + avaxNodeOpRewardAmt}();
+ Vault(getContractAddress("Vault")).depositAVAX{value: avaxNodeOpAmt + avaxNodeOpRewardAmt}();
  ...
```

* [MinipoolManager.sol#L502-L510](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/MinipoolManager.sol#L502-L510)

```diff
  // Send the nodeOps AVAX to vault so they can claim later
- Vault vault = Vault(getContractAddress("Vault"));
- vault.depositAVAX{value: avaxNodeOpAmt}();
+ Vault(getContractAddress("Vault")).depositAVAX{value: avaxNodeOpAmt}();
  
  // Return Liq stakers funds
  ggAVAX.depositFromStaking{value: avaxLiquidStakerAmt}(avaxLiquidStakerAmt, 0);

- Staking staking = Staking(getContractAddress("Staking"));
- staking.decreaseAVAXAssigned(owner, avaxLiquidStakerAmt);
+ Staking(getContractAddress("Staking")).decreaseAVAXAssigned(owner, avaxLiquidStakerAmt);
```

* [MinipoolManager.sol#L547-L551](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/MinipoolManager.sol#L547-L551)

```diff
function calculateGGPSlashAmt(uint256 avaxRewardAmt) public view returns (uint256) {
-  Oracle oracle = Oracle(getContractAddress("Oracle"));
-  (uint256 ggpPriceInAvax, ) = oracle.getGGPPriceInAVAX();
+  (uint256 ggpPriceInAvax, ) = Oracle(getContractAddress("Oracle")).getGGPPriceInAVAX();
   return avaxRewardAmt.divWadDown(ggpPriceInAvax);
}
```

* [MinipoolManager.sol#L557-L561](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/MinipoolManager.sol#L557-L561)

```diff
function getExpectedAVAXRewardsAmt(uint256 duration, uint256 avaxAmt) public view returns (uint256) {
-  ProtocolDAO dao = ProtocolDAO(getContractAddress("ProtocolDAO"));
-  uint256 rate = dao.getExpectedAVAXRewardsRate();
  uint256 rate = ProtocolDAO(getContractAddress("ProtocolDAO")).getExpectedAVAXRewardsRate();
  return (avaxAmt.mulWadDown(rate) \* duration) / 365 days;
}
```

* [MinipoolManager.sol#L646-L665](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/MinipoolManager.sol#L646-L665)

```diff
    function \_cancelMinipoolAndReturnFunds(address nodeID, int256 index) private {
      requireValidStateTransition(index, MinipoolStatus.Canceled);
      setUint(keccak256(abi.encodePacked("minipool.item", index, ".status")), uint256(MinipoolStatus.Canceled));

    	address owner = getAddress(keccak256(abi.encodePacked("minipool.item", index, ".owner")));
    	uint256 avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxNodeOpAmt")));
    	uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxLiquidStakerAmt")));

    	Staking staking = Staking(getContractAddress("Staking"));
    	staking.decreaseAVAXStake(owner, avaxNodeOpAmt);
    	staking.decreaseAVAXAssigned(owner, avaxLiquidStakerAmt);

    	staking.decreaseMinipoolCount(owner);

    	emit MinipoolStatusChanged(nodeID, MinipoolStatus.Canceled);

-    	Vault vault = Vault(getContractAddress("Vault"));
-    	vault.withdrawAVAX(avaxNodeOpAmt);
+     Vault(getContractAddress("Vault")).withdrawAVAX(avaxNodeOpAmt);
    	owner.safeTransferETH(avaxNodeOpAmt);
    }
```

* [MinipoolManager.sol#L670-L683](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/MinipoolManager.sol#L670-L683)

```diff
    function slash(int256 index) private {
      address nodeID = getAddress(keccak256(abi.encodePacked("minipool.item", index, ".nodeID")));
      address owner = getAddress(keccak256(abi.encodePacked("minipool.item", index, ".owner")));
      uint256 duration = getUint(keccak256(abi.encodePacked("minipool.item", index, ".duration")));
      uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxLiquidStakerAmt")));
      uint256 expectedAVAXRewardsAmt = getExpectedAVAXRewardsAmt(duration, avaxLiquidStakerAmt);
      uint256 slashGGPAmt = calculateGGPSlashAmt(expectedAVAXRewardsAmt);
      setUint(keccak256(abi.encodePacked("minipool.item", index, ".ggpSlashAmt")), slashGGPAmt);

    	emit GGPSlashed(nodeID, slashGGPAmt);

-   	Staking staking = Staking(getContractAddress("Staking"));
-    	staking.slashGGP(owner, slashGGPAmt);
+     Staking(getContractAddress("Staking")).slashGGP(owner, slashGGPAmt);
    }
```

* [RewardsPool.sol#L54-L61](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/RewardsPool.sol#L54-L61)

```diff
  function getInflationIntervalsElapsed() public view returns (uint256) {
-   ProtocolDAO dao = ProtocolDAO(getContractAddress("ProtocolDAO"));
    uint256 startTime = getInflationIntervalStartTime();
    if (startTime == 0) {
    revert ContractHasNotBeenInitialized();
    }
-   return (block.timestamp - startTime) / dao.getInflationIntervalSeconds();
+   return (block.timestamp - startTime) / ProtocolDAO(getContractAddress("ProtocolDAO")).getInflationIntervalSeconds();
  }
```

* [RewardsPool.sol#L82-L100](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/RewardsPool.sol#L82-L100)

```diff
    function inflate() internal {
-     ProtocolDAO dao = ProtocolDAO(getContractAddress("ProtocolDAO"));
-     uint256 inflationIntervalElapsedSeconds = (block.timestamp - getInflationIntervalStartTime());
      (uint256 currentTotalSupply, uint256 newTotalSupply) = getInflationAmt();

-    	TokenGGP ggp = TokenGGP(getContractAddress("TokenGGP"));
-    	if (newTotalSupply > ggp.totalSupply()) {
+     if (newTotalSupply > TokenGGP(getContractAddress("TokenGGP")).totalSupply()) {
    		revert MaximumTokensReached();
    	}

    	uint256 newTokens = newTotalSupply - currentTotalSupply;

    	emit GGPInflated(newTokens);

-    	dao.setTotalGGPCirculatingSupply(newTotalSupply);
+     ProtocolDAO(getContractAddress("ProtocolDAO")).setTotalGGPCirculatingSupply(newTotalSupply);

    	addUint(keccak256("RewardsPool.InflationIntervalStartTime"), inflationIntervalElapsedSeconds);
    	setUint(keccak256("RewardsPool.RewardsCycleTotalAmt"), newTokens);
    }

```

* [RewardsPool.sol#L125-L129](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/RewardsPool.sol#L125-L129)

```diff
    function getRewardsCyclesElapsed() public view returns (uint256) {
-     ProtocolDAO dao = ProtocolDAO(getContractAddress("ProtocolDAO"));
-     uint256 startTime = getRewardsCycleStartTime();
-     return (block.timestamp - startTime) / dao.getRewardsCycleSeconds();
+     return (block.timestamp - getRewardsCycleStartTime()) / ProtocolDAO(getContractAddress("ProtocolDAO")).getRewardsCycleSeconds();
    }
```

* [RewardsPool.sol#L134-L147](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/RewardsPool.sol#L134-L147)

```diff
    function getClaimingContractDistribution(string memory claimingContract) public view returns (uint256) {
-     ProtocolDAO dao = ProtocolDAO(getContractAddress("ProtocolDAO"));
-     uint256 claimContractPct = dao.getClaimingContractPct(claimingContract);
+     uint256 claimContractPct = ProtocolDAO(getContractAddress("ProtocolDAO")).getClaimingContractPct(claimingContract);

      // How much rewards are available for this claim interval?
      uint256 currentCycleRewardsTotal = getRewardsCycleTotalAmt();

    	// How much this claiming contract is entitled to in perc
    	uint256 contractRewardsTotal = 0;
    	if (claimContractPct > 0 && currentCycleRewardsTotal > 0) {
    		// Calculate how much rewards this claimer will receive based on their claiming perc
    		contractRewardsTotal = claimContractPct.mulWadDown(currentCycleRewardsTotal);
    	}
    	return contractRewardsTotal;
    }
```

* [Staking.sol#L256-L264](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/Staking.sol#L256-L264)

```diff
    function getMinimumGGPStake(address stakerAddr) public view returns (uint256) {
-    	ProtocolDAO dao = ProtocolDAO(getContractAddress("ProtocolDAO"));
-    	Oracle oracle = Oracle(getContractAddress("Oracle"));
-    	(uint256 ggpPriceInAvax, ) = oracle.getGGPPriceInAVAX();
+     (uint256 ggpPriceInAvax, ) = Oracle(getContractAddress("Oracle")).getGGPPriceInAVAX();

    	uint256 avaxAssigned = getAVAXAssigned(stakerAddr);
    	uint256 ggp100pct = avaxAssigned.divWadDown(ggpPriceInAvax);
-    	return ggp100pct.mulWadDown(dao.getMinCollateralizationRatio());
+     return ggp100pct.mulWadDown(ProtocolDAO(getContractAddress("ProtocolDAO")).getMinCollateralizationRatio());
    }
```

* [Staking.sol#L66-L69](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/Staking.sol#L66-L69)

```diff
    function getTotalGGPStake() public view returns (uint256) {
-     Vault vault = Vault(getContractAddress("Vault"));
-     return vault.balanceOfToken("Staking", ggp);
+     return Vault(getContractAddress("Vault")).balanceOfToken("Staking", ggp);
    }
```

* [Staking.sol#L269-L279](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/Staking.sol#L269-L279)

```diff
    function getCollateralizationRatio(address stakerAddr) public view returns (uint256) {
      uint256 avaxAssigned = getAVAXAssigned(stakerAddr);
      if (avaxAssigned == 0) {
        // Infinite collat ratio
        return type(uint256).max;
      }
-     Oracle oracle = Oracle(getContractAddress("Oracle"));
-     (uint256 ggpPriceInAvax, ) = oracle.getGGPPriceInAVAX();
+     (uint256 ggpPriceInAvax, ) = Oracle(getContractAddress("Oracle")).getGGPPriceInAVAX();
      uint256 ggpStakedInAvax = getGGPStake(stakerAddr).mulWadDown(ggpPriceInAvax);
      return ggpStakedInAvax.divWadDown(avaxAssigned);
    }
```

* [Staking.sol#L286-L304](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/Staking.sol#L286-L304)
```diff
    function getEffectiveRewardsRatio(address stakerAddr) public view returns (uint256) {
      uint256 avaxAssignedHighWater = getAVAXAssignedHighWater(stakerAddr);
      if (avaxAssignedHighWater == 0) {
        return 0;
      }
      if (getCollateralizationRatio(stakerAddr) < TENTH) {
        return 0;
      }

-     Oracle oracle = Oracle(getContractAddress("Oracle"));
-     (uint256 ggpPriceInAvax, ) = oracle.getGGPPriceInAVAX();
+     (uint256 ggpPriceInAvax, ) = Oracle(getContractAddress("Oracle")).getGGPPriceInAVAX();
      uint256 ggpStakedInAvax = getGGPStake(stakerAddr).mulWadDown(ggpPriceInAvax);
      uint256 ratio = ggpStakedInAvax.divWadDown(avaxAssignedHighWater);

-     ProtocolDAO dao = ProtocolDAO(getContractAddress("ProtocolDAO"));
-     uint256 maxRatio = dao.getMaxCollateralizationRatio();
+     uint256 maxRatio = ProtocolDAO(getContractAddress("ProtocolDAO")).getMaxCollateralizationRatio();
      return (ratio > maxRatio) ? maxRatio : ratio;
    }
```

* [Staking.sol#L308-L315](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/Staking.sol#L308-L315)

```diff
    function getEffectiveGGPStaked(address stakerAddr) external view returns (uint256) {
-     Oracle oracle = Oracle(getContractAddress("Oracle"));
-     (uint256 ggpPriceInAvax, ) = oracle.getGGPPriceInAVAX();
+     (uint256 ggpPriceInAvax, ) = Oracle(getContractAddress("Oracle")).getGGPPriceInAVAX();

      uint256 avaxAssignedHighWater = getAVAXAssignedHighWater(stakerAddr);
      uint256 ratio = getEffectiveRewardsRatio(stakerAddr);
      return avaxAssignedHighWater.mulWadDown(ratio).divWadDown(ggpPriceInAvax);
    }
```

* [Vault.sol#L61-L78](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/Vault.sol#L61-L78)

```diff
    function withdrawAVAX(uint256 amount) external onlyRegisteredNetworkContract nonReentrant {
      // Valid Amount?
      if (amount == 0) {
        revert InvalidAmount();
      }
      // Get the contract name the call is coming from
      string memory contractName = getContractName(msg.sender);
      // Emit the withdrawl event for that contract
      emit AVAXWithdrawn(contractName, amount);
      // Verify there are enough funds
      if (avaxBalances[contractName] < amount) {
        revert InsufficientContractBalance();
      }
      // Update balance
      avaxBalances[contractName] = avaxBalances[contractName] - amount;
-     IWithdrawer withdrawer = IWithdrawer(msg.sender);
-     withdrawer.receiveWithdrawalAVAX{value: amount}();
+     IWithdrawer(msg.sender).receiveWithdrawalAVAX{value: amount}();
    }
```

* [/tokens/TokenggAVAX.sol#L132-L140](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/tokens/TokenggAVAX.sol#L132-L140)

```diff
    function amountAvailableForStaking() public view returns (uint256) {
-     ProtocolDAO protocolDAO = ProtocolDAO(getContractAddress("ProtocolDAO"));
-     uint256 targetCollateralRate = protocolDAO.getTargetGGAVAXReserveRate();
+     uint256 targetCollateralRate = ProtocolDAO(getContractAddress("ProtocolDAO")).getTargetGGAVAXReserveRate();

      uint256 totalAssets_ = totalAssets();

      uint256 reservedAssets = totalAssets_.mulDivDown(targetCollateralRate, 1 ether);
      return totalAssets_ - reservedAssets - stakingTotalAssets;
    }
```

* [/tokens/TokenggAVAX.sol#L142-L151](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/tokens/TokenggAVAX.sol#L142-L151)

```diff
    function depositFromStaking(uint256 baseAmt, uint256 rewardAmt) public payable onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
-     uint256 totalAmt = msg.value;
-     if (totalAmt != (baseAmt + rewardAmt) || baseAmt > stakingTotalAssets) {
+     if (msg.value != (baseAmt + rewardAmt) || baseAmt > stakingTotalAssets) {
        revert InvalidStakingDeposit();
      }

      emit DepositedFromStaking(msg.sender, baseAmt, rewardAmt);
      stakingTotalAssets -= baseAmt;
      IWAVAX(address(asset)).deposit{value: totalAmt}();
    }
```

* [/tokens/TokenggAVAX.sol#L153-L164](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/tokens/TokenggAVAX.sol#L153-L164)

```diff
    function withdrawForStaking(uint256 assets) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
      if (assets > amountAvailableForStaking()) {
        revert WithdrawAmountTooLarge();
      }

      emit WithdrawnForStaking(msg.sender, assets);

      stakingTotalAssets += assets;
      IWAVAX(address(asset)).withdraw(assets);
-     IWithdrawer withdrawer = IWithdrawer(msg.sender);
-     withdrawer.receiveWithdrawalAVAX{value: assets}();
+     IWithdrawer(msg.sender).receiveWithdrawalAVAX{value: assets}();
    }
```

* [/tokens/TokenggAVAX.sol#L166-L178](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/tokens/TokenggAVAX.sol#L166-L178)

```diff
    function depositAVAX() public payable returns (uint256 shares) {
-     uint256 assets = msg.value;
      // Check for rounding error since we round down in previewDeposit.
-     if ((shares = previewDeposit(assets)) == 0) {
+     if ((shares = previewDeposit(msg.value)) == 0) {
        revert ZeroShares();
      }

-     emit Deposit(msg.sender, msg.sender, assets, shares);
+     emit Deposit(msg.sender, msg.sender, msg.value, shares);

-     IWAVAX(address(asset)).deposit{value: assets}();
+     IWAVAX(address(asset)).deposit{value: msg.value}();
      _mint(msg.sender, shares);
-     afterDeposit(assets, shares);
+     afterDeposit(msg.value, shares);
    }
```

# Use `calldata` instead of `memory` for function input

## Where
* [ClaimProtocolDAO.sol#L21
](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimProtocolDAO.sol#L21) - use `calldata` for `invoiceID`.
* [ProtocolDAO.sol#L190](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/ProtocolDAO.sol#L190) - use `calldata` for `name`
* [ProtocolDAO.sol#L211](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/ProtocolDAO.sol#L211) - use `calldata` for `newName`
* [Vault.sol#L84](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/Vault.sol#L84) - use `calldata` for `toContractName`
* [Vault.sol#L109](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/Vault.sol#L109) - use `calldata` for `networkContractName`
* [Vault.sol#L167](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/Vault.sol#L167) - use `calldata` for `networkContractName`

# Use `unchecked` for safe math operations to save gas

## Where
- [MinipoolManager.sol#L287-L303
  ](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/MinipoolManager.sol#L287-L303)


```diff
function withdrawMinipoolFunds(address nodeID) external nonReentrant {
  int256 minipoolIndex = requireValidMinipool(nodeID);
  address owner = onlyOwner(minipoolIndex);
  requireValidStateTransition(minipoolIndex, MinipoolStatus.Finished);
  setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Finished));

  uint256 avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpAmt")));
  uint256 avaxNodeOpRewardAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpRewardAmt")));
- uint256 totalAvaxAmt = avaxNodeOpAmt + avaxNodeOpRewardAmt;
+ uint256 totalAvaxAmt;
+ unchecked {
+   totalAvaxAmt = avaxNodeOpAmt + avaxNodeOpRewardAmt;
+ }
  Staking staking = Staking(getContractAddress("Staking"));
  staking.decreaseAVAXStake(owner, avaxNodeOpAmt);

  Vault vault = Vault(getContractAddress("Vault"));
  vault.withdrawAVAX(totalAvaxAmt);
  owner.safeTransferETH(totalAvaxAmt);
}
```

- [MinipoolManager.sol#L607-L631](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/MinipoolManager.sol#L607-L631)

```diff
    function getMinipools(
      MinipoolStatus status,
      uint256 offset,
      uint256 limit
    ) public view returns (Minipool[] memory minipools) {
      uint256 totalMinipools = getUint(keccak256("minipool.count"));
      uint256 max = offset + limit;
      if (max > totalMinipools || limit == 0) {
        max = totalMinipools;
      }
      minipools = new Minipool[](max - offset);
      uint256 total = 0;
-     for (uint256 i = offset; i < max; i++) {
+     for (uint256 i = offset; i < max;) {
        Minipool memory mp = getMinipool(int256(i));
        if (mp.status == uint256(status)) {
          minipools[total] = mp;
          total++;
        }
+       unchecked {
+         ++i;
+       }
      }
      // Dirty hack to cut unused elements off end of return value (from RP)
      // solhint-disable-next-line no-inline-assembly
      assembly {
        mstore(minipools, total)
      }
    }
```

- [MultisigManager.sol#L80-L91](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MultisigManager.sol#L80-L91)

```diff
    function requireNextActiveMultisig() external view returns (address) {
    	uint256 total = getUint(keccak256("multisig.count"));
    	address addr;
    	bool enabled;
-    	for (uint256 i = 0; i < total; i++) {
+    	for (uint256 i = 0; i < total;) {
    		(addr, enabled) = getMultisig(i);
    		if (enabled) {
    			return addr;
    		}
+       unchecked {
+    			++i;
+    		}
    	}
    	revert NoEnabledMultisigFound();
    }
```

- [Ocyticus.sol#L55-L67](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Ocyticus.sol#L55-L67)

```diff
    function disableAllMultisigs() public onlyDefender {
      MultisigManager mm = MultisigManager(getContractAddress("MultisigManager"));
      uint256 count = mm.getCount();

    	address addr;
    	bool enabled;
-    	for (uint256 i = 0; i < count; i++) {
+     for (uint256 i = 0; i < count;) {
    		(addr, enabled) = mm.getMultisig(i);
    		if (enabled) {
    			mm.disableMultisig(addr);
    		}
+       unchecked {
+         ++i;
+       }
    	}
    }
```

- [RewardsPool.sol#L66-L78](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/RewardsPool.sol#L66-L78)

```diff
    function getInflationAmt() public view returns (uint256 currentTotalSupply, uint256 newTotalSupply) {
      ProtocolDAO dao = ProtocolDAO(getContractAddress("ProtocolDAO"));
      uint256 inflationRate = dao.getInflationIntervalRate();
      uint256 inflationIntervalsElapsed = getInflationIntervalsElapsed();
      currentTotalSupply = dao.getTotalGGPCirculatingSupply();
      newTotalSupply = currentTotalSupply;

      // Compute inflation for total inflation intervals elapsed
-     for (uint256 i = 0; i < inflationIntervalsElapsed; i++) {
+     for (uint256 i = 0; i < inflationIntervalsElapsed;) {
        newTotalSupply = newTotalSupply.mulWadDown(inflationRate);
+       unchecked {
+         ++i;
+       }
      }
      return (currentTotalSupply, newTotalSupply);
    }
```

- [RewardsPool.sol#L203-L233](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/RewardsPool.sol#L203-L233)

```diff
    function distributeMultisigAllotment(
      uint256 allotment,
      Vault vault,
      TokenGGP ggp
    ) internal {
      MultisigManager mm = MultisigManager(getContractAddress("MultisigManager"));

    	uint256 enabledCount;
    	uint256 count = mm.getCount();
    	address[] memory enabledMultisigs = new address[](count);

    	// there should never be more than a few multisigs, so a loop should be fine here
-    	for (uint256 i = 0; i < count; i++) {
+     for (uint256 i = 0; i < count;) {
    		(address addr, bool enabled) = mm.getMultisig(i);
    		if (enabled) {
    			enabledMultisigs[enabledCount] = addr;
+         unchecked {
+           ++enabledCount;
+         }
-    			enabledCount++;
    		}
+       unchecked {
+         ++i
+       }
    	}

    	// Dirty hack to cut unused elements off end of return value (from RP)
    	// solhint-disable-next-line no-inline-assembly
    	assembly {
    		mstore(enabledMultisigs, enabledCount)
    	}

    	uint256 tokensPerMultisig = allotment / enabledCount;
-    	for (uint256 i = 0; i < enabledMultisigs.length; i++) {
+     for (uint256 i = 0; i < enabledMultisigs.length;) {
    		vault.withdrawToken(enabledMultisigs[i], ggp, tokensPerMultisig);
+       unchecked {
+         ++i;
+       }
    	}
    }
```

- [Staking.sol#L420-L439](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/Staking.sol#L420-L439)

```diff
    function getStakers(uint256 offset, uint256 limit) external view returns (Staker[] memory stakers) {
      uint256 totalStakers = getStakerCount();
      uint256 max = offset + limit;
      if (max > totalStakers || limit == 0) {
        max = totalStakers;
      }
      stakers = new Staker[](max - offset);
      uint256 total = 0;
-     for (uint256 i = offset; i < max; i++) {
+     for (uint256 i = offset; i < max;) {
-       Staker memory s = getStaker(int256(i)); // unnecesary temp variable
-       stakers[total] = s;
+       stakers[total] = getStaker(int256(i));
-       total++;
+       ++total;
+       unchecked {
+         ++i;
+       }
      }
      // Dirty hack to cut unused elements off end of return value (from RP)
      // solhint-disable-next-line no-inline-assembly
      assembly {
        mstore(stakers, total)
      }
    }
```

- [Vault.sol#L46-L57](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/Vault.sol#L46-L57)

```diff
    function depositAVAX() external payable onlyRegisteredNetworkContract {
      // Valid Amount?
      if (msg.value == 0) {
        revert InvalidAmount();
      }
      // Get contract name
      string memory contractName = getContractName(msg.sender);
      // Emit deposit event
      emit AVAXDeposited(contractName, msg.value);
      // Update balances
+     unchecked {      
        avaxBalances[contractName] = avaxBalances[contractName] + msg.value;
+     }
    }
```

- [Vault.sol#L61-L78](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/Vault.sol#L61-L78)

```diff
    function withdrawAVAX(uint256 amount) external onlyRegisteredNetworkContract nonReentrant {
      // Valid Amount?
      if (amount == 0) {
        revert InvalidAmount();
      }
      // Get the contract name the call is coming from
      string memory contractName = getContractName(msg.sender);
      // Emit the withdrawl event for that contract
      emit AVAXWithdrawn(contractName, amount);
      // Verify there are enough funds
      if (avaxBalances[contractName] < amount) {
        revert InsufficientContractBalance();
      }
      // Update balance
+     unchecked { 
      avaxBalances[contractName] = avaxBalances[contractName] - amount;
+     }
      IWithdrawer withdrawer = IWithdrawer(msg.sender);
      withdrawer.receiveWithdrawalAVAX{value: amount}();
    }
```

- [Vault.sol#L108-L131](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/Vault.sol#L108-L131)

```diff
    function depositToken(
      string memory networkContractName,
      ERC20 tokenContract,
      uint256 amount
    ) external guardianOrRegisteredContract {
      // Valid Amount?
      if (amount == 0) {
        revert InvalidAmount();
      }
      // Make sure the network contract is valid (will revert if not)
      getContractAddress(networkContractName);
      // Make sure we accept this token
      if (!allowedTokens[address(tokenContract)]) {
        revert InvalidToken();
      }
      // Get contract key
      bytes32 contractKey = keccak256(abi.encodePacked(networkContractName, address(tokenContract)));
      // Emit token transfer event
      emit TokenDeposited(contractKey, address(tokenContract), amount);
      // Send tokens to this address now, safeTransfer will revert if it fails
      tokenContract.safeTransferFrom(msg.sender, address(this), amount);
      // Update balances
+     unchecked {
      tokenBalances[contractKey] = tokenBalances[contractKey] + amount;
+     }
    }
```

- [Vault.sol#L137-L160](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/Vault.sol#L137-L160)

```diff
    function withdrawToken(
      address withdrawalAddress,
      ERC20 tokenAddress,
      uint256 amount
    ) external onlyRegisteredNetworkContract nonReentrant {
      // Valid Amount?
      if (amount == 0) {
        revert InvalidAmount();
      }
      // Get contract key
      bytes32 contractKey = keccak256(abi.encodePacked(getContractName(msg.sender), tokenAddress));
      // Emit token withdrawn event
      emit TokenWithdrawn(contractKey, address(tokenAddress), amount);
      // Verify there are enough funds
      if (tokenBalances[contractKey] < amount) {
        revert InsufficientContractBalance();
      }
      // Update balances
+     unchecked {      
      tokenBalances[contractKey] = tokenBalances[contractKey] - amount;
+     }
      // Get the toke ERC20 instance
      ERC20 tokenContract = ERC20(tokenAddress);
      // Withdraw to the withdrawal address, , safeTransfer will revert if it fails
      tokenContract.safeTransfer(withdrawalAddress, amount);
    }
```

- [Vault.sol#L166-L189](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/Vault.sol#L166-L189)

```diff
    function transferToken(
      string memory networkContractName,
      ERC20 tokenAddress,
      uint256 amount
    ) external onlyRegisteredNetworkContract {
      // Valid Amount?
      if (amount == 0) {
        revert InvalidAmount();
      }
      // Make sure the network contract is valid (will revert if not)
      getContractAddress(networkContractName);
      // Get contract keys
      bytes32 contractKeyFrom = keccak256(abi.encodePacked(getContractName(msg.sender), tokenAddress));
      bytes32 contractKeyTo = keccak256(abi.encodePacked(networkContractName, tokenAddress));
      // emit token transfer event
      emit TokenTransfer(contractKeyFrom, contractKeyTo, address(tokenAddress), amount);
      // Verify there are enough funds
      if (tokenBalances[contractKeyFrom] < amount) {
        revert InsufficientContractBalance();
      }
      // Update Balances
+     unchecked {
      tokenBalances[contractKeyFrom] = tokenBalances[contractKeyFrom] - amount;
      tokenBalances[contractKeyTo] = tokenBalances[contractKeyTo] + amount;
+     }
    }
```

- [/tokens/TokenggAVAX.sol#L88-L109](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/tokens/TokenggAVAX.sol#L88-L109)

```diff
    function syncRewards() public {
      uint32 timestamp = block.timestamp.safeCastTo32();

      if (timestamp < rewardsCycleEnd) {
        revert SyncError();
      }

      uint192 lastRewardsAmt_ = lastRewardsAmt;
      uint256 totalReleasedAssets_ = totalReleasedAssets;
      uint256 stakingTotalAssets_ = stakingTotalAssets;

      uint256 nextRewardsAmt = (asset.balanceOf(address(this)) + stakingTotalAssets_) - totalReleasedAssets_ - lastRewardsAmt_;

      // Ensure nextRewardsCycleEnd will be evenly divisible by `rewardsCycleLength`.
      uint32 nextRewardsCycleEnd = ((timestamp + rewardsCycleLength) / rewardsCycleLength) * rewardsCycleLength;

      lastRewardsAmt = nextRewardsAmt.safeCastTo192();
      lastSync = timestamp;
      rewardsCycleEnd = nextRewardsCycleEnd;
+     unchecked {      
      totalReleasedAssets = totalReleasedAssets_ + lastRewardsAmt_;
+     }
      emit NewRewardsCycle(nextRewardsCycleEnd, nextRewardsAmt);
    }
```

- [/tokens/TokenggAVAX.sol#L142-L151](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/tokens/TokenggAVAX.sol#L142-L151)

```diff
    function depositFromStaking(uint256 baseAmt, uint256 rewardAmt) public payable onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
      uint256 totalAmt = msg.value;
      if (totalAmt != (baseAmt + rewardAmt) || baseAmt > stakingTotalAssets) {
        revert InvalidStakingDeposit();
      }

      emit DepositedFromStaking(msg.sender, baseAmt, rewardAmt);
+     unchecked {      
      stakingTotalAssets -= baseAmt;
+     }
      IWAVAX(address(asset)).deposit{value: totalAmt}();
    }
```

- [/tokens/TokenggAVAX.sol#L153-L164](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/tokens/TokenggAVAX.sol#L153-L164)

```diff
    function withdrawForStaking(uint256 assets) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
      if (assets > amountAvailableForStaking()) {
        revert WithdrawAmountTooLarge();
      }

      emit WithdrawnForStaking(msg.sender, assets);

+     unchecked {
      stakingTotalAssets += assets;
+     }
      IWAVAX(address(asset)).withdraw(assets);
      IWithdrawer withdrawer = IWithdrawer(msg.sender);
      withdrawer.receiveWithdrawalAVAX{value: assets}();
    }

```

- [/tokens/TokenggAVAX.sol#L241-L253](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/tokens/TokenggAVAX.sol#L241-L253)

```diff
    function beforeWithdraw(
      uint256 amount,
      uint256 /* shares */
    ) internal override {
+     unchecked {      
      totalReleasedAssets -= amount;
+     }
    }

    function afterDeposit(
      uint256 amount,
      uint256 /* shares */
    ) internal override {
+     unchecked {
      totalReleasedAssets += amount;
+     }
    }
```

# Don't initialize variables with the default value

It's not necessary to instantiate a variable with the default value. Also, in this case, we can use the return variable instead of a temp variable to save extra gas.

## Where
[RewardsPool.sol#L141](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/RewardsPool.sol#L141)

```diff
-   function getClaimingContractDistribution(string memory claimingContract) public view returns (uint256) {
+   function getClaimingContractDistribution(string memory claimingContract) public view returns (uint256 contractRewardsTotal) {
      ProtocolDAO dao = ProtocolDAO(getContractAddress("ProtocolDAO"));
      uint256 claimContractPct = dao.getClaimingContractPct(claimingContract);
      // How much rewards are available for this claim interval?
      uint256 currentCycleRewardsTotal = getRewardsCycleTotalAmt();

    	// How much this claiming contract is entitled to in perc
-    	uint256 contractRewardsTotal = 0;
    	if (claimContractPct > 0 && currentCycleRewardsTotal > 0) {
    		// Calculate how much rewards this claimer will receive based on their claiming perc
    		contractRewardsTotal = claimContractPct.mulWadDown(currentCycleRewardsTotal);
    	}
-    	return contractRewardsTotal;
    }
```

# Cache values on temp variables instead of run multiple storage readings

## Where

* [Vault.sol#L84-L101](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/Vault.sol#L84-L101)

```diff
    function transferAVAX(string memory toContractName, uint256 amount) external onlyRegisteredNetworkContract {
      // Valid Amount?
      if (amount == 0) {
        revert InvalidAmount();
      }
      // Make sure the contract is valid, will revert if not
      getContractAddress(toContractName);
      string memory fromContractName = getContractName(msg.sender);
      // Emit transfer event
      emit AVAXTransfer(fromContractName, toContractName, amount);

+     uint256 avaxFromContractBalance = avaxBalances[fromContractName]; // @audit cache this value

      // Verify there are enough funds
-     if (avaxBalances[fromContractName] < amount) {
+     if (avaxFromContractBalance < amount) {
        revert InsufficientContractBalance();
      }
      // Update balances
-     avaxBalances[fromContractName] = avaxBalances[fromContractName] - amount;
+     avaxBalances[fromContractName] = avaxFromContractBalance - amount;
      avaxBalances[toContractName] = avaxBalances[toContractName] + amount;
    }
```

* [Vault.sol#L137-L160](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/Vault.sol#L137-L160)

```diff
    function withdrawToken(
      address withdrawalAddress,
      ERC20 tokenAddress,
      uint256 amount
    ) external onlyRegisteredNetworkContract nonReentrant {
      // Valid Amount?
      if (amount == 0) {
        revert InvalidAmount();
      }
      // Get contract key
      bytes32 contractKey = keccak256(abi.encodePacked(getContractName(msg.sender), tokenAddress));

+     uint256 contractTokenBalance = tokenBalances[contractKey];

      // Emit token withdrawn event
      emit TokenWithdrawn(contractKey, address(tokenAddress), amount);

      // Verify there are enough funds
-     if (tokenBalances[contractKey] < amount) {
+     if (contractTokenBalance < amount) {
        revert InsufficientContractBalance();
      }
      // Update balances
-     tokenBalances[contractKey] = tokenBalances[contractKey] - amount;
+     tokenBalances[contractKey] = contractTokenBalance - amount;
      // Get the toke ERC20 instance
      ERC20 tokenContract = ERC20(tokenAddress);
      // Withdraw to the withdrawal address, , safeTransfer will revert if it fails
      tokenContract.safeTransfer(withdrawalAddress, amount);
    }
```

* [Vault.sol#L166-L189](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/Vault.sol#L166-L189)

```diff
    function transferToken(
      string memory networkContractName,
      ERC20 tokenAddress,
      uint256 amount
    ) external onlyRegisteredNetworkContract {
      // Valid Amount?
      if (amount == 0) {
        revert InvalidAmount();
      }
      // Make sure the network contract is valid (will revert if not)
      getContractAddress(networkContractName);
      // Get contract keys
      bytes32 contractKeyFrom = keccak256(abi.encodePacked(getContractName(msg.sender), tokenAddress));
      bytes32 contractKeyTo = keccak256(abi.encodePacked(networkContractName, tokenAddress));
      // emit token transfer event
      emit TokenTransfer(contractKeyFrom, contractKeyTo, address(tokenAddress), amount);

+     uint256 contractKeyFromBalance = tokenBalances[contractKeyFrom];

      // Verify there are enough funds
-     if (tokenBalances[contractKeyFrom] < amount) {
+     if (contractKeyFromBalance < amount) {
        revert InsufficientContractBalance();
      }
      // Update Balances
-     tokenBalances[contractKeyFrom] = tokenBalances[contractKeyFrom] - amount;
+     tokenBalances[contractKeyFrom] = contractKeyFromBalance - amount;
      tokenBalances[contractKeyTo] = tokenBalances[contractKeyTo] + amount;
    }
```

* [/tokens/TokenggAVAX.sol#L142-L151](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/tokens/TokenggAVAX.sol#L142-L151)

```diff
    function depositFromStaking(uint256 baseAmt, uint256 rewardAmt) public payable onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
      uint256 totalAmt = msg.value;
+     uint256 _stakingTotalAssets = stakingTotalAssets;
-     if (totalAmt != (baseAmt + rewardAmt) || baseAmt > stakingTotalAssets) {
+     if (totalAmt != (baseAmt + rewardAmt) || baseAmt > _stakingTotalAssets) {
        revert InvalidStakingDeposit();
      }

      emit DepositedFromStaking(msg.sender, baseAmt, rewardAmt);
-     stakingTotalAssets -= baseAmt;    
+     stakingTotalAssets = _stakingTotalAssets - baseAmt;
      IWAVAX(address(asset)).deposit{value: totalAmt}();
    }
```

# Move the events emission to the end of the function body to save gas in case that any validation fails before.

There're multiple places where events are emitted before all the execution is done. This could result in a waste of gas if, for some reason, the transaction is reverted after the event was already emitted. I recommend to move the events to the end of the function execution and emit them only after we're sure that the transaction won't be reverted.

Example:

[/MinipoolManager.sol#L646-L665](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/MinipoolManager.sol#L646-L665)

```diff
  function _cancelMinipoolAndReturnFunds(address nodeID, int256 index) private {
		requireValidStateTransition(index, MinipoolStatus.Canceled);
		setUint(keccak256(abi.encodePacked("minipool.item", index, ".status")), uint256(MinipoolStatus.Canceled));

		address owner = getAddress(keccak256(abi.encodePacked("minipool.item", index, ".owner")));
		uint256 avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxNodeOpAmt")));
		uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxLiquidStakerAmt")));

		Staking staking = Staking(getContractAddress("Staking"));
		staking.decreaseAVAXStake(owner, avaxNodeOpAmt);
		staking.decreaseAVAXAssigned(owner, avaxLiquidStakerAmt);

		staking.decreaseMinipoolCount(owner);

-		emit MinipoolStatusChanged(nodeID, MinipoolStatus.Canceled); // EVENT IS EMITTED

		Vault vault = Vault(getContractAddress("Vault")); // THIS COULD REVERT
		vault.withdrawAVAX(avaxNodeOpAmt); // THIS COULD REVERT
		owner.safeTransferETH(avaxNodeOpAmt); // THIS COULD REVERT

+		emit MinipoolStatusChanged(nodeID, MinipoolStatus.Canceled); // EVENT MOVED TO THE END OF THE EXECUTION
	}
```

# Avoid storage readings until is necessary

Storage readings are expensive in gas, a way to save some is reading the storage only when is necessary and not before.

- [/tokens/TokenggAVAX.sol#L113-L130](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/tokens/TokenggAVAX.sol#L113-L130)

```diff
    function totalAssets() public view override returns (uint256) {
      // cache global vars
      uint256 totalReleasedAssets_ = totalReleasedAssets;
      uint192 lastRewardsAmt_ = lastRewardsAmt;
      uint32 rewardsCycleEnd_ = rewardsCycleEnd;
-     uint32 lastSync_ = lastSync;

      if (block.timestamp >= rewardsCycleEnd_) {
        // no rewards or rewards are fully unlocked
        // entire reward amount is available
        return totalReleasedAssets_ + lastRewardsAmt_;
      }

+     uint32 lastSync_ = lastSync;

      // rewards are not fully unlocked
      // return unlocked rewards and stored total
      uint256 unlockedRewards = (lastRewardsAmt_ * (block.timestamp - lastSync_)) / (rewardsCycleEnd_ - lastSync_);
      return totalReleasedAssets_ + unlockedRewards;
    }
```