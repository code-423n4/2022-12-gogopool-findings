## [YO GAS-1] USE FUNCTION INSTEAD OF MODIFIERS

### Handle
yosuke

## Vulnerability details
### Impact

### Proof of Concept
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L23-L87
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L15-L20
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L12-L17
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L28-L33
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L58-L63

### Recommended Mitigation Steps
USE FUNCTION INSTEAD OF MODIFIERS because of lower gas fee.
ex)
before
```solidity=
modifier whenTokenNotPaused(uint256 amt) {
    if (amt > 0 && getBool(keccak256(abi.encodePacked("contract.paused", "TokenggAVAX")))) {
        revert ContractPaused();
    }
    _;
}
function previewDeposit(uint256 assets) public view override whenTokenNotPaused(assets) returns (uint256) {
    return super.previewDeposit(assets);
}
```
after
```solidity=
function whenTokenNotPaused(uint256 amt) private {
    if (amt > 0 && getBool(keccak256(abi.encodePacked("contract.paused", "TokenggAVAX")))) {
        revert ContractPaused();
    }
}
function previewDeposit(uint256 assets) public view override returns (uint256) {
    whenTokenNotPaused(assets);
    return super.previewDeposit(assets);
}
```
    

## [YO GAS-2] Use calldata instead of memory for function arguments that do not get mutated.

### Handle
yosuke

## Vulnerability details
### Impact

### Proof of Concept
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L32-L37
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L90-L96
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimProtocolDAO.sol#L21
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L54-L55

### Recommended Mitigation Steps
Use calldata instead of memory for function arguments that do not get mutated because of lower gas fee.
ex) 
before
```solidity=
function spend(
    string memory invoiceID,
    ...
) external onlyGuardian {
...
}
```
after
```solidity=
function spend(
    string calldata invoiceID,
    ...
) external onlyGuardian {
...
}
```

## [YO GAS-3] USE bytes32 INSTEAD OF string

### Handle
yosuke

## Vulnerability details
### Impact

### Proof of Concept
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L32
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L51
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L82
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L90
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L100
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L37
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L52
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L67
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L91
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L23-L25
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L54-L55


### Recommended Mitigation Steps
USE bytes32 INSTEAD OF string because of lower gas fee.
ex)Since the contract name fits in 32 bytes or less, it is cheaper to use bytes32 instead of string for gas.
before
```solidity=
modifier onlySpecificRegisteredContract(string memory contractName, ...) {
    ...
}
```
after
```solidity=
modifier onlySpecificRegisteredContract(bytes32 memory contractName, ...) {
    ...
}
```

## [YO GAS-4] USING BOOLS FOR STORAGE INCURS OVERHEAD

### Handle
yosuke

## Vulnerability details
### Impact
Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and  pointer aliasing, and it cannot be disabled.

### Proof of Concept
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L25
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L41-L42
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L52-L53
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L73
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L83
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L111-L112
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L139-L140
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L167-L168
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L139-L140
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L167-L168
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L46
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L155
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L313
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L61
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L74
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L83
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L109
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L111
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L13
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L60
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L24
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L27
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L61-L62
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L68
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L74
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L191
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L200
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L35
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L38
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L151
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L216
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L16
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L29
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L76-L77
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L108-L109
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L140-L141
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L39
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L204-L210
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L59
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L207
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L217
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L70
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L78
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L96

### Recommended Mitigation Steps
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from false to true, after having been true in the past.
ex)
before
```solidity=
modifier guardianOrRegisteredContract() {
    bool isContract = getBool(keccak256(abi.encodePacked("contract.exists", msg.sender)));
    bool isGuardian = msg.sender == gogoStorage.getGuardian();

    if (!(isGuardian || isContract)) {
        revert MustBeGuardianOrValidContract();
    }
    _;
}
```
after
```solidity=
modifier guardianOrRegisteredContract() {
    uint256 isContract = 1; //false
    uint256 isGuardian = 1; //false
    if( getBool(keccak256(abi.encodePacked("contract.exists", msg.sender))) ) isContract = 2;
    if( msg.sender == gogoStorage.getGuardian() ) isGuardian = 2;

    if (!( isGuardian == 2 || isContract == 2 )) {
        revert MustBeGuardianOrValidContract();
    }
    _;
}
```



## [YO GAS-5]DON’T COMPARE BOOLEAN EXPRESSIONS TO BOOLEAN LITERALS 

### Handle
yosuke

## Vulnerability details
### Impact

### Proof of Concept
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L74

### Recommended Mitigation Steps
before
```solidity=
if (enabled == false) {
    revert MustBeMultisig();
}
```
after
```solidity=
if (!enabled) {
    revert MustBeMultisig();
}
```

