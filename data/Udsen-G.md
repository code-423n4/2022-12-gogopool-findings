### 1. ***> 0*** IS LESS EFFICIENT THAN ***!= 0*** FOR UNSIGNED INTEGERS

		if (restakeAmt > 0) {
		
This can be re-written as follows to save gas:

		if (restakeAmt != 0) {		

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L103

Similarly 3 more instances to use the !=0 inplace of > 0 operation can be found in the following code lines.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L109
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L152
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L59

### 2. USING ***CALLDATA*** INSTEAD OF ***MEMORY*** FOR READ-ONLY ARGUMENTS IN ***EXTERNAL FUNCTIONS*** SAVES GAS

	function spend(
		string memory invoiceID,
		address recipientAddress,
		uint256 amount
	) external onlyGuardian {

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimProtocolDAO.sol#L21

### 3. ++I COSTS LESS GAS COMPARED TO I++ AND INCREMENTS/DECREMENTS CAN BE UNCHECKED IN FOR-LOOPS IF THEY DONOT UNDERFLOW OR OVERFLOW

		for (uint256 i = offset; i < max; i++) { 
		
This can be re written as follows to save gas:

     for (uint256 i = offset; i < max;) {
      unchecked{ ++i;}
     }

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L619
	 
There are 9 more instances of this issue.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L623
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L84
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L61
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L74
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L215
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L219
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L230
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L428
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L431


### 4. USING ***PRIVATE*** RATHER THAN ***PUBLIC*** FOR CONSTANTS, SAVES GAS

Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table

	uint256 public constant MULTISIG_LIMIT = 10;
	
This can be re-written as:

	uint256 private constant MULTISIG_LIMIT = 10;
	
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L27

### 5. IN ARITHMETIC OPERATIONS ***+=*** CONSUMES LESS GAS SINCE IT IS ONE OPERATION COMPARED TO ADDING AND ASSIGNING WHICH IS TWO OPERATIONS

		uintStorage[key] = uintStorage[key] + amount; 
		
Above code can be re-written as 

		uintStorage[key] += amount; 
		
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L171

There are 5 more instances of this issue.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L177
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L56
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L99-L100
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L130
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L187-L188

### 6. FOLLOWING EXPRESSION CAN BE `UNCHECKED{}`. SINCE UNDERFLOW DOES NOT TAKE PLACE DUE TO PREVIOUS CONDITION CHECK. 

Below expression makes sure the underflow does not occur:
	
		if (claimAmt > ggpRewards) {
			revert InvalidAmount();
		}
		
The code can be changed as :

	unchecked{uint256 restakeAmt = ggpRewards - claimAmt;}
	
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L102