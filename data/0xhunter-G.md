at any part of the contract where there are two variables ( a and b)
if a > b
and a - b is being used
since the solidity version is up to 0.8.0 where all math calculations are being checked for underflows , the a > b requirement can be removed or a - b can be come in unchecked {  } to save more gas.

eg https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L142-L151
if : baseAmt > stakingTotalAssets --> revert ( this can be removed since the call will revert in calculation if baseAmt > stakingTotalAssets )
stakingTotalAssets -= baseAmt;
