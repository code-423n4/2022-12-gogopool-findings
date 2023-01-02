## GoGoPool Audit Report

## Gas Optimizations

### Increments should be unchecked

In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.

Also increments can be made pre-increment instead of post-increment, so `i++` would become  `++i` where necessary.

https://github.com/ethereum/solidity/issues/10695

Instances include:

```solidity
MinipoolManager.getMinipools(): for (uint256 i = offset; i < max; i++)

MultisigManager.requireNextActiveMultisig(): for (uint256 i = 0; i < total; i++)

Ocyticus.disableAllMultisigs(): for (uint256 i = 0; i < count; i++)

RewardsPool.getInflationAmt(): for (uint256 i = 0; i < inflationIntervalsElapsed; i++)
RewardsPool.distributeMultisigAllotment(): for (uint256 i = 0; i < count; i++)
RewardsPool.distributeMultisigAllotment(): for (uint256 i = 0; i < enabledMultisigs.length; i++)

Staking.getStakers(): for (uint256 i = offset; i < max; i++)
```

The code would go from: 
```solidity
for (uint256 i; i < numIterations; i++) {  
 // ...  
}  
```

To: 
```solidity
for (uint256 i; i < numIterations;) {  
 // ...  
 unchecked { ++i; }  
}  
```

### An array's length should be cached to save gas in for-loops

Reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset) in the stack.

Caching the array length in the stack saves around 3 gas per iteration.

Instances include:

```solidity
RewardsPool.distributeMultisigAllotment(): for (uint256 i = 0; i < enabledMultisigs.length; i++)
```

The code would go from: 
```solidity
for (uint256 i; i < arr.length; ++i) {  
 // ...  
}  
```

To: 
```solidity
uint length = arr.length;
for (uint256 i; i < length; ++i) {  
 // ...  
}  
```

### Usage of double require instead of && operator should be considered

The usage of multiple require statements instead of one complex saves gas in some cases.

Instances include:

```solidity
ERC20Upgradeable.permit(): require(recoveredAddress != address(0) && recoveredAddress == owner, "INVALID_SIGNER");
```

The code would go from: 
```solidity
require(x == 0 && y == 0);
```

To: 
```solidity
require(x == 0);
require(y == 0);
```

### Usage of calldata over memory when variable is read-only

Storing information inside `calldata` is less expensive than storing it in `memory`. In case you only need to read the variable, you should consider switching to `calldata`.

Instances include:

```solidity
BaseAbstract.onlySpecificRegisteredContract(string memory contractName, address contractAddress)
BaseAbstract.guardianOrSpecificRegisteredContract(string memory contractName, address contractAddress)
BaseAbstract.getContractAddress(string memory contractName)

ProtocolDAO.getContractPaused(string memory contractName)
ProtocolDAO.pauseContract(string memory contractName)
ProtocolDAO.resumeContract(string memory contractName)
ProtocolDAO.getClaimingContractPct(string memory claimingContract)
ProtocolDAO.setClaimingContractPct(string memory claimingContract, uint256 decimal)
ProtocolDAO.registerContract(address addr, string memory name)

RewardsPool.getClaimingContractDistribution(string memory claimingContract)

Vault.transferAVAX(string memory toContractName, uint256 amount)
Vault.balanceOf(string memory networkContractName)
Vault.balanceOfToken(string memory networkContractName, ERC20 tokenAddress)
```

The code would go from: 
```solidity
function example(string memory _data) {
 // ...  
}
```

To: 
```solidity
function example(string calldata _data) {
 // ...  
}
```


### Function ordering might be considered

There is additional value in renaming functions to be ordered based on the frequency you expect them to be used, saving average overall gas cost of interacting with your contract.

The function name might go from: 
```solidity
receiveWithdrawalAVAX()
```

To: 
```solidity
receiveWithdrawalAVAX_y6p() // 0x000066ed
```

Use this tool if needed: https://emn178.github.io/solidity-optimize-name/ 
