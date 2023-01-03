1) Unused error messages:

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L26
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L27
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L28

Consider removing them.

2) Allowed tokens are not easily inspectable by users

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L39

Consider adding them into a enumerableSet and add a view functions.

3) Usage of += / -= for cleaner code

Throughout the codebase 

Example: https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L100

Consider using the += / -= style.

4) Unused function `transferAvax``

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L84

Consider removing the unused function as it exposes an unnecessary governance privilege.

5) Checks-effects-interaction pattern not respected

Throughout the codebase

Example: https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L128

Consider rewriting the codebase to respect the checks-effects-interactions pattern.

6) Non-zero validation for `withdrawToken`

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L137

Consider checking that `withdrawalAddress` is not the zero address. (It would revert for erc20 standard tokens, but for custom token it might even succeed)

7) Byte storage, int storage as well as the assigned setters are unused

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L112

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L120

Consider removing them, if there is no plan to use it in the future.

8) Non-zero validation missing for `setGuardian`

Currently, the `setGuardian` function lacks a non-zero validation

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L120

We acknowledge that this can not become an issue at any time due to the two-step initialization, however, we always recommend a non-zero validation for setter functions because its considered as best-practice.

Consider validating the input address.

9) Unused import `MinipoolManager`

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L5

Consider removing the unused import to keep the contract size reasonable.

10) Sub-optimal design for token inflation

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L82

Currently, the token inflation is not happening via minting, it is just a vesting-like process. However, this might confuse regular users because the totalSupply will always be display with 21_000_000 tokens.

While we assume that this is the intended logic, we still feel like we need to point this out.

11) Multisig reward distribution rounds down

Currently, the multisig reward distribution rounds down which eventually results in a very small loss of rewards:

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L229

Consider adding a multiplier for this arithmetic calculation to decrease the down-rounding impact.

12)  Unused import: `TokenGGP`


https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L5

Consider removing this import

13) Missing non-zero validation for `setOneInch`

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Oracle.sol#L28

Consider validating the input.

14) Users can not easily inspect the list of added Defenders

Currently, there is no easy way for users to inspect the list of Defenders

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L27

Consider adding an enumerableSet.addressSet for the Defenders.

15) `avaxHalfRewards` is rounded down

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L27

the following line of code is rounded down which results in some dust.

Consider executing this operation with an appropiate multiplier.






