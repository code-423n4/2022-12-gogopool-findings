### Summary

Overall the codebase is quite readable, well organized. For the most part the codebase closley matched the documenation but there where a few mismatches that were mentioned below. Project structure is intuitive and simple to follow. Contract structure is well ogranized and makes good use of libraries and external contracts. Variable naming is mostly easily readble and intuitive. Comments were used rigourously and were beneficial and accurate in describing the object they were comenting on. Events are used in a benefical manner to track protocol outputs.
   
### Non Critical Issues   
#### [NC-01] Missing several properties from the Data Storage Schema Comments. 

Minipoolcount, reward cycles, ggp rewards, etc..

instances:1

File: Staking.sol

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L15-L27

### Low Quality Issues
#### [LQ-01] Consider adding Vault address to storage

Since Vault is also non-upgradable and this would save gas for contracts that call Vault since they don't have calcuate the mapping key hash to retreive the vault address on each call

instances:1

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseAbstract.sol#L18-L22


#### [LQ-02] Consider using dao.getMinCollateralizationRatio()

Consider using dao.getMinCollateralizationRatio() instead of const "Tenth" since the min colat ratio can be updated in protocol DAO

instances:1

File: Staking.sol
Line: 291
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L291

```javascript
	if (getCollateralizationRatio(stakerAddr) < TENTH)
```

#### [LQ-03] MinipoolStatusChanged event is not emitted

Minipool status updated to minipool.finished, MinipoolStatusEvent not emitted

instances:1

File: Staking.sol
Line: 287-303
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L287-L303

```javascript
	function withdrawMinipoolFunds(address nodeID) external nonReentrant {
		int256 minipoolIndex = requireValidMinipool(nodeID);
		address owner = onlyOwner(minipoolIndex);
		requireValidStateTransition(minipoolIndex, MinipoolStatus.Finished);
		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Finished));


		uint256 avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpAmt")));
		uint256 avaxNodeOpRewardAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpRewardAmt")));
		uint256 totalAvaxAmt = avaxNodeOpAmt + avaxNodeOpRewardAmt;


		Staking staking = Staking(getContractAddress("Staking"));
		staking.decreaseAVAXStake(owner, avaxNodeOpAmt);


		Vault vault = Vault(getContractAddress("Vault"));
		vault.withdrawAVAX(totalAvaxAmt);
		owner.safeTransferETH(totalAvaxAmt);
	}
```

#### [LQ-04]No checks to ensure delegation fee is withing acceptable brackets

No checks to ensure delegation fee is within acceptable brackets

instances:1

File: MinipoolManager.sol
Line: 196-269
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L196-L269
