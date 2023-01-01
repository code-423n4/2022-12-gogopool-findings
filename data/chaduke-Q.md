QA1: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L73
Instead of passing ``asset``as an argument, it is better to define it as a constant since the address for the WAVAX contract can be predetermined. 

QA2. https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L2
Lock all files with the most recent version of Solidity  0.8.17.

QA3: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L225-L239
The four preview-X functions need to add ``onlyProxy()`` so that they can only be called through the proxy. Otherwise, 
functions such as ``depositAVAX()`` and ``withdrawAVAX()`` will not work properly if they are not called via the proxy since they modify the state variable ``totalReleasedAssets``. 
```
function previewDeposit(uint256 assets) public view override whenTokenNotPaused(assets) onlyProxy() returns (uint256) {
		return super.previewDeposit(assets);
	}

	function previewMint(uint256 shares) public view override whenTokenNotPaused(shares) onlyProxy() returns (uint256) {
		return super.previewMint(shares);
	}

	function previewWithdraw(uint256 assets) public view override whenTokenNotPaused(assets) onlyProxy() returns (uint256) {
		return super.previewWithdraw(assets);
	}

	function previewRedeem(uint256 shares) public view override whenTokenNotPaused(shares) onlyProxy() returns (uint256) {
		return super.previewRedeem(shares);
	}

```


QA4: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L106
We need to ensure that ``receiveWithdrawalAVAX()`` can only be called by the ``TokenggAVAX`` contract or the ``Vault`` contract.
```
function receiveWithdrawalAVAX() external payable {
      assert(msg.sender == address(ggAVAX) || msg.sender == getContractAddress("Vault"));
}
```

QA5: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L239-L246
The implementation fails to conform to the documentation that says "If nodeID exists, only allow overwriting if node is finished or canceled``). It only checked the next status is ``MinipoolStatus.Prelaunch`` without checking the current status. For example, the current status might be MinipoolStatus.Withdrawable`` see the logic in ``requireValidStateTransition()``.
To fix, we need to explicitly check the current status as well:
```
if (minipoolIndex != -1) {
                       bytes32 statusKey = keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status"));
		       MinipoolStatus currentStatus = MinipoolStatus(getUint(statusKey));

                       if(currentStutus != MinipoolStaus.Finished && currentStutus != MinipoolStatus.Canceled) revert InvalidStateTransition(); // @audit: check current status explicitly!

			requireValidStateTransition(minipoolIndex, MinipoolStatus.Prelaunch);
        		resetMinipoolData(minipoolIndex);
			// Also reset initialStartTime as we are starting a whole new validation
			setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".initialStartTime")), 0);
		} else {
			minipoolIndex = int256(getUint(keccak256("minipool.count")));
			// The minipoolIndex is stored 1 greater than actual value. The 1 is subtracted in getIndexOf()
			setUint(keccak256(abi.encodePacked("minipool.index", nodeID)), uint256(minipoolIndex + 1));
			setAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".nodeID")), nodeID);
			addUint(keccak256("minipool.count"), 1);
		}
```

QA6: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L509-L510
Fail to call ``staking.decreaseMinipoolCount(owner);`` to decrease the minipool count. The fix is:

```
                Staking staking = Staking(getContractAddress("Staking"));
		staking.decreaseAVAXAssigned(owner, avaxLiquidStakerAmt);
		staking.decreaseMinipoolCount(owner);
 
```

QA7: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L165
The function ``increaseRewardsCycleCount()`` assumes that ``getRewardsCyclesElapsed() = 1``, this assumption might not be true. We should implement `increaseRewardsCycleCount()`` such that its correctness does not depend on this assumption, and will work regardless how many reward cycles have passed.
```

function increaseRewardsCycleCount() internal {
		addUint(keccak256("RewardsPool.RewardsCycleCount"), getRewardsCyclesElapsed());
}


```

QA8: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L230
The number of enabled ``enabledMultisigs`` should be ``enabledCount`` instead of ``enabledMultisigs.length``, which is equal to ``count``.

```
for (uint256 i = 0; i < enabledCount; i++) {
			vault.withdrawToken(enabledMultisigs[i], ggp, tokensPerMultisig);
}
```

QA9: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Ocyticus.sol#L55-L68
Need to function to enable a particular multisig.

QA10. https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L166
The following functions need to add modifier ``whenNotPaused``: ``depositAVAX()``, ``withdrawAVAX()``, ``redeemAVAX()``.

QA11: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L108
After ``syncRewards()``, the following invariant must hold, so it might be a good idea to assert it:
```
          assert(totalReleasedAssets+lastRewardsAmt == asset.balanceOf(address(this))+stakingTotalAssets;
```
QA12:  https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L69-L73
WE should check that ``assets != 0`` and ``receiver != address(0x0)``. 

QA13: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L93
Zero address check for ``receiver``. 

QA14: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L142-L151
Calling ``synRewards()`` from this function would be appropriate since rewards will be received from this function. 
```
function depositFromStaking(uint256 baseAmt, uint256 rewardAmt) public payable onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
		uint256 totalAmt = msg.value;
		if (totalAmt != (baseAmt + rewardAmt) || baseAmt > stakingTotalAssets) {
			revert InvalidStakingDeposit();
		}

		emit DepositedFromStaking(msg.sender, baseAmt, rewardAmt);
		stakingTotalAssets -= baseAmt;
		IWAVAX(address(asset)).deposit{value: totalAmt}();

                if(block.timestamp.safeCastTo32() >= rewardsCycleEnd) synRewards(); // better to call here
	}
