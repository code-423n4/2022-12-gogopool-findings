
Issue : ClaimNodeOp.sol and multiple other conrtracts are importing both Storage.sol and Base.sol. Base.sol is itself importing Storage.sol and BaseAbstract.sol. BaseAbstract is also importing Storage.sol. Thus the import of Storage.sol in ClaimNodeOp.sol and Base.sol is redundant.

Code : https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L9

Fix : Don't Import Storage.sol if already importing Base.sol. Remove  Import of Storage.sol from Base.sol as it's importing in it's parent BaseAbstract.sol.

