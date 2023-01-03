* function ```calculateAndDistributeRewards``` calls ```rewardsPool.getRewardsCycleCount()``` 3 times:
```solidity
  function calculateAndDistributeRewards(address stakerAddr, uint256 totalEligibleGGPStaked) external onlyMultisig {
	...
	if (rewardsPool.getRewardsCycleCount() == 0) {
		revert RewardsCycleNotStarted();
	}

	if (staking.getLastRewardsCycleCompleted(stakerAddr) == rewardsPool.getRewardsCycleCount()) {
		revert RewardsAlreadyDistributedToStaker(stakerAddr);
	}
	staking.setLastRewardsCycleCompleted(stakerAddr, rewardsPool.getRewardsCycleCount());
    	...
  }
```

* ```uint``` is unnecessary casted to ```int``` and later back to ```uint```:
```solidity
  minipoolIndex = int256(getUint(keccak256("minipool.count")));
  // The minipoolIndex is stored 1 greater than actual value. The 1 is subtracted in getIndexOf()
  setUint(keccak256(abi.encodePacked("minipool.index", nodeID)), uint256(minipoolIndex + 1));
```

* ```keccak256("Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)")``` does not change, could be extracted to a constant:
```solidity
	keccak256(
	abi.encode(
		keccak256("Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)"),
		owner,
		spender,
		value,
		nonces[owner]++,
		deadline
	)
	)
```

* No need to cache ```stakingTotalAssets``` if used only once:
```solidity
	uint256 stakingTotalAssets_ = stakingTotalAssets;

	uint256 nextRewardsAmt = (asset.balanceOf(address(this)) + stakingTotalAssets_) - totalReleasedAssets_ - lastRewardsAmt_;
```