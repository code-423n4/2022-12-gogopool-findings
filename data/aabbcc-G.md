https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L230

```
		for (uint256 i = 0; i < enabledMultisigs.length; i++) { //@audit it will call the .length method every time, the best way is use a local var to store the length then to use.
			vault.withdrawToken(enabledMultisigs[i], ggp, tokensPerMultisig);
		}
```
for example 

```
                uint256 multiSigsLength = enabledMultisigs.length;
		for (uint256 i = 0; i < multiSigsLength; i++) {
			vault.withdrawToken(enabledMultisigs[i], ggp, tokensPerMultisig);
		}
```
