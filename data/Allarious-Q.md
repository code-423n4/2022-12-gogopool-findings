L-01, ProtocolDAO.sol lines 209 - 216: 
`upgradeExistingContract` mistakenly removes the address value of the new contract if the new contract’s name is the same as the old one. This can be easily fixed with unregistering first and then registering with can be done manually by the guardian if he is careful.
Also, if the name of the contract is changing, the value of the previous contract should be moved to the new one. However, since the name of the contracts is hardcoded into the other ones for handling permissions, I assume the name of the contract would stay the same or a whole new set of contract should be deployed together.

PoC is included below:
```
	function testUpgradeExistingContractSameName() public {
		address addr = randAddress();
		string memory name = "existingName";

		address existingAddr = randAddress();
		string memory existingName = "existingName";

		vm.prank(guardian);
		dao.registerContract(existingAddr, existingName);
		assertEq(store.getBool(keccak256(abi.encodePacked("contract.exists", existingAddr))), true);
		assertEq(store.getAddress(keccak256(abi.encodePacked("contract.address", existingName))), existingAddr);
		assertEq(store.getString(keccak256(abi.encodePacked("contract.name", existingAddr))), existingName);

		vm.prank(guardian);
		dao.upgradeExistingContract(addr, name, existingAddr);
		assertEq(store.getAddress(keccak256(abi.encodePacked("contract.address", name))), address(0));
	}
```

L-02, MinipoolManager, lines 670 - 684: The slash function slashes a node operator for the amount of whole duration. Since the cycles are in 14 days and the slashing is checked in the `recordStakingEnd`, if an operator has a duration of for example 180 days, but fail to gin rewards for one cycle, she will be slashed for the amount of whole duration and that would not be fair not fair.

L-03, CliamNodeOp.sol lines 56-75: if `calculateAndDistributeRewards` is called by Rialto in the middle of a rewards cycle, can cause the stakers to lose the rewards for the rest of the round. It would be good to setup a check on how soon the `calculateAndDistributeRewards` is called.

L-04, RewardsPool.sol, line 188-230 : The absence of an enabled multisig will stop `startRewardsCycle` and the `inflate` process. This is important to notice since the defenders can only disable the multisigs when they call pauseEverything() in Ocyticus and multisigs should be enabled separately by the guardian. This stops the inflation rewards distribution and should be taken care of.

L-05, MinipoolManager, lines 196 - 201: There are not checks on the duration and the delegateFee. But according to the documentation, there is a defined range for them.

L-06 MinipoolManager, lines 196 - 201: The duration should be a multiple of 14 days, or should find the closest multiple of 14 that is smaller than the duration. In the current setting, since duration is set by the user input of duration, and is being used in slashing, can cause the slashing function to calculate the outputs wrong. for example, if a user inputs 17 days as a duration and doesn't get any profit, the protocol slashes him for 17 days instead of 14.

