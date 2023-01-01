# Gas optimization report


```js
assert(msg.sender == address(asset));
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L83
 In ``assert`` when the condition  return  false   consumes all the remaining gas and  then revert but when  `` require``   returns false it will refund all the remaining gas  . recommend to use **require** instead of assert .The **assert**  should only be used to test for internal errors, and to check invariable values
## Recommendations

```js
require(msg.sender == address(asset) , "Error! only accepting AVAX from WAVAX CONTRACT"); 

```
