Function Reference: https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L66-#L78

# getInflationAmt() increase gas costs

## Description
The getInflationAmt() is used for calculating the new total supply of GGP tokens by repeatedly multiplying the current total supply by the inflation rate.

The function getInflationAmt() increase gas costs because performing a fixed point multiplication is more expensive in terms of gas compared to a regular multiplication operation. 

The reason is, the calculation involves complex arithmetic.

## Solution
Using exponentiation would be more efficient method for calculating the new total supply. It may reduce the gas cost of calling the getInflationAmt function.
