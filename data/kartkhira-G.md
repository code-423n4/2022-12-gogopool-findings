Issue : Re-entrant guard at Vault.sol #61 is not really required as internal balances are updated before sending Avax.

Code: https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L61

Fix :  Remove the re-entrancy guard as it's just increasing gas usage