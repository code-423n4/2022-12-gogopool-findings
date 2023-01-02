## [G-01] Don't initialize variables to their default value explicitly

Initializing a variable to it's default value costs extra gas.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L74
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L215
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L230
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L428
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L619
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L61
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L84

## [G-02] Dividing by 2**N can be replaced by right shifting by N

Shifting the value by N (using ```value >> N```) is the same as dividing by 2**N and it saves gas.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L413


## [G-03] Cache the array length outside of loops

As long as the array length is not being modified, we can cache it outside of the loop to save a reading operation each cycle.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L230

## [G-04] Use calldata instead of memory

Use calldata instead of memory for function parameters when the function argument is only read.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimProtocolDAO.sol#L21
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L61
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L67
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L73
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L102
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L107
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L190
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L211
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L84
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L109
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L167
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L193
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L200


## [G-05] Use of ++i/--i is cheaper than i++/i--

Each use of ++i/--i instead of i++/i-- can save 5 gas. When used in loops where the 
operation is repeated multiple times this can be a noticeable gas saving.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L74
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L215
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L230
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L428
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L619
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L61
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L84


## [G-06] A = A + B is cheaper than A += B when using state variables

A = A + B can be used instead of A += B to save gas.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L149
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L160
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L245
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L252
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L79
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L84
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L101
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L106
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L184
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L189
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L196
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L201

## [G-07] Using uint256 instead of bool for storage is cheaper

Using uint256 is less expensive than bools.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L13

Note that the following occurances should also be updated because of the use of uint256.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L16
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L23
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L28
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L33

## [G-08] Use assembly to check for address(0)

Using assembly to check for address(0) saves 6 gas.

e.g.
```
assembly {
 if iszero(addr) {
    // the other code here
 }
}
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L73
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L92
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L202
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L111
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L154


## [G-09] ++i/i++ can be unchecked{++i}/unchecked{i++} when used in for/while loops

Using of unchecked can save 30-40 gas per loop because overflow is not possible in the occurances below.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L74
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L215
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L230
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L428
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L619
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L61
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L84


## [G-10] Internal functions called only once can be inlined

Having a separate function used only once can cost 20-40 gas because of two extra JUMP instructions and additional stack operations for the function calls.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L82
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L110
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L203


## [G-11] Public functions not called inside the contract should be external

Functions called outside can save gas by changing the visibility from public to external.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L541
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L572
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L607
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L634
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L61
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L67
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L73
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L81
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L86
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L91
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L96
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L102
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L107
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L123
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L130
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L135
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L140
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L145
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L150
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L156
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L161
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L168
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L173
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L105
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L125
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L66
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L103
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L110
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L117
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L156
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L163
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L173
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L180
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L187
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L196
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L204
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L217
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L224
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L231
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L240
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L248
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L256
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L328
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L379
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L72
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L88
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L142
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L153
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L166
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L180
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L191
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L40
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L102


## [G-12] Not using the named return variables wastes gas

The variable names can be removed to save deployment gas.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L572