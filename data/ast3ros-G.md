# [G-1] Slot packing saves slots but increases runtime gas consumption due to masking
Packed fields into a slot save storage in deployment but costs more gas in runtime due to masking, especially in case variables are not in a struct
and accessed separately.

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L50

Recommended Mitigation Steps
- Use default word size 256 to avoid masking at runtime and save gas.

# [GAS-2] Use calldata instead of memory for function arguments that do not get mutated

Instance
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseAbstract.sol#L32
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseAbstract.sol#L90

# [GAS-3] Using private state variable to save gas and be consistent

newGuardian variable is declared as public. However it is unnecessary and can be changed to private to save gas. If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table.

It is also consistent with the visibility of guardian variable.

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L25

# [GAS-4] State variables should be cached in stack variables rather than re-reading them from storage

Instance:

tokenBalances[contractKey] variable:

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L151 
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L155

rewardsCycleLength:

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L102

# [GAS-5] ++i costs less gas than i++, especially when it's used in for-loops (--i/i-- too)

Instance
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Ocyticus.sol#L61

# [GAS-6] Unneccessary event emitting in constructor
In constructor, it is not neccessary to emit the event to inform the GuardianChanged since it it not a change but an initialization.
Removing it can save gas in deployment.

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L36

# [GAS-7] Check validity of a variable early to save gas if the function fails

The check (tokenBalances[contractKeyFrom] < amount) could be performed at line 179. If it fails then the gas is saved from 2 operations: keccak256 and TokenTransfer event emission

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L183-L185

# [GAS-8] Unchange state variable should be declared constant to save gas

The rewardsCycleLength variable is set in initialize function and never changed in the contract. It should be constant to save gas.

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L76