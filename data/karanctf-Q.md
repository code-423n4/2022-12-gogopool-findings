## Use `if` statments in order to corresponding vairables in RewardsPool.sol to improve redablity
In below code the varible declaration order is 
- multisigClaimContractAllotment
- nopClaimContractAllotment
- daoClaimContractAllotment
#### And if statments order is 
- daoClaimContractAllotment
- multisigClaimContractAllotment
- nopClaimContractAllotment


```solidity
		contracts/contract/RewardsPool.sol:171:		uint256 multisigClaimContractAllotment = getClaimingContractDistribution("ClaimMultisig");
		uint256 nopClaimContractAllotment = getClaimingContractDistribution("ClaimNodeOp");
		uint256 daoClaimContractAllotment = getClaimingContractDistribution("ClaimProtocolDAO");
		if (daoClaimContractAllotment + nopClaimContractAllotment + multisigClaimContractAllotment > getRewardsCycleTotalAmt()) {
			revert IncorrectRewardsDistribution();
		}

		TokenGGP ggp = TokenGGP(getContractAddress("TokenGGP"));
		Vault vault = Vault(getContractAddress("Vault"));

		if (daoClaimContractAllotment > 0) {
			emit ProtocolDAORewardsTransfered(daoClaimContractAllotment);
			vault.transferToken("ClaimProtocolDAO", ggp, daoClaimContractAllotment);
		}

		if (multisigClaimContractAllotment > 0) {
			emit MultisigRewardsTransfered(multisigClaimContractAllotment);
			distributeMultisigAllotment(multisigClaimContractAllotment, vault, ggp);
		}

		if (nopClaimContractAllotment > 0) {
			emit ClaimNodeOpRewardsTransfered(nopClaimContractAllotment);
			ClaimNodeOp nopClaim = ClaimNodeOp(getContractAddress("ClaimNodeOp"));
			nopClaim.setRewardsCycleTotal(nopClaimContractAllotment);
			vault.transferToken("ClaimNodeOp", ggp, nopClaimContractAllotment);
		}
		
```
change above code to 
```bash
11,14d10
< 		if (daoClaimContractAllotment > 0) {
< 			emit ProtocolDAORewardsTransfered(daoClaimContractAllotment);
< 			vault.transferToken("ClaimProtocolDAO", ggp, daoClaimContractAllotment);
< 		}
26a23,29
> 		if (daoClaimContractAllotment > 0) {
> 			emit ProtocolDAORewardsTransfered(daoClaimContractAllotment);
> 			vault.transferToken("ClaimProtocolDAO", ggp, daoClaimContractAllotment);
> 		}
>
> 		
>
```