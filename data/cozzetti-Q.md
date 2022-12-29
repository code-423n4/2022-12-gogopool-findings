## Low risk

### [L1] Staking can be ended before the duration period passes

The [`recordStakingEnd` function](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L385) of the `MinipoolManager` contract does not validate the `duration` of a minipool. This means it's possible to end staking for a minipool before the actual owner-specified duration period passes. To reproduce, paste the following test in the `MinipoolManager.t.sol` file:

```solidity
function testPrematureStakingEndPossible() public {
    address liqStaker1 = getActorWithTokens("liqStaker1", MAX_AMT, MAX_AMT);
    vm.prank(liqStaker1);
    ggAVAX.depositAVAX{value: MAX_AMT}();

    uint256 duration = 2 weeks;
    uint256 depositAmt = 1000 ether;
    uint256 avaxAssignmentRequest = 1000 ether;
    uint256 validationAmt = depositAmt + avaxAssignmentRequest;
    uint128 ggpStakeAmt = 200 ether;

    vm.startPrank(nodeOp);
    ggp.approve(address(staking), MAX_AMT);
    staking.stakeGGP(ggpStakeAmt);
    MinipoolManager.Minipool memory mp1 = createMinipool(depositAmt, avaxAssignmentRequest, duration);
    vm.stopPrank();

    vm.startPrank(address(rialto));
    minipoolMgr.claimAndInitiateStaking(mp1.nodeID);
    bytes32 txID = keccak256("txid");
    minipoolMgr.recordStakingStart(mp1.nodeID, txID, block.timestamp);

    // Do not skip whole duration
    // skip(duration);
    skip(1);

    uint256 rewards = 10 ether;
    deal(address(rialto), address(rialto).balance + rewards);
    minipoolMgr.recordStakingEnd{value: validationAmt + rewards}(mp1.nodeID, block.timestamp, rewards);
}
```

I'd recommend adding a validation in the `recordStakingEnd` contract to ensure the `duration` period has passed since the minipool's `startTime`. This way users will have on-chain verifications that prevent mistakes in the recording of end of staking.

### [L2] Withdrawable minipool can be finished before funds are withdrawn

Given its name and docstings, the [`finishFailedMinipoolByMultisig` function](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L528) of the `MinipoolManager` is only intended to be executed by a trusted multisig to finish a minipool that is in the `Error` state. However, the function does not [validate](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L530) the _current_ state of the minipool, but rather that it can be moved to the `Finished` state. This means that any healthy minipool in the `Withdrawable` state can be finished by the multisig using this function. In that scenario, the owner of the minipool would not be able to collect their funds or awards, which would be lost.

It's known that multisigs are already fully trusted actors in the system. Yet this appears to be an unintentional oversight in the `finishFailedMinipoolByMultisig` function that is granting unnecessary, untested and undocumented powers to the multisig.

### [L3] Potentially incorrect accounting of minipools

The `Staking` contract keeps track of how many minipools each staker has. The amount can be increased and decreased with the [`increaseMinipoolCount`](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L180) and [`decreaseMinipoolCount`](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L187) functions. These are called from the `MinipoolManager` contract.

When a minipool is created, the counter is increased. To keep a proper accounting, before the minipool reaches the `Finished` state, the counter must be reduced. However, there's a state transition path where the counter is not correctly handled.

Say the minipool is in `Staking` state. Now it's moved to the `Error` state using the [`recordStakingError` function](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L484-L515). The counter hasn't changed. Now the minipool is moved to the `Finished` state, using the [`finishFailedMinipoolByMultisig` function](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L528-L533). Still, the counter hasn't changed. This means that the minipool can reach the `Finished` state, and the staker will end with an erroneous minipool count.

I'd recommend correctly decreasing the minipool count in the `finishFailedMinipoolByMultisig` function, making sure to add the corresponding unit tests.

### [L4] Effective rewards ratio is unaffected by ProtocolDAO settings

