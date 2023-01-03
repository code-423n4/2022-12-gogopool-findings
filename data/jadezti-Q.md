
## Non Critical Issues

|      | Issue                                       | Instances |
| ---- |:------------------------------------------- |:---------:|
| NC-1 | Using specific version in `pragma solidity` |     2     | 

### [NC-1] Using specific version in `pragma solidity`

*Instances (2)*

Using a specific version, e.g. `pragma solidity 0.8.17;`

[Link to file](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol)
```solidity
File: contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol

2:    pragma solidity >=0.8.0;
```

[Link to file](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol)
```solidity
File: contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol

2:    pragma solidity >=0.8.0;
```

---

## Low Severity Issues

|     | Issue                                                                                       | Instances |
| --- |:------------------------------------------------------------------------------------------- |:---------:|
| L-1 | Function `requireNextActiveMultisig` always returns the first Multisig                      |     1     |
| L-2 | An EOA account can be registered as a registered contract which exposes high security risks |     1     | 

### [L-1] Function `requireNextActiveMultisig` always returns the first Multisig

[Affected code](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L80-L91)

`MultisigManager.requireNextActiveMultisig` is supposed to return the next enabled Multisig. However it always returns the first matched Multisig since it always searches from the beginning and returns the first matched item.

*Mitigation Suggestion*: 
Add an extra storage variable to store the index of the current selected Multisig. When `requireNextActiveMultisig` is called, it searches from the last selected Multisig index and wraps through until to the the last selected Multisig location again.

### [L-2] An EOA account can be registered as a registered contract which exposes high security risks

[Link to affected code](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L187-L194)

`ProtocolDAO.registerContract(address addr, string memory name)` can register an EOA account as  a registered contract due to the lack of verifying whether `addr` is a contract address. This exposes high security risks since EOA has the ability to call any contract functions, which means it can change values of state variables protected by `onlyRegisteredNetworkContract`. This is in contrast to a smart contract which can only call the precoded functions that can be easily spotted through code review.

*Mitigation Suggestion*:
Verify that `addr` is an address of an existing contract, the code size at the `addr` is larger than `0`.
