# Gas Optimizations

## Summary

|               | Issue         | Instances     |
| :-------------: |:-------------|:-------------:|
| 1   | `storage` variable should be cached into `memory` variables instead of re-reading them  | 4 |
| 2   | Use `unchecked` blocks to save gas  |  7 |
| 3   | `x += y/x -= y` costs more gas than `x = x + y/x = x - y` for state variables  | 4 |
| 4   | `public` functions not called by the contract should be declared `external` instead | 56 |
| 5   | Don't compare boolean expressions to boolean literals  | 3 |
| 6   | `++i/i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as in the case when used in for & while loops | 7 |


## Findings

### 1- `storage` variable should be cached into `memory` variables instead of re-reading them :

The instances below point to the second+ access of a state variable within a function, the caching of a state variable replaces each Gwarmaccess (100 gas) with a much cheaper stack read, thus saves **100gas** for each instance.

There are 4 instances of this issue :

File: contracts/contract/Vault.sol [Line 71-75](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L71-L75)

```
if (avaxBalances[contractName] < amount) {
    revert InsufficientContractBalance();
}
// Update balance
avaxBalances[contractName] = avaxBalances[contractName] - amount;    
```

The value of ´avaxBalances[contractName]´ is read twice so the above operations should be modified as follow :
```
uint256 balance = avaxBalances[contractName];
if (balance < amount) {
    revert InsufficientContractBalance();
}
// Update balance
avaxBalances[contractName] = balance - amount;     
```


File: contracts/contract/Vault.sol [Line 95-99](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L95-L99)

```
if (avaxBalances[fromContractName] < amount) {
    revert InsufficientContractBalance();
}
// Update balances
avaxBalances[fromContractName] = avaxBalances[fromContractName] - amount;   
```

The value of ´avaxBalances[fromContractName]´ is read twice so the above operations should be modified as follow :
```
uint256 fromBalance = avaxBalances[fromContractName];
if (fromBalance < amount) {
    revert InsufficientContractBalance();
}
// Update balances
avaxBalances[fromContractName] = fromBalance - amount;      
```


File: contracts/contract/Vault.sol [Line 151-155](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L151-L155)

```
if (tokenBalances[contractKey] < amount) {
    revert InsufficientContractBalance();
}
// Update balances
tokenBalances[contractKey] = tokenBalances[contractKey] - amount; 
```

The value of ´tokenBalances[contractKey]´ is read twice so the above operations should be modified as follow :
```
uint256 balance = tokenBalances[contractKey];
if (balance < amount) {
    revert InsufficientContractBalance();
}
// Update balances
tokenBalances[contractKey] = balance - amount;  
```


File: contracts/contract/Vault.sol [Line 183-187](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L183-L187)

```
if (tokenBalances[contractKeyFrom] < amount) {
    revert InsufficientContractBalance();
}
// Update Balances
tokenBalances[contractKeyFrom] = tokenBalances[contractKeyFrom] - amount;
```

The value of ´tokenBalances[contractKeyFrom]´ is read twice so the above operations should be modified as follow :
```
uint256 fromBalance = tokenBalances[contractKeyFrom];
if (fromBalance < amount) {
    revert InsufficientContractBalance();
}
// Update Balances
tokenBalances[contractKeyFrom] = fromBalance - amount;
```

### 2- Use `unchecked` blocks to save gas :

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block.

There are 7 instances of this issue:

File: contracts/contract/tokens/TokenggAVAX.sol [Line 149](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L149)
```
stakingTotalAssets -= baseAmt;
```

The above operation cannot underflow due to the check :

[if (totalAmt != (baseAmt + rewardAmt) || baseAmt > stakingTotalAssets)](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L144) 

It should be marked as `unchecked`. 


File: contracts/contract/ClaimNodeOp.sol [Line 102](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L102)
```
uint256 restakeAmt = ggpRewards - claimAmt;
```

The above operation cannot underflow due to the check :

[if (claimAmt > ggpRewards)](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L95) 

It should be marked as `unchecked`. 


File: contracts/contract/MinipoolManager.sol [Line 417-418](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L417-L418)
```
uint256 avaxLiquidStakerRewardAmt = avaxHalfRewards - avaxHalfRewards.mulWadDown(dao.getMinipoolNodeCommissionFeePct());
uint256 avaxNodeOpRewardAmt = avaxTotalRewardAmt - avaxLiquidStakerRewardAmt;
```

The above operations cannot underflow because the `MinipoolNodeCommissionFeePct` value is equal to 0.15 ≈ 15% and thus we always have `avaxHalfRewards - avaxHalfRewards.mulWadDown(dao.getMinipoolNodeCommissionFeePct()) > 0` and because only half the amount is accounted in `avaxHalfRewards` we also always have `avaxTotalRewardAmt > avaxLiquidStakerRewardAmt` so both operations should be marked as `unchecked`. 


File: contracts/contract/Vault.sol [Line 75](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L75)
```
avaxBalances[contractName] = avaxBalances[contractName] - amount;
```

The above operation cannot underflow due to the check :

[if (avaxBalances[contractName] < amount)](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L71)

It should be marked as `unchecked`. 


File: contracts/contract/Vault.sol [Line 99](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L99)
```
avaxBalances[fromContractName] = avaxBalances[fromContractName] - amount;
```

The above operation cannot underflow due to the check :

[if (avaxBalances[fromContractName] < amount)](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L95) 

It should be marked as `unchecked`. 


File: contracts/contract/Vault.sol [Line 155](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L155)
```
tokenBalances[contractKey] = tokenBalances[contractKey] - amount;
```

The above operation cannot underflow due to the check :

[if (tokenBalances[contractKey] < amount)](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L151) 

It should be marked as `unchecked`. 


File: contracts/contract/Vault.sol [Line 187](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L187)
```
tokenBalances[contractKeyFrom] = tokenBalances[contractKeyFrom] - amount;
```

The above operation cannot underflow due to the check :

[if (tokenBalances[contractKeyFrom] < amount)](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L183) 

It should be marked as `unchecked`. 


### 3- `x += y/x -= y` costs more gas than `x = x + y/x = x - y` for state variables :

Using the addition operator instead of plus-equals saves **113 gas** as explained [here](https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8)

There are 4 instances of this issue:

File: contracts/contract/tokens/TokenggAVAX.sol [Line 149](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L149)
```
stakingTotalAssets -= baseAmt;
```

File: contracts/contract/tokens/TokenggAVAX.sol [Line 160](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L160)
```
stakingTotalAssets += assets;
```

File: contracts/contract/tokens/TokenggAVAX.sol [Line 245](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L245)
```
totalReleasedAssets -= amount;
```

File: contracts/contract/tokens/TokenggAVAX.sol [Line 252](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L252)
```
totalReleasedAssets += amount;
```


### 4- `public` functions not called by the contract should be declared `external` instead :

The `public` functions that are not called inside the contract should be declared `external` instead to save gas.

There are 56 instances of this issue:

```
File: tokens/TokenggAVAX.sol

