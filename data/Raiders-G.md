
## GAS OPTIMIZATION

### Title 1: REQUIRE VS ASSERT

#### Vulnerable Endpoint: 
- https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L83

#### Impact:

assert should only be used to test for internal errors, and to check invariants. Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract that you should fix. It should only be used in case of invariant checking.
require should only be used for validating return values and user input.

#### Remediation

If assert is not used to check an invariant consider replacing it with require. Use require in case of return and user input values.

### Title 2: CHEAPER INEQUALITIES IN REQUIRE()

#### Vulnerable Endpoint: 
- https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L127

#### Impact:
The contract was found to be performing comparisons using inequalities inside the require statement. When inside the require statements, non-strict inequalities (>=, <=) are usually costlier than strict equalities (>, <).

#### Remediation
It is recommended to go through the code logic, and, if possible, modify the non-strict inequalities with the strict ones to save ~3 gas as long as the logic of the code is not affected.

### Title 3: FUNCTION SHOULD BE EXTERNAL

#### Vulnerable Endpoint: 
- https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L92

#### Impact:
A function with public visibility modifier was detected that is not called internally.

public and external differs in terms of gas usage. The former use more than the latter when used with large arrays of data. This is due to the fact that Solidity copies arguments to memory on a public function while external read from calldata which a cheaper than memory allocation.

#### Remediation
If you know the function you create only allows for external calls, use the external visibility modifier instead of public. It provides performance benefits and you will save on gas.


### Title 4: FUNCTION SHOULD BE EXTERNAL

#### Vulnerable Endpoint: 
- https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L166

#### Impact:
A function with public visibility modifier was detected that is not called internally.

public and external differs in terms of gas usage. The former use more than the latter when used with large arrays of data. This is due to the fact that Solidity copies arguments to memory on a public function while external read from calldata which a cheaper than memory allocation.

#### Remediation
If you know the function you create only allows for external calls, use the external visibility modifier instead of public. It provides performance benefits and you will save on gas.

### Title 5: FUNCTION SHOULD BE EXTERNAL

#### Vulnerable Endpoint: 
- https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L88

#### Impact:
A function with public visibility modifier was detected that is not called internally.

public and external differs in terms of gas usage. The former use more than the latter when used with large arrays of data. This is due to the fact that Solidity copies arguments to memory on a public function while external read from calldata which a cheaper than memory allocation.

#### Remediation
If you know the function you create only allows for external calls, use the external visibility modifier instead of public. It provides performance benefits and you will save on gas.

### Title 6: GAS OPTIMIZATION IN INCREMENTS

#### Vulnerable Endpoint: 
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L84

#### Impact:
++i costs less gas compared to i++ or i += 1 for unsigned integers. In i++, the compiler has to create a temporary variable to store the initial value. This is not the case with ++i in which the value is directly incremented and returned, thus, making it a cheaper alternative.

#### Remediation
Consider changing the post-increments (i++) to pre-increments (++i) as long as the value is not used in any calculations or inside returns. Make sure that the logic of the code is not changed.

### Title 7: GAS OPTIMIZATION IN INCREMENTS

#### Vulnerable Endpoint: 
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L61

#### Impact:
++i costs less gas compared to i++ or i += 1 for unsigned integers. In i++, the compiler has to create a temporary variable to store the initial value. This is not the case with ++i in which the value is directly incremented and returned, thus, making it a cheaper alternative.

#### Remediation
Consider changing the post-increments (i++) to pre-increments (++i) as long as the value is not used in any calculations or inside returns. Make sure that the logic of the code is not changed.

### Title 8: GAS OPTIMIZATION FOR STATE VARIABLES

#### Vulnerable Endpoint: 
- https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L184

#### Impact:
Plus equals costs more gas than addition operator. Hence x +=y costs more gas than x = x + y.

#### Remediation
Consider addition operator over plus equals.

### Title 9: GAS OPTIMIZATION FOR STATE VARIABLES

#### Vulnerable Endpoint: 
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L149

#### Impact:
Plus equals costs more gas than addition operator. Hence x -=y costs more gas than x = x - y.

#### Remediation
Consider addition operator over plus equals.

### Title 10: GAS OPTIMIZATION FOR STATE VARIABLES

#### Vulnerable Endpoint: 
- https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L245

#### Impact:
Plus equals costs more gas than addition operator. Hence x -=y costs more gas than x = x - y.

#### Remediation
Consider addition operator over plus equals.

### Title 10: CHEAPER INEQUALITIES IN IF()

#### Vulnerable Endpoint: 
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimProtocolDAO.sol#L28

#### Impact:
The contract was found to be doing comparisons using inequalities inside the if statement.
When inside the if statements, non-strict inequalities (>=, <=) are usually cheaper than the strict equalities (>, <).

#### Remediation
It is recommended to go through the code logic, and, if possible, modify the strict inequalities with the non-strict ones to save ~3 gas as long as the logic of the code is not affected.

### Title 11: CHEAPER INEQUALITIES IN IF()

#### Vulnerable Endpoint: 
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L206

#### Impact:
The contract was found to be doing comparisons using inequalities inside the if statement.
When inside the if statements, non-strict inequalities (>=, <=) are usually cheaper than the strict equalities (>, <).

#### Remediation
It is recommended to go through the code logic, and, if possible, modify the strict inequalities with the non-strict ones to save ~3 gas as long as the logic of the code is not affected.


### Title 11: SUPERFLUOUS EVENT FIELDS

#### Vulnerable Endpoint: 
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Oracle.sol#L67

#### Impact:
block.timestamp and block.number are by default added to event information. Adding them manually costs extra gas.

#### Remediation
block.timestamp and block.number do not need to be added manually. Consider removing them from the emitted events.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L51

### Title 12: CHEAPER CONDITIONAL OPERATORS

#### Vulnerable Endpoint: 
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L51

#### Impact:
When optimization is disabled, x > 0 is cheaper than x != 0 for unsigned integers in solidity.

#### Remediation
Consider using x > 0 in place of x != 0 wherever possible.

### Title 13: UNUSED IMPORTS

#### Vulnerable Endpoint: 
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L5

#### Impact:
Solidity is a Gas-constrained language. Having unused code or import statements incurs extra gas usage when deploying the contract.
The contract was found to be importing the file ./MinipoolManager.sol which is not used anywhere in the code.

#### Remediation
It is recommended to remove the import statement if it’s not supposed to be used.

### Title 14: UNUSED IMPORTS

#### Vulnerable Endpoint: 
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L8

#### Impact:
Solidity is a Gas-constrained language. Having unused code or import statements incurs extra gas usage when deploying the contract.
The contract was found to be importing the file ./MinipoolManager.sol which is not used anywhere in the code.

#### Remediation
It is recommended to remove the import statement if it’s not supposed to be used.


