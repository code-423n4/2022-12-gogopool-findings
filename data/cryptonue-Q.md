# [L] Deprecated @rari-capital/solmate

The GoGoPool is still using @rari-capital/solmate library, meanwhile the package has been deprecated. https://www.npmjs.com/package/@rari-capital/solmate.

Using outdated package, can lead to another open security vulnerabilities which being patched on the latest release of a library/package. Thus it's necessary to keep the latest updated package.

Consider to use the updated one, https://github.com/transmissions11/solmate

# [L] Missing TokenggAVAX.sol storage gaps

Storage gaps are a convention for reserving storage slots in a base contract, allowing future versions of that contract to use up those slots without affecting the storage layout of child contracts. The TokenggAVAX, which inherits openzeppelin/contracts-upgradeable library is missing this storage gaps.

https://docs.openzeppelin.com/upgrades-plugins/1.x/writing-upgradeable#storage-gaps

# [L] No emit on storage variable changes

When changing storage variable commonly a setter function it's recommended to emit an event, meanwhile the protocol most of the setter function didn't emit an event. Emitting events allows monitoring activities with off-chain monitoring tools.

for example:

```solidity
File: ClaimNodeOp.sol
40: 	function setRewardsCycleTotal(uint256 amount) public onlySpecificRegisteredContract("RewardsPool", msg.sender) {
41: 		setUint(keccak256("NOPClaim.RewardsCycleTotal"), amount);
42: 	}

File: Oracle.sol
28: 	function setOneInch(address addr) external onlyGuardian {
29: 		setAddress(keccak256("Oracle.OneInch"), addr);
30: 	}

File: ProtocolDAO.sol
96: 	function setTotalGGPCirculatingSupply(uint256 amount) public onlySpecificRegisteredContract("RewardsPool", msg.sender) {
97: 		return setUint(keccak256("ProtocolDAO.TotalGGPCirculatingSupply"), amount);
98: 	}

```

The others are: `setClaimingContractPct()`, `setExpectedAVAXRewardsRate()`, `setRewardsStartTime()`, `setLastRewardsCycleCompleted()`

# [N] Open Todo

```solidity
File: BaseAbstract.sol
6: // TODO remove this when dev is complete
7: // import {console} from "forge-std/console.sol";
8: // import {format} from "sol-utils/format.sol";

File: MinipoolManager.sol
412: 		// TODO Revisit this logic if we ever allow unequal matched funds

File: Staking.sol
203: 	// TODO cant use onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) since we also call from increaseMinipoolCount. Wat do?
```