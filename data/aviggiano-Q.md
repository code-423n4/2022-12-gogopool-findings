# 1. Missing event emission from relevant functions

The functions [`Vault.addAllowedToken` and `Vault.removeAllowedToken`](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/Vault.sol#L204-L210) make important changes to the contract state without emitting an event. It is recommended to emit events on both cases when an allowed token is added or removed from the vault.

# 2. Error name corresponds to a specific value, which might change depending on DAO decisions

The error [`Staking.CannotWithdrawUnder150CollateralizationRatio`](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/Staking.sol#L35) corresponds to the specific 150% value. This threshold might change if the DAO chooses to. A more appropriate variable name would be `CannotWithdrawUnderCollateralizationRatio`. It is also possible to pass the current collateralization ratio as the error parameter: `error CannotWithdrawUnderCollateralizationRatio(uint256);`