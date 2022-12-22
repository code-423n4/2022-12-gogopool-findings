QA1: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L73
Instead of passing ``asset``as an argument, it is better to define it as a constant since the address for the WAVAX contract can be predetermined. 

