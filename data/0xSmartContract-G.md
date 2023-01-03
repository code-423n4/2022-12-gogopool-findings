### Gas Optimizations List
| Number | Optimization Details | Context |
|:--:|:-------| :-----:|
| [G-01] | Remove the `initializer` modifier| 3 |
| [G-02] |Gas optimization by changing the emit order | 3 |
| [G-03] | Avoid contract existence checks by using solidity version 0.8.10 or later | 4 |
| [G-04] | Compute known value off-chain  | 2 |
| [G-05] | keccak256() should be saved to an immutable variable, and the variable used instead| 39|
| [G-06] | Empty blocks should be removed or emit something  | 4 |
| [G-07] | Use require instead of assert  | 1 |
| [G-08] | Shifting cheaper than division  | 2 |
| [G-09] |Use a more recent version of solidity  | 2 |
| [G-10] | Upgrade Solidity's optimizer |  |
| [G-11] | Add ``unchecked {}`` for subtractions where the operands cannot underflow because of a previous ``require`` or ``if`` statement|6|
| [G-12] | Use ``assembly`` to write _address storage values_   |6 |
| [G-13] |Sort Solidity operations using short-circuit mode |6  |
| [G-14] | Don’t compare boolean expressions to boolean literals|3  |
| [G-15] | Use a different pattern when using for loops | 7 |
| [G-16] | Setting the _constructor_ to `payable` |14 |
| [G-17] | Functions guaranteed to revert_ when callled by normal users can be marked `payable`  | 64 |
| [G-18] | Optimize names to save gas |All contracts  |
| [G-19] | Use nested if and, avoid multiple check combinations| 2 |
| [G-20] | Use ``double require`` instead of using ``&&``| 1 |

Total 20 issues

### [G-01] Remove the `initializer` modifier

if we can just ensure that the `initialize()` function could only be called from within the constructor, we shouldn't need to worry about it getting called again. 

```solidity
3 results - 3 files

contracts/contract/ProtocolDAO.sol:
  23: 	function initialize() external onlyGuardian {

contracts/contract/RewardsPool.sol:
  34: 	function initialize() external onlyGuardian {
 
contracts/contract/tokens/TokenggAVAX.sol:
  72: 	function initialize(Storage storageAddress, ERC20 asset) public initializer {

```

In the EVM, the constructor's job is actually to return the bytecode that will live at the contract's address. So, while inside a constructor, your address `(address(this))` will be the deployment address, but there will be no bytecode at that address! So if we check `address(this).code.length` before the constructor has finished, even from within a delegatecall, we will get 0. So now let's update our `initialize()` function to only run if we are inside a constructor:

```diff

contracts/contract/ProtocolDAO.sol:
23:    function initialize() external onlyGuardian {
+            require(address(this).code.length == 0, 'not in constructor');

```

Now the Proxy contract's constructor can still delegatecall initialize(), but if anyone attempts to call it again (after deployment) through the Proxy instance, or tries to call it directly on the above instance, it will revert because address(this).code.length will be nonzero. 

Also, because we no longer need to write to any state to track whether initialize() has been called, we can avoid the 20k storage gas cost. In fact, the cost for checking our own code size is only 2 gas, which means we have a 10,000x gas savings over the standard version. Pretty neat!
### [G-02] Gas optimization by changing the emit order

3 results - 2 files

- Moving the 'emit' to the end of the transaction provides gas optimization in order to avoid cost in case of 'revert' of the transaction.

```diff
contracts/contract/tokens/TokenggAVAX.sol#L199
  191 	function redeemAVAX(uint256 shares) public returns (uint256 assets) {
  196 	    beforeWithdraw(assets, shares);
  197 	    _burn(msg.sender, shares);
- 199:      emit Withdraw(msg.sender, msg.sender, msg.sender, assets, shares);
  201: 	    IWAVAX(address(asset)).withdraw(assets);
  202: 	    msg.sender.safeTransferETH(assets);
+ 199: 	    emit Withdraw(msg.sender, msg.sender, msg.sender, assets, shares);
  203: 	}
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L199

```solidity
contracts/contract/tokens/TokenggAVAX.sol:
  173: 		emit Deposit(msg.sender, msg.sender, assets, shares);
  174 
  175 		IWAVAX(address(asset)).deposit{value: assets}();
  176 		_mint(msg.sender, shares);
  177 		afterDeposit(assets, shares);
  178 	}
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L173