## [YO GAS-6] USE NAMED RETURNS WHERE APPROPRIATE

### Handle
yosuke

## Vulnerability details
### Impact

### Proof of Concept
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L90
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L99
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L115
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L127
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L140
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L547
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L557
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L134
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L286
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L387
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L113
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L206
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L216
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L120
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L126
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L136
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L142

Exception
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L572-L575
Assign to return value instead of return
before
```solidity=
function getMinipoolByNodeID(address nodeID) public view returns (Minipool memory mp) {
    int256 index = getIndexOf(nodeID);
    return getMinipool(index);
}
```
after
```solidity=
function getMinipoolByNodeID(address nodeID) public view returns (Minipool memory mp) {
    int256 index = getIndexOf(nodeID);
    mp = getMinipool(index);
}
```
Exception
No need to do a RETURN
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L66-L78

### Recommended Mitigation Steps
ex)
before
```solidity=
function getContractAddress(string memory contractName) internal view returns (address) {
    address contractAddress = getAddress(keccak256(abi.encodePacked("contract.address", contractName)));
    if (contractAddress == address(0x0)) {
        revert ContractNotFound();
    }
    return contractAddress;
}
```
after
```solidity=
function getContractAddress(string memory contractName) internal view returns (address contractAddress) {
    contractAddress = getAddress(keccak256(abi.encodePacked("contract.address", contractName)));
    if (contractAddress == address(0x0)) {
        revert ContractNotFound();
    }
}
```

## [YO GAS-7] Using private rather than public for constants and immutable, saves gas

### Handle
yosuke

## Vulnerability details
### Impact

### Proof of Concept
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L27
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L78-L79

### Recommended Mitigation Steps
ex)
before
```solidity=
ERC20 public immutable ggp;
```
after
```solidity=
ERC20 private immutable ggp;
```

## [YO GAS-8] It is cheaper in gas to use "value!=0" instead of "value>0" for comparing variables of type uint.

### Handle
yosuke

## Vulnerability details
### Impact

### Proof of Concept
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L103
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L109

### Recommended Mitigation Steps
ex)
before
```solidity=
if (restakeAmt > 0) {
    ...
}
```
after
```solidity=
if (restakeAmt != 0) {
    ...
}
```

## [YO GAS-9] Delete unused functions

### Handle
yosuke

## Vulnerability details
### Impact

### Proof of Concept
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L106


### Recommended Mitigation Steps

## [YO GAS-10] ++I COSTS LESS GAS THAN I++, ESPECIALLY WHEN IT’S USED IN FORLOOPS (-I/I-- TOO)

### Handle
yosuke

## Vulnerability details
### Impact

### Proof of Concept
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L619-L625
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L84-L89
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L61-L66
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L74-L76
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L215-L221
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L230-L232
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L428-L432


### Recommended Mitigation Steps
ex)
before
```solidity=
function getMinipools(
    MinipoolStatus status,
    uint256 offset,
    uint256 limit
) public view returns (Minipool[] memory minipools) {
    ...
    for (uint256 i = offset; i < max; i++) {
        Minipool memory mp = getMinipool(int256(i));
        if (mp.status == uint256(status)) {
            minipools[total] = mp;
            total++;
        }
    }
    ...
}
```
after
```solidity=
function getMinipools(
    MinipoolStatus status,
    uint256 offset,
    uint256 limit
) public view returns (Minipool[] memory minipools) {
    ...
    for (uint256 i = offset; i < max;) {
        Minipool memory mp = getMinipool(int256(i));
        if (mp.status == uint256(status)) {
            minipools[total] = mp;
            total++;
        }
        unchecked { ++i; }
    }
    ...
}
```

## [YO GAS-11] IT COSTS MORE GAS TO INITIALIZE NON-CONSTANT/NON-IMMUTABLE VARIABLES TO ZERO THAN TO LET THE DEFAULT OF ZERO BE APPLIED

### Handle
yosuke

## Vulnerability details
### Impact
If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

### Proof of Concept
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L618
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L84
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L61
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L141

### Recommended Mitigation Steps
ex)
before
```solidity=
function getMinipools(
    MinipoolStatus status,
    uint256 offset,
    uint256 limit
) public view returns (Minipool[] memory minipools) {
    ...
    uint256 total = 0;
    ...
}
```
after
```solidity=
function getMinipools(
    MinipoolStatus status,
    uint256 offset,
    uint256 limit
) public view returns (Minipool[] memory minipools) {
    ...
    uint256 total;
    ...
}
```

## [YO GAS-12] USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE() STRINGS TO SAVE GAS

### Handle
yosuke

## Vulnerability details
### Impact
Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they’re hit by avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas.

### Proof of Concept
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L44


### Recommended Mitigation Steps
USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE() STRINGS TO SAVE GAS