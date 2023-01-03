## Gas Optimizations


| |Issue|Instances|
|-|:-|:-:|
| [GAS-1](#GAS-1) | FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED PAYABLE
| [GAS-2](#GAS-2) | USAGE OF UINTS/INTS SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD | 1 |
| [GAS-3](#GAS-3) | NOT USING THE NAMED RETURN VARIABLES WHEN A FUNCTION RETURNS, WASTES DEPLOYMENT GAS | 2 |
| [GAS-4](#GAS-4) | ++i/i++ should be unchecked{++i}/unchecked{++i} when it is not possible for them to overflow, as is the case when used in for- and while-loops | 7 |
| [GAS-5](#GAS-5) | Comparing to a constant (true or false) is a bit more expensive than directly checking the returned boolean value. | 3 |

###  [GAS-1] FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED PAYABLE

If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. 

The extra opcodes avoided are CALLVALUE(2), DUP1(3), ISZERO(3), PUSH2(3), JUMPI(10), PUSH1(3), DUP1(3), REVERT(0), JUMPDEST(1), POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost


*Instances*:
All the public and External Functions which uses onlyGuardian, guardianOrSpecificRegisteredContract, onlyMultisig, guardianOrRegisteredContract, onlyRegisteredNetworkContract or onlySpecificRegisteredContract modifier.

###  [GAS-2] USAGE OF UINTS/INTS SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD

Making lastSync as uint256 will help Saving gas as there will be no Overhead with the next 3 (rewardCycleLength, rewardsCycleEnd and lastRewardsAmt) getting packed in 1 storage slot.


*Instances (1)*:
```solidity
File: contract/tokens/TokenggAVAX.sol

	/// @notice the effective start of the current cycle
	uint32 public lastSync;

	/// @notice the maximum length of a rewards cycle
	uint32 public rewardsCycleLength;

	/// @notice the end of the current cycle. Will always be evenly divisible by `rewardsCycleLength`.
	uint32 public rewardsCycleEnd;

	/// @notice the amount of rewards distributed in a the most recent cycle.
	uint192 public lastRewardsAmt;

```
[Link to code](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L40-L50)

###  [GAS-3] NOT USING THE NAMED RETURN VARIABLES WHEN A FUNCTION RETURNS, WASTES DEPLOYMENT GAS

It is not necessary to have both a named return and a return statement.

*Instances (2)*:
```solidity
File: contract/MinipoolManager.sol

  function getMinipoolByNodeID(address nodeID) public view returns (Minipool memory mp) {
      int256 index = getIndexOf(nodeID);
      return getMinipool(index);
  }

```
[Link to code](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L572-L575)

```solidity
File: contract/RewardsPool.sol#L66-L78

66:    function getInflationAmt() public view returns (uint256 currentTotalSupply, uint256 

77:    return (currentTotalSupply, newTotalSupply);

```
[Link to code](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L66-L78)

### [GAS-4] ++i/i++ should be unchecked{++i}/unchecked{++i} when it is not possible for them to overflow, as is the case when used in for for-loops.

*Instances (7)*:
```solidity
File: contract/MinipoolManager.sol

619:       for (uint256 i = offset; i < max; i++) {

File: contract/MultisigManager.sol

84:        for (uint256 i = 0; i < total; i++) {

File: contract/Ocyticus.sol

61:        for (uint256 i = 0; i < count; i++) {

File: contract/RewardsPool.sol

74:        for (uint256 i = 0; i < inflationIntervalsElapsed; i++) {

215:       for (uint256 i = 0; i < count; i++) {

230:       for (uint256 i = 0; i < enabledMultisigs.length; i++) {

File: contract/Staking.sol

428:       for (uint256 i = offset; i < max; i++) {

```
[Link to code](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/)


### [GAS-5] Comparing to a constant (true or false) is more expensive than directly checking the returned boolean value.

I would recommend to use "!" operator ahead in the following if conditions instead of comparision.

*Instances (3)*:
```solidity
File: contract/BaseAbstraction.sol

25:    if (getBool(keccak256(abi.encodePacked("contract.exists", msg.sender))) == false) {

74:    if (enabled == false) {

File: contract/Storage.sol

29:    if (booleanStorage[keccak256(abi.encodePacked("contract.exists", msg.sender))] == false &&

```
[Link to code](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L572-L575)