72 	function initialize(Storage storageAddress, ERC20 asset) public initializer 
88      function syncRewards() public 
142 	function depositFromStaking(uint256 baseAmt, uint256 rewardAmt) public payable 
153 	function withdrawForStaking(uint256 assets) public 
166 	function depositAVAX() public payable returns (uint256 shares) 
180 	function withdrawAVAX(uint256 assets) public returns (uint256 shares) 
191 	function redeemAVAX(uint256 shares) public returns (uint256 assets) 
206 	function maxWithdraw(address _owner) public view override returns (uint256) 
216 	function maxRedeem(address _owner) public view override returns (uint256) 

File: ClaimNodeOp.sol

40      function setRewardsCycleTotal(uint256 amount) public 

File: MinipoolManager.sol

541 	function getTotalAVAXLiquidStakerAmt() public view returns (uint256) 
572 	function getMinipoolByNodeID(address nodeID) public view returns (Minipool memory mp) 
607 	function getMinipoolsgetMinipools(
            MinipoolStatus status,
            uint256 offset,
            uint256 limit
        ) public view returns (Minipool[] memory minipools)
634 	function getMinipoolCount() public view returns (uint256) 

File: MultisigManager.sol

102 	function getCount() public view returns (uint256) 

File: ProtocolDAO.sol

