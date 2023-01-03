
# QA Report

## Summary

|               | Issue         | Risk     | Instances     |
| :-------------: |:-------------|:-------------:|:-------------:|
| 1   |`recordStakingError` function doesn't decrease the minipool `avaxLiquidStakerAmt` value | Low | 1 |
| 2   | Immutable state variables lack zero address checks | Low | 3 |
| 3   | Events should be emitted in functions that chagne critical parameters | NC | 7 |
| 4   | Duplicated `require()` checks should be refactored to a modifier  | NC |  6 |
| 5   | Named return variables not used anywhere in the functions | NC | 1 |


## Findings

### 1- `recordStakingError` function doesn't decrease the minipool `avaxLiquidStakerAmt` value :

When the function `recordStakingError` is called by the multisig it decreases both the total AVAX staking amount and the AVAX assigned to the minipool owner, but it does not reset the minipool `avaxLiquidStakerAmt` back to zero and leaves it equal to the previous amount. 

In the current contract logic this doesn't have a big impact (only have an impact in the view functions : `getMinipool`, `getMinipools` as they will return wrong information) but as the contract is meant to be upgradable it can cause damage in the future implementations.

#### Risk : Low

#### Proof of Concept

Issue occurs in the instance below :

File: contracts/contract/MinipoolManager.sol [Line 484-515](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L484-L515)
```
function recordStakingError(address nodeID, bytes32 errorCode) external payable {
    int256 minipoolIndex = onlyValidMultisig(nodeID);
    requireValidStateTransition(minipoolIndex, MinipoolStatus.Error);

    address owner = getAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".owner")));
    uint256 avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpAmt")));
    uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));

    if (msg.value != (avaxNodeOpAmt + avaxLiquidStakerAmt)) {
        revert InvalidAmount();
    }

    setBytes32(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".errorCode")), errorCode);
    setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Error));
    setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxTotalRewardAmt")), 0);
    setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpRewardAmt")), 0);
    setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerRewardAmt")), 0);

    // Send the nodeOps AVAX to vault so they can claim later
    Vault vault = Vault(getContractAddress("Vault"));
    vault.depositAVAX{value: avaxNodeOpAmt}();

    // Return Liq stakers funds
    ggAVAX.depositFromStaking{value: avaxLiquidStakerAmt}(avaxLiquidStakerAmt, 0);

    /** @audit
        In the lines below the AVAX assigned to the owner & TotalAVAXLiquidStakerAmt are updated
        But the minipool.avaxLiquidStakerAmt is not reset to zero
    */

    Staking staking = Staking(getContractAddress("Staking"));
    staking.decreaseAVAXAssigned(owner, avaxLiquidStakerAmt);

    subUint(keccak256("MinipoolManager.TotalAVAXLiquidStakerAmt"), avaxLiquidStakerAmt);

    emit MinipoolStatusChanged(nodeID, MinipoolStatus.Error);
}
```

As you can see from the code above the value of `minipool.avaxLiquidStakerAmt` is not updated and will remain the same until the minipool is recreated, this will return wrong minipool information when both the functions `getMinipool` and `getMinipools` are called and it can also cause problems if the contract logic is upgraded in the future and uses this wrong value.

#### Mitigation

To avoid this issue i recommend to reset the value `minipool.avaxLiquidStakerAmt` back to zero and the `recordStakingError` function should be modified as follow :
``` 
function recordStakingError(address nodeID, bytes32 errorCode) external payable {
    int256 minipoolIndex = onlyValidMultisig(nodeID);
    requireValidStateTransition(minipoolIndex, MinipoolStatus.Error);

    address owner = getAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".owner")));
    uint256 avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpAmt")));
    uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));

    ...

    Staking staking = Staking(getContractAddress("Staking"));
    staking.decreaseAVAXAssigned(owner, avaxLiquidStakerAmt);

    subUint(keccak256("MinipoolManager.TotalAVAXLiquidStakerAmt"), avaxLiquidStakerAmt);

    /** @audit
        Reset minipool.avaxLiquidStakerAmt back to zero
    */
    setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")), 0);

    emit MinipoolStatusChanged(nodeID, MinipoolStatus.Error);
```

### 2- Immutable state variables lack zero address checks  :

Constructors should check the values written in an immutable state variables(address) is not the address(0)

#### Risk : Low

#### Proof of Concept
Instances include:

File: contracts/contract/MinipoolManager.sol [Line 183-184](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L183-L184)
```
ggp = ggp_;
ggAVAX = ggAVAX_;
```

File: contracts/contract/ClaimNodeOp.sol [Line 31](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L31)
```
ggp = ggp_;
```

#### Mitigation
Add non-zero address checks in the constructors for the instances aforementioned.


### 3- Events should be emitted in functions that chagne critical parameters :

Functions that make critical changes to  the protocol state should emit an event so that Dapps and users can detect those changes (paused, new rate,...) 

#### Risk : NON CRITICAL

#### Proof of Concept
Instances include:

File: contracts/contract/ProtocolDAO.sol [Line 67](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L67)
```
function pauseContract(string memory contractName) public
```

File: contracts/contract/ProtocolDAO.sol [Line 73](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L73)
```
function resumeContract(string memory contractName) public
```

File: contracts/contract/ProtocolDAO.sol [Line 96](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L96)
```
function setTotalGGPCirculatingSupply(uint256 amount) public 
```

File: contracts/contract/ProtocolDAO.sol [Line 156](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L156)
```
function setExpectedAVAXRewardsRate(uint256 rate) public
```

File: contracts/contract/ClaimNodeOp.sol [Line 40](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L40)
```
function setRewardsCycleTotal(uint256 amount) public
```

File: contracts/contract/Ocyticus.sol [Line 37](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L37)
```
function pauseEverything() external onlyDefender
```

File: contracts/contract/Ocyticus.sol [Line 47](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L47)
```
function resumeEverything() external onlyDefender
```

#### Mitigation
Emit an event in the aforementioned functions.


### 4- Duplicated `require()` checks should be refactored to a modifier :

`require()` checks statement used multiple times inside a contract should be refactored to a modifier for better readability.

#### Risk : NON CRITICAL

#### Proof of Concept

There are 6 instances of this issue :

```
File: contracts/contract/Vault.sol

48      if (msg.value == 0) {
			revert InvalidAmount();
		}
63      if (amount == 0) {
			revert InvalidAmount();
		}
86      if (amount == 0) {
			revert InvalidAmount();
		}
114     if (amount == 0) {
			revert InvalidAmount();
		}
143     if (amount == 0) {
			revert InvalidAmount();
		}
172     if (amount == 0) {
			revert InvalidAmount();
		}
```

Those checks should be replaced by a `ValidAmount` modifier as follow :

```
modifier ValidAmount(uint256 amount){
    if (amount == 0) {
        revert InvalidAmount();
    }
    _;
}
```

### 5- Named return variables not used anywhere in the function :

When Named return variable are declared they should be used inside the function instead of the return statement or if not they should be removed to avoid confusion.

#### Risk : NON CRITICAL

#### Proof of Concept

Instances include:

File: contracts/contract/RewardsPool.sol [Line 66](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L66)
```
returns (uint256 currentTotalSupply, uint256 newTotalSupply)
```

#### Mitigation
Either use the named return variables inplace of the return statement or remove them.