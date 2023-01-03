
## Asign and increment `total` on the same line in `staking.sol`
In below solidity code line `430` `staker[total] = s` can be writting as `staker[total++]`
```solidity
contracts/contract/Staking.sol-428-		for (uint256 i = offset; i < max; i++) {
contracts/contract/Staking.sol:429:			Staker memory s = getStaker(int256(i));
contracts/contract/Staking.sol-430-			stakers[total] = s;
contracts/contract/Staking.sol-431-			total++;
contracts/contract/Staking.sol-432-		}
```
code after update
```solidity
contracts/contract/Staking.sol-428-		for (uint256 i = offset; i < max; i++) {
contracts/contract/Staking.sol:429:			Staker memory s = getStaker(int256(i));
contracts/contract/Staking.sol-430-			stakers[total++] = s;
contracts/contract/Staking.sol-432-		}
```



