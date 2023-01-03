### Gas Optimizations

||Issue|Instance|
|---|---|---|
|[GAS-01]|State Variable should be cached to save gas on 2+ reads|8|
|[GAS-02]|Unchecked For Loop can save more gas than pre-increment or post-increment|5|
|[GAS-03]|Save slightly more on loads by using uint256|1|

### Details   
#### [GAS-01] State Variable should be cached to save gas on 2+ reads

Save an unnecessary sload by caching 2+ storage variable reads.
P.S these specific instances where not mentioned in the automated report

instances:8

File: Vault.sol
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol

```javascript
71 if (avaxBalances[contractName] < amount)
75 avaxBalances[contractName] = avaxBalances[contractName] - amount;
95 if (avaxBalances[fromContractName] < amount)
100 avaxBalances[toContractName] = avaxBalances[toContractName] + amount;
151 if (tokenBalances[contractKey] < amount)
155	tokenBalances[contractKey] = tokenBalances[contractKey] - amount;
183	if (tokenBalances[contractKeyFrom] < amount)
187 tokenBalances[contractKeyFrom] = tokenBalances[contractKeyFrom] - amount;
```

#### [GAS-02] Unchecked For Loop can save more gas than pre-increment or post-increment

You can safely use unchecked For loop without realisitic risk of overflow, saving gas on each iteration

instances:5


File: MinipoolManager.sol
Line: 619-625
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L619-L625

```javascript
		for (uint256 i = offset; i < max; i++) {
			Minipool memory mp = getMinipool(int256(i));
			if (mp.status == uint256(status)) {
				minipools[total] = mp;
				total++;
			}
		}
```


File: RewardPool.sol


https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L74-L76

```javascript
		for (uint256 i = 0; i < inflationIntervalsElapsed; i++) {
			newTotalSupply = newTotalSupply.mulWadDown(inflationRate);
		}
```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L214-L221
```javascript
		// there should never be more than a few multisigs, so a loop should be fine here
		for (uint256 i = 0; i < count; i++) {
			(address addr, bool enabled) = mm.getMultisig(i);
			if (enabled) {
				enabledMultisigs[enabledCount] = addr;
				enabledCount++;
			}
		}
```

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L230-L232
```javascript
		for (uint256 i = 0; i < enabledMultisigs.length; i++) {
			vault.withdrawToken(enabledMultisigs[i], ggp, tokensPerMultisig);
		}
```

File: Staking.sol
Line: 428-32
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L428-L432

```javascript
		for (uint256 i = offset; i < max; i++) {
			Staker memory s = getStaker(int256(i));
			stakers[total] = s;
			total++;
		}
```

#### [GAS-03] Save slightly more on reads by using uint256

Uint192 is not packed with any other variable in storage and taking a full slot even though its less than 32 bytes, so reading will need slight more operation to retrieve value

instances:1

File: TokenggAVAX.sol
Line: 52
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L50

```javascript
52	uint192 public lastRewardsAmt;
```