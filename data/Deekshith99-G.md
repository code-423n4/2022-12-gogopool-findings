## [G-1] DIVISION BY TWO SHOULD USE BIT SHIFTING
There is one instance of it & the permalink is at [MinipoolManager.sol#L413](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L413)
 <avaxTotalRewardAmt> / 2 is the same as   '<avaxTotalRewardAmt> >> 1 by this we can save some gas
here is the Ref- [link](https://gist.github.com/IllIllI000/ec0e4e6c4f52a6bca158f137a3afd4ff) (which was taken from code4rena reports)