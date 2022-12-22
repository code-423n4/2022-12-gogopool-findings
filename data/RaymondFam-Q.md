## Open TODOs
Open TODOs can point to architecture or programming issues that still need to be resolved. Consider resolving them before deploying.

Here are the instances entailed:

[File: BaseAbstract.sol#L6-L8](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L6-L8)

```
// TODO remove this when dev is complete
// import {console} from "forge-std/console.sol";
// import {format} from "sol-utils/format.sol";
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