```

QA15. https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L211
We need to consider the ``reservedAsset`` as well when calculating ``maxWithdraw()`` and ``maxRedeem()'':
```

function maxWithdraw(address _owner) public view override returns (uint256) {
		if (getBool(keccak256(abi.encodePacked("contract.paused", "TokenggAVAX")))) {
			return 0;
		}
		uint256 assets = convertToAssets(balanceOf[_owner]);

                ProtocolDAO protocolDAO = ProtocolDAO(getContractAddress("ProtocolDAO"));]
		uint256 targetCollateralRate = protocolDAO.getTargetGGAVAXReserveRate();]

		uint256 totalAssets_ = totalAssets();

		uint256 reservedAssets = totalAssets_.mulDivDown(targetCollateralRate, 1 ether);

		uint256 avail = totalAssets - stakingTotalAssets - reservedAssets; // @audit: we need to consider reservedAssets
		return assets > avail ? avail : assets;
	}

function maxRedeem(address _owner) public view override returns (uint256) {
		if (getBool(keccak256(abi.encodePacked("contract.paused", "TokenggAVAX")))) {
			return 0;
		}
		uint256 shares = balanceOf[_owner];
                ProtocolDAO protocolDAO = ProtocolDAO(getContractAddress("ProtocolDAO"));]
		uint256 targetCollateralRate = protocolDAO.getTargetGGAVAXReserveRate();]

		uint256 totalAssets_ = totalAssets();

		uint256 reservedAssets = totalAssets_.mulDivDown(targetCollateralRate, 1 ether);


		uint256 avail = convertToShares(totalAssets - stakingTotalAssets - reservedAssets); // audit: consider reservedAssets as well
		return shares > avail ? avail : shares;
}

```

QA16. https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L198
The ``duration`` parameter needs to be checked to make sure it is long enough. 

QA17. https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L180
The ``withdrawAVAX()`` needs to check to ensure the amount of withdrawal is not great than ``maxWithdraw(address _owner)``:
```
function withdrawAVAX(uint256 assets) public returns (uint256 shares) {
              if(assets > maxWithdraw(msg.sender)) revert WithdrawTooMuch(); // @audit: check this        

		shares = previewWithdraw(assets); // No need to check for rounding error, previewWithdraw rounds up.
		beforeWithdraw(assets, shares);
		_burn(msg.sender, shares);

		emit Withdraw(msg.sender, msg.sender, msg.sender, assets, shares);

		IWAVAX(address(asset)).withdraw(assets);
		msg.sender.safeTransferETH(assets);
	}
```
QA18. https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L216
The ``redeemAVAX()`` needs to check to ensure the amount of withdrawal is not great than ``maxRedeem(address _owner)``:
```
function redeemAVAX(uint256 shares) public returns (uint256 assets) {
              if(shares > maxRedeem(msg.sender)) revert WithdrawTooMuch(); // @audit: check this        


		// Check for rounding error since we round down in previewRedeem.
		if ((assets = previewRedeem(shares)) == 0) {
			revert ZeroAssets();
		}
		beforeWithdraw(assets, shares);
		_burn(msg.sender, shares);

		emit Withdraw(msg.sender, msg.sender, msg.sender, assets, shares);

		IWAVAX(address(asset)).withdraw(assets);
		msg.sender.safeTransferETH(assets);
	}
```

QA19. https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimProtocolDAO.sol#L22
Zero address check for ``recipientAddress`` is necessary, also need to make sure that ``recipientAddress`` is not equal to the address of ``ClaimNodeOp``. 



QA20. https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L444-L478
One needs to check If the ``duration`` still has time left to go when calling ``recreateMinipool()``:

```
function recreateMinipool(address nodeID) external whenNotPaused {
		int256 minipoolIndex = onlyValidMultisig(nodeID);

               Minipool m = getMinipool(minipoolINdex);
               if(block.timestamp > m.initialStartTime + m.duration) revert NoLeftTimeforDuration();
 
		requireValidStateTransition(minipoolIndex, MinipoolStatus.Prelaunch);
		Minipool memory mp = getMinipool(minipoolIndex);
		// Compound the avax plus rewards
		// NOTE Assumes a 1:1 nodeOp:liqStaker funds ratio
		uint256 compoundedAvaxNodeOpAmt = mp.avaxNodeOpAmt + mp.avaxNodeOpRewardAmt;
		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpAmt")), compoundedAvaxNodeOpAmt);
		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")), compoundedAvaxNodeOpAmt);

		Staking staking = Staking(getContractAddress("Staking"));
		// Only increase AVAX stake by rewards amount we are compounding
		// since AVAX stake is only decreased by withdrawMinipool()
		staking.increaseAVAXStake(mp.owner, mp.avaxNodeOpRewardAmt);
		staking.increaseAVAXAssigned(mp.owner, compoundedAvaxNodeOpAmt);
		staking.increaseMinipoolCount(mp.owner);

		if (staking.getRewardsStartTime(mp.owner) == 0) {
			// Edge case where calculateAndDistributeRewards has reset their rewards time even though they are still cycling
			// So we re-set it here to their initial start time for this minipool
			staking.setRewardsStartTime(mp.owner, mp.initialStartTime);
		}

		ProtocolDAO dao = ProtocolDAO(getContractAddress("ProtocolDAO"));
		uint256 ratio = staking.getCollateralizationRatio(mp.owner);
		if (ratio < dao.getMinCollateralizationRatio()) {
			revert InsufficientGGPCollateralization();
		}

		resetMinipoolData(minipoolIndex);

		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Prelaunch));

		emit MinipoolStatusChanged(nodeID, MinipoolStatus.Prelaunch);
	}


```

