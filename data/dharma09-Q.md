- Low
    1. `require()` should be used instead of `assert()`
    2. approve should be replaced with safeApprove or safeIncreaseAllowance() / safeDecreaseAllowance()
    3. _safeMint() should be used rather than _mint() wherever possible
- Non Critical
    1. The `nonReentrant` `modifier` should occur before all other modifiers
    2. Consider fixing the version to `0.8.17` across all the codebase

**[L‑01] `require()` should be used instead of `assert()`**

Prior to solidity version 0.8.0, hitting an assert consumes the **remainder of the transaction's available gas** rather than returning it, as `require()`/`revert()` do. `assert()` should be avoided even past solidity version 0.8.0 as its [documentation](https://docs.soliditylang.org/en/v0.8.14/control-structures.html#panic-via-assert-and-error-via-require) states that "The assert function creates an error of type Panic(uint256). ... Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix".

*There are 1 instances of this issue:*

```solidity
/tokens/TokenggAVAX.sol
83: assert(msg.sender == address(asset));
```

**[L-02] approve should be replaced with safeApprove or safeIncreaseAllowance() / safeDecreaseAllowance()**

approve is subject to a known front-running attack. Consider using safeApprove() or safeIncreaseAllowance() or safeDecreaseAllowance() instead

```solidity
/Staking.sol
342: ggp.approve(address(vault), amount);
```

**[L-03] _safeMint() should be used rather than _mint() wherever possible**

Calling mint this way does not ensure that the receiver of the token is able to accept them. _safeMint() should be used with reentrancy guards as a guard to protect the user as it checks to see if a user can properly accept a token and reverts otherwise.

```solidity
/ERC4626Upgradeable.sol
49: _mint(receiver, shares);
62: _mint(receiver, shares);
```

**[N‑01] The `nonReentrant` `modifier` should occur before all other modifiers**

This is a best-practice to protect against reentrancy in other modifiers

*There are 2 instances of this issue:[Vault.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L61)*

```solidity
/Vault.sol

61: function withdrawAVAX(uint256 amount) external onlyRegisteredNetworkContract nonReentrant {
137: function withdrawToken(address withdrawalAddress,ERC20 tokenAddress,uint256 amount) external onlyRegisteredNetworkContract nonReentrant {
```

**[N-02] Consider fixing the version to `0.8.17` across all the codebase**

As different compiler versions have critical behavior specifics if the contracts get accidentally deployed using another compiler version compared to the one they were tested with, various types of undesired behavior can be introduced.

```solidity
/ERC20Upgradeable.sol
2: pragma solidity >=0.8.0;

/ERC4626Upgradeable.sol
2: pragma solidity >=0.8.0;
```

Consider fixing the version to `0.8.17` across all the codebase, as Flywheel contracts has it fixed to that one.