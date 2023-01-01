### 01  UPGRADEABLE CONTRACT IS MISSING A `__GAP[50]` STORAGE VARIABLE TO ALLOW FOR NEW STORAGE VARIABLES IN LATER VERSIONS

*Number of Instances Identified: 2*

See [this](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps) link for a description of this storage variable. While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol

```
24: contract TokenggAVAX is Initializable, ERC4626Upgradeable, UUPSUpgradeable, BaseUpgradeable
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol

```
11: abstract contract ERC4626Upgradeable is Initializable, ERC20Upgradeable
```


### 02 REQUIRE() SHOULD BE USED INSTEAD OF ASSERT()

*Number of Instances Identified: 1*

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol

```
83: assert(msg.sender == address(asset));
```


### 03 MISSING EVENTS FOR FUNCTIONS THAT CHANGE CRITICAL PARAMETERS

*Number of Instances Identified: 29*

The functions that change critical parameters should emit events. Events allow capturing the changed parameters so that off-chain tools/interfaces can register such changes with timelocks that allow users to evaluate them and consider if they would like to engage/exit based on how they perceive the changes as affecting the trustworthiness of the protocol or profitability of the implemented financial services. The alternative of directly querying on-chain contract state for such changes is not considered practical for most users/usages.

Missing events and timelocks do not promote transparency and if such changes immediately affect users’ perception of fairness or trustworthiness, they could exit the protocol causing a reduction in liquidity which could negatively impact protocol TVL and reputation.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol

```
135: function setAddress(bytes32 key, address value) internal
139: function setBool(bytes32 key, bool value) internal 
143: function setBytes(bytes32 key, bytes memory value) internal
147: function setBytes32(bytes32 key, bytes32 value) internal
151: function setInt(bytes32 key, int256 value) internal
155: function setUint(bytes32 key, uint256 value) internal
159: function setString(bytes32 key, string memory value) internal
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol

```
40: function setRewardsCycleTotal(uint256 amount)
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Oracle.sol

```
28: function setOneInch(address addr) external onlyGuardian
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol

```
95: function setTotalGGPCirculatingSupply(uint256 amount)
106: function setClaimingContractPct(string memory claimingContract, uint256 decimal)
156: function setExpectedAVAXRewardsRate(uint256 rate)
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol

```
204: function setRewardsStartTime(address stakerAddr, uint256 time)
248: function setLastRewardsCycleCompleted(address stakerAddr, uint256 cycleNumber)
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol

```
41: function setGuardian(address newAddress) external
104: function setAddress(bytes32 key, address value)
108: function setBool(bytes32 key, bool value)
112: function setBytes(bytes32 key, bytes calldata value)
116: function setBytes32(bytes32 key, bytes32 value)
120: function setInt(bytes32 key, int256 value)
124: function setString(bytes32 key, string calldata value)
128: function setUint(bytes32 key, uint256 value)
136: function deleteAddress(bytes32 key)
140: function deleteBool(bytes32 key)
144: function deleteBytes(bytes32 key)
148: function deleteBytes32(bytes32 key)
152: function deleteInt(bytes32 key)
156: function deleteString(bytes32 key)
160: function deleteUint(bytes32 key)
```


### 04 INCLUDE RETURN PARAMETERS IN NATSPEC COMMENTS

*Number of Instances Identified: 35*

### Description

It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation. In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.

