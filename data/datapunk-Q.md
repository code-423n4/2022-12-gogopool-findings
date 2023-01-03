0. Unsure how this works:
```
if (staking.getAVAXAssignedHighWater(owner) < staking.getAVAXAssigned(owner)) {
                 staking.increaseAVAXAssignedHighWater(owner, avaxLiquidStakerAmt);
        }
```
A better and simpler way would be to do:
```
staking.setcAVAXAssignedHighWater(owner, staking.getAVAXAssigned(owner));
```

1. missing __gap variable in ERC20Upgradeable, ERC4626Upgradeable, BaseUpgradeable contracts.
according to the https://multisiglabs.notion.site/Audit-Parameters-3ad0ab58f96743c1b8087d131413a9bc, these contracts are upgradeable. As such, they should adhere to the recommendation and add storage gaps in order to avoid potential sotrage collisions:
https://docs.openzeppelin.com/contracts/3.x/upgradeable#storage_gaps

2. ERC4626 Implementation Standard Inconsistency
A known issue https://github.com/transmissions11/solmate/issues/348 may need follow-up with the solmate author and weight the benefits to be compliant with the standard.


3. Underflow error when redeeming to 0 after minting some rewards 
Another known issue I encountered while I was writing test for price manipulation of early users.
https://github.com/fei-protocol/ERC4626/issues/24
The underflow error is fixed once a syncRewards() is called beforehand.

4. setGuardian in Storage.sol does not emit any event.

5. Vault's calculateAndDistributeRewards() does not check for stakerAddr isEligible(). 
Although in rialto simulator, isEligible is indeed checked first, it makes sense to move the check inside of calculateAndDistributeRewards() to avoid any potential mistakes

5.
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L291
instead of using a hard-coded value, TENTH, which may get out of sync with getMinCollateralizationRatio, if it is updated, use getMinCollateralizationRatio directly, ie, change
`if (getCollateralizationRatio(stakerAddr) < TENTH) {`
to
`if (getCollateralizationRatio(stakerAddr) < dao.getMinCollateralizationRatio()) {`
