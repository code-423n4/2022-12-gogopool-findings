- using encode over encodePacked, to save on gas and more secure overall.

- emit events at the end of functions to save on reverts.

- unchecked operations to save on gas
```
File: Vault.sol
187:		tokenBalances[contractKeyFrom] = tokenBalances[contractKeyFrom] - amount;
188:		tokenBalances[contractKeyTo] = tokenBalances[contractKeyTo] + amount;
```

- add a function to return only price in Oracle contract to keep the code cohesive, so in Staking.sol line 296, the return value would only contain the price, would save on the cost of fetching timestamp and returning it while being unused.

- no need to use SafeERC20 for GGP, since the token adhere to ERC20 and return a true at all times for successful transfers otherwise it reverts.

```
File: Staking.sol
321:		ggp.safeTransferFrom(msg.sender, address(this), amount);
330:		ggp.safeTransferFrom(msg.sender, address(this), amount);
```