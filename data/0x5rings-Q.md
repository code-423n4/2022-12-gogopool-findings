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