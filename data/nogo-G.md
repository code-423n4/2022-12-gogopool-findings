# Gas optimizations

## [G-01] Prefer private over public for attributes not used in external calls

* [src/contract/MinipoolManager.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol): 78-79

```
ERC20 public immutable ggp;
TokenggAVAX public immutable ggAVAX;
```

## [G-02] Avoid a for loop by using the interest calculation formula

* [src/contract/RewardsPool.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L74): 74

```
for (uint256 i = 0; i < inflationIntervalsElapsed; i++) {
  newTotalSupply = newTotalSupply.mulWadDown(inflationRate);
}
```

can be replaced by the interest rate formula saving gas and increasing readability `amount * (1 + rate) ^ time`

## [G-03] Using > 0 costs more gas than != 0 when used on a uint in a require() statement

* [src/contract/ClaimNodeOp.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol): 103-109

```
if (restakeAmt > 0) {
			vault.withdrawToken(address(this), ggp, restakeAmt);
			ggp.approve(address(staking), restakeAmt);
			staking.restakeGGP(msg.sender, restakeAmt);
		}

if (claimAmt > 0) {
  vault.withdrawToken(msg.sender, ggp, claimAmt);
}
```

* [src/contract/RewardsPool.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol): 142-152
* [src/contract/tokens/TokenggAXAX.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAXAX.sol): 59

## [G-04] Functions guaranteed to revert when called by normal users can be marked payable

* [src/contract/ProtocolDAO.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol): 190-209

## [G-04] Using smaller type than uint256 introduces more EVM operations

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

* [src/contract/BaseAbstract.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol): 20

## [G-05] Using bool for storage brings overhead

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.
Use uint256(1) and uint256(2) for true/false instead

* [src/contract/Storage.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol): 16

## [G-06] Don't compare boolean expressions to boolean literals

* [src/contract/Storage.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol): 29

```
booleanStorage[keccak256(abi.encodePacked("contract.exists", msg.sender))] == false
```

## [G-07] Use assembly for address 0 check

```
assembly {
 if iszero(_addr) {
  mstore(0x00, "zero address")
  revert(0x00, 0x20)
 }
}
```

* [src/contract/BaseAbstract.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol): 75
* [src/contract/MinipoolManager.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol): 204
* [src/contract/MultiSigManager.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultiSigManager.sol): 112
