# QA Report
## Finding Summary
||Issue|Instances|
|-|-|-|
|[NC-01]|Long Lines (> 120 Characters)|38|
|[NC-02]|Inconsistent Trailing `.`|37|
|[NC-03]|Tabs Used Instead of 4 Spaces|18|
|[NC-04]|Spelling Mistakes / Grammar|13|
|[NC-05]|Contract Layout Voids Solidity Docs|8|
|[NC-06]|Order of Functions Not Compliant With Solidity Docs|7|
|[NC-07]|Contracts Missing `@title` NatSpec Tag|7|
|[NC-08]|Inconsistent Capitalization in Comments|7|
|[NC-09]|Inconsistent Comment Headers|4|
|[NC-10]|`TODO` Left In Production Code|3|
|[NC-11]|Commented Code Left In Production Code|2|
|[NC-12]|Underscore Notation Not Used Consistently|1|
|[NC-13]|Extra `,` in Comment|1|
|[NC-14]|Odd 4 Tab Indented Comment|1|
|[NC-15]|Inconsistant Zero Address Identifier|1|
|[NC-16]|Inconsistant `minipoolIndex` Naming|1|
|[NC-17]|Inconsistant `stakerIndex` Naming|1|
|[NC-18]|Confusing Variable Name: `avaxAssignedHighWater`|1|

||Issue|Instances|
|-|-|-|
|[L-01]|Liberal Use of Abbreviations / Inconsistent Use of Abbreviations|12|
|[L-02]|Comment Spec Disagrees With Code|1|
|[L-03]|The Zero Address Can Take Ownership of `guardian`|1|

### [NC-01] Long Lines (> 120 Characters)

