### ++i COSTS LESS GAS COMPARED TO i++
++i costs less gas compared to i++ or i += 1 for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration)
Instances include:

File: MinipoolManager.sol  
line [619](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L619) 
```
		for (uint256 i = offset; i < max; i++) {
			Minipool memory mp = getMinipool(int256(i));
			if (mp.status == uint256(status)) {
				minipools[total] = mp;
				total++;
			}
```
File:MultisigManager.sol
line [84](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MultisigManager.sol#L84)
```
              for (uint256 i = 0; i < total; i++) {
			(addr, enabled) = getMultisig(i);
			if (enabled) {
				return addr;
			}
		}
```

File:Ocyticus.sol
line: [61](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Ocyticus.sol#L61)
```
		for (uint256 i = 0; i < count; i++) {
			(addr, enabled) = mm.getMultisig(i);
			if (enabled) {
				mm.disableMultisig(addr);
			}
		}
```
File:RewardsPool.sol
line: [74](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L74)
```
		for (uint256 i = 0; i < inflationIntervalsElapsed; i++) {
			newTotalSupply = newTotalSupply.mulWadDown(inflationRate);
		}
```
File:RewardsPool.sol
line: [215](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L215)
```
		for (uint256 i = 0; i < count; i++) {
			(address addr, bool enabled) = mm.getMultisig(i);
			if (enabled) {
				enabledMultisigs[enabledCount] = addr;
				enabledCount++;
			}
		}
```
File:RewardsPool.sol
line:[230](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L230)
```
		for (uint256 i = 0; i < enabledMultisigs.length; i++) {
			vault.withdrawToken(enabledMultisigs[i], ggp, tokensPerMultisig);
		}
```