## No Input Validation for Minipool's Duration

There is no input validation for a Minipool's duration in createMinipool( ).  According to the GGP team, a Minipool's duration should be a minimum of 14 days and a maximum of 365 days.  

### Recommended Mitigation Steps

     --- a/contracts/contract/MinipoolManager.sol
     +++ b/contracts/contract/MinipoolManager.sol
     @@ -71,6 +71,7 @@ contract MinipoolManager is Base, ReentrancyGuard, IWithdrawer {
             error MinipoolNotFound();
             error OnlyOwner();
             error CancellationTooEarly();
     +       error InvalidDuration();
 
             event GGPSlashed(address indexed nodeID, uint256 ggp);
             event MinipoolStatusChanged(address indexed nodeID, MinipoolStatus indexed status);
     @@ -203,6 +204,10 @@ contract MinipoolManager is Base, ReentrancyGuard, IWithdrawer {
                             revert InvalidNodeID();
                     }
      
     +               if (duration < 14 days || duration > 365 days ) {
     +                       revert InvalidDuration();
     +               }
     
Referenced Code: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L196-L269

## No Input Validation for Minipool's Delegation Fee

There is no input validation for a Minipool's delegation fee.  According to the comments at the beginning of MinipoolManager, a Minipool's delegation fee should not exceed 1 ether in value.  

### Recommended Mitigation Steps

     --- a/contracts/contract/MinipoolManager.sol
     +++ b/contracts/contract/MinipoolManager.sol
     @@ -71,6 +71,7 @@ contract MinipoolManager is Base, ReentrancyGuard, IWithdrawer {
             error MinipoolNotFound();
             error OnlyOwner();
             error CancellationTooEarly();
     +       error InvalidDelegationFee();
 
             event GGPSlashed(address indexed nodeID, uint256 ggp);
             event MinipoolStatusChanged(address indexed nodeID, MinipoolStatus indexed status);
     @@ -203,6 +204,10 @@ contract MinipoolManager is Base, ReentrancyGuard, IWithdrawer {
                             revert InvalidNodeID();
                     }
 
     +               if (delegationFee > 1 ether) {
     +                       revert InvalidDelegationFee();  
     +               }
     +
                     ProtocolDAO dao = ProtocolDAO(getContractAddress("ProtocolDAO"));
   
Referenced Code: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L196-L269

## startRewardsCycle( ) will emit an event with an incorrect rewards cycle total amount

startRewardsCycle( ) in RewardsPool will emit an event with the rewards cycle total amount from the **prior rewards cycle** rather than **the current rewards cycle**.  This is due to the fact that the state variable, rewardsCycleTotalAmt, is updated in the internal function, inflate( ).  However, the event in startRewardsCycle( ) is emitted prior to inflate( ), meaning that the event will be emitted with the rewardsCycleTotalAmt from the prior cycle.

Referenced Code: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L156-L169

### Recommended Mitigation Steps

Move the emit of the event following the the internal call to inflate( ).

     --- a/contracts/contract/RewardsPool.sol
     +++ b/contracts/contract/RewardsPool.sol
     @@ -158,8 +158,6 @@ contract RewardsPool is Base {
                              revert UnableToStartRewardsCycle();
                     }
 
     -               emit NewRewardsCycleStarted(getRewardsCycleTotalAmt());
     -
                      // Set start of new rewards cycle
                     setUint(keccak256("RewardsPool.RewardsCycleStartTime"), block.timestamp);
                     increaseRewardsCycleCount();
     @@ -168,6 +166,8 @@ contract RewardsPool is Base {
                     //              since inflation is on a 1 day interval and it needs at least one cycle since last calculation
                     inflate();
 
     +               emit NewRewardsCycleStarted(getRewardsCycleTotalAmt());
     +
                     uint256 multisigClaimContractAllotment = getClaimingContractDistribution("ClaimMultisig");
                     uint256 nopClaimContractAllotment = getClaimingContractDistribution("ClaimNodeOp");
                     uint256 daoClaimContractAllotment = getClaimingContractDistribution("ClaimProtocolDAO");

## Unnecessary Inheritance

TokenGGAVAX and ERC4626 both unnecessarily inherit Initializable.  TokenGGAVAX inherits both Initializable and ERC4626Upgradeable.  However, ERC4626Upgradeable already inherits Initializable.  ERC4626 inherits both Initializable and ERC20Upgradeable, but ERC20Upgradable already inherits Initializable.

Referenced Code:

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L24

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L11

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L10


