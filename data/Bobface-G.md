# GoGoPool Gas Report

### Do not store `msg.value` in a local variable
The EVM offers the `VALUE` instruction which only costs 2 gas and which `msg.value` is directly translated to. Storing `msg.value` in a local variable is more expensive than reusing the expression, since it introduces more stack operations.
| File                    | Lines                         | Description |
|---|---|---|
| `contracts/contract/tokens/TokenggAVAX.sol` | 143 | `uint256 totalAmt = msg.value` |
| `contracts/contract/tokens/TokenggAVAX.sol` | 167 | `uint256 assets = msg.value` |

### Replace string storage keys with `uint`s
String identifiers are used as storage keys throughout the contracts. Strings are more expensive during runtime due to their dynamic size. The contract size also significantly grows, making deployment more expensive. Replacing the strings with integers will remove this overhead.
*Note*: Locations left out in this case since this concept is carried through the entire codebase with hundreds of occurences.
*Note*: The QA report also recommends storing these identifiers as `constant`s.


### Precalculate constant hashes
When `keccak256` hashes are calculated which always yield the same result, it is cheaper to store them as constant to avoid recalculation.
| File                    | Lines                         | Description |
|---|---|---|
| `contracts/contract/tokens/TokenggAVAX.sol` | 59 | - |
| `contracts/contract/tokens/TokenggAVAX.sol` | 207 | - |
| `contracts/contract/ClaimNodeOp.sol` | 36 | - |
| `contracts/contract/ClaimNodeOp.sol` | 41 | - |
| `contracts/contract/MinipoolManager.sol` | 248 | - |
| `contracts/contract/MinipoolManager.sol` | 252 | - |
| `contracts/contract/MinipoolManager.sol` | 333 | - |
| `contracts/contract/MinipoolManager.sol` | 433 | - |
| `contracts/contract/MinipoolManager.sol` | 512 | - |
| `contracts/contract/MinipoolManager.sol` | 542 | - |
| `contracts/contract/MinipoolManager.sol` | 612 | - |
| `contracts/contract/MinipoolManager.sol` | 635 | - |
| `contracts/contract/MultisigManager.sol` | 40 | - |
| `contracts/contract/MultisigManager.sol` | 49 | - |
| `contracts/contract/MultisigManager.sol` | 81 | - |
| `contracts/contract/MultisigManager.sol` | 103 | - |
| `contracts/contract/Oracle.sol` | 29 | - |
| `contracts/contract/Oracle.sol` | 38 | - |
| `contracts/contract/Oracle.sol` | 47 | - |
| `contracts/contract/Oracle.sol` | 51 | - |
| `contracts/contract/Oracle.sol` | 58 | - |
| `contracts/contract/Oracle.sol` | 65 - 66 | - |
| `contracts/contract/ProtocolDAO.sol` | 24 - 56 | - |
| `contracts/contract/ProtocolDAO.sol` | 82 | - |
| `contracts/contract/ProtocolDAO.sol` | 87 | - |
| `contracts/contract/ProtocolDAO.sol` | 92 | - |
| `contracts/contract/ProtocolDAO.sol` | 97 | - |
| `contracts/contract/ProtocolDAO.sol` | 117 | - |
| `contracts/contract/ProtocolDAO.sol` | 124 | - |
| `contracts/contract/ProtocolDAO.sol` | 131 | - |
| `contracts/contract/ProtocolDAO.sol` | 136 | - |
| `contracts/contract/ProtocolDAO.sol` | 141 | - |
| `contracts/contract/ProtocolDAO.sol` | 146 | - |
| `contracts/contract/ProtocolDAO.sol` | 151 | - |
| `contracts/contract/ProtocolDAO.sol` | 157 | - |
| `contracts/contract/ProtocolDAO.sol` | 162 | - |
| `contracts/contract/ProtocolDAO.sol` | 169 | - |
| `contracts/contract/ProtocolDAO.sol` | 174 | - |
| `contracts/contract/ProtocolDAO.sol` | 182 | - |
| `contracts/contract/RewardsPool.sol` | 35 - 41 | - |
| `contracts/contract/RewardsPool.sol` | 49 | - |
| `contracts/contract/RewardsPool.sol` | 98 - 99 | - |
| `contracts/contract/RewardsPool.sol` | 106 | - |
| `contracts/contract/RewardsPool.sol` | 111 | - |
| `contracts/contract/RewardsPool.sol` | 116 | - |
| `contracts/contract/RewardsPool.sol` | 121 | - |
| `contracts/contract/RewardsPool.sol` | 164 | - |
| `contracts/contract/Staking.sol` | 348 - 349 | - |


