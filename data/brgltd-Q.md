# [01] Prevent storage variables to receive address(0)

If `Oracle.OneInch` gets configured with address zero, failure to immediately reset the value can result in unexpected behavior for the project.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Oracle.sol#L28-L30

# [02] The guardian can send the DAO rewards to any address without a timelock

Since the funds can be sent to any `recipientAddress`, it can be a rugpull vector.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimProtocolDAO.sol#L19-L35

## Recommendation

Specify a list of addresses that can receive the funds, or implement a timelock functionality to give DAO members extra time in case of large transfers.

# [03] Fake balances can be created due to lack of ERC20 contract existence check

## Impact

Solmate won't check for contract existence. This can lead to silent failures while execute safe transfer functions.

If `Vault.depositToken()` is called with an allowed input token address and a large `amount`, passing a non existing ERC20 address would still compute the amount in `tokenBalances`, potentially affecting token accounting.

This by itself woudn't be an issue (creating a balance for a non existing token). However it's possible for tokens to reuse the same addresses on different networks. An attacker could use this to set traps in the protocol.

## Proof of Concept

1. A non existing ERC20 tokens gets incorrectly approved by the guardian. Assume this token doesn't existing on avalanche, but it exists on polygon or bsc or another chain.
2. An attacker takes over a registered contract and calls `depositToken()` with a large `amount`.
3. Later on, this token gets added on avalanche.
4. The attacker would have a large `tokenBalances` for this token without having spent anything initially.

Note that for this issue to occur in practice, it needs some strong hipoteticals, since the methods to approve and deposit tokens are permissioned and can only be executed by the guardian or registered contracts.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L128

## Recommendation

Inside `Vault.depositToken()` or `Vault.addAllowedToken()`, consider adding a check for contract existence, e.g. checking if `address(token).code.length` is bigger than zero.

# [04] Add an event for parameter changes

Adding evens for parameter changes would facilitade offchain monitoring.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L204

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L209

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L190

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L198

# [05] Downcasting block.timestamp

