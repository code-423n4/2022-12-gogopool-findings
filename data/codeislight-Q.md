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


```
- add a function to return only price in Oracle contract to keep the code concise, so in Staking.sol line 296, the return value would only contain the price, would save on the cost of fetching timestamp and returning it while being unused.