## [G-01]
File: ClaimProtocolDAO.sol

URL: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimProtocolDAO.sol#L21

Summary: invoiceID of string data type should be converted to bytes or size 32 if invoiceID is less than or equal to 32 digits or bytes in length;

Line 21 PoC: 
```
string memory invoiceID;
```
Remidiation: 
```
bytes32 memory invoiceID;
```

## [G-02]
File: ClaimProtocolDAO.sol

URL: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimProtocolDAO.sol

Summary: Maximum Gas limit not set. Gas requirement of functions are infinite: If the gas requirement of a function is higher than the block gas limit, it cannot be executed.

Remediation: Extend and set maximum Gas limit.

## [G-03]
File: MinipoolManager.sol

Example URL: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol

Summary: Maximum Gas limit not set.  Gas requirement of functions are infinite: If the gas requirement of a function is higher than the block gas limit, it cannot be executed. 

Remediation: Extend and set maximum Gas limit.