# [G-01] ```X = X + Y``` CONSUMES LESS GAS THAN ```X += Y``` (11 INSTANCES)

***35 gas per instance***

main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol: [L84](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L84), [L101](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L101), [L106](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L106), [L184](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L184), [L189](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L189), [L196](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L196), [L201](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L201)
main/contracts/contract/tokens/TokenggAVAX.sol: [L149](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L149), [L160](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L160), [L245](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L245), [L252](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L252)

# [G-02] UNCHECKING ARITHMETICS OPERATIONS THAT CAN’T UNDERFLOW/OVERFLOW (7 INSTANCES)

main/contracts/contract/MinipoolManager.sol: [L619](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L619)

```diff
-               for (uint256 i = offset; i < max; i++) {
-                       Minipool memory mp = getMinipool(int256(i));
-                       if (mp.status == uint256(status)) {
-                               minipools[total] = mp;
-                               total++;
+               unchecked {
+                       for (uint256 i = offset; i < max; ++i) {
+                               Minipool memory mp = getMinipool(int256(i));
+                               if (mp.status == uint256(status)) {
+                                       minipools[total] = mp;
+                                       ++total;
+                               }
                        }
                }
```

main/contracts/contract/MultisigManager.sol: [L84](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L84)

```diff
-               for (uint256 i = 0; i < total; i++) {
+               for (uint256 i = 0; i < total;) {
                        (addr, enabled) = getMultisig(i);
                        if (enabled) {
                                return addr;
                        }
+
+                       unchecked { ++i; }
                }
```

main/contracts/contract/Ocyticus.sol: [L61](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L61)

```diff
-               for (uint256 i = 0; i < count; i++) {
+               for (uint256 i = 0; i < count;) {
                        (addr, enabled) = mm.getMultisig(i);
                        if (enabled) {
                                mm.disableMultisig(addr);
                        }
+
+                       unchecked { ++i; }
                }
```

main/contracts/contract/RewardsPool.sol: [L74](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L74), [L215](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L215), [L230](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L230)

```diff
                // Compute inflation for total inflation intervals elapsed
-               for (uint256 i = 0; i < inflationIntervalsElapsed; i++) {
+               for (uint256 i = 0; i < inflationIntervalsElapsed;) {
                        newTotalSupply = newTotalSupply.mulWadDown(inflationRate);
+                       unchecked { ++i; }
                }
                return (currentTotalSupply, newTotalSupply);
        }
@@ -212,11 +213,13 @@ contract RewardsPool is Base {
                address[] memory enabledMultisigs = new address[](count);
 
                // there should never be more than a few multisigs, so a loop should be fine here
-               for (uint256 i = 0; i < count; i++) {
-                       (address addr, bool enabled) = mm.getMultisig(i);
-                       if (enabled) {
-                               enabledMultisigs[enabledCount] = addr;
-                               enabledCount++;
+               unchecked {
+                       for (uint256 i = 0; i < count; i++) {
+                               (address addr, bool enabled) = mm.getMultisig(i);
+                               if (enabled) {
+                                       enabledMultisigs[enabledCount] = addr;
+                                       enabledCount++;
+                               }
                        }
                }
 
@@ -227,8 +230,9 @@ contract RewardsPool is Base {
                }
 
                uint256 tokensPerMultisig = allotment / enabledCount;
-               for (uint256 i = 0; i < enabledMultisigs.length; i++) {
+               for (uint256 i = 0; i < enabledMultisigs.length;) {
                        vault.withdrawToken(enabledMultisigs[i], ggp, tokensPerMultisig);
+                       unchecked { ++i; }
                }
```

main/contracts/contract/Staking.sol: [L428](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L428)

```diff
-               for (uint256 i = offset; i < max; i++) {
-                       Staker memory s = getStaker(int256(i));
-                       stakers[total] = s;
-                       total++;
+               unchecked {
+                       for (uint256 i = offset; i < max; ++i) {
+                               Staker memory s = getStaker(int256(i));
+                               stakers[total] = s;
+                               ++total;
+                       }
                }
```

## OVERALL GAS REPORT DIFF

