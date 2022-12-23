# 1. dirty hack in getStakers function of Staking.sol can be removed

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L416

```
	/// @notice Get stakers in the protocol (limit=0 means no pagination)
	/// @param offset The number the result should be offset by
	/// @param limit The limit to the amount of minipools that should be returned
	/// @return stakers in the protocol that adhear to the paramaters
	function getStakers(uint256 offset, uint256 limit) external view returns (Staker[] memory stakers) {
		uint256 totalStakers = getStakerCount();
		uint256 max = offset + limit;
		if (max > totalStakers || limit == 0) {
			max = totalStakers;
		}
		stakers = new Staker[](max - offset);
		uint256 total = 0;
		for (uint256 i = offset; i < max; i++) {
			Staker memory s = getStaker(int256(i));
			stakers[total] = s;
			total++;
		}
		// Dirty hack to cut unused elements off end of return value (from RP)
		// solhint-disable-next-line no-inline-assembly
		assembly {
			mstore(stakers, total)
		}
	}
```

As the stakers'length is exactly max - offse, which would be the same as `total` variable

So there's no need to use assembly dirty hack, and also total variable can be removed

# 2. InsufficientBalance revert can be removed (auto adjust amount)

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L360

 can be modified as:

```
	function withdrawGGP(uint256 amount) external whenNotPaused {
                uint256 stakingAmount = getGGPStake(msg.sender);
		if (amount > stakingAmount) {
			amount = stakingAmount;
		}
```

In this design, user can easily withdraw all his GGP staking by specifying amount=(uint256).max-1