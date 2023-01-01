https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Oracle.sol#L39 
```
	function getGGPPriceInAVAXFromOneInch() external view returns (uint256 price, uint256 timestamp) {
		TokenGGP ggp = TokenGGP(getContractAddress("TokenGGP"));
		IOneInch oneinch = IOneInch(getAddress(keccak256("Oracle.OneInch")));
		price = oneinch.getRateToEth(ggp, false);//@audit if price == 0, we need to revert.
		timestamp = block.timestamp;
	}
```
we need to check the price when we return the price.
if price == 0, we need to revert like line49.
because the price should not be 0.