- Changing the 'reentrancy' versus 'emit' order below provides gas optimization.

```diff
contracts/contract/MinipoolManager.sol:
  324 	function claimAndInitiateStaking(address nodeID) external {
  335 	    setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Launched));
- 336: 	    emit MinipoolStatusChanged(nodeID, MinipoolStatus.Launched);
  338 	    Vault vault = Vault(getContractAddress("Vault"));
  339 	    vault.withdrawAVAX(avaxNodeOpAmt);
  341 	    uint256 totalAvaxAmt = avaxNodeOpAmt + avaxLiquidStakerAmt;
+ 336: 	    emit MinipoolStatusChanged(nodeID, MinipoolStatus.Launched);
  342 	    msg.sender.safeTransferETH(totalAvaxAmt);
  344 	}
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L336
### [G-03] Avoid contract existence checks by using solidity version 0.8.10 or later

Prior to 0.8.10 the compiler inserted extra code, including EXTCODESIZE (100 gas), to check for contract existence for external calls. In more recent solidity versions, the compiler will not insert these checks if the external call has a return value

```solidity
4 results - 1 file

contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol:
   47: 	asset.safeTransferFrom(msg.sender, address(this), assets);
   60: 	asset.safeTransferFrom(msg.sender, address(this), assets);
   88: 	asset.safeTransfer(receiver, assets);
  111: 	asset.safeTransfer(receiver, assets);