The effective rewards ratio is not affected by [minimum collateralization ratio](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ProtocolDAO.sol#L172-L175) set by the ProtocolDAO. This can be seen in the `getEffectiveRewardsRatio` of the `Staking` contract, where instead of querying the value from [`getMinCollateralizationRatio` function](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ProtocolDAO.sol#L173) of the `ProtocolDAO` contract, the code [uses a hardcoded `0.1 ether` value](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L291), which may deviate from what the DAO decides.

### [L5] Rewards may be distributed to non-eligible stakers

The [`calculateAndDistributeRewards` function](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimNodeOp.sol#L56) of the `ClaimNodeOp` contract does not check that the given `stakerAddr` address is indeed eligible for rewards. Therefore, rewards could be distributed to non-eligible stakers.

The check can be implemented using the [`isEligible` function](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimNodeOp.sol#L46).

While distributing rewards is only executed by a trusted partner, it would still be relevant to have on-chain verification on whether stakers are indeed eligible, so as to avoid granting unnecessary powers.

### [L6] Logging incorrect amount when rewards cycle is started

In the [`startRewardsCycle` function](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L156) of the `RewardsPool` contract, [the event `NewRewardsCycleStarted`](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L161) is intended to emit the amount of rewards about to be distributed.

However, it's always emitting an outdated value. Because when `emit NewRewardsCycleStarted(getRewardsCycleTotalAmt());` is executed, the `inflate` function hasn't been executed yet. And therefore the attribute `RewardsCycleTotalAmt` read by `getRewardsCycleTotalAmt()` hasn't been updated with the amount of tokens to be distributed as rewards.

The event must be emitted after [`inflate` is called](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L169). Also recommend including tests for this event to ensure it's always emitting the expected value.

### [L7] Incomplete staker information

The [`getStaker` function](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L405) of the `Staking` contract is not reading all available attributes of a staker. It is missing to assign the [`avaxAssignedHighWater` property](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L26).  As a result, it will always be read as zero.

### [L8] Incorrect version in `Ocyticus` contract

The `Ocyticus` contract is not setting the inherited [`version` state variable](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseAbstract.sol#L19) to 1 [in its constructor](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Ocyticus.sol#L22-L24). As a result, it will default to zero.

### [L9] Unused delegation fee

In the `createMinipool` function of the `MinipoolManager` contract, the [delegation fee parameter](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L199) is not validated, used nor tested. According to the [available documentation](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L35), it is supposed to be between 0 and 1 ether, but this is never enforced. Also, the parameter is never used by the protocol, other than to be [written to state](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L258).

I recommend better specifying the intended use of the delegation fee, actually using it in the on-chain code if needed, and including unit tests to ensure its use is expected.

## Non-critical


### [NC1] Not centrally defining string identifiers for contracts

There's a number of strings used to identify contracts scattered throughout the code base. These are:

- `ProtocolDAO`
- `TokenggAVAX`
- `MinipoolManager`
- `Staking`
- `MultisigManager`
- `Vault`
- `TokenGGP`
- `ClaimProtocolDAO`
- `Oracle`
- `RewardsPool`

These seems to be an error-prone approach, which can also make maintenance more difficult. It would be best to define these strings in a centralized library, and then import them as necessary.

### [NC2] Not checking multisig address is deployed contract

The [`registerMultisig` function](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MultisigManager.sol#L35) of the `MultisigManager` contract should check that the passed address is indeed a deployed contract. This can be done checking that the external code size of the passed address is greater than zero.

### [NC3] Multisigs cannot be unregistered

There's no way of unregistering a multisig in the [`MultisigManager` contract](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MultisigManager.sol). This scenario is not specified in the available documentation, so it is unclear whether the lack of this functionality is intended and known or not. Raising it just in case.

### [NC4] Unused imports and errors

To improve readability and avoid confusions, remove the following imports and errors, because they are not used:

- The [`TokenGGP` import](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L7) in `Vault.sol`
- The [`ERC20Upgradeable` import](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L7) in `TokenggAVAX.sol`
- The [`InvalidNetworkContract`, `TokenTransferFailed` and `VaultTokenWithdrawalFailed` errors](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L26-L28) declared in the `Vault` contract.

### [NC5] Not emitting relevant events

- The [`addAllowedToken` and `removeAllowedToken` functions](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L204-L210) of the `Vault` contract perform sensitive guardian actions that should emit a corresponding event to ease off-chain tracking.
- The [`withdrawMinipoolFunds` function](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L287) of the `MinipoolManager` contract is not emitting a `MinipoolStatusChanged` event after [moving to the minipool's state](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L291) to `Finished`.