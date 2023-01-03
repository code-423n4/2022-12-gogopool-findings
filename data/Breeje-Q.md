## QA Report

| |Issues|
|-|:-|
| [L-1](#L-1) | NO ZERO ADDRESS CHECK IN TRANSFERING OWNERSHIP OF GUARDIAN
| [L-2](#L-2) | Single Point of Failure
| [NC-1](#NC-1) | Possibility of Storage key collision
| [NC-2](#NC-2) | Reentrancy Gaurd on Critical functions
| [NC-3](#NC-3) | Value should be defined rather than using magic numbers

###  [L-1] NO ZERO ADDRESS CHECK IN TRANSFERING OWNERSHIP OF GUARDIAN

Having a Zero Address check is Important while transfering the Ownership of Guardian. A Zero Address smart contract as guardian can lead to dangerous implecations.

I would suggest to Use assembly to check for address(0)

*Instances (1)*:
```solidity
File: contract/Storage.sol

  function setGuardian(address newAddress) external {
		// Check tx comes from current guardian
		if (msg.sender != guardian) {
			revert MustBeGuardian();
		}
		// Store new address awaiting confirmation
		newGuardian = newAddress;
	}

```
[Link to code](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L41-L48)

### [L-2] Single Point of Failure of Protocol

If an attacker can register a single malicious contract, It has all the controls of the network to do all Damage possible. It can register anyone as Multisig through Storage Access, it can set GGP price, it can change all the Minipool, Staking and RewardsPool data in its favour or can Re-initialize Contract like: `ProtocolDAO.sol` or `RewardsPool.sol`.

###  [NC-1] Possibility of Storage key collision

In `Storage.sol` contract, The keys used are defined throughout the codebase, with some keys used across multiple contracts, others used across several but set only by one, and others used privately by a single contract.

There is some risk that maintainers accidentally reuse a key to store an unrelated value, resulting in a clash where storage values important to another contract are overwritten.
While no bugs have been identified in the current codebase associated with this issue, this can create issue in long run.

### [NC-2] Reentrancy Gaurd on critical functions

It is recommended to use Reentrancy Guard on Functions that can Potentially be Exploited by Reentrancy.

*Instances (2)*:
```solidity
File: contract/tokens/TokenggAVAX.sol

166:    function depositAVAX() public payable returns (uint256 shares) {

```
[Link to code](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L166)

```solidity
File: contract/ClaimNodeOp.sol

89:    function claimAndRestake(uint256 claimAmt) external

```
[Link to code](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L89)

### [#NC-3] Value should be defined rather than using magic numbers

*Instances (1)*:
```solidity
File: contract/ProtocolDAO.sol

41:    setUint(keccak256("ProtocolDAO.InflationIntervalRate"), 1000133680617113500);

```
[Link to code](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L41)