- in Ocyticus constructor, the inherited Base contract, lacks assigning 'version' variable.
```
contract Ocyticus is Base {
	...
	constructor(Storage storageAddress) Base(storageAddress) {
		// assign 1 to 'version' variable
		defenders[msg.sender] = true;
	}
```
- remember to remove ProtocolDAO.setExpectedAVAXRewardsRate() function in production.
```
File: ProtocolDAO.sol

156: 	function setExpectedAVAXRewardsRate(uint256 rate) public onlyMultisig valueNotGreaterThanOne(rate) {
157:		setUint(keccak256("ProtocolDAO.ExpectedAVAXRewardsRate"), rate);
158:	}

```
- check contract addresses inputs are contracts by checking code size is greater than zero.
```
File: ProtocolDAO.sol
190: 	function registerContract(address addr, string memory name) public onlyGuardian {
198:	        function unregisterContract(address addr) public onlyGuardian {
File: Base.sol
10:	         constructor(Storage _gogoStorageAddress) {
File: Oracle.sol
28:	function setOneInch(address addr) external onlyGuardian {
```

- use delete instead of set function in MiniPoolManager.resetMiniPoolData() to save on gas fees and improve on readability.
```
File: MinipoolManager.sol
	function resetMinipoolData(int256 index) private {
		deleteBytes32(keccak256(abi.encodePacked("minipool.item", index, ".txID")));
		deleteUint(keccak256(abi.encodePacked("minipool.item", index, ".startTime")));
		deleteUint(keccak256(abi.encodePacked("minipool.item", index, ".endTime")));
		deleteUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxTotalRewardAmt")));
		deleteUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxNodeOpRewardAmt")));
		deleteUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxLiquidStakerRewardAmt")));
		deleteUint(keccak256(abi.encodePacked("minipool.item", index, ".ggpSlashAmt")));
		deleteBytes32(keccak256(abi.encodePacked("minipool.item", index, ".errorCode")));
	}
```