# 1) Prefix increments
Prefix increments (++i) are cheaper than postfix increments - 6 gas. 
6 Instances:
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L84
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L61
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L619
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L74
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L215
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L428

Recommended Mitigation Steps:
Consider changing i++ to ++i.
