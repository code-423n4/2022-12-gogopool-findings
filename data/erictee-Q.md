### [L-01] ```require()``` should be used instead of ```assert()```


#### Impact
Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction’s available gas rather than returning it, as ```require()```/```revert()``` do. ```assert()``` should be avoided even past solidity version 0.8.0 as its [documentation](https://docs.soliditylang.org/en/v0.8.14/control-structures.html#panic-via-assert-and-error-via-require) states that “The assert function creates an error of type Panic(uint256). … Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix”.


#### Findings:
```
contract/tokens/TokenggAVAX.sol:L83		assert(msg.sender == address(asset));

```

### [L-02] ```decimals()``` not part of ERC20 standard.


#### Impact
```decimals()``` is not part of the official ERC20 standard and might fall for tokens that do not implement it. While in practice it is very unlikely, as usually most of the tokens implement it, this should still be considered as a potential issue.


#### Findings:
```
contract/tokens/upgradeable/ERC4626Upgradeable.sol:L34		__ERC20Upgradeable_init(_name, _symbol, _asset.decimals());

```

### [L-03] Use of ```ecrecover``` is susceptible to signature malleability


#### Impact
The ```ecrecover``` function is used to recover the address from the signature. The built-in EVM precompile ecrecover is susceptible to signature malleability which could lead to replay attacks (references: https://swcregistry.io/docs/SWC-117, https://swcregistry.io/docs/SWC-121 and https://medium.com/cryptronics/signature-replay-vulnerabilities-in-smart-contracts-3b6f7596df57).

Consider using OpenZeppelin’s ECDSA library (which prevents this malleability) instead of the built-in function.


#### Findings:
```
contract/tokens/upgradeable/ERC20Upgradeable.sol:L132			address recoveredAddress = ecrecover(

```

### [N-01] Open TODOs


#### Impact
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment.


#### Findings:
```
contract/BaseAbstract.sol:L6// TODO remove this when dev is complete

contract/Staking.sol:L203	// TODO cant use onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) since we also call from increaseMinipoolCount. Wat do?

contract/MinipoolManager.sol:L412		// TODO Revisit this logic if we ever allow unequal matched funds

```