### Reorder `abi.encodePacked` parameters
`abi.encodePacked` is cheaper to execute with less parameters. For example `abi.encodePacked("multisig.item.address.", multisigIndex)` is cheaper than `abi.encodePacked("multisig.item", multisigIndex, ".address")`.
| File                    | Lines                         | Description |
|---|---|---|
| `contracts/contract/BaseAbstract.sol` | 72 | - |
| `contracts/contract/BaseAbstract.sol` | 73 | - |
| `contracts/contract/MinipoolManager.sol` | 116 | - |
| `contracts/contract/MinipoolManager.sol` | 130 | - |
| `contracts/contract/MinipoolManager.sol` | 153 | - |
| `contracts/contract/MinipoolManager.sol` | 246 | - |
| `contracts/contract/MinipoolManager.sol` | 251 | - |
| `contracts/contract/MinipoolManager.sol` | 256 - 263 | - |
| `contracts/contract/MinipoolManager.sol` | 291 | - |
| `contracts/contract/MinipoolManager.sol` | 293 - 294 | - |
| `contracts/contract/MinipoolManager.sol` | 317 | - |
| `contracts/contract/MinipoolManager.sol` | 328 - 329 | - |
| `contracts/contract/MinipoolManager.sol` | 335 | - |
| `contracts/contract/MinipoolManager.sol` | 360 - 362 | - |
| `contracts/contract/MinipoolManager.sol` | 365 | - |
| `contracts/contract/MinipoolManager.sol` | 367 | - |
| `contracts/contract/MinipoolManager.sol` | 370 - 371 | - |
| `contracts/contract/MinipoolManager.sol` | 393 | - |
| `contracts/contract/MinipoolManager.sol` | 398 - 399 | - |
| `contracts/contract/MinipoolManager.sol` | 405 - 409 | - |
| `contracts/contract/MinipoolManager.sol` | 420 - 421 | - |
| `contracts/contract/MinipoolManager.sol` | 451 - 452 | - |
| `contracts/contract/MinipoolManager.sol` | 475 | - |
| `contracts/contract/MinipoolManager.sol` | 488 - 490 | - |
| `contracts/contract/MinipoolManager.sol` | 496 - 500 | - |
| `contracts/contract/MinipoolManager.sol` | 522 | - |
| `contracts/contract/MinipoolManager.sol` | 531 | - |
| `contracts/contract/MinipoolManager.sol` | 580 - 599 | - |
| `contracts/contract/MinipoolManager.sol` | 648 - 652 | - |
| `contracts/contract/MinipoolManager.sol` | 671 - 674 | - |
| `contracts/contract/MinipoolManager.sol` | 677 | - |
| `contracts/contract/MinipoolManager.sol` | 688 - 695 | - |
| `contracts/contract/MultisigManager.sol` | 45 | - |
| `contracts/contract/MultisigManager.sol` | 48 | - |
| `contracts/contract/MultisigManager.sol` | 61 | - |
| `contracts/contract/MultisigManager.sol` | 74 | - |
| `contracts/contract/MultisigManager.sol` | 110 - 111 | - |
| `contracts/contract/Staking.sol` | 82 | - |
| `contracts/contract/Staking.sol` | 89 | - |
| `contracts/contract/Staking.sol` | 96 | - |
| `contracts/contract/Staking.sol` | 105 | - |
| `contracts/contract/Staking.sol` | 112 | - |
| `contracts/contract/Staking.sol` | 119 | - |
| `contracts/contract/Staking.sol` | 128 | - |
| `contracts/contract/Staking.sol` | 135 | - |
| `contracts/contract/Staking.sol` | 142 | - |
| `contracts/contract/Staking.sol` | 151 | - |
| `contracts/contract/Staking.sol` | 158 | - |
| `contracts/contract/Staking.sol` | 166 | - |
| `contracts/contract/Staking.sol` | 175 | - |
| `contracts/contract/Staking.sol` | 189 | - |
| `contracts/contract/Staking.sol` | 198 | - |
| `contracts/contract/Staking.sol` | 210 | - |
| `contracts/contract/Staking.sol` | 219 | - |
| `contracts/contract/Staking.sol` | 226 | - |
| `contracts/contract/Staking.sol` | 233 | - |
| `contracts/contract/Staking.sol` | 242 | - |
| `contracts/contract/Staking.sol` | 250 | - |
| `contracts/contract/Staking.sol` | 350 - 351 | - |
| `contracts/contract/Staking.sol` | 399 | - |
| `contracts/contract/Staking.sol` | 406 - 413 | - |