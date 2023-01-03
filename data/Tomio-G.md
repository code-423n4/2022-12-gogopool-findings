1.
Title: Boolean comparison

Proof of Concept:
[BaseAbstract.sol#L74](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L74)

Recommended Mitigation Steps:
Change to:
`if(!x)` instead of `if(x == false)`
and
`if(x)` instead of `if(x == true)`
________________________________________________________________________

2.
Title: Gas improvement on returning value

Proof of Concept:
[MultisigManager.sol#L82](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L82)

Recommended Mitigation Steps:
by set `addr` in returns L#80 and delete L#82 can save gas

```
function requireNextActiveMultisig() external view returns (address addr) { //@audit-info: set here
```
________________________________________________________________________

3.
Title: Consider delete empty block or emit something

impact:
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

Proof of Concept:
[MinipoolManager.sol#L106](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L106)
________________________________________________________________________

4.
Title: Unchecking arithmetics operations that can't underflow/overflow

Proof of Concept:
[ClaimNodeOp.sol#L102](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L102) Should be unchecked due to `if` condition in L#95

Recommended Mitigation Steps:
Use `unchecked` can save gas
________________________________________________________________________

5.
Title: `>=` is cheaper than `>`

Impact:
Strict inequalities (`>`) are more expensive than non-strict ones (`>=`). This is due to some supplementary checks (ISZERO, 3 gas)

Proof of Concept:
[TokenggAVAX.sol#L212](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L212)
[](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L222)

Recommended Mitigation Steps:
Consider using `>=` instead of `>` to avoid some opcodes
________________________________________________________________________

6.
Title: Using multiple `require` instead `&&` can save gas

Proof of Concept:
[ERC20Upgradeable.sol#L154](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L154)

Recommended Mitigation Steps:
Change to:

```
	require(recoveredAddress != address(0), "INVALID_SIGNER");
	require(recoveredAddress == owner, "INVALID_SIGNER");
```
________________________________________________________________________