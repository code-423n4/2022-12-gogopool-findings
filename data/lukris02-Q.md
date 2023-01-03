# QA Report for GoGoPool contest
## Overview
During the audit, 4 low and 12 non-critical issues were found.

№ | Title | Risk Rating  | Instance Count
--- | --- | --- | ---
L-1 | The ```recordStakingEnd()``` function does not match the documentation exactly | Low | 1
L-2 | ```getMinipools()``` function may unexpectedly revert | Low | 1
L-3 | ```getStakers()``` function may unexpectedly revert | Low | 1
L-4 | "Dirty hack" | Low | 3
NC-1 | Open TODOs | Non-Critical | 3
NC-2 | Order of Functions | Non-Critical | 13
NC-3 | Order of Layout | Non-Critical | 7
NC-4 | Empty function body | Non-Critical | 1
NC-5 | Typos in event names | Non-Critical | 3
NC-6 | Typos in comments | Non-Critical | 22
NC-7 | Missing NatSpec | Non-Critical | 16
NC-8 | Unused named return variables | Non-Critical | 2
NC-9 | Maximum line length exceeded | Non-Critical | 25
NC-10 | Use Checks Effects Interactions pattern | Non-Critical | 1
NC-11 | Code can be made more consistent | Non-Critical | 1
NC-12 | Move the function for clarity | Non-Critical | 1

## Low Risk Findings(4)
### L-1. The ```recordStakingEnd()``` function does not match the documentation exactly
##### Description
[Docs](https://github.com/code-423n4/2022-12-gogopool#node-operators) says:  
"Staking rewards are split between Node Operators and Liquid Stakers with **Node Operators getting 50% + a variable commission fee**, and **Liquid Stakers receiving the remainder**."  
But the [Lines 417-418](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L417-L418) are:
```
uint256 avaxLiquidStakerRewardAmt = avaxHalfRewards - avaxHalfRewards.mulWadDown(dao.getMinipoolNodeCommissionFeePct());
uint256 avaxNodeOpRewardAmt = avaxTotalRewardAmt - avaxLiquidStakerRewardAmt;
```
Thus, even if the rewards are calculated correctly, the mismatch between code and documentation can be misleading.

##### Instances
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L417-L418

##### Recommendation
Change the documentation to:  
"Staking rewards are split between Node Operators and Liquid Stakers with **Liquid Stakers** getting 50% **-** a variable commission fee, and **Node Operators** receiving the remainder."
**or**
Change the code to:
```
uint256 avaxNodeOpRewardAmt = avaxHalfRewards + avaxHalfRewards.mulWadDown(dao.getMinipoolNodeCommissionFeePct());
uint256 avaxLiquidStakerRewardAmt = avaxTotalRewardAmt - avaxNodeOpRewardAmt;
```
#
### L-2. ```getMinipools()``` function may unexpectedly revert
##### Description
In function [```getMinipools()```](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L607), if parameter ```offset``` is bigger than ```totalMinipools```, this line causes revert():
```
minipools = new Minipool[](max - offset);
```
It may be not obvious what the cause of the error is, so it is better to handle this case.

##### Instances
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L617

##### Recommendation
**Before L617** in MinipoolManager.sol add the check:
```
if (offset > max) { //or if (offset > totalMinipools)
    revert OffsetIsBiggerThanMinipoolsAmount();
}
```
#
### L-3. ```getStakers()``` function may unexpectedly revert
##### Description
In function [```getStakers()```](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L420), if parameter ```offset``` is bigger than ```totalStakers```, this line causes revert():
```
stakers = new Staker[](max - offset);
```
It may be not obvious what the cause of the error is, so it is better to handle this case.
##### Instances
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L426

##### Recommendation
**Before L426** in Staking.sol add the check:
```
if (offset > max) { //or if (offset > totalStakers)
    revert OffsetIsBiggerThanStakersAmount();
}
```
#

### L-4. "Dirty hack"
##### Description
The code contains a comment "// Dirty hack to cut unused elements off end of return value (from RP)", which gives the impression that something bad is being done and can cause mistrust.

##### Instances
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L626
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L223
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L433

##### Recommendation
Consider reformulating the comment.
#

## Non-Critical Risk Findings(12)
### NC-1. Open TODOs
##### Instances
- [```// TODO remove this when dev is complete```](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L6) 
- [```// TODO Revisit this logic if we ever allow unequal matched funds```](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L412) 
- [```// TODO cant use onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) since we also call from increaseMinipoolCount. Wat do?```](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L203)

##### Recommendation
Resolve issues.
#
### NC-2. Order of Functions
##### Description
According to [Style Guide](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-functions), ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier.  
Functions should be grouped according to their visibility and ordered:
1) constructor
2) receive function (if exists)
3) fallback function (if exists)
4) external
5) public
6) internal
7) private
##### Instances
Public functions should be placed after external:
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L35
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L40

External functions should be placed before public:
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L115
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L181
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L213
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L156
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L308
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L319
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L358
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L420

Private functions should be placed at the end of the contract:
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L115-L175

Constructor should be placed before all functions:
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L177

Internal functions should be placed after public:
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L110

##### Recommendation
Reorder functions where possible.
#
### NC-3. Order of Layout
##### Description
According to [Order of Layout](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-layout), inside each contract, library or interface, use the following order:
1) Type declarations
2) State variables
3) Events
4) Modifiers
5) Functions
##### Instances
Events should be placed after state variables:
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L25
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L75-L76
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L23-L25
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L40-L41
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L12
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L30-L35
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L36-L38

