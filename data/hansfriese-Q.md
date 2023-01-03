### [L-01] `withdrawGGP` is too strict
- https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L358

```solidity
function withdrawGGP(uint256 amount) external whenNotPaused {
    if (amount > getGGPStake(msg.sender)) {
        revert InsufficientBalance();
    }

    emit GGPWithdrawn(msg.sender, amount);

    decreaseGGPStake(msg.sender, amount);

    ProtocolDAO dao = ProtocolDAO(getContractAddress("ProtocolDAO"));
    if (getCollateralizationRatio(msg.sender) < dao.getMaxCollateralizationRatio()) {//@audit-info why max?
        revert CannotWithdrawUnder150CollateralizationRatio();
    }

    Vault vault = Vault(getContractAddress("Vault"));
    vault.withdrawToken(msg.sender, ggp, amount);
}
```

Currently, the user needs to have at least 150% collateral to withdraw GGP tokens and this is too strict and unnecessary.
Consider changing as below.

```solidity
    if (getCollateralizationRatio(msg.sender) < dao.getMinCollateralizationRatio()) {//@audit-info fix
        revert CannotWithdrawUnder150CollateralizationRatio();
    }
```
### [L-02] `getMinimumGGPStake` should round up
- https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L256

The function `getMinimumGGPStake()` is used to get the minimum amount of GGP to stake.
Because this amount is the minimum required from the user's side, rounding up is recommended.

```solidity
function getMinimumGGPStake(address stakerAddr) public view returns (uint256) {
    ProtocolDAO dao = ProtocolDAO(getContractAddress("ProtocolDAO"));
    Oracle oracle = Oracle(getContractAddress("Oracle"));
    (uint256 ggpPriceInAvax, ) = oracle.getGGPPriceInAVAX();

    uint256 avaxAssigned = getAVAXAssigned(stakerAddr);
    uint256 ggp100pct = avaxAssigned.divWadDown(ggpPriceInAvax);
    return ggp100pct.mulWadDown(dao.getMinCollateralizationRatio());//@audit-info round up
}
```

### [N-01] Typo
- https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L215

"able to withdraw" -> "able to redeem"

### [N-02] Wrong event emission
- https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L161

The rewards cycle total amount (RewardsCycleTotalAmt) is not updated at this point, it is updated in the function `inflate()`.
So we should emit the signal after the call to `inflate()`.

```solidity
function startRewardsCycle() external {
    if (!canStartRewardsCycle()) {
        revert UnableToStartRewardsCycle();
    }

    emit NewRewardsCycleStarted(getRewardsCycleTotalAmt()); //@audit-info wrong position to emit, it's not updated yet

    // Set start of new rewards cycle
    setUint(keccak256("RewardsPool.RewardsCycleStartTime"), block.timestamp);
    increaseRewardsCycleCount();
    // Mint any new tokens from GGP inflation
    // This will always 'mint' (release) new tokens if the rewards cycle length requirement is met
    // 		since inflation is on a 1 day interval and it needs at least one cycle since last calculation
    inflate();
    ...
}
```




