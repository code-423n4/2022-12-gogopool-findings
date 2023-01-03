1. Comment misspell
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L82

2. transferAVAX is implemented but never called. Since only a contract can call it, there is no use for it.
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L84-L101

3. Although it seems like the storage architecture authors chose to use is clean, it is very prone to misspelling errors, which can be miss looked quickly. 
Since the compiler will not report any issue if one writes to a storage slot named Oracl.OneInch,  and reads from Oracle.OneInch.
I did not find any such error, but still wanted to highlight this, this is more of an observation and comment on architecture than a bug. 

4. staker.count actually shows the number of all addresses that have staked untill now, not the active stakers, as it does not delete user that have unstaked total balance.
If that was the purpose of it, the name should be something like activeStakers for better readability.
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L72-L74
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L348-L349

