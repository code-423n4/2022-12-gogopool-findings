# Gas Optimizations

### [1]  x += y cost more gas. You can use x = x + y instead.
- Although it is easier to change the value of a variable by using += or -=. It cost more gas than the written out version x = x + y and x = x - y.
 ~9-16 gas saved per instance. 

Lines Affected:
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L160
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L252


### [2] When Incrementing in a for loop uncheck each increment.
- Inside of a for loop the variable being incremented cannot overflow because the loop checks each iteration to see if that variable is less than or equal to an specific amount. 
This saves about  ~70 gas per iteration.
https://gist.github.com/devNamedKiki/d63ee50856c8ff1861d5412fecf8b2fb

Lines Affected:
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L619
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L84
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L61
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L74
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L215
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L230
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L428
