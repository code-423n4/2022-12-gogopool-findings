### [L-01] TokenggAVAX.sol will not be able to function in ~83 years

The maximum value of a uint32 is 4294967295.This is equal to ~136 years (https://www.flightpedia.org/convert/4294967295-seconds-to-year.html).
Since the block.timestamp has the value 0 on Jan. 1st, 1970, the maximum value will be reached around the year ~2106. Use a bigger uint for block.timestamp.

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L78

### [L-02] No storage gap for upgradeable contracts

For SafeAccessControlEnumerableUpgradeable and SafeOwnableUpgradeable, which are upgradeable abstract contracts, inheriting contracts may introduce new variables. In order to be able to add new variables to the upgradeable abstract contract without causing storage collisions, a storage gap should be added to the upgradeable abstract contract.

If no storage gap is added, when the upgradable abstract contract introduces new variables, it may override the variables in the inheriting contract. Consider adding a storage gap at the end of the upgradeable abstract contract.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L78
```
uint256[50] private __gap;
```
### [L-03] Order of functions

The solidity [documentation](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions) recommends the following order for functions:

constructor receive function (if exists) fallback function (if exists) external public internal private

The instance bellow shows private above constructor 

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L670-L683

### [L-04] Replace assert with require or custom error

As described on the solidity [documentation](https://docs.soliditylang.org/en/v0.8.15/control-structures.html#panic-via-assert-and-error-via-require). "The assert function creates an error of type Panic(uint256). … Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix.

Even if the code is expected to be unreacheable, consider using a custom error instead of assert.

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L83

### [N-01] The nonReentrant modifier should occur before all other modifiers

This is a best-practice to protect against re-entrancy in other modifiers.

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L61
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L141

### [N-02] Consider adding NATSPEC on all external/public functions to improve documentation.

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L132-L256

### [N-03] Event is missing indexed fields

Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it’s not necessarily best to index the maximum allowed per event (three fields). Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L38
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L24-L28