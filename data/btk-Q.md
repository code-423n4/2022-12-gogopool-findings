## [L-1] Add sanity checks to the duration

Add sanity checks to the [`duration`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L198) to prevent users from mistaknly set it to more than 365 days and less than 14 days.

Recommended Mitigation Steps:

```solidity
if (duration > 365 days || duration < 14 days) revert();
```

## [L-2] Users will still be able to burn tokens

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenGGP.sol#L9

`transferFrom` and `transfer` methods does not check if `_to` argument is the zero address, hence users can burn tokens by transferring them to address(0) without calling the burn function.

Recommended Mitigation Steps:

- add a non-zero address check for _to argument in transferFrom and transfer methods.

## [L-3] Lack of Zero Checks for new addresses
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
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L30

## [L-4] Misleading comments

- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L67

This line is saying that:
> this will prevent the multisig from completing validations. The minipool will need to be manually reassigned to a new multisig

This line is missliding because when the multisig is disabled, it will still be able to complete validations process and it can't be reassigned to a new multisig.

Update the line to: 
```solidity
// @dev Disable multisig means it would not get any new minipools
```

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
