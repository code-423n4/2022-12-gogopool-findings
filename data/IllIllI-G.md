
## Summary

### Gas Optimizations
| |Issue|Instances|Total Gas Saved|
|-|:-|:-:|:-:|
| [G&#x2011;01] | Modifier unnecessarily looks up storage variables | 2 |  4200 |
| [G&#x2011;02] | Batch pause and resume to save gas | 1 |  600 |
| [G&#x2011;03] | Cheaper to calculate than to read and update | 1 |  2100 |
| [G&#x2011;04] | Use `1` and `2` instead of `bool`s to save gas | 1 |  17100 |
| [G&#x2011;05] | Wastes gas to clear the old `newGuardian` | 1 |  17100 |
| [G&#x2011;06] | Cheaper to calculate domain separator every time | 1 |  4200 |
| [G&#x2011;07] | Contract calculations having to do with `msg.value` can be unchecked | 1 |  60 |
| [G&#x2011;08] | State variables should be cached in stack variables rather than re-reading them from storage | 4 |  388 |
| [G&#x2011;09] | The result of function calls should be cached rather than re-calling the function | 2 |  - |
| [G&#x2011;10] | `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables | 6 |  678 |
| [G&#x2011;11] | `internal` functions only called once can be inlined to save gas | 5 |  100 |
| [G&#x2011;12] | `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops | 7 |  420 |
| [G&#x2011;13] | `keccak256()` should only need to be called on a specific string literal once | 74 |  3108 |
| [G&#x2011;14] | Optimize names to save gas | 12 |  264 |
| [G&#x2011;15] | Use a more recent version of solidity | 2 |  - |
| [G&#x2011;16] | String literals passed to `abi.encode()`/`abi.encodePacked()` should not be split by commas | 3 |  - |
| [G&#x2011;17] | `>=` costs less gas than `>` | 2 |  6 |
| [G&#x2011;18] | Splitting `require()` statements that use `&&` saves gas | 1 |  3 |
| [G&#x2011;19] | Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead | 3 |  - |
| [G&#x2011;20] | Don't compare boolean expressions to boolean literals | 3 |  27 |
| [G&#x2011;21] | Stack variable used as a cheaper cache for a state variable is only used once | 1 |  3 |
| [G&#x2011;22] | Superfluous event fields | 1 |  - |
| [G&#x2011;23] | Functions guaranteed to revert when called by normal users can be marked `payable` | 63 |  1323 |

Total: 197 instances over 23 issues with **51680 gas** saved

Gas totals use lower bounds of ranges and count two iterations of each `for`-loop. All values above are runtime, not deployment, values; deployment values are listed in the individual issue descriptions. The table above as well as its gas numbers do not include any of the excluded findings.





## Gas Optimizations

### [G&#x2011;01]  Modifier unnecessarily looks up storage variables
If the else-condition isn't required, `isContract` will have been looked up unnecessarily

*There are 2 instances of this issue:*

```solidity
File: /contracts/contract/BaseAbstract.sol

40   	modifier guardianOrRegisteredContract() {
41   		bool isContract = getBool(keccak256(abi.encodePacked("contract.exists", msg.sender)));
42   		bool isGuardian = msg.sender == gogoStorage.getGuardian();
43   
44   		if (!(isGuardian || isContract)) {
45:  			revert MustBeGuardianOrValidContract();

51   	modifier guardianOrSpecificRegisteredContract(string memory contractName, address contractAddress) {
52   		bool isContract = contractAddress == getAddress(keccak256(abi.encodePacked("contract.address", contractName)));
53   		bool isGuardian = msg.sender == gogoStorage.getGuardian();
54   
55   		if (!(isGuardian || isContract)) {
56:  			revert MustBeGuardianOrValidContract();

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseAbstract.sol#L40-L45

### [G&#x2011;02]  Batch pause and resume to save gas
If `pause/resumeEverything()` are made to be functions on `ProtocolDAO`, you'll save the overhead of six external calls (100 gas each)

*There is 1 instance of this issue:*

```solidity
File: /contracts/contract/Ocyticus.sol

37   	function pauseEverything() external onlyDefender {
38   		ProtocolDAO dao = ProtocolDAO(getContractAddress("ProtocolDAO"));
39   		dao.pauseContract("TokenggAVAX");
40   		dao.pauseContract("MinipoolManager");
41   		dao.pauseContract("Staking");
42   		disableAllMultisigs();
43   	}
44   
45   	/// @notice Reestablish all contract's abilities
46   	/// @dev Multisigs will need to be enabled seperately, we dont know which ones to enable
47   	function resumeEverything() external onlyDefender {
48   		ProtocolDAO dao = ProtocolDAO(getContractAddress("ProtocolDAO"));
49   		dao.resumeContract("TokenggAVAX");
50   		dao.resumeContract("MinipoolManager");
51   		dao.resumeContract("Staking");
52:  	}

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Ocyticus.sol#L37-L52

### [G&#x2011;03]  Cheaper to calculate than to read and update
The new value is known, so it's cheaper to set the new multisig count directly, rather than having `addUint()` look up the old value, then overwrite it

*There is 1 instance of this issue:*

```solidity
File: /contracts/contract/MultisigManager.sol

48   		setUint(keccak256(abi.encodePacked("multisig.index", addr)), index + 1);
49:  		addUint(keccak256("multisig.count"), 1);

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MultisigManager.sol#L48-L49

### [G&#x2011;04]  Use `1` and `2` instead of `bool`s to save gas
Using `1` and `2` prevents the storage slot from being marked as cleared, which will save gas when later setting the value back to true, since it will already be non-zero and thus a cheaper storage write than if it had been zero

*There is 1 instance of this issue:*

```solidity
File: /contracts/contract/Storage.sol

16:  	mapping(bytes32 => bool) private booleanStorage;

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L16

### [G&#x2011;05]  Wastes gas to clear the old `newGuardian`
Clearing the value means the next time it's changed to something else, the operation is more expensive than if it had been changed from the old value

*There is 1 instance of this issue:*

```solidity
File: /contracts/contract/Storage.sol

64:  		delete newGuardian;

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L64

### [G&#x2011;06]  Cheaper to calculate domain separator every time
Since `INITIAL_CHAIN_ID` and `INITIAL_DOMAIN_SEPARATOR` are no longer immutable, but are state variables, you end up looking up their value every time, which incurs a very large gas penalty. It's cheaper to just calculate it every time, or to use [OpenZeppelin](https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/master/contracts/token/ERC20/extensions/draft-ERC20PermitUpgradeable.sol)'s version which is optimized for this case

*There is 1 instance of this issue:*

```solidity
File: /contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol

163: 		return block.chainid == INITIAL_CHAIN_ID ? INITIAL_DOMAIN_SEPARATOR : computeDomainSeparator();

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L163

### [G&#x2011;07]  Contract calculations having to do with `msg.value` can be unchecked
This is because `msg.value` will never be more than `type(uint256).max`

*There is 1 instance of this issue:*

```solidity
File: /contracts/contract/tokens/TokenggAVAX.sol

143  		uint256 totalAmt = msg.value;
144: 		if (totalAmt != (baseAmt + rewardAmt) || baseAmt > stakingTotalAssets) {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L143-L144

### [G&#x2011;08]  State variables should be cached in stack variables rather than re-reading them from storage
The instances below point to the second+ access of a state variable within a function. Caching of a state variable replaces each Gwarmaccess (**100 gas**) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

*There are 4 instances of this issue:*

```solidity
File: contracts/contract/tokens/TokenggAVAX.sol

/// @audit rewardsCycleLength on line 76
/// @audit rewardsCycleLength on line 78
78:   		rewardsCycleEnd = (block.timestamp.safeCastTo32() / rewardsCycleLength) * rewardsCycleLength;

/// @audit rewardsCycleLength on line 102
/// @audit rewardsCycleLength on line 102
102:  		uint32 nextRewardsCycleEnd = ((timestamp + rewardsCycleLength) / rewardsCycleLength) * rewardsCycleLength;

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L78

### [G&#x2011;09]  The result of function calls should be cached rather than re-calling the function
The instances below point to the second+ call of the function within a single function

*There are 2 instances of this issue:*

```solidity
File: contracts/contract/ClaimNodeOp.sol

/// @audit rewardsPool.getRewardsCycleCount() on line 61
65:   		if (staking.getLastRewardsCycleCompleted(stakerAddr) == rewardsPool.getRewardsCycleCount()) {

/// @audit rewardsPool.getRewardsCycleCount() on line 61
68:   		staking.setLastRewardsCycleCompleted(stakerAddr, rewardsPool.getRewardsCycleCount());

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimNodeOp.sol#L65

### [G&#x2011;10]  `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables
Using the addition operator instead of plus-equals saves **[113 gas](https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8)**

*There are 6 instances of this issue:*

```solidity
File: contracts/contract/tokens/TokenggAVAX.sol

149:  		stakingTotalAssets -= baseAmt;

160:  		stakingTotalAssets += assets;

245:  		totalReleasedAssets -= amount;

252:  		totalReleasedAssets += amount;

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L149

```solidity
File: contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol

184:  		totalSupply += amount;

201:  			totalSupply -= amount;

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L184

### [G&#x2011;11]  `internal` functions only called once can be inlined to save gas
Not inlining costs **20 to 40 gas** because of two extra `JUMP` instructions and additional stack operations needed for function calls.

*There are 5 instances of this issue:*

```solidity
File: contracts/contract/RewardsPool.sol

82    	function inflate() internal {
83:   		ProtocolDAO dao = ProtocolDAO(getContractAddress("ProtocolDAO"));

110   	function increaseRewardsCycleCount() internal {
111:  		addUint(keccak256("RewardsPool.RewardsCycleCount"), 1);

203   	function distributeMultisigAllotment(
204   		uint256 allotment,
205   		Vault vault,
206:  		TokenGGP ggp

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L82-L83

```solidity
File: contracts/contract/Staking.sol

87:   	function increaseGGPStake(address stakerAddr, uint256 amount) internal {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L87

```solidity
File: contracts/contract/tokens/TokenggAVAX.sol

248   	function afterDeposit(
249   		uint256 amount,
250:  		uint256 /* shares */

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L248-L250

### [G&#x2011;12]  `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops
The `unchecked` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves **30-40 gas [per loop](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)**

*There are 7 instances of this issue:*

```solidity
File: contracts/contract/MinipoolManager.sol

619:  		for (uint256 i = offset; i < max; i++) {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L619

```solidity
File: contracts/contract/MultisigManager.sol

84:   		for (uint256 i = 0; i < total; i++) {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MultisigManager.sol#L84

```solidity
File: contracts/contract/Ocyticus.sol

61:   		for (uint256 i = 0; i < count; i++) {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Ocyticus.sol#L61

```solidity
File: contracts/contract/RewardsPool.sol

74:   		for (uint256 i = 0; i < inflationIntervalsElapsed; i++) {

215:  		for (uint256 i = 0; i < count; i++) {

230:  		for (uint256 i = 0; i < enabledMultisigs.length; i++) {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L74

```solidity
File: contracts/contract/Staking.sol

428:  		for (uint256 i = offset; i < max; i++) {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L428

### [G&#x2011;13]  `keccak256()` should only need to be called on a specific string literal once
It should be saved to an immutable variable, and the variable used instead. If the hash is being used as a part of a function selector, the cast to `bytes4` should also only be done once

*There are 74 instances of this issue:*

```solidity
File: contracts/contract/ClaimNodeOp.sol

36:   		return getUint(keccak256("NOPClaim.RewardsCycleTotal"));

41:   		setUint(keccak256("NOPClaim.RewardsCycleTotal"), amount);

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimNodeOp.sol#L36

```solidity
File: contracts/contract/MinipoolManager.sol

248:  			minipoolIndex = int256(getUint(keccak256("minipool.count")));

252:  			addUint(keccak256("minipool.count"), 1);

333:  		addUint(keccak256("MinipoolManager.TotalAVAXLiquidStakerAmt"), avaxLiquidStakerAmt);

433:  		subUint(keccak256("MinipoolManager.TotalAVAXLiquidStakerAmt"), avaxLiquidStakerAmt);

512:  		subUint(keccak256("MinipoolManager.TotalAVAXLiquidStakerAmt"), avaxLiquidStakerAmt);

542:  		return getUint(keccak256("MinipoolManager.TotalAVAXLiquidStakerAmt"));

612:  		uint256 totalMinipools = getUint(keccak256("minipool.count"));

635:  		return getUint(keccak256("minipool.count"));

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L248

```solidity
File: contracts/contract/MultisigManager.sol

40:   		uint256 index = getUint(keccak256("multisig.count"));

49:   		addUint(keccak256("multisig.count"), 1);

81:   		uint256 total = getUint(keccak256("multisig.count"));

103:  		return getUint(keccak256("multisig.count"));

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MultisigManager.sol#L40

```solidity
File: contracts/contract/Oracle.sol

29:   		setAddress(keccak256("Oracle.OneInch"), addr);

38:   		IOneInch oneinch = IOneInch(getAddress(keccak256("Oracle.OneInch")));

47:   		price = getUint(keccak256("Oracle.GGPPriceInAVAX"));

51:   		timestamp = getUint(keccak256("Oracle.GGPTimestamp"));

58:   		uint256 lastTimestamp = getUint(keccak256("Oracle.GGPTimestamp"));

65:   		setUint(keccak256("Oracle.GGPPriceInAVAX"), price);

66:   		setUint(keccak256("Oracle.GGPTimestamp"), timestamp);

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Oracle.sol#L29

```solidity
File: contracts/contract/ProtocolDAO.sol

24:   		if (getBool(keccak256("ProtocolDAO.initialized"))) {

27:   		setBool(keccak256("ProtocolDAO.initialized"), true);

30:   		setUint(keccak256("ProtocolDAO.RewardsEligibilityMinSeconds"), 14 days);

33:   		setUint(keccak256("ProtocolDAO.RewardsCycleSeconds"), 28 days); // The time in which a claim period will span in seconds - 28 days by default

34:   		setUint(keccak256("ProtocolDAO.TotalGGPCirculatingSupply"), 18_000_000 ether);

35:   		setUint(keccak256("ProtocolDAO.ClaimingContractPct.ClaimMultisig"), 0.20 ether);

36:   		setUint(keccak256("ProtocolDAO.ClaimingContractPct.ClaimNodeOp"), 0.70 ether);

37:   		setUint(keccak256("ProtocolDAO.ClaimingContractPct.ClaimProtocolDAO"), 0.10 ether);

40:   		setUint(keccak256("ProtocolDAO.InflationIntervalSeconds"), 1 days);

41:   		setUint(keccak256("ProtocolDAO.InflationIntervalRate"), 1000133680617113500); // 5% annual calculated on a daily interval - Calculate in js example: let dailyInflation = web3.utils.toBN((1 + 0.05) ** (1 / (365)) * 1e18);

44:   		setUint(keccak256("ProtocolDAO.TargetGGAVAXReserveRate"), 0.1 ether); // 10% collateral held in reserve

47:   		setUint(keccak256("ProtocolDAO.MinipoolMinAVAXStakingAmt"), 2_000 ether);

48:   		setUint(keccak256("ProtocolDAO.MinipoolNodeCommissionFeePct"), 0.15 ether);

49:   		setUint(keccak256("ProtocolDAO.MinipoolMaxAVAXAssignment"), 10_000 ether);

50:   		setUint(keccak256("ProtocolDAO.MinipoolMinAVAXAssignment"), 1_000 ether);

51:   		setUint(keccak256("ProtocolDAO.ExpectedAVAXRewardsRate"), 0.1 ether); // Annual rate as pct of 1 avax

52:   		setUint(keccak256("ProtocolDAO.MinipoolCancelMoratoriumSeconds"), 5 days);

55:   		setUint(keccak256("ProtocolDAO.MaxCollateralizationRatio"), 1.5 ether);

56:   		setUint(keccak256("ProtocolDAO.MinCollateralizationRatio"), 0.1 ether);

82:   		return getUint(keccak256("ProtocolDAO.RewardsEligibilityMinSeconds"));

87:   		return getUint(keccak256("ProtocolDAO.RewardsCycleSeconds"));

92:   		return getUint(keccak256("ProtocolDAO.TotalGGPCirculatingSupply"));

97:   		return setUint(keccak256("ProtocolDAO.TotalGGPCirculatingSupply"), amount);

117:  		uint256 rate = getUint(keccak256("ProtocolDAO.InflationIntervalRate"));

124:  		return getUint(keccak256("ProtocolDAO.InflationIntervalSeconds"));

131:  		return getUint(keccak256("ProtocolDAO.MinipoolMinAVAXStakingAmt"));

136:  		return getUint(keccak256("ProtocolDAO.MinipoolNodeCommissionFeePct"));

141:  		return getUint(keccak256("ProtocolDAO.MinipoolMaxAVAXAssignment"));

146:  		return getUint(keccak256("ProtocolDAO.MinipoolMinAVAXAssignment"));

151:  		return getUint(keccak256("ProtocolDAO.MinipoolCancelMoratoriumSeconds"));

157:  		setUint(keccak256("ProtocolDAO.ExpectedAVAXRewardsRate"), rate);

162:  		return getUint(keccak256("ProtocolDAO.ExpectedAVAXRewardsRate"));

169:  		return getUint(keccak256("ProtocolDAO.MaxCollateralizationRatio"));

174:  		return getUint(keccak256("ProtocolDAO.MinCollateralizationRatio"));

182:  		return getUint(keccak256("ProtocolDAO.TargetGGAVAXReserveRate"));

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ProtocolDAO.sol#L24

```solidity
File: contracts/contract/RewardsPool.sol

35:   		if (getBool(keccak256("RewardsPool.initialized"))) {

38:   		setBool(keccak256("RewardsPool.initialized"), true);

40:   		setUint(keccak256("RewardsPool.RewardsCycleStartTime"), block.timestamp);

41:   		setUint(keccak256("RewardsPool.InflationIntervalStartTime"), block.timestamp);

49:   		return getUint(keccak256("RewardsPool.InflationIntervalStartTime"));

98:   		addUint(keccak256("RewardsPool.InflationIntervalStartTime"), inflationIntervalElapsedSeconds);

99:   		setUint(keccak256("RewardsPool.RewardsCycleTotalAmt"), newTokens);

106:  		return getUint(keccak256("RewardsPool.RewardsCycleCount"));

111:  		addUint(keccak256("RewardsPool.RewardsCycleCount"), 1);

116:  		return getUint(keccak256("RewardsPool.RewardsCycleStartTime"));

121:  		return getUint(keccak256("RewardsPool.RewardsCycleTotalAmt"));

164:  		setUint(keccak256("RewardsPool.RewardsCycleStartTime"), block.timestamp);

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L35

```solidity
File: contracts/contract/Staking.sol

73:   		return getUint(keccak256("staker.count"));

348:  			stakerIndex = int256(getUint(keccak256("staker.count")));

349:  			addUint(keccak256("staker.count"), 1);

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L73

```solidity
File: contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol

139:  								keccak256("Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)"),

170:  					keccak256("EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)"),

172:  					keccak256("1"),

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L139

### [G&#x2011;14]  Optimize names to save gas
`public`/`external` function names and `public` member variable names can be optimized to save gas. See [this](https://gist.github.com/IllIllI000/a5d8b486a8259f9f77891a919febd1a9) link for an example of how it works. Below are the interfaces/abstract contracts that can be optimized so that the most frequently-called functions use the least amount of gas possible during method lookup. Method IDs that have two leading zero bytes can save **128 gas** each during deployment, and renaming functions to have lower method IDs will save **22 gas** per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)

*There are 12 instances of this issue:*

```solidity
File: contracts/contract/ClaimNodeOp.sol

/// @audit getRewardsCycleTotal(), setRewardsCycleTotal(), isEligible(), calculateAndDistributeRewards(), claimAndRestake()
17:   contract ClaimNodeOp is Base {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimNodeOp.sol#L17

```solidity
File: contracts/contract/ClaimProtocolDAO.sol

/// @audit spend()
10:   contract ClaimProtocolDAO is Base {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimProtocolDAO.sol#L10

```solidity
File: contracts/contract/MinipoolManager.sol

/// @audit receiveWithdrawalAVAX(), createMinipool(), cancelMinipool(), withdrawMinipoolFunds(), canClaimAndInitiateStaking(), claimAndInitiateStaking(), recordStakingStart(), recordStakingEnd(), recreateMinipool(), recordStakingError(), cancelMinipoolByMultisig(), finishFailedMinipoolByMultisig(), getTotalAVAXLiquidStakerAmt(), calculateGGPSlashAmt(), getExpectedAVAXRewardsAmt(), getIndexOf(), getMinipoolByNodeID(), getMinipool(), getMinipools(), getMinipoolCount()
57:   contract MinipoolManager is Base, ReentrancyGuard, IWithdrawer {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L57

```solidity
File: contracts/contract/MultisigManager.sol

/// @audit registerMultisig(), enableMultisig(), disableMultisig(), requireNextActiveMultisig(), getIndexOf(), getCount(), getMultisig()
17:   contract MultisigManager is Base {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MultisigManager.sol#L17

```solidity
File: contracts/contract/Ocyticus.sol

/// @audit addDefender(), removeDefender(), pauseEverything(), resumeEverything(), disableAllMultisigs()
10:   contract Ocyticus is Base {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Ocyticus.sol#L10

```solidity
File: contracts/contract/Oracle.sol

/// @audit setOneInch(), getGGPPriceInAVAXFromOneInch(), getGGPPriceInAVAX(), setGGPPriceInAVAX()
17:   contract Oracle is Base {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Oracle.sol#L17

```solidity
File: contracts/contract/ProtocolDAO.sol

/// @audit initialize(), getContractPaused(), pauseContract(), resumeContract(), getRewardsEligibilityMinSeconds(), getRewardsCycleSeconds(), getTotalGGPCirculatingSupply(), setTotalGGPCirculatingSupply(), getClaimingContractPct(), setClaimingContractPct(), getInflationIntervalRate(), getInflationIntervalSeconds(), getMinipoolMinAVAXStakingAmt(), getMinipoolNodeCommissionFeePct(), getMinipoolMaxAVAXAssignment(), getMinipoolMinAVAXAssignment(), getMinipoolCancelMoratoriumSeconds(), setExpectedAVAXRewardsRate(), getExpectedAVAXRewardsRate(), getMaxCollateralizationRatio(), getMinCollateralizationRatio(), getTargetGGAVAXReserveRate(), registerContract(), unregisterContract(), upgradeExistingContract()
9:    contract ProtocolDAO is Base {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ProtocolDAO.sol#L9

```solidity
File: contracts/contract/RewardsPool.sol

/// @audit initialize(), getInflationIntervalStartTime(), getInflationIntervalsElapsed(), getInflationAmt(), getRewardsCycleCount(), getRewardsCycleStartTime(), getRewardsCycleTotalAmt(), getRewardsCyclesElapsed(), getClaimingContractDistribution(), canStartRewardsCycle(), startRewardsCycle()
15:   contract RewardsPool is Base {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L15

```solidity
File: contracts/contract/Staking.sol

/// @audit getTotalGGPStake(), getStakerCount(), getGGPStake(), getAVAXStake(), increaseAVAXStake(), decreaseAVAXStake(), getAVAXAssigned(), increaseAVAXAssigned(), decreaseAVAXAssigned(), getAVAXAssignedHighWater(), increaseAVAXAssignedHighWater(), resetAVAXAssignedHighWater(), getMinipoolCount(), increaseMinipoolCount(), decreaseMinipoolCount(), getRewardsStartTime(), setRewardsStartTime(), getGGPRewards(), increaseGGPRewards(), decreaseGGPRewards(), getLastRewardsCycleCompleted(), setLastRewardsCycleCompleted(), getMinimumGGPStake(), getCollateralizationRatio(), getEffectiveRewardsRatio(), getEffectiveGGPStaked(), stakeGGP(), restakeGGP(), withdrawGGP(), slashGGP(), requireValidStaker(), getIndexOf(), getStaker(), getStakers()
30:   contract Staking is Base {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L30

```solidity
File: contracts/contract/Storage.sol

/// @audit setGuardian(), getGuardian(), confirmGuardian(), getAddress(), getBool(), getBytes(), getBytes32(), getInt(), getString(), getUint(), setAddress(), setBool(), setBytes(), setBytes32(), setInt(), setString(), setUint(), deleteAddress(), deleteBool(), deleteBytes(), deleteBytes32(), deleteInt(), deleteString(), deleteUint(), addUint(), subUint()
7:    contract Storage {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L7

```solidity
File: contracts/contract/tokens/TokenggAVAX.sol

/// @audit initialize(), syncRewards(), amountAvailableForStaking(), depositFromStaking(), withdrawForStaking(), depositAVAX(), withdrawAVAX(), redeemAVAX()
24:   contract TokenggAVAX is Initializable, ERC4626Upgradeable, UUPSUpgradeable, BaseUpgradeable {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L24

```solidity
File: contracts/contract/Vault.sol

/// @audit depositAVAX(), withdrawAVAX(), transferAVAX(), depositToken(), withdrawToken(), transferToken(), balanceOf(), balanceOfToken(), addAllowedToken(), removeAllowedToken()
19:   contract Vault is Base, ReentrancyGuard {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L19

### [G&#x2011;15]  Use a more recent version of solidity
Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than `revert()/require()` strings
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

*There are 2 instances of this issue:*

```solidity
File: contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol

2:    pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L2

```solidity
File: contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol

2:    pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L2

### [G&#x2011;16]  String literals passed to `abi.encode()`/`abi.encodePacked()` should not be split by commas
String literals can be split into multiple parts and still be considered as a single string literal. Adding commas between each chunk makes it no longer a single string, and instead multiple strings. EACH new comma costs [***21 gas***](https://gist.github.com/IllIllI000/837d1b36c16c9bfe1010f9f775a09bbf) due to stack operations and separate `MSTORE`s

*There are 3 instances of this issue:*

```solidity
File: contracts/contract/tokens/TokenggAVAX.sol

/// @audit 1 commas
59:   		if (amt > 0 && getBool(keccak256(abi.encodePacked("contract.paused", "TokenggAVAX")))) {

/// @audit 1 commas
207:  		if (getBool(keccak256(abi.encodePacked("contract.paused", "TokenggAVAX")))) {

/// @audit 1 commas
217:  		if (getBool(keccak256(abi.encodePacked("contract.paused", "TokenggAVAX")))) {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L59

### [G&#x2011;17]  `>=` costs less gas than `>`
The compiler uses opcodes `GT` and `ISZERO` for solidity code that uses `>`, but only requires `LT` for `>=`, [which saves **3 gas**](https://gist.github.com/IllIllI000/3dc79d25acccfa16dee4e83ffdc6ffde)

*There are 2 instances of this issue:*

```solidity
File: contracts/contract/tokens/TokenggAVAX.sol

212:  		return assets > avail ? avail : assets;

222:  		return shares > avail ? avail : shares;

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L212

### [G&#x2011;18]  Splitting `require()` statements that use `&&` saves gas
See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper by **3 gas**

*There is 1 instance of this issue:*

```solidity
File: contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol

154:  			require(recoveredAddress != address(0) && recoveredAddress == owner, "INVALID_SIGNER");

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L154

### [G&#x2011;19]  Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead
> When using elements that are smaller than 32 bytes, your contractâ€™s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html
Each operation involving a `uint8` costs an extra [**22-28 gas**](https://gist.github.com/IllIllI000/9388d20c70f9a4632eb3ca7836f54977) (depending on whether the other operand is also a variable of type `uint8`) as compared to ones involving `uint256`, due to the compiler having to clear the higher bits of the memory word before operating on the `uint8`, as well as the associated stack operations of doing so. Use a larger size then downcast where needed

*There are 3 instances of this issue:*

```solidity
File: contracts/contract/tokens/TokenggAVAX.sol

/// @audit uint32 rewardsCycleLength
76:   		rewardsCycleLength = 14 days;

/// @audit uint32 rewardsCycleEnd
78:   		rewardsCycleEnd = (block.timestamp.safeCastTo32() / rewardsCycleLength) * rewardsCycleLength;

/// @audit uint192 lastRewardsAmt
104:  		lastRewardsAmt = nextRewardsAmt.safeCastTo192();

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L76

### [G&#x2011;20]  Don't compare boolean expressions to boolean literals
`if (<x> == true)` => `if (<x>)`, `if (<x> == false)` => `if (!<x>)`

*There are 3 instances of this issue:*

```solidity
File: contracts/contract/BaseAbstract.sol

25:   		if (getBool(keccak256(abi.encodePacked("contract.exists", msg.sender))) == false) {

74:   		if (enabled == false) {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseAbstract.sol#L25

```solidity
File: contracts/contract/Storage.sol

29:   		if (booleanStorage[keccak256(abi.encodePacked("contract.exists", msg.sender))] == false && msg.sender != guardian) {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L29

### [G&#x2011;21]  Stack variable used as a cheaper cache for a state variable is only used once
If the variable is only accessed once, it's cheaper to use the state variable directly that one time, and save the **3 gas** the extra stack assignment would spend

*There is 1 instance of this issue:*

```solidity
File: contracts/contract/tokens/TokenggAVAX.sol

97:   		uint256 stakingTotalAssets_ = stakingTotalAssets;

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L97

### [G&#x2011;22]  Superfluous event fields
`block.timestamp` and `block.number` are added to event information by default so adding them manually wastes gas

*There is 1 instance of this issue:*

```solidity
File: contracts/contract/Oracle.sol

21:   	event GGPPriceUpdated(uint256 indexed price, uint256 timestamp);

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Oracle.sol#L21

### [G&#x2011;23]  Functions guaranteed to revert when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are 
`CALLVALUE`(2),`DUP1`(3),`ISZERO`(3),`PUSH2`(3),`JUMPI`(10),`PUSH1`(3),`DUP1`(3),`REVERT`(0),`JUMPDEST`(1),`POP`(2), which costs an average of about **21 gas per call** to the function, in addition to the extra deployment cost

*There are 63 instances of this issue:*

```solidity
File: contracts/contract/BaseUpgradeable.sol

10:   	function __BaseUpgradeable_init(Storage gogoStorageAddress) internal onlyInitializing {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseUpgradeable.sol#L10

```solidity
File: contracts/contract/ClaimNodeOp.sol

40:   	function setRewardsCycleTotal(uint256 amount) public onlySpecificRegisteredContract("RewardsPool", msg.sender) {

56:   	function calculateAndDistributeRewards(address stakerAddr, uint256 totalEligibleGGPStaked) external onlyMultisig {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimNodeOp.sol#L40

```solidity
File: contracts/contract/ClaimProtocolDAO.sol

20    	function spend(
21    		string memory invoiceID,
22    		address recipientAddress,
23    		uint256 amount
24:   	) external onlyGuardian {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimProtocolDAO.sol#L20-L24

```solidity
File: contracts/contract/MultisigManager.sol

35:   	function registerMultisig(address addr) external onlyGuardian {

55:   	function enableMultisig(address addr) external onlyGuardian {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MultisigManager.sol#L35

```solidity
File: contracts/contract/Ocyticus.sol

27:   	function addDefender(address defender) external onlyGuardian {

32:   	function removeDefender(address defender) external onlyGuardian {

37:   	function pauseEverything() external onlyDefender {

47:   	function resumeEverything() external onlyDefender {

55:   	function disableAllMultisigs() public onlyDefender {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Ocyticus.sol#L27

```solidity
File: contracts/contract/Oracle.sol

28:   	function setOneInch(address addr) external onlyGuardian {

57:   	function setGGPPriceInAVAX(uint256 price, uint256 timestamp) external onlyMultisig {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Oracle.sol#L28

```solidity
File: contracts/contract/ProtocolDAO.sol

23:   	function initialize() external onlyGuardian {

67:   	function pauseContract(string memory contractName) public onlySpecificRegisteredContract("Ocyticus", msg.sender) {

73:   	function resumeContract(string memory contractName) public onlySpecificRegisteredContract("Ocyticus", msg.sender) {

96:   	function setTotalGGPCirculatingSupply(uint256 amount) public onlySpecificRegisteredContract("RewardsPool", msg.sender) {

107:  	function setClaimingContractPct(string memory claimingContract, uint256 decimal) public onlyGuardian valueNotGreaterThanOne(decimal) {

156:  	function setExpectedAVAXRewardsRate(uint256 rate) public onlyMultisig valueNotGreaterThanOne(rate) {

190:  	function registerContract(address addr, string memory name) public onlyGuardian {

198:  	function unregisterContract(address addr) public onlyGuardian {

209   	function upgradeExistingContract(
210   		address newAddr,
211   		string memory newName,
212   		address existingAddr
213:  	) external onlyGuardian {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ProtocolDAO.sol#L23

```solidity
File: contracts/contract/RewardsPool.sol

34:   	function initialize() external onlyGuardian {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L34

```solidity
File: contracts/contract/Staking.sol

110:  	function increaseAVAXStake(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

117:  	function decreaseAVAXStake(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

133:  	function increaseAVAXAssigned(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

140:  	function decreaseAVAXAssigned(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

156:  	function increaseAVAXAssignedHighWater(address stakerAddr, uint256 amount) public onlyRegisteredNetworkContract {

163:  	function resetAVAXAssignedHighWater(address stakerAddr) public onlyRegisteredNetworkContract {

180:  	function increaseMinipoolCount(address stakerAddr) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

187:  	function decreaseMinipoolCount(address stakerAddr) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

204:  	function setRewardsStartTime(address stakerAddr, uint256 time) public onlyRegisteredNetworkContract {

224:  	function increaseGGPRewards(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {

231:  	function decreaseGGPRewards(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {

248:  	function setLastRewardsCycleCompleted(address stakerAddr, uint256 cycleNumber) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {

328:  	function restakeGGP(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {

379:  	function slashGGP(address stakerAddr, uint256 ggpAmt) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L110

```solidity
File: contracts/contract/Storage.sol

104:  	function setAddress(bytes32 key, address value) external onlyRegisteredNetworkContract {

108:  	function setBool(bytes32 key, bool value) external onlyRegisteredNetworkContract {

112:  	function setBytes(bytes32 key, bytes calldata value) external onlyRegisteredNetworkContract {

116:  	function setBytes32(bytes32 key, bytes32 value) external onlyRegisteredNetworkContract {

120:  	function setInt(bytes32 key, int256 value) external onlyRegisteredNetworkContract {

124:  	function setString(bytes32 key, string calldata value) external onlyRegisteredNetworkContract {

128:  	function setUint(bytes32 key, uint256 value) external onlyRegisteredNetworkContract {

136:  	function deleteAddress(bytes32 key) external onlyRegisteredNetworkContract {

140:  	function deleteBool(bytes32 key) external onlyRegisteredNetworkContract {

144:  	function deleteBytes(bytes32 key) external onlyRegisteredNetworkContract {

148:  	function deleteBytes32(bytes32 key) external onlyRegisteredNetworkContract {

152:  	function deleteInt(bytes32 key) external onlyRegisteredNetworkContract {

156:  	function deleteString(bytes32 key) external onlyRegisteredNetworkContract {

160:  	function deleteUint(bytes32 key) external onlyRegisteredNetworkContract {

170:  	function addUint(bytes32 key, uint256 amount) external onlyRegisteredNetworkContract {

176:  	function subUint(bytes32 key, uint256 amount) external onlyRegisteredNetworkContract {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L104

```solidity
File: contracts/contract/tokens/TokenggAVAX.sol

153:  	function withdrawForStaking(uint256 assets) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

255:  	function _authorizeUpgrade(address newImplementation) internal override onlyGuardian {}

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L153

```solidity
File: contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol

53    	function __ERC20Upgradeable_init(
54    		string memory _name,
55    		string memory _symbol,
56    		uint8 _decimals
57:   	) internal onlyInitializing {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L53-L57

```solidity
File: contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol

29    	function __ERC4626Upgradeable_init(
30    		ERC20 _asset,
31    		string memory _name,
32    		string memory _symbol
33:   	) internal onlyInitializing {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L29-L33

```solidity
File: contracts/contract/Vault.sol

61:   	function withdrawAVAX(uint256 amount) external onlyRegisteredNetworkContract nonReentrant {

84:   	function transferAVAX(string memory toContractName, uint256 amount) external onlyRegisteredNetworkContract {

137   	function withdrawToken(
138   		address withdrawalAddress,
139   		ERC20 tokenAddress,
140   		uint256 amount
141:  	) external onlyRegisteredNetworkContract nonReentrant {

166   	function transferToken(
167   		string memory networkContractName,
168   		ERC20 tokenAddress,
169   		uint256 amount
170:  	) external onlyRegisteredNetworkContract {

204:  	function addAllowedToken(address tokenAddress) external onlyGuardian {

208:  	function removeAllowedToken(address tokenAddress) external onlyGuardian {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L61


___

## Excluded findings
These findings are excluded from awards calculations because there are publicly-available automated tools that find them. The valid ones appear here for completeness

## Summary

### Gas Optimizations
| |Issue|Instances|Total Gas Saved|
|-|:-|:-:|:-:|
| [G&#x2011;01] | Using `calldata` instead of `memory` for read-only arguments in `external` functions saves gas | 12 |  1440 |
| [G&#x2011;02] | State variables should be cached in stack variables rather than re-reading them from storage | 2 |  194 |
| [G&#x2011;03] | `<array>.length` should not be looked up in every loop of a `for`-loop | 1 |  3 |
| [G&#x2011;04] | Using `bool`s for storage incurs overhead | 3 |  51300 |
| [G&#x2011;05] | `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too) | 10 |  50 |
| [G&#x2011;06] | Using `private` rather than `public` for constants, saves gas | 1 |  - |
| [G&#x2011;07] | Division by two should use bit shifting | 1 |  20 |
| [G&#x2011;08] | Use custom errors rather than `revert()`/`require()` strings to save gas | 4 |  - |

Total: 34 instances over 8 issues with **53007 gas** saved

Gas totals use lower bounds of ranges and count two iterations of each `for`-loop. All values above are runtime, not deployment, values; deployment values are listed in the individual issue descriptions. The table above as well as its gas numbers do not include any of the excluded findings.





## Gas Optimizations

### [G&#x2011;01]  Using `calldata` instead of `memory` for read-only arguments in `external` functions saves gas
When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for-loop to copy each index of the `calldata` to the `memory` index. **Each iteration of this for-loop costs at least 60 gas** (i.e. `60 * <mem_array>.length`). Using `calldata` directly, obliviates the need for such a loop in the contract code and runtime execution. Note that even if an interface defines a function as having `memory` arguments, it's still valid for implementation contracs to use `calldata` arguments instead. 

If the array is passed to an `internal` function which passes the array to another internal function where the array is modified and therefore `memory` is used in the `external` call, it's still more gass-efficient to use `calldata` when the `external` function uses modifiers, since the modifiers may prevent the internal functions from being called. Structs have the same overhead as an array of length one

Note that I've also flagged instances where the function is `public` but can be marked as `external` since it's not called by the contract, and cases where a constructor is involved

*There are 12 instances of this issue:*

```solidity
File: contracts/contract/ClaimProtocolDAO.sol

/// @audit invoiceID - (valid but excluded finding)
20    	function spend(
21    		string memory invoiceID,
22    		address recipientAddress,
23    		uint256 amount
24:   	) external onlyGuardian {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimProtocolDAO.sol#L20-L24

```solidity
File: contracts/contract/ProtocolDAO.sol

/// @audit contractName - (valid but excluded finding)
61:   	function getContractPaused(string memory contractName) public view returns (bool) {

/// @audit contractName - (valid but excluded finding)
67:   	function pauseContract(string memory contractName) public onlySpecificRegisteredContract("Ocyticus", msg.sender) {

/// @audit contractName - (valid but excluded finding)
73:   	function resumeContract(string memory contractName) public onlySpecificRegisteredContract("Ocyticus", msg.sender) {

/// @audit claimingContract - (valid but excluded finding)
102:  	function getClaimingContractPct(string memory claimingContract) public view returns (uint256) {

/// @audit claimingContract - (valid but excluded finding)
107:  	function setClaimingContractPct(string memory claimingContract, uint256 decimal) public onlyGuardian valueNotGreaterThanOne(decimal) {

/// @audit newName - (valid but excluded finding)
209   	function upgradeExistingContract(
210   		address newAddr,
211   		string memory newName,
212   		address existingAddr
213:  	) external onlyGuardian {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ProtocolDAO.sol#L61

```solidity
File: contracts/contract/Vault.sol

/// @audit toContractName - (valid but excluded finding)
84:   	function transferAVAX(string memory toContractName, uint256 amount) external onlyRegisteredNetworkContract {

/// @audit networkContractName - (valid but excluded finding)
108   	function depositToken(
109   		string memory networkContractName,
110   		ERC20 tokenContract,
111   		uint256 amount
112:  	) external guardianOrRegisteredContract {

/// @audit networkContractName - (valid but excluded finding)
166   	function transferToken(
167   		string memory networkContractName,
168   		ERC20 tokenAddress,
169   		uint256 amount
170:  	) external onlyRegisteredNetworkContract {

/// @audit networkContractName - (valid but excluded finding)
193:  	function balanceOf(string memory networkContractName) external view returns (uint256) {

/// @audit networkContractName - (valid but excluded finding)
200:  	function balanceOfToken(string memory networkContractName, ERC20 tokenAddress) external view returns (uint256) {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L84

### [G&#x2011;02]  State variables should be cached in stack variables rather than re-reading them from storage
The instances below point to the second+ access of a state variable within a function. Caching of a state variable replaces each Gwarmaccess (**100 gas**) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

*There are 2 instances of this issue:*

```solidity
File: contracts/contract/Storage.sol

/// @audit newGuardian on line 57 - (valid but excluded finding)
63:   		guardian = newGuardian;

/// @audit guardian on line 63 - (valid but excluded finding)
65:   		emit GuardianChanged(oldGuardian, guardian);

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L63

### [G&#x2011;03]  `<array>.length` should not be looked up in every loop of a `for`-loop
The overheads outlined below are _PER LOOP_, excluding the first loop
* storage arrays incur a Gwarmaccess (**100 gas**)
* memory arrays use `MLOAD` (**3 gas**)
* calldata arrays use `CALLDATALOAD` (**3 gas**)

Caching the length changes each of these to a `DUP<N>` (**3 gas**), and gets rid of the extra `DUP<N>` needed to store the stack offset

*There is 1 instance of this issue:*

```solidity
File: contracts/contract/RewardsPool.sol

/// @audit (valid but excluded finding)
230:  		for (uint256 i = 0; i < enabledMultisigs.length; i++) {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L230

### [G&#x2011;04]  Using `bool`s for storage incurs overhead
```solidity
    // Booleans are more expensive than uint256 or any type that takes up a full
    // word because each write operation emits an extra SLOAD to first read the
    // slot's contents, replace the bits taken up by the boolean, and then write
    // back. This is the compiler's defense against contract upgrades and
    // pointer aliasing, and it cannot be disabled.
```
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27
Use `uint256(1)` and `uint256(2)` for true/false to avoid a Gwarmaccess (**[100 gas](https://gist.github.com/IllIllI000/1b70014db712f8572a72378321250058)**) for the extra SLOAD, and to avoid Gsset (**20000 gas**) when changing from `false` to `true`, after having been `true` in the past

*There are 3 instances of this issue:*

```solidity
File: contracts/contract/Ocyticus.sol

/// @audit (valid but excluded finding)
13:   	mapping(address => bool) public defenders;

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Ocyticus.sol#L13

```solidity
File: contracts/contract/Storage.sol

/// @audit (valid but excluded finding)
16:   	mapping(bytes32 => bool) private booleanStorage;

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L16

```solidity
File: contracts/contract/Vault.sol

/// @audit (valid but excluded finding)
39:   	mapping(address => bool) private allowedTokens;

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L39

### [G&#x2011;05]  `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too)
Saves **5 gas per loop**

*There are 10 instances of this issue:*

```solidity
File: contracts/contract/MinipoolManager.sol

/// @audit (valid but excluded finding)
619:  		for (uint256 i = offset; i < max; i++) {

/// @audit (valid but excluded finding)
623:  				total++;

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L619

```solidity
File: contracts/contract/MultisigManager.sol

/// @audit (valid but excluded finding)
84:   		for (uint256 i = 0; i < total; i++) {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MultisigManager.sol#L84

```solidity
File: contracts/contract/Ocyticus.sol

/// @audit (valid but excluded finding)
61:   		for (uint256 i = 0; i < count; i++) {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Ocyticus.sol#L61

```solidity
File: contracts/contract/RewardsPool.sol

/// @audit (valid but excluded finding)
74:   		for (uint256 i = 0; i < inflationIntervalsElapsed; i++) {

/// @audit (valid but excluded finding)
215:  		for (uint256 i = 0; i < count; i++) {

/// @audit (valid but excluded finding)
219:  				enabledCount++;

/// @audit (valid but excluded finding)
230:  		for (uint256 i = 0; i < enabledMultisigs.length; i++) {

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L74

```solidity
File: contracts/contract/Staking.sol

/// @audit (valid but excluded finding)
428:  		for (uint256 i = offset; i < max; i++) {

/// @audit (valid but excluded finding)
431:  			total++;

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L428

### [G&#x2011;06]  Using `private` rather than `public` for constants, saves gas
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that [returns a tuple](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPair.sol#L156-L178) of the values of all currently-public constants. Saves **3406-3606 gas** in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

*There is 1 instance of this issue:*

```solidity
File: contracts/contract/MultisigManager.sol

/// @audit (valid but excluded finding)
27:   	uint256 public constant MULTISIG_LIMIT = 10;

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MultisigManager.sol#L27

### [G&#x2011;07]  Division by two should use bit shifting
`<x> / 2` is the same as `<x> >> 1`. While the compiler uses the `SHR` opcode to accomplish both, the version that uses division incurs an overhead of [**20 gas**](https://gist.github.com/IllIllI000/ec0e4e6c4f52a6bca158f137a3afd4ff) due to `JUMP`s to and from a compiler utility function that introduces checks which can be avoided by using `unchecked {}` around the division by two

*There is 1 instance of this issue:*

```solidity
File: contracts/contract/MinipoolManager.sol

/// @audit (valid but excluded finding)
413:  		uint256 avaxHalfRewards = avaxTotalRewardAmt / 2;

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L413

### [G&#x2011;08]  Use custom errors rather than `revert()`/`require()` strings to save gas
Custom errors are available from solidity version 0.8.4. Custom errors save [**~50 gas**](https://gist.github.com/IllIllI000/ad1bd0d29a0101b25e57c293b4b0c746) each time they're hit by [avoiding having to allocate and store the revert string](https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth). Not defining the strings also save deployment gas

*There are 4 instances of this issue:*

```solidity
File: contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol

/// @audit (valid but excluded finding)
127:  		require(deadline >= block.timestamp, "PERMIT_DEADLINE_EXPIRED");

/// @audit (valid but excluded finding)
154:  			require(recoveredAddress != address(0) && recoveredAddress == owner, "INVALID_SIGNER");

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L127

```solidity
File: contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol

/// @audit (valid but excluded finding)
44:   		require((shares = previewDeposit(assets)) != 0, "ZERO_SHARES");

/// @audit (valid but excluded finding)
103:  		require((assets = previewRedeem(shares)) != 0, "ZERO_ASSETS");

```
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L44
