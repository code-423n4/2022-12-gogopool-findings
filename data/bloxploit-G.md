# Empty blocks should be made to emit something


## Functions 

_authorizeUpgrade in [TokenggAVAX.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol) [Line 255]

beforeWithdraw in [ERC4626Upgradeable.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol) [Line 176]

afterDeposit in [ERC4626Upgradeable.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol) [Line 178]


# abi.encode() should be replaced with abi.encodepacked() wherever possible

abi encode() is comparatively less efficient than abi.encodepacked()

## Functions

permit [ERC20Upgradeable.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol) [Line 138]

computeDomainSeparator [ERC20Upgradeable.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol) [Line 169]


# Using Custom Error Function instead of revert(), require()

Custom Error Function saves around 50 gas. It is available in Solidity >= 0.8.4

## Functions

permit [ERC20Upgradeable.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol) [Line 127,154]




 