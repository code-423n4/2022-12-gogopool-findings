## Summary
### Gas optimizations

| Issue | Description | Instances |
| -- | ----------- | -------- |
|1| Avoid use of '&&' within a `require` function|1|
|2| Inline a function that is called only once|4|
|3| Avoid use of default "checked" behavior in `for` loops|7|
|4| Avoid storage of uints or ints smaller than 32 bytes, if possible|3|
|| Total|15|

| No. | Description | 
|--| ----------- | 
|1|**Avoid use of '&&' within a `require` function**|
|  | Splitting into separate `require()` statements saves gas|							
___			
[ERC20Upgradeable.sol: L154](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L154)			
```solidity			
			require(recoveredAddress != address(0) && recoveredAddress == owner, "INVALID_SIGNER");
```			
Suggestion:			
```solidity			
			require(recoveredAddress != address(0), "INVALID_SIGNER");
			require(recoveredAddress == owner, "INVALID_SIGNER");
```			
___			
___			

| No. | Description | 
|--| ----------- | 
|2|**Inline a function that is called only once**|
|  | It costs less gas to inline the code of functions that are only called once. Doing so saves the cost of allocating the function selector, and the cost of the jump when the function is called.|
___			
[RewardsPool.sol: L82](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L82)			
```solidity			
	function inflate() internal {		
```			
Since `inflate()` is used only once in the contract, as shown below, it should be inlined to save gas:		
			
[RewardsPool.sol: L109](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L109)			
```solidity			
		inflate();	
```			
___
		
Similarly for the following functions, which are only called once:			
			
[RewardsPool.sol: L110](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L110)			
			
[RewardsPool.sol: L203](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L203)			
			
[Staking.sol: L87](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L87)			
___			
___			

| No. | Description | 
|--| ----------- | 
|3|**Avoid use of default "checked" behavior in `for` loops**|
|  | Underflow/overflow checks are made every time `++i` (or equivalent counter) is called. Such checks are unnecessary since `i` is already limited. Therefore, use `unchecked {++i}` instead, as shown below:|
				
___				
[MinipoolManager.sol: L619-625](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L619-L625)				
```solidity				
		for (uint256 i = offset; i < max; i++) {		
			Minipool memory mp = getMinipool(int256(i));	
			if (mp.status == uint256(status)) {	
				minipools[total] = mp;
				total++;
			}	
		}		
```				
Suggestion:				
```solidity				
		for (uint256 i = offset; i < max;) {		
		        Minipool memory mp = getMinipool(int256(i));		
			if (mp.status == uint256(status)) {	
				minipools[total] = mp;
				total++;
			}	
				
			unchecked {	
			  i++;	
		    }		
		}		
```				
___
				
Similarly, for the following `for` loops:				
				
[MultisigManager.sol: L84-89](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MultisigManager.sol#L84-L89)				
				
[Ocyticus.sol: L61-66](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Ocyticus.sol#L61-L66)				
				
[RewardsPool.sol: L74-76](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L74-L76)				
				
[RewardsPool.sol: L215-221](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L215-L221)				
				
[RewardsPool.sol: L230-232](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L230-L232)				
				
[Staking.sol: L428-432](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L428-L432)				
___				
___				

| No. | Description | 
|--| ----------- | 
|4|**Avoid storage of uints or ints smaller than 32 bytes, if possible**|
|  | Storage of uints or ints smaller than 32 bytes incurs overhead. Instead, use size of at least 32, then downcast where needed|
				
___				
[BaseAbstract.sol: L19](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseAbstract.sol#L19)				
```solidity				
	uint8 public version;			
```				
___				
[ERC20Upgradeable.sol: L27](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L27)				
```solidity				
	uint8 public decimals;			
```				
___				
[ERC20Upgradeable.sol: L53-57](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L52-L57)				
```solidity				
	function __ERC20Upgradeable_init(			
		string memory _name,		
		string memory _symbol,		
		uint8 _decimals		
	) internal onlyInitializing {			
```				
___	
___