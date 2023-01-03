
1. Use of unsafe approve()
The _stakeGGP function calls an ERC20 approve() https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L342


2. Ignored return value
MinipoolManager.onlyOwner() return value is ignored in cancelMinipool()

3. ERC4626Upgradeable does not conform to EIP-4626
** convertToShares() is expected to return an output with name shares, however the function in the contract returns output with name supply.
** convertToAssets() is expected to return an output with name assets, however the function in the contract returns output with name supply.
** maxDeposit() is expected to take an input address with name receiver and return an output with name maxAssets.
** previewMint()  is expected to return an output with name assets, however the function in the contract returns output with name supply.
** previewDeposit() is expected to return an output with name shares, however the function in the contract returns unnamed uint256 output.