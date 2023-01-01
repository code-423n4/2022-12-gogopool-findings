# Code of Line

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L157

# Impact

```ERC20 tokenContract = ERC20(tokenAddress);```

# POF

tokenAddress is already instance of ERC20. so do not need to make instance again.

# Recommended Mitigation Steps

before

```solidity
ERC20 tokenContract = ERC20(tokenAddress);
tokenContract.safeTransfer(withdrawalAddress, amount);
```

after

```solidity
tokenAddress.safeTransfer(withdrawalAddress, amount);
```