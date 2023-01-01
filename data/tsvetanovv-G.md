## [G-01] UNCHECKING ARITHMETICS OPERATIONS THAT CAN’T UNDERFLOW/OVERFLOW

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block.
[Link](https://docs.soliditylang.org/en/v0.8.10/control-structures.html#checked-or-unchecked-arithmetic)

```
MinipoolManager.sol
619:	for (uint256 i = offset; i < max; i++) {

Ocyticus.sol
61: 	for (uint256 i = 0; i < count; i++) {

RewardsPool.sol
74: 	for (uint256 i = 0; i < inflationIntervalsElapsed; i++) {
215: 	for (uint256 i = 0; i < count; i++) {
230: 	for (uint256 i = 0; i < enabledMultisigs.length; i++) {
```

## [G-02] `x = x - y` costs less gas than `x -= y`, same for addition
```
TokenggAVAX.sol
245: 		totalReleasedAssets -= amount;
252:		totalReleasedAssets += amount;

ERC20Upgradeable.sol
79: 		balanceOf[msg.sender] -= amount;
84:	 	balanceOf[to] += amount;
101: 		balanceOf[from] -= amount;
106: 		balanceOf[to] += amount;
184:		totalSupply += amount;
189:		balanceOf[to] += amount;
196:		balanceOf[from] -= amount;
201:		totalSupply -= amount;
```

You can replace all `-=` and `+=` occurrences to save gas.