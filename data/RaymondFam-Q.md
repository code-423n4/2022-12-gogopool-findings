## Open TODOs
Open TODOs can point to architecture or programming issues that still need to be resolved. Consider resolving them before deploying.

Here are the instances entailed:

[File: BaseAbstract.sol#L6-L8](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L6-L8)

```
// TODO remove this when dev is complete
// import {console} from "forge-std/console.sol";
// import {format} from "sol-utils/format.sol";
```
[File: Staking.sol#L203](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L203)

```
	// TODO cant use onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) since we also call from increaseMinipoolCount. Wat do?
```
## Modularity on import usages
For cleaner Solidity code in conjunction with the rule of modularity and modular programming, use named imports with curly braces instead of adopting the global import approach. Throughout the code bases, most of the imports are adopting the recommended approach but some are not. Consider synchronizing them to the recommended method.

Here are the instances entailed:

[File: Base.sol#L4](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Base.sol#L4)
[File: BaseUpgradeable.sol#L4](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseUpgradeable.sol#L4)

```diff
- import "./BaseAbstract.sol";
+ import { BaseAbstract} from "./BaseAbstract.sol";
```
[File: ClaimNodeOp.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol)

```diff
- import "./Base.sol";
+ import {Base} from "./Base.sol";
```
## Inadequate NatSpec
Solidity contracts can use a special form of comments, i.e., the Ethereum Natural Language Specification Format (NatSpec) to provide rich documentation for functions, return variables and more. Please visit the following link for further details:

https://docs.soliditylang.org/en/v0.8.16/natspec-format.html

Here are the contract instances with missing NatSpec in its entirety:

[File: BaseUpgradeable.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseUpgradeable.sol)

## Typo mistakes

[File: MinipoolManager.sol#L309](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L309)

```diff
-	/// @notice Verifies that the minipool related the the given node ID is able to a validator
+	/// @notice Verifies that the minipool related to the given node ID is able to become a validator
```
## 5% annual calculated on a daily interval not precised enough
[File: ProtocolDAO.sol#L41](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L41)

The max safe integer for JavaScript without losing precision is `(2^53 – 1)`, which is [around 9 quadrillion](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number/MAX_SAFE_INTEGER#:~:text=The%20Number.,larger%20integers%2C%20consider%20using%20BigInt%20.). As such, the code line linked above that entails 19 digits will not be fully accurate.

The calculated number should be `1,000,133,680,617,113,440` instead of `1,000,133,680,617,113,500`.

Note: `BigInt((1 + 0.05) ** (1 / (365)) * 1e18)` would also yield an inaccurate figure, `1000133680617113472n`.

`dao.getInflationIntervalRate()`, i.e. 1000133680617113500, is currently used to calculate the inflation amount in the code line below:

[File: RewardsPool.sol#L75](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L75) 

```
			newTotalSupply = newTotalSupply.mulWadDown(inflationRate);
```
It has no impact in the protocol with the last three differing digits since the total supply of GGP is capped at 22,500,000. It could be an issue elsewhere if the a different reward token were to be implemented with its total supply capped at a much higher value, e.g. in quadrillion like Shiba Inu.  

## Hard coded initialization
In `ProtocolDAO.sol`, `initialize()` could only be called once by `onlyGuardian` because of the bool logic in [lines 24 - 27](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L24-L27). All getters are hard coded with only `TotalGGPCirculatingSupply`, `ClaimingContractPct`, and `ExpectedAVAXRewardsRate` having their respective setters implemented in the contract.

Consider implementing setters for all of them where possible so that the community will have more proposal options when the protocol go full DAO. Otherwise, a proposal for changing `dao.getInflationIntervalRate()`, currently hard coded to 1000133680617113500, for an example, would not be viable if there was no setter function catered for it.

## Inexpedient ternary logic
The ternary code line below:

[File: ProtocolDAO.sol#L118](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L118)

```
		return rate < 1 ether ? 1 ether : rate;
```
is deemed impractical considering 1 ether, which is 1e18, is going to make the fraction of the following arithmetic calculation always equal to 1:

[File: RewardsPool.sol#L75](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L75)

```
			newTotalSupply = newTotalSupply.mulWadDown(inflationRate);
```
With `1 ether / WAD == 1`, GGP would never be able to inflate and `ggp.totalSupply()` would forever stay at the initialized value of 18,000,000. Additionally, running a GGP rewards cycle via `startRewardsCycle()` would also be impossible because the function would readily revert on [the second if block](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L174-L176) due to [`newTokens == 0`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L92). 

There is no risk foreseeable since `InflationIntervalRate` is currently hard coded to 5 % annual. It would have to be refactored to assume something higher than 1 ether if `(rate < 1 ether) == false` if a setter for `InflationIntervalRate` was going to be implemented in the contract. 
