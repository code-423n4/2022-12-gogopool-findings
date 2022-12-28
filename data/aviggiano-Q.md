# 1. Missing event emission from relevant functions

The functions [`Vault.addAllowedToken` and `Vault.removeAllowedToken`](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/Vault.sol#L204-L210) make important changes to the contract state without emitting an event. It is recommended to emit events on both cases when an allowed token is added or removed from the vault.

# 2. Error name corresponds to a specific value, which might change depending on DAO decisions

The error [`Staking.CannotWithdrawUnder150CollateralizationRatio`](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/Staking.sol#L35) corresponds to the specific 150% value. This threshold might change if the DAO chooses to. A more appropriate variable name would be `CannotWithdrawUnderCollateralizationRatio`. It is also possible to pass the current collateralization ratio as the error parameter: `error CannotWithdrawUnderCollateralizationRatio(uint256);`

# 3. Inconsistent event ordering emission between `TokenggAVAX.depositAVAX` and `TokenggAVAX.deposit`

The function `depositAVAX` emit `Deposit` before emitting `Transfer`. This is the opposite behavior from the [`ERC4626Upgradeable.deposit`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L42) inherited implementation, which performs _first_ a `Transfer` and _then_ a `Deposit`. 

Consider changing the function order for the sake of consistency:

```diff
diff --git a/contracts/contract/tokens/TokenggAVAX.sol b/contracts/contract/tokens/TokenggAVAX.sol
index dc3948d..2f813c3 100644
--- a/contracts/contract/tokens/TokenggAVAX.sol
+++ b/contracts/contract/tokens/TokenggAVAX.sol
@@ -170,10 +170,11 @@ contract TokenggAVAX is Initializable, ERC4626Upgradeable, UUPSUpgradeable, Base
 			revert ZeroShares();
 		}
 
-		emit Deposit(msg.sender, msg.sender, assets, shares);
-
 		IWAVAX(address(asset)).deposit{value: assets}();
 		_mint(msg.sender, shares);
+
+		emit Deposit(msg.sender, msg.sender, assets, shares);
+
 		afterDeposit(assets, shares);
 	}
 

```