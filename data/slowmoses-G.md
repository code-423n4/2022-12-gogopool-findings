## Function Should be in Staking Contract

The isEligible function in ClaimNodeOp should be in the Staking contract;
It does not need anything from ClaimNodeOp but several things from Staking.

	function isEligible(address stakerAddr) external view returns (bool) {
		Staking staking = Staking(getContractAddress("Staking"));
		uint256 rewardsStartTime = staking.getRewardsStartTime(stakerAddr);
		uint256 elapsedSecs = (block.timestamp - rewardsStartTime);
		ProtocolDAO dao = ProtocolDAO(getContractAddress("ProtocolDAO"));
		return (rewardsStartTime != 0 && elapsedSecs >= dao.getRewardsEligibilityMinSeconds());
	}
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L46-L52

## Start with Zero Index

Starting the staker index at zero would save unnecessary +1s and -1s.

    int256 stakerIndex = getIndexOf(stakerAddr);
    if (stakerIndex == -1) {
        // create index for the new staker
        stakerIndex = int256(getUint(keccak256("staker.count")));
        addUint(keccak256("staker.count"), 1);
        setUint(keccak256(abi.encodePacked("staker.index", stakerAddr)), uint256(stakerIndex + 1));
        setAddress(keccak256(abi.encodePacked("staker.item", stakerIndex, ".stakerAddr")), stakerAddr);
    }
    increaseGGPStake(stakerAddr, amount);
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L345-L353

	function getIndexOf(address stakerAddr) public view returns (int256) {
		return int256(getUint(keccak256(abi.encodePacked("staker.index", stakerAddr)))) - 1;
	}
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L398-L400