- using encode over encodePacked, to save on gas and more secure overall.

- emit events at the end of functions to save on reverts.

- unchecked operations to save on gas
```
File: Vault.sol
187:		tokenBalances[contractKeyFrom] = tokenBalances[contractKeyFrom] - amount;
188:		tokenBalances[contractKeyTo] = tokenBalances[contractKeyTo] + amount;
```

- add a function to return only price in Oracle contract to keep the code concise, so in Staking.sol line 296, the return value would only contain the price, would save on the cost of fetching timestamp and returning it while being unused.