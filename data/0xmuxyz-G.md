### Report title
- Using `++i` for incrementing `i` inside of loop should be replaced with `unchecked { ++ }`


### Description
- The `unchecked` block is able to use from solidity version 0.8.0
- The variable `i` inside of loop is not able to overflow because of the condition `i < length`, where length is defined as uint256. The maximum value `i` that can reach is `max(uint)-1` . Therefore, incrementing `i` inside `unchecked` block is safe. Also consuming gas of incrementing `i` inside `unchecked` block is less than using `++i`.



### Code Snippet that has not been optimized yet
- Below are lines that has not been optimized yet: [RewardsPool.sol#L215-L221](https://github.com/masaun/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L215-L221)
```solidity
	function distributeMultisigAllotment(
		uint256 allotment,
		Vault vault,
		TokenGGP ggp
	) internal {
                /// ...	

		for (uint256 i = 0; i < count; i++) {  /// @audit - This line is the point to be optimized.
			(address addr, bool enabled) = mm.getMultisig(i);
			if (enabled) {
				enabledMultisigs[enabledCount] = addr;
				enabledCount++;
			}
		}

                /// ...	
	}
```



- Below are also code snippets that has not been optimized yet:
  - [RewardsPool.sol#L230-L232](https://github.com/masaun/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L230-L232)
  - [MinipoolManager.sol#L619-L625](https://github.com/masaun/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L619-L625)
  - [MultisigManager.sol#L84-L89](https://github.com/masaun/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L84-L89)
  - [Ocyticus.sol#L61-L66](https://github.com/masaun/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L61-L66)




### Recommendation
- Consider adding code that is incrementing `i` inside `unchecked` block to the lines to be optimized like below: 
  - eg). In case of [RewardsPool.sol#L215-L221](https://github.com/masaun/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L215-L221) , the code above should be added like below: 
```solidity
	function distributeMultisigAllotment(
		uint256 allotment,
		Vault vault,
		TokenGGP ggp
	) internal {
                /// ...	

		for (uint256 i = 0; i < count;) {  /// @audit - Should remove "i++" from here.
			(address addr, bool enabled) = mm.getMultisig(i);
			if (enabled) {
				enabledMultisigs[enabledCount] = addr;
				enabledCount++;
			}
		}

                /// @audit - Incrementing "i++" inside unchecked block
		unchecked {
			i++
		}

                /// ...	
	}
```