```
...

testCanClaimAndInitiateStaking() (gas: -32 (-0.003%)) 
testGetTotalAVAXLiquidStakerAmt() (gas: -80 (-0.004%)) 
testRecordStakingStart() (gas: -48 (-0.004%)) 
testClaimAndInitiateStaking() (gas: -48 (-0.004%)) 
testRecreateMinipool() (gas: -64 (-0.004%)) 
testRecordStakingEndWithSlash() (gas: -64 (-0.004%)) 
testRecordStakingEnd() (gas: -64 (-0.004%)) 
testRecordStakingStartInvalidStartTime() (gas: -48 (-0.004%)) 
testRecordStakingError() (gas: -64 (-0.005%)) 
testFullCycle_WithUserFunds() (gas: -64 (-0.005%)) 
testWithdrawMinipoolFunds() (gas: -64 (-0.005%)) 
testFullCycle_Error() (gas: -64 (-0.005%)) 
testTotalAssetsAfterWithdraw(uint128,uint128) (gas: 19 (0.010%)) 
testTotalAssetsDuringDelayedRewardDistribution(uint128,uint128) (gas: -32 (-0.011%)) 
testTotalAssetsDuringRewardDistribution(uint128,uint128) (gas: -32 (-0.011%)) 
test_previewRedeem((address[4],uint256[4],uint256[4],int256),uint256) (gas: -61 (-0.012%)) 
test_withdraw((address[4],uint256[4],uint256[4],int256),uint256,uint256) (gas: -62 (-0.012%)) 
test_redeem((address[4],uint256[4],uint256[4],int256),uint256,uint256) (gas: -62 (-0.012%)) 
test_previewWithdraw((address[4],uint256[4],uint256[4],int256),uint256) (gas: -62 (-0.012%)) 
testSyncRewardsAfterFullCycle(uint128,uint128,uint128) (gas: -32 (-0.014%)) 
testSyncRewardsAfterEmptyCycle(uint128,uint128) (gas: -32 (-0.014%)) 
testTransferFrom() (gas: 13 (0.015%)) 
testSyncRewardsFailsDuringCycle(uint128,uint128,uint256) (gas: -32 (-0.016%)) 
test_RT_mint_redeem((address[4],uint256[4],uint256[4],int256),uint256) (gas: -87 (-0.016%)) 
test_RT_mint_withdraw((address[4],uint256[4],uint256[4],int256),uint256) (gas: -87 (-0.016%)) 
test_RT_deposit_redeem((address[4],uint256[4],uint256[4],int256),uint256) (gas: -87 (-0.017%)) 
test_RT_withdraw_deposit((address[4],uint256[4],uint256[4],int256),uint256) (gas: -88 (-0.017%)) 
test_RT_withdraw_mint((address[4],uint256[4],uint256[4],int256),uint256) (gas: -88 (-0.017%)) 
test_RT_redeem_deposit((address[4],uint256[4],uint256[4],int256),uint256) (gas: -88 (-0.017%)) 
test_RT_redeem_mint((address[4],uint256[4],uint256[4],int256),uint256) (gas: -88 (-0.017%)) 
test_RT_deposit_withdraw((address[4],uint256[4],uint256[4],int256),uint256) (gas: -88 (-0.017%)) 
testTransferFrom(address,uint256,uint256) (gas: 16 (0.017%)) 
testFail_withdraw((address[4],uint256[4],uint256[4],int256),uint256) (gas: -128 (-0.020%)) 
testFail_redeem((address[4],uint256[4],uint256[4],int256),uint256) (gas: -128 (-0.021%)) 
test_maxWithdraw((address[4],uint256[4],uint256[4],int256)) (gas: -102 (-0.021%)) 
test_maxRedeem((address[4],uint256[4],uint256[4],int256)) (gas: -102 (-0.021%)) 
test_convertToShares((address[4],uint256[4],uint256[4],int256),uint256) (gas: -102 (-0.021%)) 
test_convertToAssets((address[4],uint256[4],uint256[4],int256),uint256) (gas: -102 (-0.021%)) 
test_totalAssets((address[4],uint256[4],uint256[4],int256)) (gas: -102 (-0.021%)) 
test_asset((address[4],uint256[4],uint256[4],int256)) (gas: -102 (-0.021%)) 
test_maxDeposit((address[4],uint256[4],uint256[4],int256)) (gas: -102 (-0.021%)) 
test_maxMint((address[4],uint256[4],uint256[4],int256)) (gas: -103 (-0.022%)) 
test_mint((address[4],uint256[4],uint256[4],int256),uint256,uint256) (gas: -128 (-0.024%)) 
test_deposit((address[4],uint256[4],uint256[4],int256),uint256,uint256) (gas: -128 (-0.025%)) 
test_previewMint((address[4],uint256[4],uint256[4],int256),uint256) (gas: -128 (-0.025%)) 
test_previewDeposit((address[4],uint256[4],uint256[4],int256),uint256) (gas: -128 (-0.025%)) 
testMint(address,uint256) (gas: -16 (-0.030%)) 
testMint() (gas: -16 (-0.030%)) 
testTotalAssetsAfterDeposit(uint128,uint128) (gas: -64 (-0.036%)) 
testTransfer(address,uint256) (gas: 22 (0.036%)) 
testTransfer() (gas: 22 (0.036%)) 
testGetMinipools() (gas: -2305 (-0.055%)) 
testFindActive() (gas: -136 (-0.059%)) 
testDisableAllMultisigs() (gas: -136 (-0.077%)) 
testOnlyDefender() (gas: -163 (-0.086%)) 
testPauseEverything() (gas: -163 (-0.108%)) 
testClaimAndRestake() (gas: -2145 (-0.118%)) 
testGetRewardsCycleTotal() (gas: -2097 (-0.588%)) 

...

testZellic45() (gas: -64 (-0.003%)) 
testGetEffectiveGGPStakedWithLowGGPPrice() (gas: -48 (-0.004%)) 
testGetEffectiveGGPStaked() (gas: -48 (-0.004%)) 
testWithdrawForStaking() (gas: -64 (-0.005%)) 
testDepositStakingRewards() (gas: -64 (-0.009%)) 
testSingleDepositWithdrawWAVAX(uint128) (gas: 16 (0.009%)) 
testSingleDepositWithdrawAVAX(uint128) (gas: 16 (0.010%)) 
testSingleMintRedeem(uint128) (gas: 16 (0.010%)) 
testAmountAvailableForStaking() (gas: -32 (-0.018%)) 
testInfiniteApproveTransferFrom() (gas: 16 (0.018%)) 
testFailTransferFromInsufficientAllowance(address,uint256,uint256) (gas: -16 (-0.019%)) 
testFailTransferFromInsufficientAllowance() (gas: -16 (-0.020%)) 
testMintPause() (gas: -32 (-0.033%)) 
testFailTransferFromInsufficientBalance(address,uint256,uint256) (gas: -28 (-0.033%)) 
testFailTransferFromInsufficientBalance() (gas: -28 (-0.034%)) 
testHalfRewardsForUnvestedGGPLargerScale() (gas: -3273 (-0.043%)) 
testFailBurnInsufficientBalance(address,uint256,uint256) (gas: -28 (-0.050%)) 
testWithdrawPaused() (gas: 51 (0.052%)) 
testWithdrawAVAXPaused() (gas: 51 (0.052%)) 
testFailTransferInsufficientBalance(address,uint256,uint256) (gas: -34 (-0.061%)) 
testHalfRewardsForUnvestedGGPSmallScale() (gas: -2685 (-0.064%)) 
testFailTransferInsufficientBalance() (gas: -34 (-0.064%)) 
testBurn(address,uint256,uint256) (gas: 40 (0.067%)) 
testBurn() (gas: 40 (0.070%)) 
testCalculateAndDistributeRewards() (gas: -2193 (-0.081%)) 
testMetadata(string,string,uint8) (gas: -1013 (-0.097%)) 
testFullCycleNoRewards() (gas: -2223 (-0.105%)) 
testFullCycleHappyPath() (gas: -2223 (-0.106%)) 
testStakeMinipoolUnstakeStakeScenario() (gas: -2293 (-0.116%)) 
testGetLastRewardsCycleCompleted() (gas: -3229 (-0.184%)) 
testGetGGPRewards() (gas: -3229 (-0.185%)) 
testRewardsManipulation() (gas: -8880 (-0.270%)) 
testMulitpleMultisigRewards() (gas: -2554 (-0.331%)) 
testStakingGGPOnly() (gas: -2229 (-0.376%)) 
testStartRewardsCycle() (gas: -2097 (-0.497%)) 
testGetClaimingContractDistribution() (gas: -2097 (-0.540%)) 
testMaxInflation() (gas: -113957 (-13.812%)) 
testInflationCalculate() (gas: -114308 (-21.109%)) 
```
```
Overall gas change: -279319 (NaN%)
```