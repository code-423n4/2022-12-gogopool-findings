# GoGoPool gas optimization report by Timenov

## Summary
G-01 Using `> 0` costs more gas than using `!= 0`
G-02 Using `i++` costs more gas than using `++i`. Also can add `unchecked { ++i; }` in for loops where overflow is not possible

### [G-01] Using `> 0` costs more gas than using `!= 0`
This change saves 4 gas per instance.

*There are 9 instances of this issue.*

```solidity
File: contracts/contract/ClaimNodeOp.sol

103: if (restakeAmt > 0)
109: if (claimAmt > 0)
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol

```solidity
File: contracts/contract/RewardsPool.sol

142: if (claimContractPct > 0 && currentCycleRewardsTotal > 0)
152: return getRewardsCyclesElapsed() > 0 && getInflationIntervalsElapsed() > 0;
181: if (daoClaimContractAllotment > 0)
186: if (multisigClaimContractAllotment > 0)
191: if (nopClaimContractAllotment > 0)
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol

### [G-02] Using `i++` costs more gas than using `++i`. Also can add `unchecked { ++i; }` in for loops where overflow is not possible
Changing `i++` to `++i` saves 57 gas on instance. Using `unchecked { ++i; }` instead of `i++` saves 1437 gas on instance.

*There are 10 instances of this issue.*

```solidity
File: contracts/contract/MinipoolManager.sol

619: for (uint256 i = offset; i < max; i++)
623: total++;
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol

```solidity
File: contracts/contract/MultisigManager.sol

84: for (uint256 i = 0; i < total; i++)
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol

```solidity
File: contracts/contract/Ocyticus.sol

61: for (uint256 i = 0; i < count; i++)
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol

```solidity
File: contracts/contract/RewardsPool.sol

74: for (uint256 i = 0; i < inflationIntervalsElapsed; i++)
215: for (uint256 i = 0; i < count; i++)
219: enabledCount++;
230: for (uint256 i = 0; i < enabledMultisigs.length; i++)
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol

```solidity
File: contracts/contract/Staking.sol

428: for (uint256 i = offset; i < max; i++)
431: total++;
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol