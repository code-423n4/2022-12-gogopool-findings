### [Low-01] Absence of zero address check for receiver address, while withdrawing token via ```withdrawToken()```

```solidity
tokenContract.safeTransfer(withdrawalAddress, amount);
```
There is no check whether withdrawalAddress is Zero address or not, so that may cause token loss
*Instances(1)*
```solidity
File : Vault.sol
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L159
```

### [Low-02] Precision loss while Calculating ```rewardsCycleEnd``` and ```nextRewardsCycleEnd```.
```solidity
rewardsCycleEnd = (block.timestamp.safeCastTo32() / rewardsCycleLength) * rewardsCycleLength; 

uint32 nextRewardsCycleEnd = ((timestamp + rewardsCycleLength) / rewardsCycleLength) * rewardsCycleLength;
``` 
Here issue is hat Division is preformed before Multiplication which may cause precision loss, and unpleasant or inaccuratee result will got.

*Instances(2)*
```solidity
File : contract/tokens/TokenggAVAX.sol
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L78 
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L102
```


### [Low-03] No checks for ```delegationFee``` while creating minipool

According to context ```Percentage delegation fee in units of ether (2% is 0.2 ether)``` but while creating ```miniPool``` and setting ```delegationFee``` there is no such condition check present.
*Instances(1)*
```solidity
File : MinipoolManager.sol
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L258
```