[https://docs.soliditylang.org/en/v0.8.15/natspec-format.html](https://docs.soliditylang.org/en/v0.8.15/natspec-format.html)

If Return parameters are declared, you must prefix them with `/// @return`.

Some code analysis programs do analysis by reading NatSpec details, if they can’t see the `@return` tag, they do incomplete analysis.

### Recommendation

Include return parameters in NatSpec comments

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol

```
90: function getContractAddress(string memory contractName)
99: function getContractName(address contractAddress)
107: function getAddress(bytes32 key)
111: function getBool(bytes32 key)
115: function getBytes(bytes32 key)
119: function getBytes32(bytes32 key)
123: function getInt(bytes32 key)
127: function getUint(bytes32 key)
131: function getString(bytes32 key)
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol

```
35: function getRewardsCycleTotal()
46: function isEligible(address stakerAddr)
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol

```
541: function getTotalAVAXLiquidStakerAmt()
547: function calculateGGPSlashAmt(uint256 avaxRewardAmt)
572: function getMinipoolByNodeID(address nodeID)
634: function getMinipoolCount()
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol

```
80: function requireNextActiveMultisig()
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol

```
85: function getRewardsCycleSeconds()
90: function getTotalGGPCirculatingSupply()
95: function setTotalGGPCirculatingSupply(uint256 amount)
106: function setClaimingContractPct(string memory claimingContract, uint256 decimal)
129: function getMinipoolMinAVAXStakingAmt()
134: function getMinipoolNodeCommissionFeePct()
139: function getMinipoolMaxAVAXAssignment()
144: function getMinipoolMinAVAXAssignment()
149: function getMinipoolCancelMoratoriumSeconds()
161: function getExpectedAVAXRewardsRate()
168: function getMaxCollateralizationRatio()
173: function getMinCollateralizationRatio()
181: function getTargetGGAVAXReserveRate()
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol

```
105: function getRewardsCycleCount()
110: function increaseRewardsCycleCount()
115: function getRewardsCycleStartTime()
120: function getRewardsCycleTotalAmt()
125: function getRewardsCyclesElapsed()
151: function canStartRewardsCycle()
```


### 05 OPEN TODOS

*Number of Instances Identified: 3*

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol

```
6: // TODO remove this when dev is complete
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol

```
412: // TODO Revisit this logic if we ever allow unequal matched funds
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol

```
203: // TODO cant use onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) since we also call from increaseMinipoolCount. Wat do?
```


## 06 TYPOS

*Number of Instances Identified: 9*

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol

```
203: // TODO cant use onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) since we also call from increaseMinipoolCount. Wat do?
```

`what` should be used instead of `wat`

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol

```
49: /// @notice the amount of rewards distributed in a the most recent cycle.
```

`distributed in a the` should be `distributed in the` instead

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol

```
411: // Calculate rewards splits (these will all be zero if no rewards were recvd)
415: // Node operators recv an additional commission fee
442: /// @notice Re-stake a minipool, compounding all rewards recvd
556: /// @return The approximate rewards the node should recieve from Avalanche for beign a validator
606: /// @return minipools in the protocol that adhear to the paramaters
```

it should be `received` or `receive` instead of `recvd` and `recv`
it should be `being` instead of `beign`
it should be `adhere` and `parameters` instead of `adhear` and `paramaters`

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol

```
81: /// @dev No funds actually move, just bookeeping
82: /// @param toContractName Name of the contract fucns are being transferred to
```

 it should be `bookkeeping` instead of `bookeeping`
 it should be `funds` instead of `fucns`


### 07 USE A MORE RECENT VERSION OF SOLIDITY

### Recommendation

*Number of Instances Identified: 2*

Old version of Solidity is used `0.7.6`, newer version can be used `0.8.17`

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol

```
2: pragma solidity >=0.8.0;
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol

```
2: pragma solidity >=0.8.0;
```


### 08 FLOATING PRAGMA VERSION SHOULD NOT BE USED

*Number of Instances Identified: 2*

In the contracts, floating pragmas should not be used. Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

### Proof of Concept

[https://swcregistry.io/docs/SWC-103](https://swcregistry.io/docs/SWC-103)


https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol

```
2: pragma solidity >=0.8.0;
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol

```
2: pragma solidity >=0.8.0;
```


### 09 ADDING A RETURN STATEMENT WHEN THE FUNCTION DEFINES A NAMED RETURN VARIABLE, IS REDUNDANT

*Number of Instances Identified: 2*

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol

```
572: function getMinipoolByNodeID(address nodeID) public view returns (Minipool memory mp) {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol

```
66: function getInflationAmt() public view returns (uint256 currentTotalSupply, uint256 newTotalSupply) {
```

