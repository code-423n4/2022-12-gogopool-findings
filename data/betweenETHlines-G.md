1) `rewardsCycleLength` can be made constant 

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L44

Since this variable is initialized and never changed, this can simply be made constant which makes its init redundant and saves gas.

2) `emitGuardianChanged` can be emitted without caching

If the event is emitted at the beginning of the function it can simply be done using the parameters (guardian, newGuardian). This will save gas because `address oldguardian` does not need to be cached into memory.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L65
