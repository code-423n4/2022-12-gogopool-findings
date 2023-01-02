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