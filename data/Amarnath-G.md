### 1. Unnecessary Event emission in the Middle of the Function

**File: contracts/contract/Vault.sol**

The transaction can potentially revert after the event emission. [**Adding the event emission costs 2205 gas with 3 parameters and 1598 gas with 2 parameters **](https://medium.com/layerx/how-to-reduce-gas-cost-in-solidity-f2e5321e0395#:~:text=Avoid%20unnecessary%20event%20emission,-Basically%2C%20logging%20opcodes&text=If%20only%20one%20argument%20are,the%20gas%20cost%20is%201125.) as listed in the functions and the if the transaction reverts after the emission its going to cost 2205 gas for the event itself therefore it should be placed after checking the potential revert and at the end of the function.            

**There is multiple instances of this issue:**

(i) **FunctionName: transferAVAX** 

**Code Snippet:**

> emit AVAXTransfer(fromContractName, toContractName, amount);

Code: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L93

(ii) **FunctionName: withdrawAVAX** 

**Code Snippet:**

>  // Emit the withdrawl event for that contract
    emit AVAXWithdrawn(contractName, amount);

Code: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L69

               



### 2. ++I/I++ SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS 

**File: contracts/contract/Staking.sol**
**FunctionName: getStakers()** 

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas [***PER LOOP***](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)

**There is 1 instance of this issue:**

> for (uint256 i = offset; i < max; i++) {
			Staker memory s = getStaker(int256(i));
			stakers[total] = s;
			total++;
     }

Code: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L428