Downcasting block.timestamp will not be an issue for a long time. However, the project could use a bigger value than 32 bits, to improve the resilience of the protocol. See this [item](https://code4rena.com/reports/2022-07-juicebox/#l-04-splits-cant-be-locked-once-the-timestamp-passes-typeuint48max) marked as a finding for a previous code4rena QA report.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L78

# [06] Missing storage gap for upgradeable contracts

Consider adding a `__gap[]` storage variable to allow for new storage variables in later versions.

See this [link](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps) for a description of this storage variable issue. Please, refer to this [example](https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/Gap10000.sol) for an implementation reference.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L24

# [07] Avoid receiving stale values for setter functions that emit events

Consider adding a revert statement when the new `price` and the new `timestamp` equal the existing `price` and `timestamp`. This can avoid emitting unnecessarely events when receiving stale values.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Oracle.sol#L57-L68

# [08] Lack of checks-effects-interactions

Consider making external calls after state changes to follow the checks effects interactions pattern.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L127-L130

# [09] Missing tests

Some contract are missing test coverage, e.g. `ProtocolDAO` has 60.78% for number of lines and 62.26% for branches. Increasing the coverage can help increase the code quality and it's important for smart contract systems.

# [10] Avoid using the optimizer if possible, due to it's potential security bugs which can affect the contracts in scope

Optimizations are being actively developed and are not considered safe. Check the following [audit](https://github.com/trailofbits/publications/blob/master/reviews/SeaportProtocol.pdf) recommending agaist deployments with the optimizer enabled (item 3 in the audit document).

If possible, consider measuring and documenting the tradeoffs when enabling the optimizer.

# [11] Usage of ERC20 approve which is susceptible to race conditions

The original ERC20 approve function is susceptible race condition which can result in a front-running attack. More information [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L105

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L342

## Recommendation

On the two declarations calling `ggp.approve`, consider implementing a functionality like `increaseAllowance` from [OpenZeppelin](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/ERC20.sol#L177-L181).

# [12] Open TODO

TODOs should be resolved before deployment.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L203

# [13] Replace assert with require or custom error

As described on the solidity [documentation](https://docs.soliditylang.org/en/v0.8.15/control-structures.html#panic-via-assert-and-error-via-require). "The assert function creates an error of type Panic(uint256). … Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L82-L84

# [14] Repeated validation statements

Repeated validation statements can be converted into a functions modifier to improve code reusability

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L47-L50

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L62-L65

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L85-L88

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L113-L116

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L142-L145

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L171-L174

# [15] Use named imports

Named imports are being used on most contracts, except for when importing `BaseAbstract.sol` and `Base.sol`. The project still compiles when replacing `import "./BaseAbstract.sol"` with `import {BaseAbstract} from "./BaseAbstract.sol"`.

If there's any reason why `BaseContract.sol` and `Base.sol` are not named imports, consider adding a comment on the codebase. Otherwise, consider using named imports.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Base.sol#L4

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L6

# [16] Consistent use of address(0) identifier

Consider using only one approach throughout the codebase. E.g. only `address(0)` instead of `address(0x0)`.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L92

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L73

# [17] Order of functions

The solidity [documentation](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions) recommends placing view functions on the bottom of a grouping.

Consider moving `getGuardian()` bellow `confirmGuardian()`.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L51

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L56

Another recommendation is to move private functions bellow the constructor and bellow external/public functions. Consider moving the private functions in `MinipoolManager` bellow the constructor and bellow external/public functions.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L152

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L177

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L196-L201

# [18] Reuse existing computed values

`MinipoolManager.slash()` is a private function only called by `MinipoolManager.recordStakingEnd()`. The values `nodeID`, `owner` and `avaxLiquidStakerAmt` don't need to be recomputed inside `MinipoolManager.recordStakingEnd()` since they are already computed inside `MinipoolManager.slash()`. 

`nodeID` is an argument of `MinipoolManager.recordStakingEnd()` - it will be used to get the minipool index, and the values for `owner` and `avaxLiquidStakerAmt` are computed from the storage inside `MinipoolManager.recordStakingEnd()` logic.

Consider passing `nodeID`, `owner` and `avaxLiquidStakerAmt` as arguments from `MinipoolManager.recordStakingEnd()` to `MinipoolManager.slash()`.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L386

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L399

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L405

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L671-L672

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L674

# [19] Usage of return named variables and explicit values

The return statement on `RewardsPool.getInflationAmt()` can be removed.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L66

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L77

Also, for `MinipoolManager.getMinipoolByNodeID()`, the returned named variable is not being used.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L572-L575

Furthermore, throughout the codebase, some functions return named variables, others return explicit values.

Following function returns an explicit value.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L102

Following function return returns named variables.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L109

Consider adopting the same approach throughout the codebase to improve the explicitness and readability of the code.

# [20] Unused declaration

The declaration `using SafeTransferLib for address;` for `Vault.sol` can be removed. Safe transfer functions are only being used for ERC20 instances on this contract.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L21

# [21] Missing NATSPEC

Consider addding NATSPEC on all external/public functions to improve documentation.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L132

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L142

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L204

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L208

# [22] Fix typo

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L156

# [23] Consistent use of leading underscore for function arguments

For some functions, the arguments are defined leading underscores.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L53-L57

While other don't follow this pattern.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L92-L96

Consider using the same approach throughout the codebase.

# [24] Move revert statements to the top the of function when validating input parameters

Consider moving the logic on [L356-358](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L356-L358) above the initial function declaration on [L354](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L354).

# [25] Add a limit for the maximum number of characters per line

The solidity [documentation](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-length) recommends a maximum of 120 characters.

Consider adding a limit of 120 characters or less to prevent large lines.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L142
