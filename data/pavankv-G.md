## 1  Functions guaranteed to revert when called by normal users can be marked payable:-
If a function modifier such as onlyOwner or other modifier  is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

code snippet:-
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L108
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L61
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L56
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimProtocolDAO.sol#L20
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L35
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L55
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L27
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L37
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L47
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L55
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L23
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L209
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L198

## 2 Division by two should use bit shifting

<x> / 2 is the same as <x> >> 1. While the compiler uses the SHR opcode to accomplish both, the version that uses division incurs an overhead of 20 gas due to JUMPs to and from a compiler utility function that introduces checks which can be avoided by using unchecked {} around the division by two.

code snippet:-
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L413


## 3 Use Storage instead of memory for Structs , array and mapping :-
It may not be obvious, but every time you copy a storage struct/array/mapping to a memory variable, you are copying each member by reading it from storage, which is expensive. And when you use the storage keyword, you are just storing a pointer to the storage, which is much cheaper. Exception: case when you need to read all or many members multiple times. In report included only cases that saved gas

code snippet:-
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L447

## 4 ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for- and while-loops

code snippet:-
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L619
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L84
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L61
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L74
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L215
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L230
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L428

## 5 Use named returns for local variables where it is possible:-

code snippet:-
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L134
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L115
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L80

function requireNextActiveMultisig() external view returns (address) {
		uint256 total = getUint(keccak256("multisig.count"));
		address addr;                         //try to change 
		bool enabled;
		for (uint256 i = 0; i < total; i++) {
			(addr, enabled) = getMultisig(i);
			if (enabled) {
				return addr;
			}
		}
		revert NoEnabledMultisigFound();
	}

change to :-

function requireNextActiveMultisig() external view returns (address addr) {
		uint256 total = getUint(keccak256("multisig.count"));
		
		bool enabled;
		for (uint256 i = 0; i < total; i++) {
			(addr, enabled) = getMultisig(i);
			if (enabled) {
				return addr;
			}
		}
		revert NoEnabledMultisigFound();
	}

## 6 Expressions that cannot be overflowed can be unchecked :-

code snippet:-
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L229

## 7. x = x + y is cheaper than x += y  / x = x-y is cheaper than x-=y :-
code snippet:-
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L149
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L160
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L245
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L252
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L79
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L101
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L106