Lines with greater length than 120 characters are used. The [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-lengthhttps://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-length) suggests that all lines should be 120 characters or less in width.

#### Findings:

*/contracts/contract/BaseAbstract.sol*
Links: [73](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/BaseAbstract.sol#L73).
```solidity
73:		bool enabled = (addr != address(0)) && getBool(keccak256(abi.encodePacked("multisig.item", multisigIndex, ".enabled")));
```

*/contracts/contract/MinipoolManager.sol*
Links: [53](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L53), [191](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L191), [285](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L285), [294](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L294), [317](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L317), [321](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L321), [329](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L329), [371](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L371), [399](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L399), [417](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L417), [421](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L421), [432](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L432), [490](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L490), [598](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L598).
```solidity
53:	minipool.item<index>.ggpSlashAmt = amt of ggp bond that was slashed if necessary (expected reward amt = avaxLiquidStakerAmt * x%/yr / ggpPriceInAvax)
191:	/// @notice Accept AVAX deposit from node operator to create a Minipool. Node Operator must be staking GGP. Open to public.
285:	/// @notice Withdraw function for a Node Operator to claim all AVAX funds they are due (original AVAX staked, plus any AVAX rewards)
294:		uint256 avaxNodeOpRewardAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpRewardAmt")));
317:		uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));
321:	/// @notice Removes the AVAX associated with a minipool from the protocol to stake it on Avalanche and register the node as a validator
329:		uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));
371:		uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));
399:		uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));
417:		uint256 avaxLiquidStakerRewardAmt = avaxHalfRewards - avaxHalfRewards.mulWadDown(dao.getMinipoolNodeCommissionFeePct());
421:		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerRewardAmt")), avaxLiquidStakerRewardAmt);
432:		ggAVAX.depositFromStaking{value: avaxLiquidStakerAmt + avaxLiquidStakerRewardAmt}(avaxLiquidStakerAmt, avaxLiquidStakerRewardAmt);
490:		uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));
598:		mp.avaxLiquidStakerRewardAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxLiquidStakerRewardAmt")));
```

*/contracts/contract/MultisigManager.sol*
Links: [67](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MultisigManager.sol#L67).
```Solidity
67:	/// @dev this will prevent the multisig from completing validations. The minipool will need to be manually reassigned to a new multisig
```

*/contracts/contract/ProtocolDAO.sol*
Links: [33](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ProtocolDAO.sol#L33), [41](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ProtocolDAO.sol#L41), [96](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ProtocolDAO.sol#L96), [107](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ProtocolDAO.sol#L107).
```Solidity
33:		setUint(keccak256("ProtocolDAO.RewardsCycleSeconds"), 28 days); // The time in which a claim period will span in seconds - 28 days by default
41:		setUint(keccak256("ProtocolDAO.InflationIntervalRate"), 1000133680617113500); // 5% annual calculated on a daily interval - Calculate in js example: let dailyInflation = web3.utils.toBN((1 + 0.05) ** (1 / (365)) * 1e18);
96:	function setTotalGGPCirculatingSupply(uint256 amount) public onlySpecificRegisteredContract("RewardsPool", msg.sender) {
107:	function setClaimingContractPct(string memory claimingContract, uint256 decimal) public onlyGuardian valueNotGreaterThanOne(decimal) {
```

*/contracts/contract/RewardsPool.sol*
Links: [174](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/RewardsPool.sol#L174).
```Solidity
174:		if (daoClaimContractAllotment + nopClaimContractAllotment + multisigClaimContractAllotment > getRewardsCycleTotalAmt()) {
```

*/contracts/contract/Staking.sol*
Links: [110](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L110), [117](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L117), [133](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L133), [140](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L140), [180](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L180), [187](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L187), [203](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L203), [224](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L224), [231](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L231), [248](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L248), [328](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L328), [379](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L379), [413](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L413).
```Solidity
110:	function increaseAVAXStake(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
117:	function decreaseAVAXStake(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
133:	function increaseAVAXAssigned(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
140:	function decreaseAVAXAssigned(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
180:	function increaseMinipoolCount(address stakerAddr) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
187:	function decreaseMinipoolCount(address stakerAddr) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
203:	// TODO cant use onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) since we also call from increaseMinipoolCount. Wat do?
224:	function increaseGGPRewards(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {
231:	function decreaseGGPRewards(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {
248:	function setLastRewardsCycleCompleted(address stakerAddr, uint256 cycleNumber) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {
328:	function restakeGGP(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {
379:	function slashGGP(address stakerAddr, uint256 ggpAmt) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
413:		staker.lastRewardsCycleCompleted = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".lastRewardsCycleCompleted")));
```

*/contracts/contract/Vault.sol*
Links: [104](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Vault.sol#L104).
```Solidity
104:	/// @dev (saves a large amount of gas this way through not needing a double token transfer via a network contract first)
```

*/contracts/contract/tokens/TokenggAVAX.sol*
Links: [99](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L99), [142](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L142).
```Solidity
99:			uint256 nextRewardsAmt = (asset.balanceOf(address(this)) + stakingTotalAssets_) - totalReleasedAssets_ - lastRewardsAmt_;
142:		function depositFromStaking(uint256 baseAmt, uint256 rewardAmt) public payable onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
```

*/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol*
Links: [21](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L21).
```Solidity
21:		event Withdraw(address indexed caller, address indexed receiver, address indexed owner, uint256 assets, uint256 shares);
```

### [NC-02] Inconsistent Trailing `.`

Some comments end with a `.` while most do not in the codebase (except used to seperate sentences - some also void this). It is best to keep a consistant style. **Suggestion:** remove all trailing `.` if not used to indicate sentence seperation.

#### Findings:

*/contracts/contract/MinipoolManager.sol*
[L249](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L249) does not end with a `.` unlike most multi-sentence comments (EX. [L45](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol), [L191](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L191), [310](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L310), ...). 
links: [249](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L249), [323](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L323), [423](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L423), [668](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L668).
```solidity
249:	// The minipoolIndex is stored 1 greater than actual value. The 1 is subtracted in getIndexOf()
232:	/// @dev Rialto calls this to initiate registering a minipool for staking and validation of the P-chain.
423:	// No rewards means validation period failed, must slash node ops GGP.
668:	/// @dev Extracted this because of "stack too deep" errors.
```

*/contracts/contract/MultisigManager.sol*
[L67](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L67) & [L108](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L108) do not end with a `.` unlike most multi-sentence comments. 
Links: [9](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L9), [47](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L47), [67](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L67), [108](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L108).
```solidity
9:	multisig.count = Starts at 0 and counts up by 1 after an addr is added.
47:	// The index is stored 1 greater than the actual value. The 1 is subtracted in getIndexOf().
67:	/// @dev this will prevent the multisig from completing validations. The minipool will need to be manually reassigned to a new multisig
108:	/// @return addr and enabled. The address and the enabled status of the multisig
```

*/contracts/contract/Oracle.sol*
Links: [32](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Oracle.sol#L32).
```solidity
32:	/// @notice Get an aggregated price from the 1Inch contract.
```

*/contracts/contract/Staking.sol*
[L283](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L283) does not end with a `.` unlike most multi-sentence comments. 
Links: [19](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L19), [283](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L283).
```solidity
19:	staker.count = Starts at 0 and counts up by 1 after a staker is added.
283:	///         on up to 150% collat ratio
```

*/contracts/contract/tokens/TokenggAVAX.sol*
Links: [49](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L49), [77](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L77), [87](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L87), [101](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L101), [168](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L168), [181](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L181), [192](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L192).
```solidity
49:	/// @notice the amount of rewards distributed in a the most recent cycle.
77:	// Ensure it will be evenly divisible by `rewardsCycleLength`.
87:	/// 				All surplus `asset` balance of the contract over the internal balance becomes queued for the next cycle.
101:	// Ensure nextRewardsCycleEnd will be evenly divisible by `rewardsCycleLength`.
168:	// Check for rounding error since we round down in previewDeposit.
181:	shares = previewWithdraw(assets); // No need to check for rounding error, previewWithdraw rounds up.
192:	// Check for rounding error since we round down in previewRedeem.
```

*/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol*
As the contract was written by another individual the comment style disagrees with that of GoGoPool. All standard comments end with a `.` (7 occurrences). 

*/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol*
As the contract was written by another individual the comment style disagrees with that of GoGoPool. All standard comments end with a `.` (12 occurrences). 

### [NC-03] Tabs Used Instead of 4 Spaces

According to the [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#tabs-or-spaces), "Spaces are the preferred indentation method" although not forced unless mixed with space indentation. There are a few places where 4 spaces are used. Consider changing all indentation to space indentation (preffered) or at the least keep the same indentation style (change the few space indents, not preferred).

#### Findings:

The following contracts are indented with tabs:
1. [Base.sol](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Base.sol)
2. [BaseUpgradeable.sol](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/BaseUpgradeable.sol)
3. [BaseAbstract.sol](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/BaseAbstract.sol)
4. [ClaimNodeOp.sol](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ClaimNodeOp.sol)
5. [ClaimProtocolDAO.sol](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ClaimProtocolDAO.sol)
6. [MinipoolManager.sol](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol)
7. [MultisigManager.sol](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MultisigManager.sol)
8. [Ocyticus.sol](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Ocyticus.sol)
9. [Oracle.sol](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Oracle.sol)
10. [ProtocolDAO.sol](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ProtocolDAO.sol)
11. [RewardsPool.sol](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/RewardsPool.sol)
12. [Staking.sol](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol)
13. [Storage.sol](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol)
14. [Vault.sol](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Vault.sol)
15. [TokenGGP.sol](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenGGP.sol)
16. [TokenggAVAX.sol](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol)
17. [ERC20Upgradeable.sol](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol)
18. [ERC4626Upgradeable.sol](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol)

The following lines use 4 space indentation:

*/contracts/contract/Staking.sol*

```solidity
282:	///         based on current GGP price and AVAX high water mark. A staker can earn GGP rewards
283:	///         on up to 150% collat ratio
```

*/contracts/contract/tokens/TokenggAVAX.sol*

```solidity
112:	///         Increases linearly during a reward distribution period from the sync call, not the cycle start.
```

*/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol*
All headers in the following style use space indentation for their respected title and end comment line: [12](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L12), [13](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L13), [20](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L20), [21](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L21), [30](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L30), [31](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L31), [40](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L40), [41](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L41), [50](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L50), [51](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L51), [67](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L67), [68](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L68), [115](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L115), [116](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L116), [180](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L180), [181](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L181).

### [NC-04] Spelling Mistakes / Grammar

There are some spelling / grammar mistakes throughout the codebase. Consider fixing all spelling / grammar mistakes.

#### Findings:

*/contracts/contract/MinipoolManager.sol*

* The word `validator` is misspelled as `validater` ([347](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L347)).
* The word `received` is misspelled as `recieved` ([383](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L383)).
* The word `occurred` is misspelled as `occured` ([480](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L480)).
* `dont` is used instead of  `do not` or  `don't` ([643](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L643)).

*/contracts/contract/Ocyticus.sol*

* The word `separately` is misspelled as `seperately` ([46](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L46)).
* `dont` is used instead of  `do not` or  `don't` ([46](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L46)).

*/contracts/contract/ProtocolDAO.sol*

* The word `commission` is misspelled as `commision` ([134](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L134)).
* The word `registering` is misspelled as `registring` ([205](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L205)).

*/contracts/contract/Staking.sol*

* The word `adhere` is misspelled as `adhear` ([419](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L419)).

*/contracts/contract/Vault.sol*

* The word `bookkeeping` is misspelled as `bookeeping` ([81](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L81)).
* The word `funds` seems to be misspelled as `fucns` ([82](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L82)).
* The word `receive` is misspelled as `recieve` ([134](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L134)).
* The word `who's` is used instead of `whose` ([198](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L198)).

### [NC-05] Contract Layout Voids Solidity Docs

The [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-layout) suggests the following contract layout order: Type declarations, State variables, Events, Modifiers, Functions. Some contracts void the Solidity Style Guide by placing Events before State variables.

[MinipoolManager.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol): Events are placed before State variables.
[MultisigManager.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol): Events are placed before State variables.
[Staking.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol): Events are placed before State variables.
[Storage.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol): Events are placed before State variables.
[Vault.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol): Events are placed before State variables.
[TokenggAVAX.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol): Events are placed before State variables.
[ERC20Upgradeable.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol): Events are placed before State variables.
[ERC4626Upgradeable.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol): Events are placed before State variables.

### [NC-06] Order of Functions Not Compliant With Solidity Docs

The [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions) suggests the following function ordering:  constructor, receive function (if exists), fallback function (if exists), external, public, internal, private.

#### Findings:

The following contracts are not compliant (examples are only to prove the functions are out of order NOT a full description): 

[ClaimNodeOp.sol](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ClaimNodeOp.sol): external functions are positioned after public functions.
[MinipoolManager.sol](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol): external functions are positioned after private functions.
[ProtocolDAO.sol](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ProtocolDAO.sol): external functions are positioned after public functions.
[RewardsPool.sol](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/RewardsPool.sol): public functions are positioned after internal functions.
[Staking.sol](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol): public functions are positioned after internal functions.
[ERC20Upgradeable.sol](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol): public functions are positioned after internal functions.
[ERC4626Upgradeable.sol](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol): public functions are positioned after internal functions.

### [NC-07] Contracts Missing `@title` NatSpec Tag

7 out of 18 of the contracts in scope are missing a `@title` tag. Given that 11 contracts all have a `@title` tag consider adding one per the 7 remaining contracts.

#### Findings:

[Base.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Base.sol), [BaseUpgradeable.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseUpgradeable.sol), [ERC4626Upgradeable.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol), [ERC20Upgradeable.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol), [TokenGGP.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenGGP.sol), [TokenggAVAX.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol), and [Vault.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol) are missing a `@title` tag.

### [NC-08] Inconsistent Capitalization in Comments

Most plain comments in the codebase start with a space followed by a capitalized word but not all. It is best to keep a consistent style. **Suggestion:** change all non-capitilized comments to capitilized comments.

#### Findings: 

*/contracts/contract/ClaimNodeOp.sol*
Links: [80](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L80).
```solidity
80:	// check if their rewards time should be reset
```

*/contracts/contract/MinipoolManager.sol*
Links: [278](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L278).
```solidity
278:	// make sure they meet the wait period requirement
```

*/contracts/contract/RewardsPool.sol*
Links: [214](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L214).
```solidity
214:	// there should never be more than a few multisigs, so a loop should be fine here
```

*/contracts/contract/tokens/TokenggAVAX.sol*
Links: [121](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L121), [122](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L122), [126](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L126), [127](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L127).
```solidity
121:	// no rewards or rewards are fully unlocked
122:	// entire reward amount is available
126:	// rewards are not fully unlocked
127:	// return unlocked rewards and stored total
```

### [NC-09] Inconsistent Comment Headers

There are 4 different header comment styles used. They look as follows:

1.  Used by [MinipoolManager.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L109) & [Storage.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol).
```solidity
//
// GUARDS
//
```
2.  Used by [ProtocolDAO.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol).
```solidity
// *** Rewards Pool ***
```
3.  Used by [RewardsPool.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol) & [Staking.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol).
```solidity
/* INFLATION */
```
4. Losely followed by both [ERC20Upgradeable.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol) & [ERC4626Upgradeable.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol)
```solidity
/*//////////////////////////////////////////////////////////////
                            METADATA STORAGE
//////////////////////////////////////////////////////////////*/
```

Consider sticking to a single header style.

### [NC-10] `TODO` Left In Production Code

`TODO`s should not be in production code. Each `TODO` should either be discarded or implemented if needed prior to production.

#### Findings:

*/contracts/contract/BaseAbstract.sol*
Links: [6](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/BaseAbstract.sol#L6).
```solidity
6:	// TODO remove this when dev is complete
```

*/contracts/contract/MinipoolManager.sol*
Links: [412](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L412).
```solidity
412:		// TODO Revisit this logic if we ever allow unequal matched funds
```

*/main/contracts/contract/Staking.sol*
Links: [203](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L203).
```solidity
203:	// TODO cant use onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) since we also call from increaseMinipoolCount. Wat do?
```

### [NC-11] Commented Code Left In Production Code

As [L6](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L6) of [BaseAbstract.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol) suggests commented code meant to be removed should be removed prior to production. Consider removing all commented code.

#### Findings:
*/contracts/contract/BaseAbstract.sol*
Links: [7-8](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L7-L8).
```solidity
7:	// import {console} from "forge-std/console.sol";
8:	// import {format} from "sol-utils/format.sol";
```

### [NC-12] Underscore Notation Not Used Consistently

Consider using underscore notation to help with contract readability (Ex. `23453` -> `23_453`).

#### Findings:

*/contracts/contract/ProtocolDAO.sol*
Links: [41](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L41)
```solidity
41:	setUint(keccak256("ProtocolDAO.InflationIntervalRate"), 1000133680617113500);
```

### [NC-13] Extra `,` in Comment

The following comment has an extra `,` that appears not to belong. Consider removing the extra `,` to avoid confusion.

#### Findings:

*/contracts/contract/Vault.sol*
Links: [158](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L158).
```solidity
158:	// Withdraw to the withdrawal address, , safeTransfer will revert if it fails
```

### [NC-14] Odd 4 Tab Indented Comment

There is an odd 4 tabbed comment indentation that does not follow the style of other indentations (EX. [L112](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L112)). Consider only using one tab (or preferably comply with 4 space indentation in all).

#### Findings:

*/contracts/contract/tokens/TokenggAVAX.sol*
Links: [87](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L87).
```solidity
87:	/// 				All surplus `asset` balance of the contract over the internal balance becomes queued for the next cycle.
```

### [NC-15] Inconsistant Zero Address Identifier

The syntax `address(0)` (EX. [L73](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L73)) and `address(0x0)` are used. Consider sticking to a single syntax for the zero address. There are 7 occurrences of `address(0)` and only 1 occurrence of `address(0x0)` shown below.

#### Findings

*/contracts/contract/BaseAbstract.sol*
Links: [92](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L92).
```solidity
92:	if (contractAddress == address(0x0)) {
```

### [NC-16] Inconsistant `minipoolIndex` Naming

In almost all places where a minipool's index is being referenced the variable is called `minipoolIndex` (EX. [288](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L288), [314](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L314) ...). On [L276](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L276) the index for a minipool is referenced with the name `index` instead of `minipoolIndex`. Consider making all references to a minipool's index the same name (ideally `minipoolIndex`).

#### Findings:

*/contracts/contract/MinipoolManager.sol*
Links: [276](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L276).
```solidity
276:	int256 index = requireValidMinipool(nodeID);
```

### [NC-17] Inconsistant `stakerIndex` Naming

In almost all places where a staker's index is being referenced the variable is called `stakerIndex` (EX. [127](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L127), [134](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L134) ...). On [L388](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L388) the index for a staker is referenced with the name `index` instead of `stakerIndex`. Consider making all references to a staker's index the same name (ideally `stakerIndex`).

#### Findings:

*/contracts/contract/Staking.sol*
Links: [388](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L388).
```solidity
388:	int256 index = getIndexOf(stakerAddr);
```

### [NC-18] Confusing Variable Name: `avaxAssignedHighWater`

On [L49](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L49) of [Staking.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol) a struct element is named `avaxAssignedHighWater`. `avaxAssignedHighWater` may confuse readers when a simpler name like `avaxAssignedCap` or `avaxAssignedMax` could be used. Consider changing the name `avaxAssignedHighWater`.

### [L-01] Liberal Use of Abbreviations / Inconsistent Use of Abbreviations

There are times where words appear to be shortened which may interfere with readability and understanding. Especially when a more clear longer substitute does not make a line longer than 120 characters it is best to be as clear as possible. Abbreviations like `secs` for `seconds` ([L45](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L45)) or `msg` for `message` ([L48](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L48)) are common and should not be a problem. On the other hand, abbreviations like `recvd` may not instantly be recognized as `received` (assumed) which is only 3 more letters. 

Below are a few less common abbreviations used that may interfere with readability. These abbreviations should not make the lines overflow (> 120 characters).

*/contracts/contract/MinipoolManager.sol*

`xfer` for `transfer` (4 more characters).
Links: [384](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#384).
```solidity
384:	/// @dev Rialto will xfer back all staked avax + avax rewards. Also handles the slashing of node ops GGP bond.
```

`recvd` for `received` (3 more characters). The full word is also used elsewhere, which is inconsistent ([elsewhere](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L383)).
Links: [411](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L411), [442](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L442).
```solidity
411:	// Calculate rewards splits (these will all be zero if no rewards were recvd)
442:	/// @notice Re-stake a minipool, compounding all rewards recvd
```

*/contracts/contract/MultisigManager.sol*

`addr` for `address` (3 more characters). `addr` is easily recognized as `address`; however, both are used. Consider sticking to either `addr` or `address`. For example `address` is used on [L16](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L16) whereas `addr` is used on [L9](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L9). **Suggested change:** change all `addr` to `address`.
Links: [9](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L9).
```solidity
9:	multisig.count = Starts at 0 and counts up by 1 after an addr is added.
```

*/contracts/contract/Staking.sol*

`amt` for `amount` (3 more characters). `amt` and `amount` both are used (Ex. [L78](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L78)).
Links: [23](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L23), [24](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L24), [25](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L25), [26](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L26), [147](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L147).
```solidity
23:	staker.item<index>.ggpStaked = Total amt of GGP staked across all minipools
24:	staker.item<index>.avaxStaked = Total amt of AVAX staked across all minipools
25:	staker.item<index>.avaxAssigned = Total amt of liquid staker funds assigned across all minipools
26:	staker.item<index>.avaxAssignedHighWater = Highest amt of liquid staker funds assigned during a GGP rewards cycle
147:	/// @notice Largest total AVAX amt assigned to a staker during a rewards period
```

`collat` for `collateralization` (11 more characters). `collat` and `collateralization` are both used (Ex. [L281](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L281)).
Links: [272](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L272), [283](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L283).
```solidity
272:	// Infinite collat ratio
283:	///         on up to 150% collat ratio
```

`RP` for `RocketPool` (assumed). `RocketPool` is mentioned in full [elsewhere](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L16) (8 more characters).
Links: [433](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L433).
```solidity
433:	// Dirty hack to cut unused elements off end of return value (from RP)
```

### [L-02] Comment Spec Disagrees With Code

Consider making all comments exactly reflect implementation.

#### Findings:

*/contracts/contract/ClaimNodeOp.sol*

[L45](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L45) states that `isEligible` returns `if time in protocol (secs) > RewardsEligibilityMinSeconds`; however, the return checks `if time in protocol (secs) >= RewardsEligibilityMinSeconds`.

Links: [45](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L45)/[51](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L51).
```solidity
45:	/// @dev Eligiblity: time in protocol (secs) > RewardsEligibilityMinSeconds. Rialto will call this.
51:	return (rewardsStartTime != 0 && elapsedSecs >= dao.getRewardsEligibilityMinSeconds());
```

### [L-03] The Zero Address Can Take Ownership of `guardian`

In [Storage.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol) regardless of `guardian`'s value, the Zero Address can call `confirmGuardian` and set itself as `guardian` between `guardian` transfers (`newGuardian` = `address(0)`). `newGuardian` defaults to the Zero Address and due to [L64](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L64) each time a `guardian` is confirmed `newGuardian` is set back to the Zero Address. Therefore, whenever a `setGuardian` is not queueing up a new `guardian` (`newGuardian` != `address(0)`) the Zero Address can take ownership by becoming the `guardian`.

**Suggestion:** require that `msg.sender != address(0)` in `confirmGuardian`.