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

