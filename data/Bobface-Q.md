# GoGoPool QA Report

## Low risk

### Timestamp as uint32
Using `uint32` for timestamps only allows to handle times up to roughly the year 2108. Afterwards the contract would be unable to operate due to the conversions to `uint32` failing. Using `uint64` is recommended.
| File                    | Lines                         | Description |
|---|---|---|
| `contracts/contract/tokens/TokenggAVAX.sol` | 78 | `block.timestamp.safeCastTo32()`|
| `contracts/contract/tokens/TokenggAVAX.sol` | 89 | `block.timestamp.safeCastTo32()`|

## Non-Critical

### Use constants for storage IDs
The contracts use strings for storage keys. These identifiers should be set up as `constant`s instead of being retyped for every use.
*Note*: Locations left out in this case since this concept is carried through the entire codebase with hundreds of occurences.
*Note*: The gas report also makes more recommendations on handling these identifiers differently to become more gas efficient.


### ERC-4626 offers more functionality for certain functions
`TokenggAVAX` extends the ERC-4626 vault standard. Since it should also be possible for users to directly deposit `AVAX` instead of `WAVAX`, it adds functions such as `depositAVAX()` on top of the existing ERC-4626 `deposit()`. However, the ERC-4626 standard offers more functionality, specifically specifying a different receiver and using allowances to spend from different accounts. The same parameters should be implemented in the added functions to provide the same feature-set to end users.
| File                    | Lines                         | Description |
|---|---|---|
| `contracts/contract/tokens/TokenggAVAX.sol` | 166 | `function depositAVAX()` should include a `receiver` parameter (see ERC-4626 `deposit()`) |
| `contracts/contract/tokens/TokenggAVAX.sol` | 180 | `function withdrawAVAX(uint256 assets)` should include a `receiver` and `owner` parameter (see ERC-4626 `withdraw()`) |
| `contracts/contract/tokens/TokenggAVAX.sol` | 191 | `function redeemAVAX(uint256 shares)` should include a `receiver` and `owner` parameter (see ERC-4626 `redeem()`) |

### Development comments
Comments left over from the development process should be removed.
| File                    | Lines                         | Description |
|---|---|---|
| `contracts/contract/BaseAbstract.sol` | 6 - 8 | - |

### Typos
Typos in comments or code.
| File                    | Lines                         | Description |
|---|---|---|
| `contracts/contract/Vault.sol` | 156 | `toke` -> `token`|
| `contracts/contract/Vault.sol` | 158 | `, ,` -> `,`|
