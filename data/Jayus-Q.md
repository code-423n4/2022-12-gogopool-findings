1. Events should be emitted only after actions have occurred and not prior.
Some occurrences of this exist but not limited to Vault.sol and Staking.sol
2. No check for zero amount in `stakeGGP(uint256 amount)` in Staking.sol
3. No check for zero assets `withdrawAvax(uint256 assets)` in TokenggAvax.sol