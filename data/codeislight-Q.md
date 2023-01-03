### 1 - Ocyticus constructor, the inherited Base contract, lacks assigning 'version' variable
```
contract Ocyticus is Base {
	...
	constructor(Storage storageAddress) Base(storageAddress) {
		// assign 1 to 'version' variable
		defenders[msg.sender] = true;
	}
```
### 2 - remember to remove ProtocolDAO.setExpectedAVAXRewardsRate() function in production.
```
File: ProtocolDAO.sol

156: 	function setExpectedAVAXRewardsRate(uint256 rate) public onlyMultisig valueNotGreaterThanOne(rate) {
157:		setUint(keccak256("ProtocolDAO.ExpectedAVAXRewardsRate"), rate);

```
### 3 - check contract addresses inputs are contracts by checking code size is greater than zero.
```
File: ProtocolDAO.sol
190: 	function registerContract(address addr, string memory name) public onlyGuardian {
198:	        function unregisterContract(address addr) public onlyGuardian {

File: Base.sol
10:	         constructor(Storage _gogoStorageAddress) {

File: Oracle.sol
28:	function setOneInch(address addr) external onlyGuardian {
```

### 4 - use delete instead of set function in MiniPoolManager.resetMiniPoolData() to save on gas fees and improve on readability.
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
### 5 - in MiniPoolManager.createMinipool() has payable and avaxAssignmentRequest parameters, which are both must be equal, therefore using only msg.value as a value instead of passing `avaxAssignmentRequest` the extra parameter would be sufficient and better UX.

```
File: MiniPoolManager.sol
196:	function createMinipool(
197:		address nodeID,
198:		uint256 duration,
199:		uint256 delegationFee,
200:		uint256 avaxAssignmentRequest
201:	) external payable whenNotPaused {
```
### 6 - creating a new MiniPool lacks checking duration validity

entering high values would lead to higher expected rewards in getExpectedAVAXRewardsAmt that could damage the slashing process, and also in the RialtoSimulator despite being out of scope, with the current implementation, it could lead to the rewards being amplified when called by processMinipoolEndWithRewards().

### 7 - creating a new MiniPool lacks checking delegationFee

it should have values max up to 1 ether <=> 10%, due to the latter, it could lead the node operators to set the fees at 100% and profit off of it all.