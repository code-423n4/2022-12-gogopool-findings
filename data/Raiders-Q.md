
1. Title: INTERNAL FUNCTIONS NEVER USED

### Vulnerable Endpoint:
- https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L255

### Impact:
The contract declared internal functions but was not using them in any of the functions or contracts. Since internal functions can only be called from inside the contracts, it makes no sense to have them if they are not used. This uses up gas and causes issues for auditors when understanding the contract logic.

### Remediation
Having dead code in the contracts uses up unnecessary gas and increases the complexity of the overall smart contract.
It is recommended to remove the internal functions from the contracts if they are never used.

2. Title: OUTDATED COMPILER VERSION

### Vulnerable Endpoint 1: 
- https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L2

### Vulnerable Endpoint 2:
- https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L2

### Impact:
Using an outdated compiler version can be problematic especially if there are publicly disclosed bugs and issues that affect the current compiler version.
The following outdated versions were detected: >=0.8.0

### Remediation:
It is recommended to use a recent version of the Solidity compiler that should not be the most recent version, and it should not be an outdated version as well. Using very old versions of Solidity prevents the benefits of bug fixes and newer security checks. Consider using the solidity version 0.8.7, which patches most solidity vulnerabilities.

3. Title: USE OF FLOATING PRAGMA

### Vulnerable Endpoint 1: 
- https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L2

### Vulnerable Endpoint 2:
- https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L2

 ### Impact:
Solidity source files indicate the versions of the compiler they can be compiled with using a pragma directive at the top of the solidity file. This can either be a floating pragma or a specific compiler version.
The contract was found to be using a floating pragma which is not considered safe as it can be compiled with all the versions described.
The following affected files were found to be using floating pragma: >=0.8.0

### Remediation:
It is recommended to use a fixed pragma version, as future compiler versions may handle certain language constructions in a way the developer did not foresee.
Using a floating pragma may introduce several vulnerabilities if compiled with an older version.
The developers should always use the exact Solidity compiler version when designing their contracts as it may break the changes in the future.
Instead of >=0.8.0 use pragma solidity 0.8.7, which is a stable and recommended version right now.

4.1  Title: MISSING EVENTS 

### Vulnerable Endpoint :
- https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseUpgradeable.sol#L10-L12

### Impact:
Events are inheritable members of contracts. When you call them, they cause the arguments to be stored in the transaction’s log--a special data structure in the blockchain.
These logs are associated with the address of the contract which can then be used by developers and auditors to keep track of the transactions.
The contract BaseUpgradeable was found to be missing these events on the function __BaseUpgradeable_init which would make it difficult or impossible to track these transactions off-chain.

### Remediation
Consider emitting events for the functions mentioned above. It is also recommended to have the addresses indexed.

4.2 Title: Missing Events

### Vulnerable Endpoint:
- https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseAbstract.sol#L135-137

### Impact:
Events are inheritable members of contracts. When you call them, they cause the arguments to be stored in the transaction’s log -- a special data structure in the blockchain.
These logs are associated with the address of the contract which can then be used by developers and auditors to keep track of the transactions.
The contract BaseAbstract was found to be missing these events on the function setAddress which would make it difficult or impossible to track these transactions off-chain.

### Remediation
Consider emitting events for the functions mentioned above. It is also recommended to have the addresses indexed.

4.3 Title: Missing Events

### Vulnerable Endpoint:
- https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Ocyticus.sol#L64

### Impact:
Events are inheritable members of contracts. When you call them, they cause the arguments to be stored in the transaction’s log.
These logs are associated with the address of the contract which can then be used by developers and auditors to keep track of the transactions.
The contract Ocyticus was found to be missing these events on the function disableAllMultisigs which would make it difficult or impossible to track these transactions off-chain.

### Remediation
Consider emitting events for the functions mentioned above. It is also recommended to have the addresses indexed.

4.4 Title: Missing Events

### Vulnerable Endpoint:
- https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L87-L90

### Impact:
Events are inheritable members of contracts. When you call them, they cause the arguments to be stored in the transaction’s log -- a special data structure in the blockchain.
These logs are associated with the address of the contract which can then be used by developers and auditors to keep track of the transactions.
The contract Staking was found to be missing these events on the function increaseGGPStake which would make it difficult or impossible to track these transactions off-chain.

