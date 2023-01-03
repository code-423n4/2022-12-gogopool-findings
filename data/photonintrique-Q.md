## `initialize()` function can be called by anybody

`initialize()` function can be called anybody when the contract is not initialized.

If someone else than deployer calls the function, he or she could pass a malicious `storageAddress` which could be problematic.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L72

### Recommended Mitigation Steps

Add a control that makes `initialize()` only call the Deployer contract or address.
