QA1: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L73
Instead of passing ``asset``as an argument, it is better to define it as a constant since the address for the WAVAX contract can be predetermined. 

QA2. https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L2
Lock all files with the most recent version of Solidity  0.8.17.

QA3: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L207-L209
The check of if-paused for ``TokenggAVAX`` should be done for the following important functions as well: 
``depositAVAX()``, ``withdrawAVAX()``, ``redeemAVAX()``, ``depositFromStaking()``, and ``withdrawForStaking()``. 

