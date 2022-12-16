### 1st Bug
Unsafe ERC20 Operation(s)

#### Impact
Issue Information: 
ERC20 operations can be unsafe due to different implementations and vulnerabilities in the standard.

It is therefore recommended to always either use OpenZeppelin's SafeERC20 library or at least to wrap each operation in a require statement.

To circumvent ERC20's approve functions race-condition vulnerability use OpenZeppelin's SafeERC20 library's safe{Increase|Decrease}Allowance functions.

In case the vulnerability is of no danger for your implementation, provide enough documentation explaining the reasonings.

Example
🤦 Bad:

IERC20(token).transferFrom(msg.sender, address(this), amount);
🚀 Good (using OpenZeppelin's SafeERC20):

import {SafeERC20} from "openzeppelin/token/utils/SafeERC20.sol";

// ...

IERC20(token).safeTransferFrom(msg.sender, address(this), amount);
🚀 Good (using require):

bool success = IERC20(token).transferFrom(msg.sender, address(this), amount);
require(success, "ERC20 transfer failed");

#### Findings:
```
2022-12-gogopool/contracts/contract/ClaimNodeOp.sol::105 => ggp.approve(address(staking), restakeAmt);
2022-12-gogopool/contracts/contract/Staking.sol::342 => ggp.approve(address(vault), amount);
```
#### Tools used
Manual code review

### 2nd Bug
Unspecific Compiler Version Pragma

#### Impact
Issue Information: 
Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

Example
🤦 Bad:

pragma solidity ^0.8.0;
🚀 Good:

pragma solidity 0.8.4;

#### Findings:
```
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol::2 => pragma solidity >=0.8.0;
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol::2 => pragma solidity >=0.8.0;
```
#### Tools used
Manual code review