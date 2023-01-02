## Pass Address in Constructor

Fixed addresses (for non-upgradeable contracts) should be passed in the constructor. Using a centralized storage for such contract addresses needlessly opens an additional attack surface.

Below is one instance of this issue. There are some more. One can grep for "getContractAddress" to find them all.

	Vault vault = Vault(getContractAddress("Vault"));
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L101

## Typo

"fucns" should be "funds"

	/// @param toContractName Name of the contract fucns are being transferred to
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L82

## Open Todos

	// TODO remove this when dev is complete
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L6

	// TODO cant use onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) since we also call from increaseMinipoolCount. Wat do?
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L203

## Inconsistent Parameter Names

"ggp_" is just about the only parameter name with an underscore. (The Staking constructor has the same problem.)

	constructor(Storage storageAddress, ERC20 ggp_) Base(storageAddress) {
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L29

## Missing Division by Zero Check

An explicit check for division by zero might make sense.

	return (block.timestamp - startTime) / dao.getInflationIntervalSeconds();
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L60

## GGPInflated Signal Before Actual Inflation

The signal should not be emitted before the fact.

	emit GGPInflated(newTokens);

	dao.setTotalGGPCirculatingSupply(newTotalSupply);
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L94-L96

## Avoid Dirty Hacks

If it is a 'Dirty hack', then don't do it.

	// Dirty hack to cut unused elements off end of return value (from RP)
	// solhint-disable-next-line no-inline-assembly
	assembly {
		mstore(enabledMultisigs, enabledCount)
	}
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L223-L227

	// Dirty hack to cut unused elements off end of return value (from RP)
	// solhint-disable-next-line no-inline-assembly
	assembly {
		mstore(stakers, total)
	}
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L433-L437

## Misleading Error Name

The "150" in "CannotWithdrawUnder150CollateralizationRatio();" can be parameterized so this name is misleading.

	ProtocolDAO dao = ProtocolDAO(getContractAddress("ProtocolDAO"));
	if (getCollateralizationRatio(msg.sender) < dao.getMaxCollateralizationRatio()) {
		revert CannotWithdrawUnder150CollateralizationRatio();
	}
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L367-L370

## Missing Validity Check

Below no check is made, if the requested staker index is valid. Calling 'requireValidStaker()' would seem appropriate.

	/// @notice Gets the staker information using the staker's index
	/// @param stakerIndex Index of the staker
	/// @return staker struct containing the staker's properties
	function getStaker(int256 stakerIndex) public view returns (Staker memory staker) {
		staker.ggpStaked = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpStaked")));
		staker.avaxAssigned = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssigned")));
		staker.avaxStaked = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxStaked")));
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L402-L408

## Missing Check for Underflow

GGP-Stake could get decreased below zero and underflow.

	function slashGGP(address stakerAddr, uint256 ggpAmt) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
		Vault vault = Vault(getContractAddress("Vault"));
		decreaseGGPStake(stakerAddr, ggpAmt);
		vault.transferToken("ProtocolDAO", ggp, ggpAmt);
	}
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L379-L383