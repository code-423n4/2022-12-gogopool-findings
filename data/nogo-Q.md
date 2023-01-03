# QA

## [L-01] `shares` is not used in `afterDeposit()`

```
afterDeposit(assets, shares);

function afterDeposit(
  uint256 amount,
  uint256 /* shares */
) internal override {
  totalReleasedAssets += amount;
}
```

## [L-02] duration input is not validated

* [src/contract/MinipoolManager.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol): 196-269

```
function createMinipool(
  address nodeID,
  uint256 duration,
  uint256 delegationFee,
  uint256 avaxAssignmentRequest
) external payable whenNotPaused {
```

## [L-03] Open TODOs

Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment,

* [src/contract/MinipoolManager.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol): 412
```
// TODO Revisit this logic if we ever allow unequal matched funds
```

## [L-04] Useless casting

* [src/contract/Vault.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L157: 415

```
ERC20 tokenContract = ERC20(tokenAddress);
```

## [N-01] Lines are too long

Usually, lines in source code are limited to 80 characters. Today’s screens are much larger so it’s reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length.
Reference:
https://docs.soliditylang.org/en/v0.8.10/style-guide.html#maximum-line-length

## [N-02] NATSPEC IS MISSING

NatSpec is missing for some functions.

* [src/contract/Storage.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L157


```
function setAddress(bytes32 key, address value) external onlyRegisteredNetworkContract {
  addressStorage[key] = value;
}

function setBool(bytes32 key, bool value) external onlyRegisteredNetworkContract {
  booleanStorage[key] = value;
}
```
  

