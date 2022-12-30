Storage.sol 
Instead of address(0) write it out, it saves gas 
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L36


Vault.sol 
Use immutable if you want to assign a permanent value at construction
State var only set in the constructor should be immutable
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L42
uint8 public version - why not use immutable?
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L19
Use Assembly To Hash
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L147
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L178
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L179
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L201
Add payable to these functions for gas savings
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L61
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L141
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L112


MultisigManager.sol 
Use private instead of public for constants/immutable
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L27
Use Immutable If You Want To Assign a Permanent Value At construction
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L30
Use assembly to hash
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L48
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L49
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L61
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L74
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L111
State var only set in the constructor should be immutable
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L111
Using Unchecked
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L84
Delete useless variable declarations to save gas. 
Declaring uint256 i = 0; means doing an MSTORE of the value 0 
Instead you could just declare uint256 i to declare the variable without assigning it’s default value, saving 3 gas per declaration
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L84


Ocyticus.sol 
Delete useless variable declarations to save gas. 
Declaring uint256 i = 0; means doing an MSTORE of the value 0 
Instead you could just declare uint256 i to declare the variable without assigning it’s default value, saving 3 gas per declaration
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L61
Using Unchecked
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L61


BaseAbstract.sol 
Instead of address(0) write it out, it saves gas
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L92


Base.sol 
State var only set in the constructor should be immutable
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Base.sol#L11

BaseUpgradeable.sol 
State var only set in the constructor should be immutable
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseUpgradeable.sol#L11

ERC20Upgradeable.sol 
keccak256() should only need to be called on a specific string literal once.
It should be saved to an immutable variable, and the variable used instead.
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L139
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L170
Use assembly to check for address(0)
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L154
Splitting require() statements that use && saves gas
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L154
There is no constructor is the strict sense, so this comment is misleading
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L49-L51

TokenggAVAX.sol 
Use assembly to hash
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L217
Empty blocks should be removed or emit something ?
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L255
<X> += <Y> costs more gas than <X> = <X> + <Y> for state variables
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L245
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L252
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L149
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L160
Use != 0 instead of > 0 for Unsigned Integer Comparison 
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L59

MinipoolManager.sol 
Best practice concerning for-loops is to use unchecked{ ++i } for the increment operator 
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L619

RewardsPool.sol 
Best practice concerning for-loops is to use unchecked{ ++i } for the increment operator 
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L215
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L230
