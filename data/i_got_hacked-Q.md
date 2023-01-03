## Missing `zero address` check in `constructor`

* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L24-L25



## open `TODO` comments

* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L203

* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L412

* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L6




## `TYPOS`

* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L67
//@audit `exectued`
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L82
//@audit `fucns`
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L134
//@audit ’recieve’
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L203
//@audit ’Wat’
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L419
//@audit ’paramaters’&'adhear'
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L134
//@audit ’commision’
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L46
//@audit ’seperately’
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L383
//@audit ’recieved’
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L556
//@audit ’beign’
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L606
//@audit ’adhear’ & ‘paramaters’



## Use of `block.timestamp`

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.


* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L89
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L120
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L128
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L206
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L40-L41
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L60
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L84
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L128
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L164
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L226
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L279
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L356
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L394

## `require()` should be used instead of `assert()`

* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L83


## Use of `ecrecover()`

* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L132


## Storing the `block.chainid` is not safe. 


* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L62





## `NatSpec` is incomplete

* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L191-L193 
   /// @audit Missing: `return`
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L197-L200
   /// @audit Missing: `return`
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L65-L66
   /// @audit Missing: `return`
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L71-L72
   /// @audit Missing: `return`
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L78-L80
   /// @audit Missing: `return`
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L85-L87
   /// @audit Missing: `return`&'param'
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L92-L94
   /// @audit Missing: `return`&'param'
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L101-L103
   /// @audit Missing: `return`
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L149-L151
   /// @audit Missing: `return`
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L59-L61
   /// @audit Missing: `return`
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L100-L102
   /// @audit Missing: `param`
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L539-L541
   /// @audit Missing: `return`
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L545-L547
   /// @audit Missing: `return`
* https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L570-L572
   /// @audit Missing: `return`
