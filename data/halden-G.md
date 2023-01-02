## [G-01] Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.
BaseAbstract: [19](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L19)

## [G-02] Avoid comparing of bool variable with true/false in if condition
Comparing bool variable with boolean literal is more expensive. Save around 20-40 gas. 
Avoid writing of code like that:
```
bool param = true;
if(param == false) {...}
``` 
BaseAbstract: [25](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L25), [74](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L74)
Storage: [29](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L29)

```
contract Test {
    error InvalidOrOutdatedContract();

    function t1(bool param) public pure {
        bool helper = param;
        if(param == false) {
            revert InvalidOrOutdatedContract();
        }
    }

    function t2(bool param) public pure {
        bool helper = param;
        if(!param) {
            revert InvalidOrOutdatedContract();
        }
    }
}
```

if param is FALSE for t1 gas is 21686
if param is FALSE for t2 gas is 21643
if param is TRUE for t1 gas is 21667
if param is TRUE for t2 gas is 21624

## [G-03] Use calldata instead of memory for function/modifier arguments that do not get mutated
List of missed function/modifier in the C4udit [output](https://gist.github.com/Picodes/49996eaab291146dc068c56752d2d1f3#GAS-6)  

File BaseAbstract: [32](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L32), [51](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L51), [90](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L90)

File ERC20Upgradeable.sol : [54-55](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L54-L55)

## [G-04] Redundant imports
File ClaimNodeOp.sol [10](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L10)

File MinipoolManager.sol [13](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L13)

File ProtocolDAO.sol [5](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L5)

File Vault.sol [7](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L7)

## [G-05] Use unchecked{} where the operands can not underflow/overflow because of a previous check
File ClaimNodeOp.sol: [102](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L102) -> previous check line 95

## [G-06] Remove unnecessary if condition
Removing of unnecessary if condition will reduce gas. Every time the variable `claimAmt` will be bigger than zero because we have previous check if it is bigger than `ggpRewards` and we know that `ggpRewards` is of type uint256 and it is different than zero.
File ClaimNodeOp.sol: [109](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L109)

## [G-07] Use unchecked{++x} in for loop
Unchecked block can be used in for loop for i because the operands can not overflow.
File MultisigManager.sol: [84](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L84)

File Ocyticus.sol: [61](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L61)

File MinipoolManager.sol [619](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L619), [623](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L623)

File RewardsPool.sol [74](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L74), [215](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L215), [219](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L219), [230](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L230)

File Staking.sol: [428](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L428), [431](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L431)

## [G-08] Avoid using of additional local variable if it used once. Minimize MLOAD opcode.
The gas it will be smaller if it is not used additional local variable which is only used once. The negative effect from that is that readability become less but it will be cheaper for users.

File BaseAbstract.sol: [41-42](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L41-L42), [52-53](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L52-L53), [73](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L73), [82](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L82)

File MultisigManager.sol: [56](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L56)

File ProtocolDAO.sol: [199](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L199)


## [G-09] Use custom errors instead of revert string to save gas
Some ERC standards can be optimized and will not affect their logic. 

Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met). Custom errors are defined using the error statement, which can be used inside and outside of contracts (including interfaces and libraries).

File ERC20Upgradeable.sol: [127](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L127), [L154](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L154)

File ERC4626Upgradeable.sol: [44](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L44), [103](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L103)


## [G-10] Splitting require() statements that use && saves gas
Some ERC standards can be optimized and will not affect their logic. 

File ERC20Upgradeable.sol: [L154](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L154)

## [G-11] Splitting if condition can saves gas
If the first condition is false, we don't need to have a check for the second condition.

File BaseAbstract.sol: [41-46](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L41-L46), [52-57](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L41-L46)

Recommendation:
```
		bool isContract = getBool(keccak256(abi.encodePacked("contract.exists", msg.sender)));
		bool isGuardian = msg.sender == gogoStorage.getGuardian();

		if (!(isGuardian || isContract)) {
			revert MustBeGuardianOrValidContract();
		}
```
to
```
        if(!getBool(keccak256(abi.encodePacked("contract.exists", msg.sender)))) {
            revert MustBeGuardianOrValidContract();
        }

        if(msg.sender == gogoStorage.getGuardian()) {
            revert MustBeGuardianOrValidContract();
        }
```