61 	function getContractPaused(string memory contractName) public view returns (bool) 
67 	function pauseContract(string memory contractName) public 
73 	function resumeContract(string memory contractName) public 
81 	function getRewardsEligibilityMinSeconds() public view returns (uint256) 
86     function getRewardsCycleSeconds() public view returns (uint256) 
91 	function getTotalGGPCirculatingSupply() public view returns (uint256) 
96     function setTotalGGPCirculatingSupply(uint256 amount) public 
102 	function getClaimingContractPct(string memory claimingContract) public view returns (uint256) 
107 	function setClaimingContractPct(string memory claimingContract, uint256 decimal) public 
123 	function getInflationIntervalSeconds() public view returns (uint256) 
130 	function getMinipoolMinAVAXStakingAmt() public view returns (uint256) 
135 	function getMinipoolNodeCommissionFeePct() public view returns (uint256) 
140 	function getMinipoolMaxAVAXAssignment() public view returns (uint256) 
145 	function getMinipoolMinAVAXAssignment() public view returns (uint256) 
150 	function getMinipoolCancelMoratoriumSeconds() public view returns (uint256) 
156 	function setExpectedAVAXRewardsRate(uint256 rate) public 
161 	function getExpectedAVAXRewardsRate() public view returns (uint256) 
168 	function getMaxCollateralizationRatio() public view returns (uint256) 
173 	function getMinCollateralizationRatio() public view returns (uint256) 

File: RewardsPool.sol

105 	function getRewardsCycleCount() public view returns (uint256) 

File: Staking.sol

66 	  function getTotalGGPStake() public view returns (uint256) 
103 	function getAVAXStake(address stakerAddr) public view returns (uint256) 
110 	function increaseAVAXStake(address stakerAddr, uint256 amount) public 
117 	function decreaseAVAXStake(address stakerAddr, uint256 amount) public 
133 	function increaseAVAXAssigned(address stakerAddr, uint256 amount) public 
140 	function decreaseAVAXAssigned(address stakerAddr, uint256 amount) public 
156 	function increaseAVAXAssignedHighWater(address stakerAddr, uint256 amount) public 
163 	function resetAVAXAssignedHighWater(address stakerAddr) public 
173 	function getMinipoolCount(address stakerAddr) public view returns (uint256) 
180 	function increaseMinipoolCount(address stakerAddr) public  
187 	function decreaseMinipoolCount(address stakerAddr) public  
196 	function getRewardsStartTime(address stakerAddr) public view returns (uint256) 
204 	function setRewardsStartTime(address stakerAddr, uint256 time) public 
217 	function getGGPRewards(address stakerAddr) public view returns (uint256) 
224 	function increaseGGPRewards(address stakerAddr, uint256 amount) public 
231 	function decreaseGGPRewards(address stakerAddr, uint256 amount) public 
240 	function getLastRewardsCycleCompleted(address stakerAddr) public view returns (uint256) 
248 	function setLastRewardsCycleCompleted(address stakerAddr, uint256 cycleNumber) public 
256 	function getMinimumGGPStake(address stakerAddr) public view returns (uint256) 
328 	function restakeGGP(address stakerAddr, uint256 amount) public 
379 	function slashGGP(address stakerAddr, uint256 ggpAmt) public 
```

## 5- Don't compare boolean expressions to boolean literals :

The following formulas should be used : `if (<x> == true)` => `if (<x>)`, `if (<x> == false)` => `if (!<x>)`

There are 3 instances of this issue:

```
File: contracts/contract/Storage.sol

29       booleanStorage[keccak256(abi.encodePacked("contract.exists", msg.sender))] == false

File: contracts/contract/BaseAbstract.sol

25       if (getBool(keccak256(abi.encodePacked("contract.exists", msg.sender))) == false)
74       if (enabled == false)
```

### 6- `++i/i++` should be `unchecked{++i}/unchecked{i++}` when it is not possible for them to overflow, as in the case when used in for & while loops :

There are 7 instances of this issue:
 
```
File: contracts/contract/MinipoolManager.sol

619     for (uint256 i = offset; i < max; i++)

File: contracts/contract/MultisigManager.sol

84      for (uint256 i = 0; i < total; i++)

File: contracts/contract/Ocyticus.sol

61      for (uint256 i = 0; i < count; i++)

File: contracts/contract/RewardsPool.sol

74      for (uint256 i = 0; i < inflationIntervalsElapsed; i++)
215     for (uint256 i = 0; i < count; i++)
230     for (uint256 i = 0; i < enabledMultisigs.length; i++)

File: contracts/contract/Staking.sol

428     for (uint256 i = offset; i < max; i++)
```