```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L47
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L60
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L88
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L111


### [G-04] Compute known value off-chain

If it is known which data to hash, no more computational power consumption is needed to hash using keccak256, it causes 2 times more gas consumption.

2 results - 1 file

```solidity
contracts\contract\tokens\TokenggAVAX.sol:
  206 	function maxWithdraw(address _owner) public view override returns (uint256) {
  207: 		if (getBool(keccak256(abi.encodePacked("contract.paused", "TokenggAVAX")))) {
  208 			return 0;
  209 		}
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L206-L207

```solidity
  216 	function maxRedeem(address _owner) public view override returns (uint256) {
  217: 		if (getBool(keccak256(abi.encodePacked("contract.paused", "TokenggAVAX")))) {
  218 			return 0;
  219 		}
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L216-L217


**Recommendation Code:**

```diff
contracts\contract\tokens\TokenggAVAX.sol#L207

// keccak256(abi.encodePacked("contract.paused", "TokenggAVAX"))
+ bytes32 internal constant CONTRACT_PAUSED = 0xd748943df119de1ba522f3631e2275b6c0cf7f08d3c179b836ec594b1eaaea39; 

  contracts\contract\tokens\TokenggAVAX.sol:
  206 	function maxWithdraw(address _owner) public view override returns (uint256) {
- 207: 		if (getBool(keccak256(abi.encodePacked("contract.paused", "TokenggAVAX")))) {
+ 207: 		if (CONTRACT_PAUSED) {
  208 			return 0;
  209 		}
```

### [G-05] keccak256() should be saved to an immutable variable, and the variable used instead

```solidity
39 results - 7 files

contracts/contract/ClaimNodeOp.sol:
  36: 		return getUint(keccak256("NOPClaim.RewardsCycleTotal"));

contracts/contract/MinipoolManager.sol:
  248: 			minipoolIndex = int256(getUint(keccak256("minipool.count")));
  252: 			addUint(keccak256("minipool.count"), 1);
  333: 		addUint(keccak256("MinipoolManager.TotalAVAXLiquidStakerAmt"), avaxLiquidStakerAmt);
  433: 		subUint(keccak256("MinipoolManager.TotalAVAXLiquidStakerAmt"), avaxLiquidStakerAmt);
  512: 		subUint(keccak256("MinipoolManager.TotalAVAXLiquidStakerAmt"), avaxLiquidStakerAmt);
  542: 		return getUint(keccak256("MinipoolManager.TotalAVAXLiquidStakerAmt"));
  618: 		uint256 totalMinipools = getUint(keccak256("minipool.count"));
  641: 		return getUint(keccak256("minipool.count"));

contracts/contract/MultisigManager.sol:
   43: 		uint256 index = getUint(keccak256("multisig.count"));
   52: 		addUint(keccak256("multisig.count"), 1);
   86: 		uint256 total = getUint(keccak256("multisig.count"));
  111: 		return getUint(keccak256("multisig.count"));

contracts/contract/Oracle.sol:
  38: 		IOneInch oneinch = IOneInch(getAddress(keccak256("Oracle.OneInch")));
  47: 		price = getUint(keccak256("Oracle.GGPPriceInAVAX"));
  51: 		timestamp = getUint(keccak256("Oracle.GGPTimestamp"));

contracts/contract/ProtocolDAO.sol:
   82: 		return getUint(keccak256("ProtocolDAO.RewardsEligibilityMinSeconds"));
   87: 		return getUint(keccak256("ProtocolDAO.RewardsCycleSeconds"));
   92: 		return getUint(keccak256("ProtocolDAO.TotalGGPCirculatingSupply"));
  124: 		return getUint(keccak256("ProtocolDAO.InflationIntervalSeconds"));
  131: 		return getUint(keccak256("ProtocolDAO.MinipoolMinAVAXStakingAmt"));
  136: 		return getUint(keccak256("ProtocolDAO.MinipoolNodeCommissionFeePct"));
  141: 		return getUint(keccak256("ProtocolDAO.MinipoolMaxAVAXAssignment"));
  146: 		return getUint(keccak256("ProtocolDAO.MinipoolMinAVAXAssignment"));
  151: 		return getUint(keccak256("ProtocolDAO.MinipoolCancelMoratoriumSeconds"));
  162: 		return getUint(keccak256("ProtocolDAO.ExpectedAVAXRewardsRate"));
  169: 		return getUint(keccak256("ProtocolDAO.MaxCollateralizationRatio"));
  174: 		return getUint(keccak256("ProtocolDAO.MinCollateralizationRatio"));
  182: 		return getUint(keccak256("ProtocolDAO.TargetGGAVAXReserveRate"));

contracts/contract/RewardsPool.sol:
   98: 		addUint(keccak256("RewardsPool.InflationIntervalStartTime"), inflationIntervalElapsedSeconds);
   99: 		setUint(keccak256("RewardsPool.RewardsCycleTotalAmt"), newTokens);
  106: 		return getUint(keccak256("RewardsPool.RewardsCycleCount"));
  111: 		addUint(keccak256("RewardsPool.RewardsCycleCount"), 1);
  116: 		return getUint(keccak256("RewardsPool.RewardsCycleStartTime"));
  121: 		return getUint(keccak256("RewardsPool.RewardsCycleTotalAmt"));
  164: 		setUint(keccak256("RewardsPool.RewardsCycleStartTime"), block.timestamp);

contracts/contract/Staking.sol:
   73: 		return getUint(keccak256("staker.count"));
  348: 			stakerIndex = int256(getUint(keccak256("staker.count")));
  349: 			addUint(keccak256("staker.count"), 1);
```

### [G-06] Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}}). Empty receive()/fallback() payable functions that are not used, can be removed to save deployment gas.

```solidity
4 results - 3 files

contracts\contract\MinipoolManager.sol:
  106: 	function receiveWithdrawalAVAX() external payable {}

contracts\contract\tokens\TokenggAVAX.sol:
  255: 	function _authorizeUpgrade(address newImplementation) internal override onlyGuardian {}

contracts\contract\tokens\upgradeable\ERC4626Upgradeable.sol:
  176: 	function beforeWithdraw(uint256 assets, uint256 shares) internal virtual {}
  178: 	function afterDeposit(uint256 assets, uint256 shares) internal virtual {}

```
### [G-07] Use require instead of assert

The assert() and require() functions are a part of the error handling aspect in Solidity. Solidity makes use of state-reverting error handling exceptions. This means all changes made to the contract on that call or any sub-calls are undone if an error is thrown. It also flags an error.

They are quite similar as both check for conditions and if they are not met, would throw an error.

The big difference between the two is that the assert() function when false, uses up all the remaining gas and reverts all the changes made.

```solidity
contracts\contract\tokens\TokenggAVAX.sol:
  82 	receive() external payable {
  83: 		assert(msg.sender == address(asset));
  84 	}

```
### [G-08] Shifting cheaper than division

A division by 2 can be calculated by shifting one to the right. While the `DIV` opcode uses `5 gas`, the `SHR` opcode only uses `3 gas`. 

Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting. 

```solidity

2 results - 2 files

contracts\contract\MinipoolManager.sol:
  413: 	uint256 avaxHalfRewards = avaxTotalRewardAmt / 2;

contracts\contract\utils\RialtoSimulator.sol:
  109: 	effectiveGGPStaked = staking.getEffectiveGGPStaked(allStakers[i].stakerAddr) / 2;

```

**Recommendation:**
```diff
contracts\contract\MinipoolManager.sol#413

- 413: 	uint256 avaxHalfRewards = avaxTotalRewardAmt / 2;
+ 413: 	uint256 avaxHalfRewards = avaxTotalRewardAmt >> 1;

```
### [G-09] Use a more recent version of solidity

Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings.

Solidity 0.8.10 has a useful change that reduced gas costs of external calls which expect a return value. 

In 0.8.15 the conditions necessary for inlining are relaxed. Benchmarks show that the change significantly decreases the bytecode size (which impacts the deployment cost) while the effect on the runtime gas usage is smaller.

In 0.8.17 prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call; Simplify the starting offset of zero-length operations to zero. More efficient overflow checks for multiplication.

```solidity
2 results - 2 files

contracts\contract\tokens\upgradeable\ERC20Upgradeable.sol:
  2: pragma solidity >=0.8.0;

contracts\contract\tokens\upgradeable\ERC4626Upgradeable.sol:
  2: pragma solidity >=0.8.0;

```
### [G-10] Upgrade Solidity's optimizer

Make sure Solidity’s optimizer is enabled. It reduces gas costs. If you want to gas optimize for contract deployment (costs less to deploy a contract) then set the Solidity optimizer at a low number. If you want to optimize for run-time gas costs (when functions are called on a contract) then set the optimizer to a high number.

Set the optimization value higher than 800 in your hardhat.config.ts file.

```js
hardhat.config.ts:
  36:     solidity: {
  37: 	version: "0.8.17",
  38: 	settings: {
  39:                 optimizer: {
  40: 	            enabled: true,
  41: 	            runs: 800,
  42:                 },
  43:            },
  44:     },
```
### [G-11] Add ``unchecked {}`` for subtractions where the operands cannot underflow because of a previous ``require`` or ``if`` statement
```
require(a <= b); x = b - a => require(a <= b); unchecked { x = b - a } 
if(a <= b); x = b - a => if(a <= b); unchecked { x = b - a }
```
This will stop the check for overflow and underflow so it will save gas.

6 results - 1 file

```solidity
contracts\contract\ClaimNodeOp.sol:
   95 	if (claimAmt > ggpRewards) {
   96 	    revert InvalidAmount();
   97 	}
  102: 	uint256 restakeAmt = ggpRewards - claimAmt;
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L102

```solidity
contracts\contract\MinipoolManager.sol:
  614 	if (max > totalMinipools || limit == 0) {
  615 	    max = totalMinipools;
  616 	}
  617: 	minipools = new Minipool[](max - offset);
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L617

```solidity
contracts\contract\Staking.sol:
  423 	if (max > totalStakers || limit == 0) {
  424 	    max = totalStakers;
  425 	}
  426: 	stakers = new Staker[](max - offset);
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L426


```solidity
contracts\contract\Vault.sol:
   95 	if (avaxBalances[fromContractName] < amount) {
   96 	    revert InsufficientContractBalance();
   97 	}
   99: 	avaxBalances[fromContractName] = avaxBalances[fromContractName] - amount;

  151 	if (tokenBalances[contractKey] < amount) {
  152 	    revert InsufficientContractBalance();
  153 	}
  155: 	tokenBalances[contractKey] = tokenBalances[contractKey] - amount;

  183 	if (tokenBalances[contractKeyFrom] < amount) {
  184 	    revert InsufficientContractBalance();
  185 	}
  187: 	tokenBalances[contractKeyFrom] = tokenBalances[contractKeyFrom] - amount;
  ```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L99
### [G-12] Use ``assembly`` to write _address storage values_ 

```solidity
6 results - 4 files

contracts\contract\Staking.sol:
  60 	constructor(Storage storageAddress, ERC20 ggp_) Base(storageAddress) {
  62: 		ggp = ggp_;

contracts\contract\Base.sol:
   9 	constructor(Storage _gogoStorageAddress) {
  11: 		gogoStorage = Storage(_gogoStorageAddress);

contracts\contract\MinipoolManager.sol:
  177 	constructor(
  178 		Storage storageAddress,
  179 		ERC20 ggp_,
  180 		TokenggAVAX ggAVAX_
  181 	) Base(storageAddress) {
  182 		version = 1;
  183: 		ggp = ggp_;
  184: 		ggAVAX = ggAVAX_;

contracts\contract\Storage.sol:
  35 	constructor() {
  37: 		guardian = msg.sender;

  41 	function setGuardian(address newAddress) external {
  47: 		newGuardian = newAddress;

```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L62
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Base.sol#L11
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L183-L184
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L37
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L47


**Recommendation Code:**
```solidity
contracts\contract\Staking.sol#62

      constructor(Storage storageAddress, ERC20 ggp_) Base(storageAddress) {
               assembly {
                   sstore(ggp.slot, _ ggp)
               }

```

### [G-13] Sort Solidity operations using short-circuit mode

Short-circuiting is a solidity contract development model that uses ```OR/AND``` logic to sequence different cost operations. It puts low gas cost operations in the front and high gas cost operations in the back, so that if the front is low If the cost operation is feasible, you can skip (short-circuit) the subsequent high-cost Ethereum virtual machine operation.

```
//f(x) is a low gas cost operation 
//g(y) is a high gas cost operation 

//Sort operations with different gas costs as follows 
f(x) || g(y) 
f(x) && g(y)
```

6 results - 5 files
```solidity
contracts\contract\MinipoolManager.sol:
  394: 	if (endTime <= startTime || endTime > block.timestamp) {
  614: 	if (max > totalMinipools || limit == 0) {
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L394

```solidity
contracts\contract\Oracle.sol:
  59: 	if (timestamp < lastTimestamp || timestamp > block.timestamp) {
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Oracle.sol#L59

```solidity
contracts\contract\Staking.sol:
  423: 	if (max > totalStakers || limit == 0) {
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L423

```solidity
contracts\contract\tokens\TokenggAVAX.sol:
  144: 	if (totalAmt != (baseAmt + rewardAmt) || baseAmt > stakingTotalAssets) {
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L144

```solidity
contracts\contract\Storage.sol:
  29: 	if (booleanStorage[keccak256(abi.encodePacked("contract.exists", msg.sender))] == false && msg.sender != guardian) {
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L29

### [G-14] Don’t compare boolean expressions to boolean literals

Comparing to a constant (true or false) is a bit more expensive than directly checking the returned boolean value.

```solidity
3 results - 2 files

contracts\contract\BaseAbstract.sol:
  25: 	if (getBool(keccak256(abi.encodePacked("contract.exists", msg.sender))) == false) {
  74: 	if (enabled == false) {

contracts\contract\Storage.sol:
  29: 	if (booleanStorage[keccak256(abi.encodePacked("contract.exists", msg.sender))] == false && msg.sender != guardian) {

```

**Recommendation:**
Use if (enabled) instead of if (enabled == true) and if (!enabled) instead of if (enabled == false) .

### [G-15] Use a different pattern when using for loops

In the use of for loops, this structure, which will reduce gas costs, can be preferred. It saves approximately 400 gas in an array with 6 members.

7 results - 5 files
```solidity
contracts\contract\MinipoolManager.sol:
  619: 	for (uint256 i = offset; i < max; i++) {
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L619


```solidity
contracts\contract\MultisigManager.sol:
  84: 	for (uint256 i = 0; i < total; i++) {
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L84


```solidity
contracts\contract\Ocyticus.sol:
  61: 	for (uint256 i = 0; i < count; i++) {
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L61


```solidity
contracts\contract\RewardsPool.sol:
   74: 	for (uint256 i = 0; i < inflationIntervalsElapsed; i++) {
  215: 	for (uint256 i = 0; i < count; i++) {
  230: 	for (uint256 i = 0; i < enabledMultisigs.length; i++) {
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L74


```solidity
contracts\contract\Staking.sol:
  428: 	for (uint256 i = offset; i < max; i++) {
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L428


**Recommendation code:**
```diff
contracts\contract\Staking.sol#L428
  420 	function getStakers(uint256 offset, uint256 limit) external view returns (Staker[] memory stakers) {
  421 		uint256 totalStakers = getStakerCount();
  422 		uint256 max = offset + limit;
  423 		if (max > totalStakers || limit == 0) {
  424 			max = totalStakers;
  425 		}
  426 		stakers = new Staker[](max - offset);
  427 		uint256 total = 0;
- 428: 		for (uint256 i = offset; i < max; i++) {
+ 428: 		for (uint256 i = offset; i < max; i = unchecked_inc(i)) {
            		// do something that doesn't change the value of i
  429 			Staker memory s = getStaker(int256(i));
  430 			stakers[total] = s;
  431 			total++;
  432 		}

+ function unchecked_inc(uint i) internal pure returns (uint256) {
+        unchecked {
+            return i + 1;
+        }
+    }
```
### [G-16] Setting the _constructor_ to `payable`

You can cut out 10 opcodes in the creation-time EVM bytecode if you declare a constructor payable. Making the constructor payable eliminates the need for an initial check of ```msg.value == 0``` and saves ```13 gas``` on deployment with no security risks.

**Context:**
```solidity
14 results - 14 files

contracts\contract\Base.sol:
  9: 	constructor(Storage _gogoStorageAddress) {

contracts\contract\ClaimNodeOp.sol:
  29: 	constructor(Storage storageAddress, ERC20 ggp_) Base(storageAddress) {

contracts\contract\ClaimProtocolDAO.sol:
  15: 	constructor(Storage storageAddress) Base(storageAddress) {

contracts\contract\MinipoolManager.sol:
  177: 	constructor(

contracts\contract\MultisigManager.sol:
  29: 	constructor(Storage storageAddress) Base(storageAddress) {

contracts\contract\Ocyticus.sol:
  22: 	constructor(Storage storageAddress) Base(storageAddress) {

contracts\contract\Oracle.sol:
  23: 	constructor(Storage storageAddress) Base(storageAddress) {

contracts\contract\ProtocolDAO.sol:
  19: 	constructor(Storage storageAddress) Base(storageAddress) {

contracts\contract\RewardsPool.sol:
  30: 	constructor(Storage storageAddress) Base(storageAddress) {

contracts\contract\Staking.sol:
  60: 	constructor(Storage storageAddress, ERC20 ggp_) Base(storageAddress) {

contracts\contract\Storage.sol:
  35: 	constructor() {

contracts\contract\Vault.sol:
  41: 	constructor(Storage storageAddress) Base(storageAddress) {

contracts\contract\tokens\TokenggAVAX.sol:
  66: 	constructor() {

contracts\contract\tokens\TokenGGP.sol:
  12: 	constructor() ERC20("GoGoPool Protocol", "GGP", 18) {

```

**Recommendation:**
Set the constructor to ```payable```

### [G-17]  Functions guaranteed to revert_ when callled by normal users can be marked `payable` 

If a function modifier or require such as onlyOwner-admin is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2), DUP1(3), ISZERO(3), PUSH2(3), JUMPI(10), PUSH1(3), DUP1(3), REVERT(0), JUMPDEST(1), POP(2) which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

```solidity
64 results - 14 files

contracts\contract\BaseUpgradeable.sol:
  10: 	function __BaseUpgradeable_init(Storage gogoStorageAddress) internal onlyInitializing {

contracts\contract\ClaimNodeOp.sol:
  40: 	function setRewardsCycleTotal(uint256 amount) public onlySpecificRegisteredContract("RewardsPool", msg.sender) {
  56: 	function calculateAndDistributeRewards(address stakerAddr, uint256 totalEligibleGGPStaked) external onlyMultisig {

contracts\contract\ClaimProtocolDAO.sol:
  24: 	) external onlyGuardian {

contracts\contract\MultisigManager.sol:
  35: 	function registerMultisig(address addr) external onlyGuardian {
  55: 	function enableMultisig(address addr) external onlyGuardian {

contracts\contract\Ocyticus.sol:
  27: 	function addDefender(address defender) external onlyGuardian {
  32: 	function removeDefender(address defender) external onlyGuardian {
  37: 	function pauseEverything() external onlyDefender {
  47: 	function resumeEverything() external onlyDefender {
  55: 	function disableAllMultisigs() public onlyDefender {

contracts\contract\Oracle.sol:
  28: 	function setOneInch(address addr) external onlyGuardian {
  57: 	function setGGPPriceInAVAX(uint256 price, uint256 timestamp) external onlyMultisig {

contracts\contract\ProtocolDAO.sol:
   23: 	function initialize() external onlyGuardian {
   67: 	function pauseContract(string memory contractName) public onlySpecificRegisteredContract("Ocyticus", msg.sender) {
   73: 	function resumeContract(string memory contractName) public onlySpecificRegisteredContract("Ocyticus", msg.sender) {
   96: 	function setTotalGGPCirculatingSupply(uint256 amount) public onlySpecificRegisteredContract("RewardsPool", msg.sender) {
  107: 	function setClaimingContractPct(string memory claimingContract, uint256 decimal) public onlyGuardian valueNotGreaterThanOne(decimal) {
  156: 	function setExpectedAVAXRewardsRate(uint256 rate) public onlyMultisig valueNotGreaterThanOne(rate) {
  190: 	function registerContract(address addr, string memory name) public onlyGuardian {
  198: 	function unregisterContract(address addr) public onlyGuardian {
  213: 	) external onlyGuardian {

contracts\contract\RewardsPool.sol:
  34: 	function initialize() external onlyGuardian {

contracts\contract\Staking.sol:
  110: 	function increaseAVAXStake(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
  117: 	function decreaseAVAXStake(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
  133: 	function increaseAVAXAssigned(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
  140: 	function decreaseAVAXAssigned(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
  156: 	function increaseAVAXAssignedHighWater(address stakerAddr, uint256 amount) public onlyRegisteredNetworkContract {
  163: 	function resetAVAXAssignedHighWater(address stakerAddr) public onlyRegisteredNetworkContract {
  180: 	function increaseMinipoolCount(address stakerAddr) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
  187: 	function decreaseMinipoolCount(address stakerAddr) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
  204: 	function setRewardsStartTime(address stakerAddr, uint256 time) public onlyRegisteredNetworkContract {
  224: 	function increaseGGPRewards(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {
  231: 	function decreaseGGPRewards(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {
  248: 	function setLastRewardsCycleCompleted(address stakerAddr, uint256 cycleNumber) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {
  328: 	function restakeGGP(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {
  379: 	function slashGGP(address stakerAddr, uint256 ggpAmt) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

contracts\contract\Storage.sol:
  104: 	function setAddress(bytes32 key, address value) external onlyRegisteredNetworkContract {
  108: 	function setBool(bytes32 key, bool value) external onlyRegisteredNetworkContract {
  112: 	function setBytes(bytes32 key, bytes calldata value) external onlyRegisteredNetworkContract {
  116: 	function setBytes32(bytes32 key, bytes32 value) external onlyRegisteredNetworkContract {
  120: 	function setInt(bytes32 key, int256 value) external onlyRegisteredNetworkContract {
  124: 	function setString(bytes32 key, string calldata value) external onlyRegisteredNetworkContract {
  128: 	function setUint(bytes32 key, uint256 value) external onlyRegisteredNetworkContract {
  136: 	function deleteAddress(bytes32 key) external onlyRegisteredNetworkContract {
  140: 	function deleteBool(bytes32 key) external onlyRegisteredNetworkContract {
  144: 	function deleteBytes(bytes32 key) external onlyRegisteredNetworkContract {
  148: 	function deleteBytes32(bytes32 key) external onlyRegisteredNetworkContract {
  152: 	function deleteInt(bytes32 key) external onlyRegisteredNetworkContract {
  156: 	function deleteString(bytes32 key) external onlyRegisteredNetworkContract {
  160: 	function deleteUint(bytes32 key) external onlyRegisteredNetworkContract {
  170: 	function addUint(bytes32 key, uint256 amount) external onlyRegisteredNetworkContract {
  176: 	function subUint(bytes32 key, uint256 amount) external onlyRegisteredNetworkContract {

contracts\contract\Vault.sol:
   46: 	function depositAVAX() external payable onlyRegisteredNetworkContract {
   61: 	function withdrawAVAX(uint256 amount) external onlyRegisteredNetworkContract nonReentrant {
   84: 	function transferAVAX(string memory toContractName, uint256 amount) external onlyRegisteredNetworkContract {
  141: 	) external onlyRegisteredNetworkContract nonReentrant {
  170: 	) external onlyRegisteredNetworkContract {
  204: 	function addAllowedToken(address tokenAddress) external onlyGuardian {
  208: 	function removeAllowedToken(address tokenAddress) external onlyGuardian {

contracts\contract\tokens\TokenggAVAX.sol:
  142: 	function depositFromStaking(uint256 baseAmt, uint256 rewardAmt) public payable onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
  153: 	function withdrawForStaking(uint256 assets) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
  255: 	function _authorizeUpgrade(address newImplementation) internal override onlyGuardian {}

contracts\contract\tokens\upgradeable\ERC20Upgradeable.sol:
  57: 	) internal onlyInitializing {

contracts\contract\tokens\upgradeable\ERC4626Upgradeable.sol:
  33: 	) internal onlyInitializing {

```

**Recommendation:**
Functions guaranteed to revert when called by normal users can be marked payable  (for only ```onlyRegisteredNetworkContract, onlySpecificRegisteredContract, onlyGuardian, onlyMultisig, onlyInitializing or onlyDefender``` functions)

### [G-18] Optimize names to save gas
Contracts most called functions could simply save gas by function ordering via ```Method ID```. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because ```22 gas``` are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions. 

**Context:** 
All Contracts


**Recommendation:** 
Find a lower ```method ID``` name for the most called functions for example Call() vs. Call1() is cheaper by ```22 gas```
For example, the function IDs in the ``` Storage.sol ``` contract will be the most used; A lower method ID may be given.

**Proof of Consept:**
https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

Storage.sol function names can be named and sorted according to METHOD ID

```js
Sighash   |   Function Signature
========================
8a0dac4a  =>  setGuardian(address)
a75b87d2  =>  getGuardian()
1e0ea61e  =>  confirmGuardian()
21f8a721  =>  getAddress(bytes32)
7ae1cfca  =>  getBool(bytes32)
c031a180  =>  getBytes(bytes32)
a6ed563e  =>  getBytes32(bytes32)
dc97d962  =>  getInt(bytes32)
986e791a  =>  getString(bytes32)
bd02d0f5  =>  getUint(bytes32)
ca446dd9  =>  setAddress(bytes32,address)
abfdcced  =>  setBool(bytes32,bool)
2e28d084  =>  setBytes(bytes32,bytes)
4e91db08  =>  setBytes32(bytes32,bytes32)
3e49bed0  =>  setInt(bytes32,int256)
6e899550  =>  setString(bytes32,string)
e2a4853a  =>  setUint(bytes32,uint256)
0e14a376  =>  deleteAddress(bytes32)
2c62ff2d  =>  deleteBool(bytes32)
616b59f6  =>  deleteBytes(bytes32)
0b9adc57  =>  deleteBytes32(bytes32)
8c160095  =>  deleteInt(bytes32)
f6bb3cc4  =>  deleteString(bytes32)
e2b202bf  =>  deleteUint(bytes32)
adb353dc  =>  addUint(bytes32,uint256)
ebb9d8c9  =>  subUint(bytes32,uint256)

```
### [G-19] Use nested if and, avoid multiple check combinations

Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.

```solidity
2 results - 2 files

contracts\contract\RewardsPool.sol:
  142: 	if (claimContractPct > 0 && currentCycleRewardsTotal > 0) {

contracts\contract\tokens\TokenggAVAX.sol:
  59: 	if (amt > 0 && getBool(keccak256(abi.encodePacked("contract.paused", "TokenggAVAX")))) {

contracts\contract\Storage.sol:
  29: 	if (booleanStorage[keccak256(abi.encodePacked("contract.exists", msg.sender))] == false && msg.sender != guardian) {

```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L142
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L59
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L29


**Recomendation Code:**

```diff
contracts\contract\RewardsPool.sol#142

- 142: 	if (claimContractPct > 0 && currentCycleRewardsTotal > 0) {
+  	if (claimContractPct > 0) {
+  	    if (currentCycleRewardsTotal > 0) {
 	    }
                   } 

```
### [G-20] Use ``double require`` instead of using ``&&``

Using double require instead of operator && can save more gas
When having a require statement with 2 or more expressions needed, place the expression that cost less gas first.

**Context:** 
```solidity
1 result - 1 file

contracts\contract\tokens\upgradeable\ERC20Upgradeable.sol:
  154: 	require(recoveredAddress != address(0) && recoveredAddress == owner, "INVALID_SIGNER");

```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L154


**Recommendation Code:**
 ```diff

contracts\contract\tokens\upgradeable\ERC20Upgradeable.sol#154

- 154: 	require(recoveredAddress != address(0) && recoveredAddress == owner, "INVALID_SIGNER");
+ 	require(recoveredAddress != address(0));
+ 	require(recoveredAddress == owner, "INVALID_SIGNER");

```
