(1)

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L230

```
		for (uint256 i = 0; i < enabledMultisigs.length; i++) { //@audit it will call the .length method every time, the best way is use a local var to store the length then to use.
			vault.withdrawToken(enabledMultisigs[i], ggp, tokensPerMultisig);
		}
```
for example 

```
                uint256 multiSigsLength = enabledMultisigs.length;
		for (uint256 i = 0; i < multiSigsLength; i++) {
			vault.withdrawToken(enabledMultisigs[i], ggp, tokensPerMultisig);
		}
```

(2)
I saw a lot of place to use the keccak256 functions , 

but some of place use a constant string as the param in the keccak256, it will  return a fixed value when call the func. 

because every time we call the func, it will call the kecak256, we need to use a constant value in the storage to replace the kecak256 call, it will save the gas. 

the places i found in TokenggAVAX.sol:

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L59

```
modifier whenTokenNotPaused(uint256 amt) {
		if (amt > 0 && getBool(keccak256(abi.encodePacked("contract.paused", "TokenggAVAX")))) { //@audit because keccak256(abi.encodePacked("contract.paused", "TokenggAVAX")) will always return a constant value, we need to use the constant value to replace the func call, it will reduce the gas.
			revert ContractPaused();
		}
		_;
	}
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L207
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L217

(3) i also found the place call the keccak256 func with a constant string.

in ERC20Upgradeable.sol
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L139
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L170
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L172

in Staking.sol
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L73
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L348
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L349

in RewardsPool.sol
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L35
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L38
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L40
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L41
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L49
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L98
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L99
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L106
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L111
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L116
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L121
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L164

in ProcolDAO.sol
in line 
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L24
L27,L30,L33,L35,L36,L37,L40,L41,L44,L47~L52,L55,L56,
and a lot of palce. 

In Oracle.sol
L29, L38, L47, L51, L65, L66, L58

In MultisigManager.sol
L40,L49,L103,L81

In MinipoolManager.sol
L248, L252, L333,L433,L512,L542,L612,L635

In ClaimNodeOp.sol
L36,L41






 