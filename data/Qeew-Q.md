1. ERC4626 Implementation Standard Inconsistency

According to the standard, the totalAssets() function should include fees charged on assets in the Vault. However, the current implementation includes these fees in the calculation of the asset <=> share rate, which lowers the share rate artificially. It is uncertain whether the standard intends for fees to be excluded from the totalAssets() function or if the implementation should remove fees before using totalAssets() in conversion calculations.


Relevant code


	function totalAssets() public view virtual returns (uint256);

	function convertToShares(uint256 assets) public view virtual returns (uint256) {
		uint256 supply = totalSupply; // Saves an extra SLOAD if totalSupply is non-zero.

		return supply == 0 ? assets : assets.mulDivDown(supply, totalAssets());
	}

	function convertToAssets(uint256 shares) public view virtual returns (uint256) {
		uint256 supply = totalSupply; // Saves an extra SLOAD if totalSupply is non-zero.

		return supply == 0 ? shares : shares.mulDivDown(totalAssets(), supply);
	}

	function previewDeposit(uint256 assets) public view virtual returns (uint256) {
		return convertToShares(assets);
	}

	function previewMint(uint256 shares) public view virtual returns (uint256) {
		uint256 supply = totalSupply; // Saves an extra SLOAD if totalSupply is non-zero.

		return supply == 0 ? shares : shares.mulDivUp(totalAssets(), supply);
	}

	function previewWithdraw(uint256 assets) public view virtual returns (uint256) {
		uint256 supply = totalSupply; // Saves an extra SLOAD if totalSupply is non-zero.

		return supply == 0 ? assets : assets.mulDivUp(supply, totalAssets());
	}

	function previewRedeem(uint256 shares) public view virtual returns (uint256) {
		return convertToAssets(shares);
	}


2.   Inconsistent data type could result in overflow

 Relevant code

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L128



