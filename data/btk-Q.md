## [L-1] Add sanity checks to the `createMinipool()` function
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L196

A sanity checks should be added to the `createMinipool()` function to prevent malicious behavior or mistakes (which may force them to [cancel](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L273) the minipool and create new one) from users. 

Recommended Mitigation Steps:

```solidity
if (duration > 365 days || duration < 14 days) InvalidDuration();
```

```solidity
if (delegationFee > 1 ether || delegationFee < 0.1 ether) InvalidDelegationFee();
```

## [L-4] No storage gap for upgradeable contracts

## Impact
For ERC20Upgradeable and ERC4626Upgradeable, which are upgradeable abstract contracts, inheriting contracts may introduce new variables. In order to be able to add new variables to the upgradeable abstract contract without causing storage collisions, a storage gap should be added to the upgradeable abstract contract.

If no storage gap is added, when the upgradable abstract contract introduces new variables, it may override the variables in the inheriting contract which cause storage collisions.

## Proof of Concept
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L10
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol
- 
## Tools Used
Manual Review

## Recommended Mitigation Steps
Consider adding a storage gap at the end of the upgradeable abstract contract:
```solidity
uint256[50] private __gap;
```

## [L-4] Leftover tokens in the `MinipoolManager`

## Impact
The [`MinipoolManager`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L57) have no mechanism to rescue the trapped tokens, therefore tokens will be stuck in the contract.

## Proof of Concept
Due the nature of arithmetics and rounding errors :

```solidity
uint256 avaxLiquidStakerRewardAmt = avaxHalfRewards - avaxHalfRewards.mulWadDown(dao.getMinipoolNodeCommissionFeePct());

Line: https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L417
```

```solidity
return (avaxAmt.mulWadDown(rate) * duration) / 365 days;

Line: https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L560
```
Some tokens will be stuck out in the `MinipoolManager` contract, hence lost of funds since there is no mechanism to rescue them.

## Tools Used
Manual Review

## Recommended Mitigation Steps
Add a mechanism to rescue the the trapped tokens.

## [L-4] Misleading comments

- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L67

This line is saying that:
> this will prevent the multisig from completing validations. The minipool will need to be manually reassigned to a new multisig

This line is missliding because when the multisig is disabled, it will still be able to complete validations process and it can't be reassigned to a new multisig.

Update the line to: 
```solidity
// @dev Disable multisig means it would not get any new minipools
```

## [L-5] Users will still be able to burn tokens

- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenGGP.sol#L9

`transferFrom` and `transfer` methods does not check if `_to` argument is the zero address, hence users can burn tokens by transferring them to address(0) without calling the burn function.

Recommended Mitigation Steps:

- add a non-zero address check for _to argument in transferFrom and transfer methods.

## [NC-1] TYPOS

Error1 : fucns ( funds )
- Line : https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L82

Error2 : adhear ( adhere )
- Line : https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L419

Error3 : the the ( to the )
- Line : https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L309

Error4 : in a the most recent cycle ( in the most recent cycle )
- Line : https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L49

Error5 : adhear ( adhere )    /    paramaters ( parameters )
- Line : https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L606

## [NC-2] Open TODO

- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L6-L8
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L203
- https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L412

## [NC-3] Lack of Zero Checks for new addresses
Constructors don't have zero-checks, which could force a re-deployment, funds are not at risk in those cases.

- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Base.sol#L9
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L29
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimProtocolDAO.sol#L15
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L177
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L29
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L22
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Oracle.sol#L23
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L19
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L60
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L41
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#
