1. 

There are several typos. Go to 2022-12-gogopool/contracts/contract folders and search and replace all the following words.

exectued should be executed
transfered should be transferred
Eligiblity should be Eligibility
validater should be validator
recieved should be received
occured should be occurred
beign should be begin
adhear should be adhere
seperately should be separately
registring should be registering
Transfered should be Transferred
stakers's should be stakers'
withdrawl should be withdrawal
fucns should be funcs

2.

Unnecessary casting for the following variables. We could have more than 256 variables and lastRewardsAmt that larger than 6.2771017e+57

(See https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol)

```
uint8 public version;  // Only allow 256 variables
```

(See https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L50)

```
uint192 public lastRewardsAmt; // This is the result of the calculation of a few unt256 variables
```

