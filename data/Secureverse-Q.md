### [Low-01] There is no checks for ```delegationFee``` while creating minipool

According to context ```Percentage delegation fee in units of ether (2% is 0.2 ether)``` but while creating ```miniPool``` and setting ```delegationFee``` there is no such condition check present.
*Instances(1)*
```solidity
File : MinipoolManager.sol
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L258
```

### [Low-02] While withdrawing token via ```withdrawToken()```, there is absence of zero address check for receiver address

```solidity
tokenContract.safeTransfer(withdrawalAddress, amount);
```
There is no check whether withdrawalAddress is Zero address or not, so that may cause token loss
*Instances(1)*
```solidity
File : Vault.sol
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L159
```

### [Low-03] Calculation of ```rewardsCycleEnd``` and ```nextRewardsCycleEnd``` may be inaccurate, because of precision loss
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

### [NC-01] Bool Comparision
```solidity
if (enabled == false) {  
```
```solidity
else {
			isValid = false;
```
Here inside if and in else condition boolean comparision occurs

*Instances(3)*
```solidity
File : BaseAbstract.sol

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L06-L08
```
```solidity
File : MinipoolManager.sol
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L169
```
```solidity
File : Storage.sol
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L29 
```


### [NC-02] Unused lines of code present

*Instances(5)*
```solidity
File : BaseAbstract.sol

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L06-L08
```
```solidity
File : contract/tokens/TokenggAVAX.sol
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L225
```


### [NC-03] Code can simply rearranged, Instead of setting ```isValid = false;``` simply revert there will save some line of code

```solidity
......
.....
 } else {
			isValid = false;  // @audit
		}

		if (!isValid) {
			revert InvalidStateTransition(); // @audit rearrange
		}
```
*Instances(1)*
```solidity
File : MinipoolManager.sol
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L169
```