### Remediation
Consider emitting events for the functions mentioned above. It is also recommended to have the addresses indexed.

4.5 Title: Missing Events

### Vulnerable Endpoint:
- https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L117-L120

### Impact:
Events are inheritable members of contracts. When you call them, they cause the arguments to be stored in the transaction’s log--a special data structure in the blockchain.
These logs are associated with the address of the contract which can then be used by developers and auditors to keep track of the transactions.
The contract Staking was found to be missing these events on the function decreaseAVAXStake which would make it difficult or impossible to track these transactions off-chain.

### Remediation
Consider emitting events for the functions mentioned above. It is also recommended to have the addresses indexed.

4.6 Title: Missing Events

### Vulnerable Endpoint: 
- https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L204-L211

### Impact:
Events are inheritable members of contracts. When you call them, they cause the arguments to be stored in the transaction’s log--a special data structure in the blockchain.
These logs are associated with the address of the contract which can then be used by developers and auditors to keep track of the transactions.
The contract Staking was found to be missing these events on the function setRewardsStartTime which would make it difficult or impossible to track these transactions off-chain.

### Remediation
Consider emitting events for the functions mentioned above. It is also recommended to have the addresses indexed.

4.7 Missing Events:

### Vulnerable Endpoint:
- https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L204-L206

### Impact:
The contract Vault was found to be missing these events on the function addAllowedToken which would make it difficult or impossible to track these transactions off-chain.These logs are associated with the address of the contract which can then be used by developers and auditors to keep track of the transactions.

### Remediation
Consider emitting events for the functions mentioned above. It is also recommended to have the addresses indexed.

4.8 Title: Missing Events

### Vulnerable Endpoints
- https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L248-L253

### Impact:
The contract TokenggAVAX was found to be missing these events on the function afterDeposit which would make it difficult or impossible to track these transactions off-chain.These logs are associated with the address of the contract which can then be used by developers and auditors to keep track of the transactions.

### Remediation
Consider emitting events for the functions mentioned above. It is also recommended to have the addresses indexed.

5. Title: In-line assembly used

### Vulnerable Endpoint:
- https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L628-L630

### Impact:
Inline assembly is a way to access the Ethereum Virtual Machine at a low level. This bypasses several important safety features and checks of Solidity. This should only be used for tasks that need it and if there is confidence in using it.
Multiple vulnerabilities have been detected previously when the assembly is not properly used within the Solidity code; therefore, caution should be exercised while using them.

### Remediation
Avoid using inline assembly instructions if possible because it might introduce certain issues in the code if not dealt with properly because it bypasses several safety features that are already implemented in Solidity.

5.1 Title: In-line assembly used

### Vulnerable Endpoint:
- https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L435-L437

### Impact:
Inline assembly is a way to access the Ethereum Virtual Machine at a low level. This bypasses several important safety features and checks of Solidity. This should only be used for tasks that need it and if there is confidence in using it.
Multiple vulnerabilities have been detected previously when the assembly is not properly used within the Solidity code; therefore, caution should be exercised while using them.

### Remediation
Avoid using inline assembly instructions if possible because it might introduce certain issues in the code if not dealt with properly because it bypasses several safety features that are already implemented in Solidity.

6. BLOCK VALUES AS A PROXY FOR TIME

### Vulnerable Endpoint 1:
- https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimNodeOp.sol#L49

### Vulnerable Endpoint 2:
- https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L226

### Vulnerable Endpoint 3:
- https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L279

### Vulnerable Endpoint 4:
- https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L356

### Vulnerable Endpoint 5:
- https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Oracle.sol#L59

### Vulnerable Endpoint 6:
- https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L206

### Impact:
Contracts often need access to time values to perform certain types of functionality. Values such as block.timestamp and block.number can be used to determine the current time or the time delta. However, they are not recommended for most use cases.
For block.number, as Ethereum block times are generally around 14 seconds, the delta between blocks can be predicted. The block times, on the other hand, do not remain constant and are subject to change for a number of reasons, e.g., fork reorganizations and the difficulty bomb.
Due to variable block times, block.number should not be relied on for precise calculations of time.


### Remediations
Smart contracts should be written with the idea that block values are not precise, and their use can have unexpected results. Alternatively, oracles can be used.

## Note:
More such endpoints were vulnerable , let me know if you need all 





