## Storage keys can be pre-calculated as constants

These keys are hashed in runtime in the initialize function, but can be stored as a global constant instead. So the hashing can be done offchain.

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ProtocolDAO.sol#L24-L56

```solidity
		if (getBool(keccak256("ProtocolDAO.initialized"))) {
			return;
		}
		setBool(keccak256("ProtocolDAO.initialized"), true);

		// ClaimNodeOp
		setUint(keccak256("ProtocolDAO.RewardsEligibilityMinSeconds"), 14 days);

		// RewardsPool
		setUint(keccak256("ProtocolDAO.RewardsCycleSeconds"), 28 days); // The time in which a claim period will span in seconds - 28 days by default
		setUint(keccak256("ProtocolDAO.TotalGGPCirculatingSupply"), 18_000_000 ether);
		setUint(keccak256("ProtocolDAO.ClaimingContractPct.ClaimMultisig"), 0.20 ether);
		setUint(keccak256("ProtocolDAO.ClaimingContractPct.ClaimNodeOp"), 0.70 ether);
		setUint(keccak256("ProtocolDAO.ClaimingContractPct.ClaimProtocolDAO"), 0.10 ether);

		// GGP Inflation
		setUint(keccak256("ProtocolDAO.InflationIntervalSeconds"), 1 days);
		setUint(keccak256("ProtocolDAO.InflationIntervalRate"), 1000133680617113500); // 5% annual calculated on a daily interval - Calculate in js example: let dailyInflation = web3.utils.toBN((1 + 0.05) ** (1 / (365)) * 1e18);

		// TokenGGAVAX
		setUint(keccak256("ProtocolDAO.TargetGGAVAXReserveRate"), 0.1 ether); // 10% collateral held in reserve

		// Minipool
		setUint(keccak256("ProtocolDAO.MinipoolMinAVAXStakingAmt"), 2_000 ether);
		setUint(keccak256("ProtocolDAO.MinipoolNodeCommissionFeePct"), 0.15 ether);
		setUint(keccak256("ProtocolDAO.MinipoolMaxAVAXAssignment"), 10_000 ether);
		setUint(keccak256("ProtocolDAO.MinipoolMinAVAXAssignment"), 1_000 ether);
		setUint(keccak256("ProtocolDAO.ExpectedAVAXRewardsRate"), 0.1 ether); // Annual rate as pct of 1 avax
		setUint(keccak256("ProtocolDAO.MinipoolCancelMoratoriumSeconds"), 5 days);

		// Staking
		setUint(keccak256("ProtocolDAO.MaxCollateralizationRatio"), 1.5 ether);
		setUint(keccak256("ProtocolDAO.MinCollateralizationRatio"), 0.1 ether);
```

## Inefficient inflation calculation

The inflation is currently calculated by multiplying a constant inflationIntervalsElapsed times, which can be inefficient as time passes (as much as 54 months, 1620 cycle as the test case suggested). 

Instead we can cache the last inflation interval and the corrisponding amount.

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L69-L76

```solidity
		uint256 inflationIntervalsElapsed = getInflationIntervalsElapsed();
		currentTotalSupply = dao.getTotalGGPCirculatingSupply();
		newTotalSupply = currentTotalSupply;

		// Compute inflation for total inflation intervals elapsed
		for (uint256 i = 0; i < inflationIntervalsElapsed; i++) {
			newTotalSupply = newTotalSupply.mulWadDown(inflationRate);
		}
```

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/test/unit/RewardsPool.t.sol#L95-L96

```solidity
		// skip 54 months ahead and start rewards cycle
		skip(142006867);
```

## Do not use assert in production

Use require instead as it will not consume all remaining gas.

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L82-L84

```solidity
	receive() external payable {
		assert(msg.sender == address(asset));
	}
```