##### Recommendation
Place events after state variables.
#
### NC-4. Empty function body
##### Description
The function has no comments and no code.

##### Instances
- [```function receiveWithdrawalAVAX() external payable {}```](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L106) 

##### Recommendation
Consider adding a comment to clarify the purpose of the function.
#
### NC-5. Typos in event names
##### Instances
- [```event ClaimNodeOpRewardsTransfered(uint256 value);```](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L26) => ```Transferred```
- [```event ProtocolDAORewardsTransfered(uint256 value);```](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L27) => ```Transferred```
- [```event MultisigRewardsTransfered(uint256 value);```](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L28) => ```Transferred```

#
### NC-6. Typos in comments
##### Instances
- [```/// @dev Eligiblity: time in protocol (secs) > RewardsEligibilityMinSeconds. Rialto will call this.```](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L45) => ```Eligibility```
- [```minipool.item<index>.avaxTotalRewardAmt = Actual total avax rewards paid by avalanchego to the TSS P-chain addr```](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L47) => ```avalanche go```
- [```/// @param txID The ID of the transaction that successfully registered the node with Avalanche to become a validater```](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L347) => ```validator```
- [```/// @param avaxTotalRewardAmt The rewards the node recieved from Avalanche for being a validator```](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L383) => ```received```
- [```// Calculate rewards splits (these will all be zero if no rewards were recvd)```](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L411) => ```received```
- [```/// @notice Re-stake a minipool, compounding all rewards recvd```](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L442) => ```received```
- [```/// @return The approximate rewards the node should recieve from Avalanche for beign a validator```](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L556) => ```receive```
- [```/// @return The approximate rewards the node should recieve from Avalanche for beign a validator```](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L556) => ```being```
- [```/// @return minipools in the protocol that adhear to the paramaters```](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L606) => ```adhere```
- [```/// @return minipools in the protocol that adhear to the paramaters```](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L606) => ```parameters```
- [```/// @dev Multisigs will need to be enabled seperately, we dont know which ones to enable```](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L46) => ```separately```
- [```/// @notice The node commision fee for running the hardware for the minipool```](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L134) => ```commission```
- [```/// @notice Upgrade a contract by unregistering the existing address, and registring a new address and name```](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L205) => ```unregistering```
- [```/// @notice Upgrade a contract by unregistering the existing address, and registring a new address and name```](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L205) => ```registering```
- [```/// @return stakers in the protocol that adhear to the paramaters```](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L419) => ```adhere```
- [```/// @return stakers in the protocol that adhear to the paramaters```](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L419) => ```parameters```
- [```// Emit the withdrawl event for that contract```](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L68) => ```withdrawn```
- [```/// @dev No funds actually move, just bookeeping```](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L81) => ```bookkeeping```
- [```/// @param toContractName Name of the contract fucns are being transferred to```](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L82) => ```fucns?```
- [```/// @param withdrawalAddress Address that will recieve the token```](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L134) => ```receive```
- [```// Get the toke ERC20 instance```](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L156) => ```token```
- [```// The constructor is exectued only when creating implementation contract```](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L67) => ```executed```

#
### NC-7. Missing NatSpec
##### Description
NatSpec is missing for 16 functions in 2 contracts.
##### Instances 
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L204
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L208

and 14 out of 18 functions in TokenggAVAX.sol
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol

##### Recommendation
Add NatSpec for all functions.
#
### NC-8. Unused named return variables
##### Description
Both named return variable(s) and return statement are used.
##### Instances
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L572-L574
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L66-L77

##### Recommendation
To improve clarity use only named return variables.  
For example, change:
```
function functionName() returns (uint id) {
    return x;
```
to
```
function functionName() returns (uint id) {
    id = x;
```
#
### NC-9. Maximum line length exceeded
##### Description
According to [Style Guide](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#maximum-line-length), maximum suggested line length is 120 characters.
##### Instances 
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L73
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L53
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L294
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L317
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L329
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L371
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L399
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L417
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L421
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L490
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L33
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L41
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L96
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L107
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L174
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L110 
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L117
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L133
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L140
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L224
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L231
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L248
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L99
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L142
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L21 

##### Recommendation
Make the lines shorter.
#
### NC-10. Use Checks Effects Interactions pattern
##### Description
It is best practice to use [this pattern](https://fravoll.github.io/solidity-patterns/checks_effects_interactions.html).
##### Instances
[Link](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L127-L130):
```
		// Send tokens to this address now, safeTransfer will revert if it fails
		tokenContract.safeTransferFrom(msg.sender, address(this), amount);
		// Update balances
		tokenBalances[contractKey] = tokenBalances[contractKey] + amount;
```

##### Recommendation
Swap these lines of code
#
### NC-11. Code can be made more consistent
##### Description
There are two functions that behave similarly, and in a similar part of the code, one uses ```getUint(keccak256("minipool.count"));``` , and the other one calls the function ``` getStakerCount();```.

##### Instances
function getMinipools:
[```uint256 totalMinipools = getUint(keccak256("minipool.count"));```](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L612)

function getStakers:
[```uint256 totalStakers = getStakerCount();```](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L421)

##### Recommendation
To make code more consistent and clear:
1) Change *MinipoolManager.sol#L612* to ```uint256 totalMinipools = getMinipoolCount();```
**or**
2) Change *Staking.sol#L421* to ```uint256 totalStakers = getUint(keccak256("staker.count"));```
#
### NC-12. Move the function for clarity
##### Description
For clarity, it is better to place ```function getMinipoolCount()``` before ```function getMinipools()```.

##### Instances
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L634
- https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L607