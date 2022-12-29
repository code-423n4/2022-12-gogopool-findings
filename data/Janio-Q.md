## The functions `Staking.increaseGGPStake` and `Staking.decreaseGGPStake` should be private

The functions `Staking.increaseGGPStake` and `Staking.decreaseGGPStake` are only called inside the contract, therefore they should be private, instead of internal (letting them internal adds a risk of future contracts that could inherits from Staking to change staking funds inadvertently).

File: [`Staking.sol#L87`](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L87)

```solidity
      function increaseGGPStake(address stakerAddr, uint256 amount) internal {
		int256 stakerIndex = requireValidStaker(stakerAddr);
		addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpStaked")), amount);
	}

      function decreaseGGPStake(address stakerAddr, uint256 amount) internal {
		int256 stakerIndex = requireValidStaker(stakerAddr);
		subUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpStaked")), amount);
	}
```