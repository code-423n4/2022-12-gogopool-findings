# low

## L-1 if `calculateAndDistributeRewards` is not called each reward cycle node operators lose out on `GGP` rewards

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L194

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L71

### Description

The GGP rewards is based on the inflation of `GGP` tokens:

```javascript
File: ClaimNodeOp.sol

71: 		uint256 rewardsCycleTotal = getRewardsCycleTotal();
72: 		uint256 rewardsAmt = percentage.mulWadDown(rewardsCycleTotal);
73: 		if (rewardsAmt > rewardsCycleTotal) {
74: 			revert InvalidAmount();
75: 		}
76:
77: 		staking.resetAVAXAssignedHighWater(stakerAddr);
78: 		staking.increaseGGPRewards(stakerAddr, rewardsAmt);
```

Which is read from `NOPClaim.RewardsCycleTotal` that is set, and overwritten, by `startRewardsCycle` in `RewardsPool.sol` each rewards cycle:

```javascript
File: RewardsPool.sol

191:		if (nopClaimContractAllotment > 0) {
192:			emit ClaimNodeOpRewardsTransfered(nopClaimContractAllotment);
193:			ClaimNodeOp nopClaim = ClaimNodeOp(getContractAddress("ClaimNodeOp"));
194:			nopClaim.setRewardsCycleTotal(nopClaimContractAllotment); // old value is overwritten here
195:			vault.transferToken("ClaimNodeOp", ggp, nopClaimContractAllotment);
196:		}
```

`startRewardsCycle` is public and can be executed by anyone. `calculateAndDistributeRewards` however can only be called by multisig (Rialto). If Rialto fails to call this for every node operator on every rewards cycle the old `RewardsCycleTotal` will be overwritten and node operators will lose their `GGP` rewards.

### Impact
If Rialto fails to call `calculateAndDistributeRewards` node operators will lose out on `GGP` rewards.

This is a risk for any node operator signing up to this protocol.

### Recommended Mitigation Steps
The rewards system cold be refactored to a accumulative rewards system where each node ops debt is being tracked, together with updating share each time they stake/unstake `GGP`.

## L-2 no way to remove compromised/broken multisigs without upgrading the contract

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L41-L43

### Description

multisig addresses are tracked in `MultisigManager.sol`, there a multisig can be enabled/disabled.

There is also a hard maximum of how many multisigs there can be:

```javascript
File: `MultisigManager.sol`

27: 	uint256 public constant MULTISIG_LIMIT = 10;

...

40: 		uint256 index = getUint(keccak256("multisig.count"));
41: 		if (index >= MULTISIG_LIMIT) {
42: 			revert MultisigLimitReached();
43: 		}
```

If all ten multisig addresses are disabled for whatever reasons, they have been compromised, lost or anything else there is no way without an upgrade of the contract to add more. 

### Impact
If there are no active multisigs starting new minipools will not work since that relies on fetching an active multisig.

### Recommended Mitigation Steps
Track begin and end index of multisigs and have just 10 of those. That way you can move the lower limit up and add new on top.

## L-3 anyone can run `TokenggAVAX::initialize`

`initialize()` function can be called anybody when the contract is not initialized.

This allows an attacker to set the storage address which would give them complete control of 
the contract:

```javascript
File: tokens/TokenggAVAX.sol

72: 	function initialize(Storage storageAddress, ERC20 asset) public initializer {
73: 		__ERC4626Upgradeable_init(asset, "GoGoPool Liquid Staking Token", "ggAVAX");
74: 		__BaseUpgradeable_init(storageAddress);
75:
76: 		rewardsCycleLength = 14 days;
77: 		// Ensure it will be evenly divisible by `rewardsCycleLength`.
78: 		rewardsCycleEnd = (block.timestamp.safeCastTo32() / rewardsCycleLength) * rewardsCycleLength;
79: 	}
```

### Recommended Mitigation Steps
Add a control that verifies it is the deployer that is running the initializer:

```javascript
if (msg.sender != deployer) {
    revert NotDeployer();
}
```

## L-4 Very little information about how Rialto works

Rialto has a very critical role in this protocol and there is little to no public information to be found about how Rialto works, any audits on their code.

## L-5 incorrect comment

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L54

```javascript
File: ClaimNodeOp.sol

54:	/// @notice Set the share of rewards for a staker as a fraction of 1 ether
```

The function increases ggp rewards and resets highwater. 

## L-6 copy paste mistake

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L418

```javascript
File: Staking.sol

418:	/// @param limit The limit to the amount of minipools that should be returned
```

_minipools_ should be _stakers_

# non-crit

## NC-1 `requireValidStateTransition`can be made easier to read

to me, it would be easier to understand the state transitions if it were written based on which state you are transitioning to:

```javascript
if(to == MinipoolStatus.Launched) {
    isValid = (currentStatus == MinipoolStatus.Prelaunch)
} else if(to == MinipoolStatus.Staking) {
    isValid = (currentStatus == MinipoolStatus.Launched)
}
...
```
Because the code uses it to verify _from_ which states it is allowed to transition. Rewriting it like this makes it easier to read, at least for me, which states you can come from.

## NC-2 Misspelt comments

### `tokens/TokenggAVAX.sol`

#### _exectued_
executed

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L67

```javascript
67: 		// The constructor is exectued only when creating implementation contract
```

### `ClaimNodeOp.sol`

### _Eligiblity_
Eligibility

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L45

```javascript
45: 	/// @dev Eligiblity: time in protocol (secs) > RewardsEligibilityMinSeconds. Rialto will call this.
```

### `MinipoolManager.sol`

#### _validater_
validator

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L347

```javascript
347:	/// @param txID The ID of the transaction that successfully registered the node with Avalanche to become a validater
```

#### _recieved_
received

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L383

```javascript
383:	/// @param avaxTotalRewardAmt The rewards the node recieved from Avalanche for being a validator
```

#### _occured_
occurred

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L480

```javascript
480:	/// @notice A staking error occured while registering the node as a validator
```

#### _recieve_, _beign_
receive, begin

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L556

```javascript
556:	/// @return The approximate rewards the node should recieve from Avalanche for beign a validator
```

#### _adhear_, _paramaters_
adhere, parameters

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L606

```javascript
606:	/// @return minipools in the protocol that adhear to the paramaters
```

### `Ocyticus.sol`

#### _seperately_
separately

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L46

```javascript
46: 	/// @dev Multisigs will need to be enabled seperately, we dont know which ones to enable
```

### `ProtocolDAO.sol`

#### _registring_
registering

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L205

```javascript
205:	/// @notice Upgrade a contract by unregistering the existing address, and registring a new address and name
```

### `Staking.sol`

#### _adhear_, _paramaters_
ahere, parameters

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L419

```javascript
419:	/// @return stakers in the protocol that adhear to the paramaters
```

### `Vault.sol`

#### _withdrawl_
withdrawal

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L68

```javascript
68: 		// Emit the withdrawl event for that contract
```

#### _bookeeping_
bookkeeping

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L81
```javascript
81: 	/// @dev No funds actually move, just bookeeping
```

#### _recieve_
receive

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L134

```javascript
134:	/// @param withdrawalAddress Address that will recieve the token
```