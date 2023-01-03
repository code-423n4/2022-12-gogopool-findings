### 1. EVENT IS MISSING `INDEXED` FIELDS

Each event can use upto three `indexed` fields if gas is not particularly of concern. Index event fields make the fields more quickly accessible to the off-chain tools that parse events.

	event GGPRewardsClaimed(address indexed to, uint256 amount);
	
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L25

There are 10 more instances of this issue

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimProtocolDAO.sol#L13
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L75-76
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L23-L25
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Oracle.sol#L21
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L24-L28
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L40-L41
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L12
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L30-L33
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L35
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L36-38


### 2. PROPER COMMENTING WILL INCREASE THE READABILITY OF THE CODE.

There are two "the" in the comment which seems to be a typing error. And comment is unclear or wrong.

	/// @notice Verifies that the minipool related the the given node ID is able to a validator
	
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L309

"validater" spellings are wrong

	/// @param txID The ID of the transaction that successfully registered the node with Avalanche to become a validater
	
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L347

"adhear" is wrong

	/// @return minipools in the protocol that adhear to the paramaters 
	
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L606

This should be corrected as an "external function".

	/// @notice Public function that will run a GGP rewards cycle if possible

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L155-L156	

This should be corrected as "token"

		// Get the toke ERC20 instance
		
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L156

### 3. THE VARIABLE ASSIGNMENT TYPES ARE WRONG.

Following assignments require bytes32 value 0x0 to be given as input, but instead uint256 value 0 was provided

		setBytes32(keccak256(abi.encodePacked("minipool.item", index, ".txID")), 0); 
		setBytes32(keccak256(abi.encodePacked("minipool.item", index, ".errorCode")), 0); 

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L688
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L695	

### 4. THERE IS A POSSIBILITY OF DIVISION BY ZERO.

		uint256 tokensPerMultisig = allotment / enabledCount; 
		
Here in the case multisig count is zero then enabledCount++ will not be executed. Which will keep the enabledCount at 0. Hence division by zero is a possibility.

Better add the following check to the code before division operation:

		require(enabledCount != 0, "enabledCount is zero") 
	
This will make sure division by zero is not possible.
	
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L211-L229

### 5. EVENTS HAVE ALREADY BEEN EMITTED, EVEN BEFORE THE STATE CHANGES AND CONDITIONAL CHECKS HAVE BEEN COMPLETED INSIDE FUNCTIONS.

The emitted events will not revert even if the function reverts due to conditional checks failing and reverting.

Here the function emits the `GGPStaked(stakerAddr, amount)` event even though the function might revert in the `vault.depositToken("Staking", ggp, amount)` call.
So the event has already been emitted even though the function execution and state changes are reverted.

	function _stakeGGP(address stakerAddr, uint256 amount) internal {
		emit GGPStaked(stakerAddr, amount);

		// Deposit GGP tokens from this contract to vault
		Vault vault = Vault(getContractAddress("Vault"));
		ggp.approve(address(vault), amount);
		vault.depositToken("Staking", ggp, amount);
		
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L337-L343

There are 4 more instances of this issue:

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L363
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L69
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L126
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L181

### 6. NO NEED TO CHECK THE BOOLEAN VARIABLES EXPLICITLY AGAINST THE "FALSE" OR "TRUE".

The boolean value is explicitly checked against the "false" inside the if condition.

		if (booleanStorage[keccak256(abi.encodePacked("contract.exists", msg.sender))] == false && msg.sender != guardian) { 
		
This can be re-written as:

		if (!(booleanStorage[keccak256(abi.encodePacked("contract.exists", msg.sender))]) && msg.sender != guardian) { 
		
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L29

### 7. ADDRESS VARIABLE IS EXPECTED INTO THE `abi.encodePacked`. BUT CONTRACT INSTANCE IS PROVIDED.

Type cast the contract object into an address to get the bytes32 representation of the abi.encodePacked.

		bytes32 contractKey = keccak256(abi.encodePacked(getContractName(msg.sender), tokenAddress)); 
		
This should be re-written as :

		bytes32 contractKey = keccak256(abi.encodePacked(getContractName(msg.sender), address(tokenAddress))); 
		
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L147

There are 2 more instances of this issue:

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L178
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L179

### 8. ERC20 CONTRACT OBJECT IS ONCE AGAIN CASTED INTO ERC20 CONTRACT ERRONEOUSLY

		ERC20 tokenContract = ERC20(tokenAddress);
		
Here tokenAddress is an ERC20 contract object. Hence above line should be corrected as:

		ERC20 tokenContract = tokenAddress;
		
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L157

### 9.`assert()` FUNCTION IS USED WITHOUT DESCRIPTION FOR THE REVERT.

		assert(msg.sender == address(asset));

Add a description to the assert function for the revert for better readability of the code.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L83

### 10. SHOULD PERFORM THE `_owner != address(0)` FOR THE NULL ADDRESS CHECK WHEN PASSING IN ADDRESS PARAMETERS INTO THE FUNCTION

	function maxWithdraw(address _owner) public view override returns (uint256) { //@audit - check _owner != address(0) for the null address
		if (getBool(keccak256(abi.encodePacked("contract.paused", "TokenggAVAX")))) {
			return 0;
		}	
		uint256 assets = convertToAssets(balanceOf[_owner]);
		uint256 avail = totalAssets() - stakingTotalAssets; //@audit - can this be unchecked?
		return assets > avail ? avail : assets;
	}
	
The first line of the above function should perform the null address check and revert if null address is found as follows:

	require(_owner != address(0), "Null Address Passed In");
	
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L206-L213

There is 1 more instance of this issue:

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L216-L223	