# Divide before multiply:  
## Integer division might truncate. As a result, performing multiplication before division can sometimes avoid loss of precision.

## contract/tokens/TokenggAVAX.sol#78:
   rewardsCycleEnd = (block.timestamp.safeCastTo32() / rewardsCycleLength) * rewardsCycleLength;
## contract/tokens/TokenggAVAX.sol#102:
  uint32 nextRewardsCycleEnd = ((timestamp + rewardsCycleLength) / rewardsCycleLength) * rewardsCycleLength;
===============
# Recommended Mitigation Steps:
## contract/tokens/TokenggAVAX.sol#78:
   rewardsCycleEnd =  rewardsCycleLength * (block.timestamp.safeCastTo32() / rewardsCycleLength);
## contract/tokens/TokenggAVAX.sol#102:
  uint32 nextRewardsCycleEnd = rewardsCycleLength * ((timestamp + rewardsCycleLength) / rewardsCycleLength) ;
