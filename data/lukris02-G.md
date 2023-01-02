# Gas Optimizations Report for GoGoPool contest
## Overview
During the audit, 4 gas issues were found.  
Total savings > 685.

№ | Title | Instance Count | Saved
--- | --- | --- | ---
G-1 | Use unchecked blocks for incrementing i | 10 | 350
G-2 | Use unchecked blocks for subtractions where underflow is impossible | 5 | 175
G-3 | Elements that are smaller than 32 bytes (256 bits) may increase gas usage | 6 | 
G-4 | Use local variable cache instead of accessing mapping or array multiple times | 4 | 160

## Gas Optimizations Findings(4)
### G-1. Use unchecked blocks for incrementing i
##### Description
In Solidity 0.8+, there’s a default overflow and underflow check on unsigned integers. In the loops, "i" will not overflow because the loop will run out of gas before that.
##### Instances
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L619
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L623
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L84
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L61
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L74
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L215
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L219
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L230
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L428
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L431

##### Recommendation
Change:
```
for (uint256 i; i < n; ++i) {
 // ...
}
```
to:
```
for (uint256 i; i < n;) { 
 // ...
 unchecked { ++i; }
}
```

##### Saved
This saves ~30-40 gas per iteration.  
So, ~35*10 = 350
#
### G-2. Use unchecked blocks for subtractions where underflow is impossible
##### Description
In Solidity 0.8+, there’s a default overflow and underflow check on unsigned integers. When an overflow or underflow isn’t possible (after require or if-statement), some gas can be saved by using [unchecked blocks](https://docs.soliditylang.org/en/v0.8.17/control-structures.html#checked-or-unchecked-arithmetic).
##### Instances
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L102
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L75
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L99
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L155
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L187

##### Saved
This saves ~35.  
So, ~35*5 = 175
#
### G-3. Elements that are smaller than 32 bytes (256 bits) may increase gas usage
##### Description
According to [docs](https://docs.soliditylang.org/en/v0.8.16/internals/layout_in_storage.html#layout-of-state-variables-in-storage), when using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.
##### Instances
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L19
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L41
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L44
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L47
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L50
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L27

##### Recommendation
Consider using a larger size where needed.
#
### G-4. Use local variable cache instead of accessing mapping or array multiple times
##### Description
It saves gas due to not having to perform the same key’s keccak256 hash and/or offset recalculation.
##### Instances
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L75
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L99
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L155
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L187

##### Saved
This saves ~40.  
So, ~40*4 = 160