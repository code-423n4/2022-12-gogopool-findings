### [N-01] Remove TODO  
**Filename**: *contracts/contract/BaseAbstract.sol*
```solidity=6
// TODO remove this when dev is complete
// import {console} from "forge-std/console.sol";
// import {format} from "sol-utils/format.sol";
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L6
*contracts/contract/Staking.sol*
```solidity=203
	// TODO cant use onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) since we also call from increaseMinipoolCount. Wat do?
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L203

### [N-02] Modifiers with different restrictions use same name 
`Storage.onlyRegisteredNetworkContract` restricts access to registered contracts and guardian. Modifier with same name in `BaseAbstract` doesn't allow the guardian. `BaseAbstract.guardianOrSpecificRegisteredContract` instead works the same as that in `Storage` contract.
**Filename**: *contracts/contract/BaseAbstract.sol*
```solidity=31
	/// @dev Verify caller is registered version of `contractName`
	modifier onlySpecificRegisteredContract(string memory contractName, address contractAddress) {
		if (contractAddress != getAddress(keccak256(abi.encodePacked("contract.address", contractName)))) {
			revert InvalidOrOutdatedContract();
		}
		_;
	}

	/// @dev Verify caller is a guardian or registered network contract
	modifier guardianOrRegisteredContract() {
		bool isContract = getBool(keccak256(abi.encodePacked("contract.exists", msg.sender)));
		bool isGuardian = msg.sender == gogoStorage.getGuardian();

		if (!(isGuardian || isContract)) {
			revert MustBeGuardianOrValidContract();
		}
		_;
	}
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L31
**Filename**: *contracts/contract/Storage.sol*
```solidity=27
	/// @dev Only allow access from guardian or the latest version of a contract in the GoGoPool network
	modifier onlyRegisteredNetworkContract() {
		if (booleanStorage[keccak256(abi.encodePacked("contract.exists", msg.sender))] == false && msg.sender != guardian) {
			revert InvalidOrOutdatedContract();
		}
		_;
	}
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L27

### [N-03] Remove unused state variables and imports
`ggp` is initialized, but never used in the contract.
**Filename**: *contracts/contract/BaseAbstract.sol*
```solidity=78
	ERC20 public immutable ggp;
```
```solidity=177
	constructor(
		Storage storageAddress,
		ERC20 ggp_,
		TokenggAVAX ggAVAX_
	) Base(storageAddress) {
		version = 1;
		ggp = ggp_;
		ggAVAX = ggAVAX_;
	}
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L78

In `Staking` contract, `safeTransferETH` is not used and this `using...for` can be removed.
```solidity=32
	using SafeTransferLib for address;
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L32
This is also added in `Vault.sol` but not used.

Unused imports in `Vault.sol`.
```solidity=7
import {TokenGGP} from "./tokens/TokenGGP.sol";
import {WAVAX} from "./utils/WAVAX.sol";
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L7

### [N-04] Remove unused transition from `requireValidStateTransition`
The code does not contain any functions that transition from `MinipoolStatus.Launched` to`MinipoolStatus.Error` but the path exists in `requireValidStateTransition`.
```solidity=159
		} else if (currentStatus == MinipoolStatus.Launched) {
			isValid = (to == MinipoolStatus.Staking || to == MinipoolStatus.Error);
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L160

### [N-05] Assignment of default value
In `MinipoolManager.requireValidStateTransition`, explicit assignment of false to `isValid` is not required.
```solidity=168
		} else {
			isValid = false;
		}
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L169

In `MinipoolManager.createMinipool`, the change to `Prelaunch` status can be done only for a previously existing nodeID, since it is the default for new nodeIDs (uint8 of `Prelaunch` status is 0). For this, it can be moved to `resetMinipoolData`.
```solidity=256
		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Prelaunch));
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L256

### [N-06] Missing events for important state changes
The following functions make important state changes with respect to the users of the protocol and should emit events.
`ProtocolDAO.setExpectedAVAXRewardsRate`: Users may decide to stake based on this.

`ProtocolDAO.unregisterContract`: This can change contracts that act as entry points to the users, such as `Staking` and `MinipoolManager`, so events are highly recommended.

`upgradeExistingContract`: For the same reason as above, as this one may modify the logic of the entry points.

### [N-07] Wrong value emitted in `RewardPool.startRewardCycle`
`startRewardCycle` emits the value of  `getRewardsCycleTotalAmt()` before `inflate` is executed. This results in the event containing the total amount of the last rewards cycle instead of the current one.
```solidity=161
		emit NewRewardsCycleStarted(getRewardsCycleTotalAmt());

		// Set start of new rewards cycle
		setUint(keccak256("RewardsPool.RewardsCycleStartTime"), block.timestamp);
		increaseRewardsCycleCount();
		// Mint any new tokens from GGP inflation
		// This will always 'mint' (release) new tokens if the rewards cycle length requirement is met
		// 		since inflation is on a 1 day interval and it needs at least one cycle since last calculation
		inflate();
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L161

Recommended mitigation:
Emit the event after `inflate` is called.

### [N-08] User-facing contracts should include a sweep function
It is recommended to have a sweep function in the user-facing contracts such as `Staking` and `MinipoolManager`.

### [N-09] Prepend `internal` methods with an underscore as per style guidelines
`internal` methods should start with an underscore. This aids the auditability of the code by helping glance over possible entry points and non-entry points.
