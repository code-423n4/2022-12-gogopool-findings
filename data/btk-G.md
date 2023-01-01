## [GAS-1] ++i should be unchecked{ ++i; }

## Lines of code:

- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L619
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L84
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L74
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L215
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L230
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L61
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L428

```solidity
for ( uint256 i;  i < len; ) {

 // do something that doesn't change the value of i

 unchecked{ ++i;  }
}
```

In this example, the for loop post condition, i.e. `++i`  involves checked arithmetic, which is not required. This is because the value of `i` is always strictly less than length ```<= 2**256 - 1```. Therefore, the theoretical maximum value of `i` to enter the for-loop body is ```2**256 - 2```. This means that the `++i`  in the for loop can never overflow. Regardless, the overflow checks are performed by the compiler.

Unfortunately, the Solidity optimizer is not smart enough to detect this and remove the checks.

Gas savings: roughly speaking this can save 30-40 gas per loop iteration. For lengthy loops, this can be significant!

## [GAS-2] wrap ```addAllowedToken``` and ```removeAllowedToken``` into a single function

## Lines of code:

- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L204-L210

This will reduce the code size and deployment cost.

## Before:

```solidity
function addAllowedToken(address tokenAddress) external onlyGuardian {
	allowedTokens[tokenAddress] = true;
}

function removeAllowedToken(address tokenAddress) external onlyGuardian {
	allowedTokens[tokenAddress] = false;
}
```

| contracts/contract/Vault.sol:Vault contract |                 |       |        |       |         |
|---------------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                             | Deployment Size |       |        |       |         |
| 1200490                                     | 5972            |       |        |       |         |
| Function Name                               | min             | avg   | median | max   | # calls |
| addAllowedToken                             | 1336            | 21346 | 23570  | 23570 | 10      |
| removeAllowedToken                          | 3607            | 7007  | 7007   | 10407 | 2       |

## After:

```solidity
function setAllowedTokens(address tokenAddress, bool _allowed) external onlyGuardian {
    allowedTokens[tokenAddress] = _allowed;
}
```
| contracts/contract/Vault.sol:Vault contract |                 |       |        |       |         |
|---------------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                             | Deployment Size |       |        |       |         |
| 1158244                                     | 5761            |       |        |       |         |
| Function Name                               | min             | avg   | median | max   | # calls |
| setAllowedTokens                            | 1427            | 19059 | 23683  | 23683 | 12      |

## [GAS-3] use require instead of assert

## Lines of code:

- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L83

The bigger difference between the two keyword is that assert when condition is false tend to consume all gas remaining gas and reverts all the changes made. In reverse, require when the condition is false refund all the remaining gas fees we offered to pay beyond reverts all the changes.

Exactly for this last reason, require is recommended than assert.

For more [info](https://stackoverflow.com/questions/71502322/difference-between-assert-and-require#:~:text=Require%3A%20Similar%20to%20assert%2C%20this,back%20to%20the%20original%20state.).

## Before:

```solidity
receive() external payable {
    assert(msg.sender == address(asset));
}
```

| contracts/contract/tokens/TokenggAVAX.sol:TokenggAVAX contract |                 |        |        |        |         |
|----------------------------------------------------------------|-----------------|--------|--------|--------|---------|
| Deployment Cost                                                | Deployment Size |        |        |        |         |
| 2850662                                                        | 14408           |        |        |        |         |

## After:

```solidity
receive() external payable {
    require(msg.sender == address(asset));
}
```

| contracts/contract/tokens/TokenggAVAX.sol:TokenggAVAX contract |                 |        |        |        |         |
|----------------------------------------------------------------|-----------------|--------|--------|--------|---------|
| Deployment Cost                                                | Deployment Size |        |        |        |         |
| 2845654                                                        | 14383           |        |        |        |         |

## [GAS-4] DON’T COMPARE BOOLEAN EXPRESSIONS TO BOOLEAN LITERALS

## Lines of code:

- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L74
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L25
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L29

## Before:

```solidity
if (getBool(keccak256(abi.encodePacked("contract.exists", msg.sender))) == false) {
	revert InvalidOrOutdatedContract();
}
```

```solidity
if (enabled == false) {
	revert MustBeMultisig();
}
```

```solidity
if (booleanStorage[keccak256(abi.encodePacked("contract.exists", msg.sender))] == false && msg.sender != guardian) {
	revert InvalidOrOutdatedContract();
}
```

## After:

```solidity
if (!getBool(keccak256(abi.encodePacked("contract.exists", msg.sender)))) {
	revert InvalidOrOutdatedContract();
}
```

```solidity
if (!enabled) {
	revert MustBeMultisig();
}
```

```solidity
if (!booleanStorage[keccak256(abi.encodePacked("contract.exists", msg.sender))] && msg.sender != guardian) {
	revert InvalidOrOutdatedContract();
}
```

## [GAS-5]  Use checks as early as possible

## Lines of code:

- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L424
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L82

Use if as early as possible in the function because it returns only unused gas in the case of a failure. This means that if some logic before the if statement is gas-consuming, you will not get the gas for that logic if it fails.
