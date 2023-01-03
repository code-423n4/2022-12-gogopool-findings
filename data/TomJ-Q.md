## Table of Contents
### Low Risk Issues
- Missing Zero Address Check 
- Require should be used instead of Assert
- Avoid Using ecrecover
- Performing a Multiplication on the Result of a Division

&ensp;
## Low Risk Issues
### Missing Zero Address Check 

#### Issue
I recommend adding check of 0-address for input validation of critical address parameters.
Not doing so might lead to non-functional contract and have to redeploy the contract, when it is updated to 0-address accidentally.

#### PoC
Total of 2 instances found.
1. Oracle.sol:setOneInch()
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Oracle.sol#L28-L30
```solidity
	function setOneInch(address addr) external onlyGuardian {
		setAddress(keccak256("Oracle.OneInch"), addr);
	}
```

2. Storage.sol:setGuardian()
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L41-L48

#### Mitigation
Add 0-address check for above addresses.

&ensp;
### Require should be used instead of Assert

#### Issue
Solidity documents mention that properly functioning code should never reach a failing assert statement
and if this happens there is a bug in the contract which should be fixed.
Reference: https://docs.soliditylang.org/en/v0.8.15/control-structures.html#panic-via-assert-and-error-via-require

#### PoC
Total of 1 instance found.
```
./TokenggAVAX.sol:83:		assert(msg.sender == address(asset));
```

#### Mitigation
Replace assert by require.

&ensp;
### Avoid Using ecrecover

#### Issue
It is best practice to avoide using ecrecover since the ecrecover EVM opcode allows malleable signatures
and thus is vulnerable to reply attacks. Since the contract uses nonce, replay attack seems not possible here
but it is still recommended to use recover instead of ecrecover.

Reference: https://docs.soliditylang.org/en/v0.8.15/units-and-global-variables.html?highlight=ecrecover#mathematical-and-cryptographic-functions

#### PoC
Total of 1 instance found.
```
./ERC20Upgradeable.sol:132:			address recoveredAddress = ecrecover(
```

#### Mitigation
Use the recover function from OpenZeppelin‚Äôs ECDSA library.

&ensp;
### Performing a Multiplication on the Result of a Division

#### Issue
Solidity integer division might truncate. As a result, performing multiplication before division can sometimes avoid loss of precision.
Reference: https://github.com/crytic/slither/wiki/Detector-Documentation#divide-before-multiply

#### PoC
Total of 2 instances found.
```
./TokenggAVAX.sol:128:		uint256 unlockedRewards = (lastRewardsAmt_ * (block.timestamp - lastSync_)) / (rewardsCycleEnd_ - lastSync_);
./MinipoolManager.sol:560:		return (avaxAmt.mulWadDown(rate) * duration) / 365 days;
```

#### Mitigation
Consider ordering multiplication before division.

&ensp;