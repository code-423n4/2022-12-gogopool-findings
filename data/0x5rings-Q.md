Findings: Across the code base the emit function has been added in the middle of a function code. It is advised to move emits to the end of the function code block to ensure all variables are updated as final.

Example of one, (however many more instance):

Code: [https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L192-L193](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L192-L193)

Mitigation: Move emit to the end of the function block.

``` solidity

if (nopClaimContractAllotment > 0) {
			emit ClaimNodeOpRewardsTransfered(nopClaimContractAllotment);
			ClaimNodeOp nopClaim = ClaimNodeOp(getContractAddress("ClaimNodeOp"));
			nopClaim.setRewardsCycleTotal(nopClaimContractAllotment);
			vault.transferToken("ClaimNodeOp", ggp, nopClaimContractAllotment);
		}

// to 

if (nopClaimContractAllotment > 0) {
			ClaimNodeOp nopClaim = ClaimNodeOp(getContractAddress("ClaimNodeOp"));
			nopClaim.setRewardsCycleTotal(nopClaimContractAllotment);
			vault.transferToken("ClaimNodeOp", ggp, nopClaimContractAllotment);
			emit ClaimNodeOpRewardsTransfered(nopClaimContractAllotment);		
}

```
----

Findings: Emit Important functions:

Across the codebase important function are essential to be emitted for indexing solutions off-chain and transparency.

e.g. Below (however more function across the codebase)

Code: [https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L302-L303](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L302-L303)

On the event of a pause, Individual Multsig would required to be re-enabled manually via `resumeEverything`. Failure can lead to price lags in the oracle.

On the event of a pause protocol via `pauseEverything` all multisigs would be disabled. When the contract is resumed the protocol functions such as `setGGPPriceInAVAX` would fail to update leading to price lags / arbitration and in some cases degradation of token value due to outdated oracle. In this scenario a guardian enabled address would require to manually enable the previously registered multisigs.

Code: [https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Ocyticus.sol#L47-L48](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Ocyticus.sol#L47-L48)

Mitigation: Add emits to important setter functions, also add `indexed` within emit address

----

[**Recipient Address can be a contract that calls back leading to reentrancy**](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L302-L303)

Further, the recipient may be a smart contract that may not support interface ERC20, leading to revert if contract.

Code: [https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimProtocolDAO.sol#L32-L33](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimProtocolDAO.sol#L32-L33)
