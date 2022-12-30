## 01 Require() should be used instead of assert()

Assert should not be used except for tests, `require` should be used

Prior to Solidity 0.8.0, pressing a confirm consumes the remainder of the process’s available gas instead of returning it, as request()/revert() did.

Assertion() should be avoided even after solidity version 0.8.0, because its documentation states “The Assert function generates an error of type Panic(uint256). Code that works properly should never Panic, even on invalid external input. If this happens, you need to fix it in your contract. there’s a mistake”.

_There is 1 instance of this issue_

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol

```
File: contract/tokens/TokenggAVAX.sol

83: assert(msg.sender == address(asset));
```

---------
## 02 Upgradeable contract is missing a gap[50] storage variable to allow for new storage variables in later versions

See [this](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps) link for a description of this storage variable. While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.

_There are 2 instances of this issue_

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol

```
File: contract/tokens/TokenggAVAX.sol

24: contract TokenggAVAX is Initializable, ERC4626Upgradeable, UUPSUpgradeable, BaseUpgradeable {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol

```
File: contract/tokens/upgradeable/ERC4626Upgradeable.sol

11: abstract contract ERC4626Upgradeable is Initializable, ERC20Upgradeable {
```

----------

## 03 Missing events for functions that change critical parameters

The afunctions that change critical parameters should emit events. Events allow capturing the changed parameters so that off-chain tools/interfaces can register such changes with timelocks that allow users to evaluate them and consider if they would like to engage/exit based on how they perceive the changes as affecting the trustworthiness of the protocol or profitability of the implemented financial services. The alternative of directly querying on-chain contract state for such changes is not considered practical for most users/usages.

Missing events and timelocks do not promote transparency and if such changes immediately affect users’ perception of fairness or trustworthiness, they could exit the protocol causing a reduction in liquidity which could negatively impact protocol TVL and reputation.

_There are 36 instances of this issue_

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol

```
File: contract/BaseAbstract.sol

135: function setAddress(bytes32 key, address value) internal {
139: function setBool(bytes32 key, bool value) internal {
143: function setBytes(bytes32 key, bytes memory value) internal {
147: function setBytes32(bytes32 key, bytes32 value) internal {
151: function setInt(bytes32 key, int256 value) internal {
155: function setUint(bytes32 key, uint256 value) internal {
159: function setString(bytes32 key, string memory value) internal {
163: function deleteAddress(bytes32 key) internal {
167: function deleteBool(bytes32 key) internal {
171: function deleteBytes(bytes32 key) internal {
175: function deleteBytes32(bytes32 key) internal {
179: function deleteInt(bytes32 key) internal {
183: function deleteUint(bytes32 key) internal {
187: function deleteString(bytes32 key) internal {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol

```
File: contract/ClaimNodeOp.sol

40: function setRewardsCycleTotal(uint256 amount) public onlySpecificRegisteredContract("RewardsPool", msg.sender) {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Oracle.sol

```
File: contract/ClaimNodeOp.sol

28: function setOneInch(address addr) external onlyGuardian {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol

```
File: contract/ProtocolDAO.sol

96: function setTotalGGPCirculatingSupply(uint256 amount) public onlySpecificRegisteredContract("RewardsPool", msg.sender) {
107: function setClaimingContractPct(string memory claimingContract, uint256 decimal) public onlyGuardian valueNotGreaterThanOne(decimal) {
156: function setExpectedAVAXRewardsRate(uint256 rate) public onlyMultisig valueNotGreaterThanOne(rate) {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol

```
File: contract/Staking.sol

204: function setRewardsStartTime(address stakerAddr, uint256 time) public onlyRegisteredNetworkContract {
248: function setLastRewardsCycleCompleted(address stakerAddr, uint256 cycleNumber) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol

```
File: contract/Storage.sol

41: function setGuardian(address newAddress) external {
104: function setAddress(bytes32 key, address value) external onlyRegisteredNetworkContract
108: function setBool(bytes32 key, bool value) external onlyRegisteredNetworkContract {
112: function setBytes(bytes32 key, bytes calldata value) external onlyRegisteredNetworkContract {
116: function setBytes32(bytes32 key, bytes32 value) external onlyRegisteredNetworkContract {
120: function setInt(bytes32 key, int256 value) external onlyRegisteredNetworkContract {
124: function setString(bytes32 key, string calldata value) external onlyRegisteredNetworkContract {
128: function setUint(bytes32 key, uint256 value) external onlyRegisteredNetworkContract {
136: function deleteAddress(bytes32 key) external onlyRegisteredNetworkContract {
140: function deleteBool(bytes32 key) external onlyRegisteredNetworkContract 
144: function deleteBytes(bytes32 key) external onlyRegisteredNetworkContract {
148: function deleteBytes32(bytes32 key) external onlyRegisteredNetworkContract {
152: function deleteInt(bytes32 key) external onlyRegisteredNetworkContract {
156: function deleteString(bytes32 key) external onlyRegisteredNetworkContract {
160: function deleteUint(bytes32 key) external onlyRegisteredNetworkContract
```

### Recommended Mitigation Steps

Add events to all functions that change critical parameters.

--------
## 04 Open TODOS

Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

_There are 3 instances of this issue_

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol

```
File: contract/BaseAbstract.sol

6: // TODO remove this when dev is complete
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol

```
File: contract/MinipoolManager.sol

412: // TODO Revisit this logic if we ever allow unequal matched funds
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol

```
File: contract/Staking.sol

203: // TODO cant use onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) since we also call from increaseMinipoolCount. Wat do?
```

-----

## 05 Include return parameters in natspec comment

It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation. In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.

[https://docs.soliditylang.org/en/v0.8.15/natspec-format.html](https://docs.soliditylang.org/en/v0.8.15/natspec-format.html)

If Return parameters are declared, you must prefix them with `/// @return`.

_There are 24 instances of this issue_

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol

```
File: contract/BaseAbstract.sol

90: function getContractAddress(string memory contractName) internal view returns (address) {
99: function getContractName(address contractAddress) internal view returns (string memory) {
107: function getAddress(bytes32 key) internal view returns (address) {
111: function getBool(bytes32 key) internal view returns (bool) {
115: function getBytes(bytes32 key) internal view returns (bytes memory) {
119: function getBytes32(bytes32 key) internal view returns (bytes32) {
123: function getInt(bytes32 key) internal view returns (int256) {
127: function getUint(bytes32 key) internal view returns (uint256) {
131: function getString(bytes32 key) internal view returns (string memory) {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol

```
File: contract/MinipoolManager.sol

541: function getTotalAVAXLiquidStakerAmt() public view returns (uint256) {
547: function calculateGGPSlashAmt(uint256 avaxRewardAmt) public view returns (uint256) {
572: function getMinipoolByNodeID(address nodeID) public view returns (Minipool memory mp) {
634: function getMinipoolCount() public view returns (uint256) {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol

```
File: contract/MultisigManager.sol

80: function requireNextActiveMultisig() external view returns (address)
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol

```
File: contract/ProtocolDAO.sol

86: function getRewardsCycleSeconds() public view returns (uint256) {
91: function getTotalGGPCirculatingSupply() public view returns (uint256) {
96: function setTotalGGPCirculatingSupply(uint256 amount) public onlySpecificRegisteredContract("RewardsPool", msg.sender) {
107: function setClaimingContractPct(string memory claimingContract, uint256 decimal) public onlyGuardian valueNotGreaterThanOne(decimal) {
130: function getMinipoolMinAVAXStakingAmt() public view returns (uint256) {
135: function getMinipoolNodeCommissionFeePct() public view returns (uint256) {
140: function getMinipoolMaxAVAXAssignment() public view returns (uint256) {
145: function getMinipoolMinAVAXAssignment() public view returns (uint256) {
150: function getMinipoolCancelMoratoriumSeconds() public view returns (uint256) {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol

```
File: contract/ClaimNodeOp.sol

35: function getRewardsCycleTotal() public view returns (uint256) {
```

### Recommended Mitigation Steps

Include return parameters in NatSpec comments

--------

## 06 Typos

_There are 9 instances of this issue_

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol

```
File: contract/Staking.sol

203: // TODO cant use onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) since we also call from increaseMinipoolCount. Wat do?
```

Typo: `wat` should be `what` instead

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol

```
File: contract/tokens/TokenggAVAX.sol

49: /// @notice the amount of rewards distributed in a the most recent cycle.
```

Typo: `distributed in a the` should be `distributed in the` instead

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol

```
File: contract/MinipoolManager.sol

411: // Calculate rewards splits (these will all be zero if no rewards were recvd)
415: // Node operators recv an additional commission fee
442: /// @notice Re-stake a minipool, compounding all rewards recvd
556: /// @return The approximate rewards the node should recieve from Avalanche for beign a validator
606: /// @return minipools in the protocol that adhear to the paramaters
```

Typo: In lines 411,  415, 442 it should be `received` or `receive` instead of `recvd` and `recv`
In line 556, it should be `being` instead of `beign`
In line 606, it should be `adhere` and `parameters` instead of `adhear` and `paramaters`

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol

```
File: contract/Vault.sol

81: /// @dev No funds actually move, just bookeeping
82: /// @param toContractName Name of the contract fucns are being transferred to
```

Typo: In line 81, it should be `bookkeeping` instead of `bookeeping`
In line 82, it should be `funds` instead of `fucns`

-------

## 07 Best practice is to prevent signature malleability

_There is 1 instance of this issue_

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol

```
File: contract/tokens/upgradeable/ERC20Upgradeable.sol

132-152: address recoveredAddress = ecrecover(
				keccak256(
					abi.encodePacked(
						"\x19\x01",
						DOMAIN_SEPARATOR(),
						keccak256(
							abi.encode(
								keccak256("Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)"),
								owner,
								spender,
								value,
								nonces[owner]++,
								deadline
							)
						)
					)
				),
				v,
				r,
				s
			);
```

------

## 08 Using both named returns and a return statement isn't necessary 

Removing unused named returns variables can reduce gas usage (MSTOREs/MLOADs) and improve code clarity. To save gas and improve code quality: consider using only one of those.

_There are 2 instances of this issue:_

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol

```
File: contract/MinipoolManager.sol

572: function getMinipoolByNodeID(address nodeID) public view returns (Minipool memory mp) {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol

```
File: contract/RewardsPool.sol

66: function getInflationAmt() public view returns (uint256 currentTotalSupply, uint256 newTotalSupply) {
```

----

## 09 Non-library or interface files should use fixed compiler version, not floating ones

In the contracts, floating pragmas should not be used. Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

_There are 2 instances of this issue:_

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol

```
File: contract/tokens/upgradeable/ERC20Upgradeable.sol

2: pragma solidity >=0.8.0;
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol

```
File: contract/tokens/upgradeable/ERC4626Upgradeable.sol

2: pragma solidity >=0.8.0;
```

-------

## 10 Use a more recent version of solidity

It's a best practice to use the latest compiler version.

The specified minimum compiler version is quite old. Older compilers might be susceptible to some bugs. We recommend changing the solidity version pragma to the latest version to enforce the use of an up to date compiler.

List of known compiler bugs and their severity can be found here: [https://etherscan.io/solcbuginfo](https://etherscan.io/solcbuginfo)

_There are 2 instances of this issue:_

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol

```
File: contract/tokens/upgradeable/ERC20Upgradeable.sol

2: pragma solidity >=0.8.0;
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol

```
File: contract/tokens/upgradeable/ERC4626Upgradeable.sol

2: pragma solidity >=0.8.0;
```

-------

