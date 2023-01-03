##### Gas Optimizations

Gas savings are estimated using the gas report of existing `forge test --gas-report --fuzz-seed 1337` tests (the sum of all deployment costs and the sum of the costs of calling methods) and may vary depending on the implementation of the fix.
**NOTE**: `withdrawAVAX` and `depositAVAX` excluded due to high volatility. They can be evaluated separately, for the specific fixes they affect.
**NOTE**: Sorted by average user savings

|       | Issue                                                                               | Instances | Estimated gas(deployments) | Estimated gas(min method call) | Estimated gas(avg method call) | Estimated gas(max method call) | forge snapshot (negative is better) |
| :---: | :---------------------------------------------------------------------------------- | :-------: | :------------------------: | :----------------------------: | :----------------------------: | :----------------------------: | :---------------------------------: |
| **1** | Unchecking arithmetics operations that can't underflow/overflow                     |    10     |           93 406           |             3 269              |             39 142             |            231 131             |              -273 492               |
| **2** | unnecessary caching                                                                 |    42     |          133 497           |             1 181              |             1 988              |             3 828              |               -29 660               |
| **3** | Use named returns whenever possible                                                 |     5     |          -10 022           |              440               |              721               |             1 110              |               -9 928                |
| **4** | Don't compare boolean expressions to boolean literals                               |     3     |           24 037           |              247               |              413               |              652               |               -5 035                |
| **5** | `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables              |     2     |           1 200            |               54               |              137               |              167               |               -2 680                |
| **6** | do not cache global varaibles                                                       |     2     |           13 231           |               23               |               34               |               41               |                -464                 |
| **7** | Duplicated require()/revert() checks should be refactored to a modifier or function |    10     |           74 952           |              -613              |              -187              |             -1 439             |               14 972                |
| **8** | Instead of using a modifier you can save gas using private/internal view functions  |    11     |         1 957 398          |             -5 424             |             -6 868             |             -8 054             |               50 619                |
|       | Overall gas Change                                                                  |    85     |   **2 274 105** (4,62%)    |       **1 954** (0,06%)        |       **37 304** (0,55%)       |       **230 123** (2,2%)       |              -255 082               |

**Total: 85 instances over 8 issues**

---

#### 1. **Unchecking arithmetics operations that can't underflow/overflow (10 instances)**

Deployment. Gas Saved: **93 406**

Minimum Method Call. Gas Saved: **3 269**

Average Method Call. Gas Saved: **39 142**

Maximum Method Call. Gas Saved: **231 131**

Overall gas change: **-273 492** (NaN%)

##### - contracts/contract/MinipoolManager.sol:[619](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L619), [623](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L623)

```diff
diff --git a/contracts/contract/MinipoolManager.sol b/contracts/contract/MinipoolManager.sol
index 8563580..4cc7642 100644
--- a/contracts/contract/MinipoolManager.sol
+++ b/contracts/contract/MinipoolManager.sol
@@ -616,11 +616,16 @@ contract MinipoolManager is Base, ReentrancyGuard, IWithdrawer {
  616, 616: 		}
  617, 617: 		minipools = new Minipool[](max - offset);
  618, 618: 		uint256 total = 0;
- 619     :-		for (uint256 i = offset; i < max; i++) {
+      619:+		for (uint256 i = offset; i < max; ) {
  620, 620: 			Minipool memory mp = getMinipool(int256(i));
  621, 621: 			if (mp.status == uint256(status)) {
  622, 622: 				minipools[total] = mp;
- 623     :-				total++;
+      623:+				unchecked {
+      624:+					total++;
+      625:+				}
+      626:+			}
+      627:+			unchecked {
+      628:+				i++;
  624, 629: 			}
  625, 630: 		}
  626, 631: 		// Dirty hack to cut unused elements off end of return value (from RP)
```

##### - contracts/contract/MultisigManager.sol:[84](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MultisigManager.sol#L84)

```diff
diff --git a/contracts/contract/MultisigManager.sol b/contracts/contract/MultisigManager.sol
index 06cb014..9e80436 100644
--- a/contracts/contract/MultisigManager.sol
+++ b/contracts/contract/MultisigManager.sol
@@ -81,11 +81,14 @@ contract MultisigManager is Base {
   81,  81: 		uint256 total = getUint(keccak256("multisig.count"));
   82,  82: 		address addr;
   83,  83: 		bool enabled;
-  84     :-		for (uint256 i = 0; i < total; i++) {
+       84:+		for (uint256 i = 0; i < total;) {
   85,  85: 			(addr, enabled) = getMultisig(i);
   86,  86: 			if (enabled) {
   87,  87: 				return addr;
   88,  88: 			}
+       89:+			unchecked {
+       90:+				i++;
+       91:+			}
   89,  92: 		}
   90,  93: 		revert NoEnabledMultisigFound();
   91,  94: 	}
```

##### - contracts/contract/Ocyticus.sol:[61](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Ocyticus.sol#L61)

```diff
diff --git a/contracts/contract/Ocyticus.sol b/contracts/contract/Ocyticus.sol
index 71c473b..6b5d733 100644
--- a/contracts/contract/Ocyticus.sol
+++ b/contracts/contract/Ocyticus.sol
@@ -58,11 +58,14 @@ contract Ocyticus is Base {
   58,  58: 
   59,  59: 		address addr;
   60,  60: 		bool enabled;
-  61     :-		for (uint256 i = 0; i < count; i++) {
+       61:+		for (uint256 i = 0; i < count;) {
   62,  62: 			(addr, enabled) = mm.getMultisig(i);
   63,  63: 			if (enabled) {
   64,  64: 				mm.disableMultisig(addr);
   65,  65: 			}
+       66:+			unchecked {
+       67:+				i++;
+       68:+			}
   66,  69: 		}
   67,  70: 	}
   68,  71: }
```

##### - contracts/contract/RewardsPool.sol:[74](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L74), [215](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L215), [219](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L219), [230](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L230)

```diff
diff --git a/contracts/contract/RewardsPool.sol b/contracts/contract/RewardsPool.sol
index 20ce6cb..5b873ee 100644
--- a/contracts/contract/RewardsPool.sol
+++ b/contracts/contract/RewardsPool.sol
@@ -71,8 +71,11 @@ contract RewardsPool is Base {
   71,  71: 		newTotalSupply = currentTotalSupply;
   72,  72: 
   73,  73: 		// Compute inflation for total inflation intervals elapsed
-  74     :-		for (uint256 i = 0; i < inflationIntervalsElapsed; i++) {
+       74:+		for (uint256 i = 0; i < inflationIntervalsElapsed;) {
   75,  75: 			newTotalSupply = newTotalSupply.mulWadDown(inflationRate);
+       76:+			unchecked {
+       77:+				i++;
+       78:+			}
   76,  79: 		}
   77,  80: 		return (currentTotalSupply, newTotalSupply);
   78,  81: 	}
@@ -212,11 +215,16 @@ contract RewardsPool is Base {
  212, 215: 		address[] memory enabledMultisigs = new address[](count);
  213, 216: 
  214, 217: 		// there should never be more than a few multisigs, so a loop should be fine here
- 215     :-		for (uint256 i = 0; i < count; i++) {
+      218:+		for (uint256 i = 0; i < count;) {
  216, 219: 			(address addr, bool enabled) = mm.getMultisig(i);
  217, 220: 			if (enabled) {
  218, 221: 				enabledMultisigs[enabledCount] = addr;
- 219     :-				enabledCount++;
+      222:+				unchecked {
+      223:+					enabledCount++;
+      224:+				}
+      225:+			}
+      226:+			unchecked {
+      227:+				i++;
  220, 228: 			}
  221, 229: 		}
  222, 230: 
@@ -227,8 +235,11 @@ contract RewardsPool is Base {
  227, 235: 		}
  228, 236: 
  229, 237: 		uint256 tokensPerMultisig = allotment / enabledCount;
- 230     :-		for (uint256 i = 0; i < enabledMultisigs.length; i++) {
+      238:+		for (uint256 i = 0; i < enabledMultisigs.length;) {
  231, 239: 			vault.withdrawToken(enabledMultisigs[i], ggp, tokensPerMultisig);
+      240:+			unchecked {
+      241:+				i++;
+      242:+			}
  232, 243: 		}
  233, 244: 	}
  234, 245: }
```

##### - contracts/contract/Staking.sol:[428](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L428), [431](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L431)

```diff
diff --git a/contracts/contract/Staking.sol b/contracts/contract/Staking.sol
index 611cce0..30699d3 100644
--- a/contracts/contract/Staking.sol
+++ b/contracts/contract/Staking.sol
@@ -425,10 +425,13 @@ contract Staking is Base {
  425, 425: 		}
  426, 426: 		stakers = new Staker[](max - offset);
  427, 427: 		uint256 total = 0;
- 428     :-		for (uint256 i = offset; i < max; i++) {
+      428:+		for (uint256 i = offset; i < max;) {
  429, 429: 			Staker memory s = getStaker(int256(i));
  430, 430: 			stakers[total] = s;
- 431     :-			total++;
+      431:+			unchecked {
+      432:+				total++;
+      433:+				i++;
+      434:+			}
  432, 435: 		}
  433, 436: 		// Dirty hack to cut unused elements off end of return value (from RP)
  434, 437: 		// solhint-disable-next-line no-inline-assembly
```

---

#### 2. **unnecessary caching (42 instances)**

Deployment. Gas Saved: **133 497**

Minimum Method Call. Gas Saved: **1 181**

Average Method Call. Gas Saved: **1 988**

Maximum Method Call. Gas Saved: **3 828**

Overall gas change: **-29 660** (NaN%)

Don't cache a variable for one use only

##### - contracts/contract/BaseAbstract.sol:[72](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseAbstract.sol#L72)

```diff
diff --git a/contracts/contract/BaseAbstract.sol b/contracts/contract/BaseAbstract.sol
index a10b65d..6ab0e37 100644
--- a/contracts/contract/BaseAbstract.sol
+++ b/contracts/contract/BaseAbstract.sol
@@ -69,9 +69,9 @@ abstract contract BaseAbstract {
   69,  69: 	/// @dev Verify caller is a valid multisig
   70,  70: 	modifier onlyMultisig() {
   71,  71: 		int256 multisigIndex = int256(getUint(keccak256(abi.encodePacked("multisig.index", msg.sender)))) - 1;
-  72     :-		address addr = getAddress(keccak256(abi.encodePacked("multisig.item", multisigIndex, ".address")));
-  73     :-		bool enabled = (addr != address(0)) && getBool(keccak256(abi.encodePacked("multisig.item", multisigIndex, ".enabled")));
-  74     :-		if (enabled == false) {
+       72:+		if (
+       73:+			getAddress(keccak256(abi.encodePacked("multisig.item", multisigIndex, ".address"))) == address(0) || 
+       74:+			!getBool(keccak256(abi.encodePacked("multisig.item", multisigIndex, ".enabled")))) {
   75,  75: 			revert MustBeMultisig();
   76,  76: 		}
   77,  77: 		_;
```

##### - contracts/contract/ClaimNodeOp.sol:[48](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimNodeOp.sol#L48), [69](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimNodeOp.sol#L69), [81](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimNodeOp.sol#L81)

```diff
diff --git a/contracts/contract/ClaimNodeOp.sol b/contracts/contract/ClaimNodeOp.sol
index efcce70..9b31ffb 100644
--- a/contracts/contract/ClaimNodeOp.sol
+++ b/contracts/contract/ClaimNodeOp.sol
@@ -45,10 +45,9 @@ contract ClaimNodeOp is Base {
   45,  45: 	/// @dev Eligiblity: time in protocol (secs) > RewardsEligibilityMinSeconds. Rialto will call this.
   46,  46: 	function isEligible(address stakerAddr) external view returns (bool) {
   47,  47: 		Staking staking = Staking(getContractAddress("Staking"));
-  48     :-		uint256 rewardsStartTime = staking.getRewardsStartTime(stakerAddr);
-  49     :-		uint256 elapsedSecs = (block.timestamp - rewardsStartTime);
   50,  48: 		ProtocolDAO dao = ProtocolDAO(getContractAddress("ProtocolDAO"));
-  51     :-		return (rewardsStartTime != 0 && elapsedSecs >= dao.getRewardsEligibilityMinSeconds());
+       49:+		uint256 rewardsStartTime = staking.getRewardsStartTime(stakerAddr);
+       50:+		return (rewardsStartTime != 0 && (block.timestamp - rewardsStartTime) >= dao.getRewardsEligibilityMinSeconds());
   52,  51: 	}
   53,  52: 
   54,  53: 	/// @notice Set the share of rewards for a staker as a fraction of 1 ether
@@ -66,10 +65,8 @@ contract ClaimNodeOp is Base {
   66,  65: 			revert RewardsAlreadyDistributedToStaker(stakerAddr);
   67,  66: 		}
   68,  67: 		staking.setLastRewardsCycleCompleted(stakerAddr, rewardsPool.getRewardsCycleCount());
-  69     :-		uint256 ggpEffectiveStaked = staking.getEffectiveGGPStaked(stakerAddr);
-  70     :-		uint256 percentage = ggpEffectiveStaked.divWadDown(totalEligibleGGPStaked);
   71,  68: 		uint256 rewardsCycleTotal = getRewardsCycleTotal();
-  72     :-		uint256 rewardsAmt = percentage.mulWadDown(rewardsCycleTotal);
+       69:+		uint256 rewardsAmt = staking.getEffectiveGGPStaked(stakerAddr).divWadDown(totalEligibleGGPStaked).mulWadDown(rewardsCycleTotal);
   73,  70: 		if (rewardsAmt > rewardsCycleTotal) {
   74,  71: 			revert InvalidAmount();
   75,  72: 		}
@@ -78,8 +75,7 @@ contract ClaimNodeOp is Base {
   78,  75: 		staking.increaseGGPRewards(stakerAddr, rewardsAmt);
   79,  76: 
   80,  77: 		// check if their rewards time should be reset
-  81     :-		uint256 minipoolCount = staking.getMinipoolCount(stakerAddr);
-  82     :-		if (minipoolCount == 0) {
+       78:+		if (staking.getMinipoolCount(stakerAddr) == 0) {
   83,  79: 			staking.setRewardsStartTime(stakerAddr, 0);
   84,  80: 		}
   85,  81: 	}
```

##### - contracts/contract/MinipoolManager.sol:[153](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L153), [229](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L229), [294](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L294), [317](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L317), [341](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L341), [393](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L393), [559](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L559), [573](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L573), 

```diff
diff --git a/contracts/contract/MinipoolManager.sol b/contracts/contract/MinipoolManager.sol
index 8563580..64b25aa 100644
--- a/contracts/contract/MinipoolManager.sol
+++ b/contracts/contract/MinipoolManager.sol
@@ -150,8 +150,7 @@ contract MinipoolManager is Base, ReentrancyGuard, IWithdrawer {
  150, 150: 	/// @param minipoolIndex A valid minipool index
  151, 151: 	/// @param to New status
  152, 152: 	function requireValidStateTransition(int256 minipoolIndex, MinipoolStatus to) private view {
- 153     :-		bytes32 statusKey = keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status"));
- 154     :-		MinipoolStatus currentStatus = MinipoolStatus(getUint(statusKey));
+      153:+		MinipoolStatus currentStatus = MinipoolStatus(getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status"))));
  155, 154: 		bool isValid;
  156, 155: 
  157, 156: 		if (currentStatus == MinipoolStatus.Prelaunch) {
@@ -226,8 +225,7 @@ contract MinipoolManager is Base, ReentrancyGuard, IWithdrawer {
  226, 225: 			staking.setRewardsStartTime(msg.sender, block.timestamp);
  227, 226: 		}
  228, 227: 
- 229     :-		uint256 ratio = staking.getCollateralizationRatio(msg.sender);
- 230     :-		if (ratio < dao.getMinCollateralizationRatio()) {
+      228:+		if (staking.getCollateralizationRatio(msg.sender) < dao.getMinCollateralizationRatio()) {
  231, 229: 			revert InsufficientGGPCollateralization();
  232, 230: 		}
  233, 231: 
@@ -291,8 +289,7 @@ contract MinipoolManager is Base, ReentrancyGuard, IWithdrawer {
  291, 289: 		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Finished));
  292, 290: 
  293, 291: 		uint256 avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpAmt")));
- 294     :-		uint256 avaxNodeOpRewardAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpRewardAmt")));
- 295     :-		uint256 totalAvaxAmt = avaxNodeOpAmt + avaxNodeOpRewardAmt;
+      292:+		uint256 totalAvaxAmt = avaxNodeOpAmt + getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpRewardAmt")));
  296, 293: 
  297, 294: 		Staking staking = Staking(getContractAddress("Staking"));
  298, 295: 		staking.decreaseAVAXStake(owner, avaxNodeOpAmt);
@@ -314,8 +311,7 @@ contract MinipoolManager is Base, ReentrancyGuard, IWithdrawer {
  314, 311: 		int256 minipoolIndex = onlyValidMultisig(nodeID);
  315, 312: 		requireValidStateTransition(minipoolIndex, MinipoolStatus.Launched);
  316, 313: 
- 317     :-		uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));
- 318     :-		return avaxLiquidStakerAmt <= ggAVAX.amountAvailableForStaking();
+      314:+		return getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt"))) <= ggAVAX.amountAvailableForStaking();
  319, 315: 	}
  320, 316: 
  321, 317: 	/// @notice Removes the AVAX associated with a minipool from the protocol to stake it on Avalanche and register the node as a validator
@@ -338,8 +334,7 @@ contract MinipoolManager is Base, ReentrancyGuard, IWithdrawer {
  338, 334: 		Vault vault = Vault(getContractAddress("Vault"));
  339, 335: 		vault.withdrawAVAX(avaxNodeOpAmt);
  340, 336: 
- 341     :-		uint256 totalAvaxAmt = avaxNodeOpAmt + avaxLiquidStakerAmt;
- 342     :-		msg.sender.safeTransferETH(totalAvaxAmt);
+      337:+		msg.sender.safeTransferETH(avaxNodeOpAmt + avaxLiquidStakerAmt);
  343, 338: 	}
  344, 339: 
  345, 340: 	/// @notice Rialto calls this after successfully registering the minipool as a validator for Avalanche
@@ -390,8 +385,7 @@ contract MinipoolManager is Base, ReentrancyGuard, IWithdrawer {
  390, 385: 		int256 minipoolIndex = onlyValidMultisig(nodeID);
  391, 386: 		requireValidStateTransition(minipoolIndex, MinipoolStatus.Withdrawable);
  392, 387: 
- 393     :-		uint256 startTime = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".startTime")));
- 394     :-		if (endTime <= startTime || endTime > block.timestamp) {
+      388:+		if (endTime <= getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".startTime"))) || endTime > block.timestamp) {
  395, 389: 			revert InvalidEndTime();
  396, 390: 		}
  397, 391: 
@@ -556,8 +550,7 @@ contract MinipoolManager is Base, ReentrancyGuard, IWithdrawer {
  556, 550: 	/// @return The approximate rewards the node should recieve from Avalanche for beign a validator
  557, 551: 	function getExpectedAVAXRewardsAmt(uint256 duration, uint256 avaxAmt) public view returns (uint256) {
  558, 552: 		ProtocolDAO dao = ProtocolDAO(getContractAddress("ProtocolDAO"));
- 559     :-		uint256 rate = dao.getExpectedAVAXRewardsRate();
- 560     :-		return (avaxAmt.mulWadDown(rate) * duration) / 365 days;
+      553:+		return (avaxAmt.mulWadDown(dao.getExpectedAVAXRewardsRate()) * duration) / 365 days;
  561, 554: 	}
  562, 555: 
  563, 556: 	/// @notice The index of a minipool. Returns -1 if the minipool is not found
@@ -570,8 +563,7 @@ contract MinipoolManager is Base, ReentrancyGuard, IWithdrawer {
  570, 563: 	/// @notice Gets the minipool information from the node ID
  571, 564: 	/// @param nodeID 20-byte Avalanche node ID
  572, 565: 	function getMinipoolByNodeID(address nodeID) public view returns (Minipool memory mp) {
- 573     :-		int256 index = getIndexOf(nodeID);
- 574     :-		return getMinipool(index);
+      566:+		return getMinipool(getIndexOf(nodeID));
  575, 567: 	}
  576, 568: 
  577, 569: 	/// @notice Gets the minipool information using the minipool's index
```

##### - contracts/contract/MultisigManager.sol:[36](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MultisigManager.sol#L36)

```diff
diff --git a/contracts/contract/MultisigManager.sol b/contracts/contract/MultisigManager.sol
index 06cb014..05b3854 100644
--- a/contracts/contract/MultisigManager.sol
+++ b/contracts/contract/MultisigManager.sol
@@ -33,8 +33,7 @@ contract MultisigManager is Base {
   33,  33: 	/// @notice Register a multisig. Defaults to disabled when first registered.
   34,  34: 	/// @param addr Address of the multisig that is being registered
   35,  35: 	function registerMultisig(address addr) external onlyGuardian {
-  36     :-		int256 multisigIndex = getIndexOf(addr);
-  37     :-		if (multisigIndex != -1) {
+       36:+		if (getIndexOf(addr) != -1) {
   38,  37: 			revert MultisigAlreadyRegistered();
   39,  38: 		}
   40,  39: 		uint256 index = getUint(keccak256("multisig.count"));
```

##### - contracts/contract/Staking.sol:[67](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L67), [81](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L81), [88](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L88), [95](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L95), [104](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L104), [111](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L111), [118](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L118), [127](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L127), [134](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L134), [141](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L141), [150](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L150), [157](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L157), [174](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L174), [181](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L181), [188](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L188), [197](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L197), [205](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L205), [210](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L210), [218](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L218), [225](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L225), [232](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L232), [241](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L241), [249](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L249), [261](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L261), [277](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L277), [297](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L297), [312](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L312)

```diff
diff --git a/contracts/contract/Staking.sol b/contracts/contract/Staking.sol
index 611cce0..3b641cc 100644
--- a/contracts/contract/Staking.sol
+++ b/contracts/contract/Staking.sol
@@ -64,8 +64,7 @@ contract Staking is Base {
   64,  64: 
   65,  65: 	/// @notice Total GGP (stored in vault) assigned to this contract
   66,  66: 	function getTotalGGPStake() public view returns (uint256) {
-  67     :-		Vault vault = Vault(getContractAddress("Vault"));
-  68     :-		return vault.balanceOfToken("Staking", ggp);
+       67:+		return Vault(getContractAddress("Vault")).balanceOfToken("Staking", ggp);
   69,  68: 	}
   70,  69: 
   71,  70: 	/// @notice Total count of GGP stakers in the protocol
@@ -78,22 +77,19 @@ contract Staking is Base {
   78,  77: 	/// @notice The amount of GGP a given staker is staking
   79,  78: 	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
   80,  79: 	function getGGPStake(address stakerAddr) public view returns (uint256) {
-  81     :-		int256 stakerIndex = requireValidStaker(stakerAddr);
-  82     :-		return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpStaked")));
+       80:+		return getUint(keccak256(abi.encodePacked("staker.item", requireValidStaker(stakerAddr), ".ggpStaked")));
   83,  81: 	}
   84,  82: 
   85,  83: 	/// @notice Increase the amount of GGP a given staker is staking
   86,  84: 	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
   87,  85: 	function increaseGGPStake(address stakerAddr, uint256 amount) internal {
-  88     :-		int256 stakerIndex = requireValidStaker(stakerAddr);
-  89     :-		addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpStaked")), amount);
+       86:+		addUint(keccak256(abi.encodePacked("staker.item", requireValidStaker(stakerAddr), ".ggpStaked")), amount);
   90,  87: 	}
   91,  88: 
   92,  89: 	/// @notice Decrease the amount of GGP a given staker is staking
   93,  90: 	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
   94,  91: 	function decreaseGGPStake(address stakerAddr, uint256 amount) internal {
-  95     :-		int256 stakerIndex = requireValidStaker(stakerAddr);
-  96     :-		subUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpStaked")), amount);
+       92:+		subUint(keccak256(abi.encodePacked("staker.item", requireValidStaker(stakerAddr), ".ggpStaked")), amount);
   97,  93: 	}
   98,  94: 
   99,  95: 	/* AVAX STAKE */
@@ -101,22 +97,19 @@ contract Staking is Base {
  101,  97: 	/// @notice The amount of AVAX a given staker is staking
  102,  98: 	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
  103,  99: 	function getAVAXStake(address stakerAddr) public view returns (uint256) {
- 104     :-		int256 stakerIndex = requireValidStaker(stakerAddr);
- 105     :-		return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxStaked")));
+      100:+		return getUint(keccak256(abi.encodePacked("staker.item", requireValidStaker(stakerAddr), ".avaxStaked")));
  106, 101: 	}
  107, 102: 
  108, 103: 	/// @notice Increase the amount of AVAX a given staker is staking
  109, 104: 	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
  110, 105: 	function increaseAVAXStake(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
- 111     :-		int256 stakerIndex = requireValidStaker(stakerAddr);
- 112     :-		addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxStaked")), amount);
+      106:+		addUint(keccak256(abi.encodePacked("staker.item", requireValidStaker(stakerAddr), ".avaxStaked")), amount);
  113, 107: 	}
  114, 108: 
  115, 109: 	/// @notice Decrease the amount of AVAX a given staker is staking
  116, 110: 	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
  117, 111: 	function decreaseAVAXStake(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
- 118     :-		int256 stakerIndex = requireValidStaker(stakerAddr);
- 119     :-		subUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxStaked")), amount);
+      112:+		subUint(keccak256(abi.encodePacked("staker.item", requireValidStaker(stakerAddr), ".avaxStaked")), amount);
  120, 113: 	}
  121, 114: 
  122, 115: 	/* AVAX ASSIGNED */
@@ -124,22 +117,19 @@ contract Staking is Base {
  124, 117: 	/// @notice The amount of AVAX a given staker is assigned by the protocol (for minipool creation)
  125, 118: 	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
  126, 119: 	function getAVAXAssigned(address stakerAddr) public view returns (uint256) {
- 127     :-		int256 stakerIndex = getIndexOf(stakerAddr);
- 128     :-		return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssigned")));
+      120:+		return getUint(keccak256(abi.encodePacked("staker.item", getIndexOf(stakerAddr), ".avaxAssigned")));
  129, 121: 	}
  130, 122: 
  131, 123: 	/// @notice Increase the amount of AVAX a given staker is assigned by the protocol (for minipool creation)
  132, 124: 	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
  133, 125: 	function increaseAVAXAssigned(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
- 134     :-		int256 stakerIndex = requireValidStaker(stakerAddr);
- 135     :-		addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssigned")), amount);
+      126:+		addUint(keccak256(abi.encodePacked("staker.item", requireValidStaker(stakerAddr), ".avaxAssigned")), amount);
  136, 127: 	}
  137, 128: 
  138, 129: 	/// @notice Decrease the amount of AVAX a given staker is assigned by the protocol (for minipool creation)
  139, 130: 	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
  140, 131: 	function decreaseAVAXAssigned(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
- 141     :-		int256 stakerIndex = requireValidStaker(stakerAddr);
- 142     :-		subUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssigned")), amount);
+      132:+		subUint(keccak256(abi.encodePacked("staker.item", requireValidStaker(stakerAddr), ".avaxAssigned")), amount);
  143, 133: 	}
  144, 134: 
  145, 135: 	/* AVAX ASSIGNED HIGH-WATER */
@@ -147,15 +137,13 @@ contract Staking is Base {
  147, 137: 	/// @notice Largest total AVAX amt assigned to a staker during a rewards period
  148, 138: 	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
  149, 139: 	function getAVAXAssignedHighWater(address stakerAddr) public view returns (uint256) {
- 150     :-		int256 stakerIndex = getIndexOf(stakerAddr);
- 151     :-		return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssignedHighWater")));
+      140:+		return getUint(keccak256(abi.encodePacked("staker.item", getIndexOf(stakerAddr), ".avaxAssignedHighWater")));
  152, 141: 	}
  153, 142: 
  154, 143: 	/// @notice Increase the AVAXAssignedHighWater
  155, 144: 	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
  156, 145: 	function increaseAVAXAssignedHighWater(address stakerAddr, uint256 amount) public onlyRegisteredNetworkContract {
- 157     :-		int256 stakerIndex = requireValidStaker(stakerAddr);
- 158     :-		addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssignedHighWater")), amount);
+      146:+		addUint(keccak256(abi.encodePacked("staker.item", requireValidStaker(stakerAddr), ".avaxAssignedHighWater")), amount);
  159, 147: 	}
  160, 148: 
  161, 149: 	/// @notice Reset the AVAXAssignedHighWater to what the current AVAXAssigned is for the staker
@@ -171,22 +159,19 @@ contract Staking is Base {
  171, 159: 	/// @notice The number of minipools the given staker has
  172, 160: 	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
  173, 161: 	function getMinipoolCount(address stakerAddr) public view returns (uint256) {
- 174     :-		int256 stakerIndex = getIndexOf(stakerAddr);
- 175     :-		return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".minipoolCount")));
+      162:+		return getUint(keccak256(abi.encodePacked("staker.item", getIndexOf(stakerAddr), ".minipoolCount")));
  176, 163: 	}
  177, 164: 
  178, 165: 	/// @notice Increase the number of minipools the given staker has
  179, 166: 	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
  180, 167: 	function increaseMinipoolCount(address stakerAddr) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
- 181     :-		int256 stakerIndex = requireValidStaker(stakerAddr);
- 182     :-		addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".minipoolCount")), 1);
+      168:+		addUint(keccak256(abi.encodePacked("staker.item", requireValidStaker(stakerAddr), ".minipoolCount")), 1);
  183, 169: 	}
  184, 170: 
  185, 171: 	/// @notice Decrease the number of minipools the given staker has
  186, 172: 	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
  187, 173: 	function decreaseMinipoolCount(address stakerAddr) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
- 188     :-		int256 stakerIndex = requireValidStaker(stakerAddr);
- 189     :-		subUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".minipoolCount")), 1);
+      174:+		subUint(keccak256(abi.encodePacked("staker.item", requireValidStaker(stakerAddr), ".minipoolCount")), 1);
  190, 175: 	}
  191, 176: 
  192, 177: 	/* REWARDS START TIME */
@@ -194,20 +179,18 @@ contract Staking is Base {
  194, 179: 	/// @notice The timestamp when the staker registered for GGP rewards
  195, 180: 	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
  196, 181: 	function getRewardsStartTime(address stakerAddr) public view returns (uint256) {
- 197     :-		int256 stakerIndex = getIndexOf(stakerAddr);
- 198     :-		return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".rewardsStartTime")));
+      182:+		return getUint(keccak256(abi.encodePacked("staker.item", getIndexOf(stakerAddr), ".rewardsStartTime")));
  199, 183: 	}
  200, 184: 
  201, 185: 	/// @notice Set the timestamp when the staker registered for GGP rewards
  202, 186: 	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
  203, 187: 	// TODO cant use onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) since we also call from increaseMinipoolCount. Wat do?
  204, 188: 	function setRewardsStartTime(address stakerAddr, uint256 time) public onlyRegisteredNetworkContract {
- 205     :-		int256 stakerIndex = requireValidStaker(stakerAddr);
  206, 189: 		if (time > block.timestamp) {
  207, 190: 			revert InvalidRewardsStartTime();
  208, 191: 		}
  209, 192: 
- 210     :-		setUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".rewardsStartTime")), time);
+      193:+		setUint(keccak256(abi.encodePacked("staker.item", requireValidStaker(stakerAddr), ".rewardsStartTime")), time);
  211, 194: 	}
  212, 195: 
  213, 196: 	/* GGP REWARDS */
@@ -215,22 +198,19 @@ contract Staking is Base {
  215, 198: 	/// @notice The amount of GGP rewards the staker has earned and not claimed
  216, 199: 	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
  217, 200: 	function getGGPRewards(address stakerAddr) public view returns (uint256) {
- 218     :-		int256 stakerIndex = getIndexOf(stakerAddr);
- 219     :-		return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpRewards")));
+      201:+		return getUint(keccak256(abi.encodePacked("staker.item", getIndexOf(stakerAddr), ".ggpRewards")));
  220, 202: 	}
  221, 203: 
  222, 204: 	/// @notice Increase the amount of GGP rewards the staker has earned and not claimed
  223, 205: 	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
  224, 206: 	function increaseGGPRewards(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {
- 225     :-		int256 stakerIndex = requireValidStaker(stakerAddr);
- 226     :-		addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpRewards")), amount);
+      207:+		addUint(keccak256(abi.encodePacked("staker.item", requireValidStaker(stakerAddr), ".ggpRewards")), amount);
  227, 208: 	}
  228, 209: 
  229, 210: 	/// @notice Decrease the amount of GGP rewards the staker has earned and not claimed
  230, 211: 	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
  231, 212: 	function decreaseGGPRewards(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {
- 232     :-		int256 stakerIndex = requireValidStaker(stakerAddr);
- 233     :-		subUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpRewards")), amount);
+      213:+		subUint(keccak256(abi.encodePacked("staker.item", requireValidStaker(stakerAddr), ".ggpRewards")), amount);
  234, 214: 	}
  235, 215: 
  236, 216: 	/* LAST REWARDS CYCLE PAID OUT */
@@ -238,16 +218,14 @@ contract Staking is Base {
  238, 218: 	/// @notice The most recent reward cycle number that the staker has been paid out for
  239, 219: 	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
  240, 220: 	function getLastRewardsCycleCompleted(address stakerAddr) public view returns (uint256) {
- 241     :-		int256 stakerIndex = getIndexOf(stakerAddr);
- 242     :-		return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".lastRewardsCycleCompleted")));
+      221:+		return getUint(keccak256(abi.encodePacked("staker.item", getIndexOf(stakerAddr), ".lastRewardsCycleCompleted")));
  243, 222: 	}
  244, 223: 
  245, 224: 	/// @notice Set the most recent reward cycle number that the staker has been paid out for
  246, 225: 	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
  247, 226: 	/// @param cycleNumber The cycle that the staker was just rewarded for
  248, 227: 	function setLastRewardsCycleCompleted(address stakerAddr, uint256 cycleNumber) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {
- 249     :-		int256 stakerIndex = requireValidStaker(stakerAddr);
- 250     :-		setUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".lastRewardsCycleCompleted")), cycleNumber);
+      228:+		setUint(keccak256(abi.encodePacked("staker.item", requireValidStaker(stakerAddr), ".lastRewardsCycleCompleted")), cycleNumber);
  251, 229: 	}
  252, 230: 
  253, 231: 	/// @notice Get a stakers's minimum GGP stake to collateralize their minipools, based on current GGP price
@@ -258,9 +236,7 @@ contract Staking is Base {
  258, 236: 		Oracle oracle = Oracle(getContractAddress("Oracle"));
  259, 237: 		(uint256 ggpPriceInAvax, ) = oracle.getGGPPriceInAVAX();
  260, 238: 
- 261     :-		uint256 avaxAssigned = getAVAXAssigned(stakerAddr);
- 262     :-		uint256 ggp100pct = avaxAssigned.divWadDown(ggpPriceInAvax);
- 263     :-		return ggp100pct.mulWadDown(dao.getMinCollateralizationRatio());
+      239:+		return getAVAXAssigned(stakerAddr).divWadDown(ggpPriceInAvax).mulWadDown(dao.getMinCollateralizationRatio());
  264, 240: 	}
  265, 241: 
  266, 242: 	/// @notice Returns collateralization ratio based on current GGP price
@@ -274,8 +250,8 @@ contract Staking is Base {
  274, 250: 		}
  275, 251: 		Oracle oracle = Oracle(getContractAddress("Oracle"));
  276, 252: 		(uint256 ggpPriceInAvax, ) = oracle.getGGPPriceInAVAX();
- 277     :-		uint256 ggpStakedInAvax = getGGPStake(stakerAddr).mulWadDown(ggpPriceInAvax);
- 278     :-		return ggpStakedInAvax.divWadDown(avaxAssigned);
+      253:+
+      254:+		return getGGPStake(stakerAddr).mulWadDown(ggpPriceInAvax).divWadDown(avaxAssigned);
  279, 255: 	}
  280, 256: 
  281, 257: 	/// @notice Returns effective collateralization ratio which will be used to pay out rewards
@@ -294,8 +270,7 @@ contract Staking is Base {
  294, 270: 
  295, 271: 		Oracle oracle = Oracle(getContractAddress("Oracle"));
  296, 272: 		(uint256 ggpPriceInAvax, ) = oracle.getGGPPriceInAVAX();
- 297     :-		uint256 ggpStakedInAvax = getGGPStake(stakerAddr).mulWadDown(ggpPriceInAvax);
- 298     :-		uint256 ratio = ggpStakedInAvax.divWadDown(avaxAssignedHighWater);
+      273:+		uint256 ratio = getGGPStake(stakerAddr).mulWadDown(ggpPriceInAvax).divWadDown(avaxAssignedHighWater);
  299, 274: 
  300, 275: 		ProtocolDAO dao = ProtocolDAO(getContractAddress("ProtocolDAO"));
  301, 276: 		uint256 maxRatio = dao.getMaxCollateralizationRatio();
@@ -309,9 +284,7 @@ contract Staking is Base {
  309, 284: 		Oracle oracle = Oracle(getContractAddress("Oracle"));
  310, 285: 		(uint256 ggpPriceInAvax, ) = oracle.getGGPPriceInAVAX();
  311, 286: 
- 312     :-		uint256 avaxAssignedHighWater = getAVAXAssignedHighWater(stakerAddr);
- 313     :-		uint256 ratio = getEffectiveRewardsRatio(stakerAddr);
- 314     :-		return avaxAssignedHighWater.mulWadDown(ratio).divWadDown(ggpPriceInAvax);
+      287:+		return getAVAXAssignedHighWater(stakerAddr).mulWadDown(getEffectiveRewardsRatio(stakerAddr)).divWadDown(ggpPriceInAvax);
  315, 288: 	}
  316, 289: 
  317, 290: 	/// @notice Accept a GGP stake
```

##### - contracts/contract/tokens/TokenggAVAX.sol:[134](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L134), [138](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L138)

```diff
diff --git a/contracts/contract/tokens/TokenggAVAX.sol b/contracts/contract/tokens/TokenggAVAX.sol
index dc3948d..58c1f99 100644
--- a/contracts/contract/tokens/TokenggAVAX.sol
+++ b/contracts/contract/tokens/TokenggAVAX.sol
@@ -131,12 +131,10 @@ contract TokenggAVAX is Initializable, ERC4626Upgradeable, UUPSUpgradeable, Base
  131, 131: 
  132, 132: 	function amountAvailableForStaking() public view returns (uint256) {
  133, 133: 		ProtocolDAO protocolDAO = ProtocolDAO(getContractAddress("ProtocolDAO"));
- 134     :-		uint256 targetCollateralRate = protocolDAO.getTargetGGAVAXReserveRate();
  135, 134: 
  136, 135: 		uint256 totalAssets_ = totalAssets();
  137, 136: 
- 138     :-		uint256 reservedAssets = totalAssets_.mulDivDown(targetCollateralRate, 1 ether);
- 139     :-		return totalAssets_ - reservedAssets - stakingTotalAssets;
+      137:+		return totalAssets_ - totalAssets_.mulDivDown(protocolDAO.getTargetGGAVAXReserveRate(), 1 ether) - stakingTotalAssets;
  140, 138: 	}
  141, 139: 
  142, 140: 	function depositFromStaking(uint256 baseAmt, uint256 rewardAmt) public payable onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
```

---

#### 3. **Use named returns whenever possible (5 instancess)**

Deployment. Gas Saved: **-10 022**

Minimum Method Call. Gas Saved: **440**

Average Method Call. Gas Saved: **721**

Maximum Method Call. Gas Saved: **1 110**

Overall gas change: **-9 928 (NaN%)**

##### - contracts/contract/BaseAbstract.sol:[90](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseAbstract.sol#L90)

```diff
diff --git a/contracts/contract/BaseAbstract.sol b/contracts/contract/BaseAbstract.sol
index a10b65d..9bd05d4 100644
--- a/contracts/contract/BaseAbstract.sol
+++ b/contracts/contract/BaseAbstract.sol
@@ -87,12 +87,11 @@ abstract contract BaseAbstract {
   87,  87: 	}
   88,  88: 
   89,  89: 	/// @dev Get the address of a network contract by name
-  90     :-	function getContractAddress(string memory contractName) internal view returns (address) {
-  91     :-		address contractAddress = getAddress(keccak256(abi.encodePacked("contract.address", contractName)));
+       90:+	function getContractAddress(string memory contractName) internal view returns (address contractAddress) {
+       91:+		contractAddress = getAddress(keccak256(abi.encodePacked("contract.address", contractName)));
   92,  92: 		if (contractAddress == address(0x0)) {
   93,  93: 			revert ContractNotFound();
   94,  94: 		}
-  95     :-		return contractAddress;
   96,  95: 	}
   97,  96: 
   98,  97: 	/// @dev Get the name of a network contract by address
```

##### - contracts/contract/MinipoolManager.sol:[114](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L114), [126](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L126), [140](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L140)

```diff
diff --git a/contracts/contract/MinipoolManager.sol b/contracts/contract/MinipoolManager.sol
index 8563580..09ebd94 100644
--- a/contracts/contract/MinipoolManager.sol
+++ b/contracts/contract/MinipoolManager.sol
@@ -111,39 +111,35 @@ contract MinipoolManager is Base, ReentrancyGuard, IWithdrawer {
  111, 111: 
  112, 112: 	/// @notice Look up minipool owner by minipool index
  113, 113: 	/// @param minipoolIndex A valid minipool index
- 114     :-	/// @return minipool owner or revert
- 115     :-	function onlyOwner(int256 minipoolIndex) private view returns (address) {
- 116     :-		address owner = getAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".owner")));
+      114:+	/// @return owner or revert
+      115:+	function onlyOwner(int256 minipoolIndex) private view returns (address owner) {
+      116:+		owner = getAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".owner")));
  117, 117: 		if (msg.sender != owner) {
  118, 118: 			revert OnlyOwner();
  119, 119: 		}
- 120     :-		return owner;
  121, 120: 	}
  122, 121: 
  123, 122: 	/// @notice Verifies the multisig trying to use the given node ID is valid
  124, 123: 	/// @dev Look up multisig index by minipool nodeID
  125, 124: 	/// @param nodeID 20-byte Avalanche node ID
- 126     :-	/// @return minipool index or revert
- 127     :-	function onlyValidMultisig(address nodeID) private view returns (int256) {
- 128     :-		int256 minipoolIndex = requireValidMinipool(nodeID);
+      125:+	/// @return minipoolIndex or revert
+      126:+	function onlyValidMultisig(address nodeID) private view returns (int256 minipoolIndex) {
+      127:+		minipoolIndex = requireValidMinipool(nodeID);
  129, 128: 
  130, 129: 		address assignedMultisig = getAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".multisigAddr")));
  131, 130: 		if (msg.sender != assignedMultisig) {
  132, 131: 			revert InvalidMultisigAddress();
  133, 132: 		}
- 134     :-		return minipoolIndex;
  135, 133: 	}
  136, 134: 
  137, 135: 	/// @notice Look up minipool index by minipool nodeID
  138, 136: 	/// @param nodeID 20-byte Avalanche node ID
- 139     :-	/// @return minipool index or revert
- 140     :-	function requireValidMinipool(address nodeID) private view returns (int256) {
- 141     :-		int256 minipoolIndex = getIndexOf(nodeID);
+      137:+	/// @return minipoolIndex or revert
+      138:+	function requireValidMinipool(address nodeID) private view returns (int256 minipoolIndex) {
+      139:+		minipoolIndex = getIndexOf(nodeID);
  142, 140: 		if (minipoolIndex == -1) {
  143, 141: 			revert MinipoolNotFound();
  144, 142: 		}
- 145     :-
- 146     :-		return minipoolIndex;
  147, 143: 	}
  148, 144: 
  149, 145: 	/// @notice Ensure a minipool is allowed to move to the "to" state
```

##### - contracts/contract/RewardsPool.sol:[134](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L134)

```diff
diff --git a/contracts/contract/RewardsPool.sol b/contracts/contract/RewardsPool.sol
index 20ce6cb..d2bfaf4 100644
--- a/contracts/contract/RewardsPool.sol
+++ b/contracts/contract/RewardsPool.sol
@@ -130,20 +130,19 @@ contract RewardsPool is Base {
  130, 130: 
  131, 131: 	/// @notice Get the approx amount of GGP rewards owed for this cycle per claiming contract
  132, 132: 	/// @param claimingContract Name of the contract being claimed for
- 133     :-	/// @return GGP Rewards amount for current cycle per claiming contract
- 134     :-	function getClaimingContractDistribution(string memory claimingContract) public view returns (uint256) {
+      133:+	/// @return contractRewardsTotal GGP Rewards amount for current cycle per claiming contract
+      134:+	function getClaimingContractDistribution(string memory claimingContract) public view returns (uint256 contractRewardsTotal) {
  135, 135: 		ProtocolDAO dao = ProtocolDAO(getContractAddress("ProtocolDAO"));
  136, 136: 		uint256 claimContractPct = dao.getClaimingContractPct(claimingContract);
  137, 137: 		// How much rewards are available for this claim interval?
  138, 138: 		uint256 currentCycleRewardsTotal = getRewardsCycleTotalAmt();
  139, 139: 
  140, 140: 		// How much this claiming contract is entitled to in perc
- 141     :-		uint256 contractRewardsTotal = 0;
+      141:+		contractRewardsTotal = 0;
  142, 142: 		if (claimContractPct > 0 && currentCycleRewardsTotal > 0) {
  143, 143: 			// Calculate how much rewards this claimer will receive based on their claiming perc
  144, 144: 			contractRewardsTotal = claimContractPct.mulWadDown(currentCycleRewardsTotal);
  145, 145: 		}
- 146     :-		return contractRewardsTotal;
  147, 146: 	}
  148, 147: 
  149, 148: 	/// @notice Checking if enough time has passed since the last rewards cycle
```

---

#### 4. **Don't compare boolean expressions to boolean literals (3 instances)**

Deployment. Gas Saved: **24 037**

Minimum Method Call. Gas Saved: **247**

Average Method Call. Gas Saved: **413**

Maximum Method Call. Gas Saved: **652**

Overall gas change: **-5 035** (NaN%)

`if (<x> == true)` => `if (<x>)`, `if (<x> == false)` => `if (!<x>)`

##### - contracts/contract/BaseAbstract.sol:[25](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseAbstract.sol#L25), [74](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseAbstract.sol#L74)

```diff
diff --git a/contracts/contract/BaseAbstract.sol b/contracts/contract/BaseAbstract.sol
index a10b65d..824621b 100644
--- a/contracts/contract/BaseAbstract.sol
+++ b/contracts/contract/BaseAbstract.sol
@@ -22,7 +22,7 @@ abstract contract BaseAbstract {
   22,  22: 
   23,  23: 	/// @dev Verify caller is a registered network contract
   24,  24: 	modifier onlyRegisteredNetworkContract() {
-  25     :-		if (getBool(keccak256(abi.encodePacked("contract.exists", msg.sender))) == false) {
+       25:+		if (!getBool(keccak256(abi.encodePacked("contract.exists", msg.sender)))) {
   26,  26: 			revert InvalidOrOutdatedContract();
   27,  27: 		}
   28,  28: 		_;
@@ -71,7 +71,7 @@ abstract contract BaseAbstract {
   71,  71: 		int256 multisigIndex = int256(getUint(keccak256(abi.encodePacked("multisig.index", msg.sender)))) - 1;
   72,  72: 		address addr = getAddress(keccak256(abi.encodePacked("multisig.item", multisigIndex, ".address")));
   73,  73: 		bool enabled = (addr != address(0)) && getBool(keccak256(abi.encodePacked("multisig.item", multisigIndex, ".enabled")));
-  74     :-		if (enabled == false) {
+       74:+		if (!enabled) {
   75,  75: 			revert MustBeMultisig();
   76,  76: 		}
   77,  77: 		_;
```

##### - contracts/contract/Storage.sol:[29](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L29)

```diff
diff --git a/contracts/contract/Storage.sol b/contracts/contract/Storage.sol
index 218542f..c433636 100644
--- a/contracts/contract/Storage.sol
+++ b/contracts/contract/Storage.sol
@@ -26,7 +26,7 @@ contract Storage {
   26,  26: 
   27,  27: 	/// @dev Only allow access from guardian or the latest version of a contract in the GoGoPool network
   28,  28: 	modifier onlyRegisteredNetworkContract() {
-  29     :-		if (booleanStorage[keccak256(abi.encodePacked("contract.exists", msg.sender))] == false && msg.sender != guardian) {
+       29:+		if (!booleanStorage[keccak256(abi.encodePacked("contract.exists", msg.sender))] && msg.sender != guardian) {
   30,  30: 			revert InvalidOrOutdatedContract();
   31,  31: 		}
   32,  32: 		_;
```

---

#### 5. **`<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables (2 instances)**

Deployment. Gas Saved: **1 200**

Minimum Method Call. Gas Saved: **54**

Average Method Call. Gas Saved: **137**

Maximum Method Call. Gas Saved: **167**

Overall gas change: **-2 680** (NaN%)

##### - contracts/contract/tokens/TokenggAVAX.sol:[149](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L149), [252](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L252)

```diff
diff --git a/contracts/contract/tokens/TokenggAVAX.sol b/contracts/contract/tokens/TokenggAVAX.sol
index dc3948d..89a0a80 100644
--- a/contracts/contract/tokens/TokenggAVAX.sol
+++ b/contracts/contract/tokens/TokenggAVAX.sol
@@ -146,7 +146,7 @@ contract TokenggAVAX is Initializable, ERC4626Upgradeable, UUPSUpgradeable, Base
  146, 146: 		}
  147, 147: 
  148, 148: 		emit DepositedFromStaking(msg.sender, baseAmt, rewardAmt);
- 149     :-		stakingTotalAssets -= baseAmt;
+      149:+		stakingTotalAssets = stakingTotalAssets - baseAmt;
  150, 150: 		IWAVAX(address(asset)).deposit{value: totalAmt}();
  151, 151: 	}
  152, 152: 
@@ -249,7 +249,7 @@ contract TokenggAVAX is Initializable, ERC4626Upgradeable, UUPSUpgradeable, Base
  249, 249: 		uint256 amount,
  250, 250: 		uint256 /* shares */
  251, 251: 	) internal override {
- 252     :-		totalReleasedAssets += amount;
+      252:+		totalReleasedAssets = totalReleasedAssets + amount;
  253, 253: 	}
  254, 254: 
  255, 255: 	function _authorizeUpgrade(address newImplementation) internal override onlyGuardian {}
```

---

#### 6. **do not cache global varaibles (2 instances)**

Deployment. Gas Saved: **13 231**

Minimum Method Call. Gas Saved: **23**

Average Method Call. Gas Saved: **34**

Maximum Method Call. Gas Saved: **41**

Overall gas change: **-464** (NaN%)

##### - contracts/contract/tokens/TokenggAVAX.sol:[143](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L143), [167](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L167)

```diff
diff --git a/contracts/contract/tokens/TokenggAVAX.sol b/contracts/contract/tokens/TokenggAVAX.sol
index dc3948d..f6707b6 100644
--- a/contracts/contract/tokens/TokenggAVAX.sol
+++ b/contracts/contract/tokens/TokenggAVAX.sol
@@ -140,14 +140,13 @@ contract TokenggAVAX is Initializable, ERC4626Upgradeable, UUPSUpgradeable, Base
  140, 140: 	}
  141, 141: 
  142, 142: 	function depositFromStaking(uint256 baseAmt, uint256 rewardAmt) public payable onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
- 143     :-		uint256 totalAmt = msg.value;
- 144     :-		if (totalAmt != (baseAmt + rewardAmt) || baseAmt > stakingTotalAssets) {
+      143:+		if (msg.value != (baseAmt + rewardAmt) || baseAmt > stakingTotalAssets) {
  145, 144: 			revert InvalidStakingDeposit();
  146, 145: 		}
  147, 146: 
  148, 147: 		emit DepositedFromStaking(msg.sender, baseAmt, rewardAmt);
  149, 148: 		stakingTotalAssets -= baseAmt;
- 150     :-		IWAVAX(address(asset)).deposit{value: totalAmt}();
+      149:+		IWAVAX(address(asset)).deposit{value: msg.value}();
  151, 150: 	}
  152, 151: 
  153, 152: 	function withdrawForStaking(uint256 assets) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
@@ -164,17 +163,16 @@ contract TokenggAVAX is Initializable, ERC4626Upgradeable, UUPSUpgradeable, Base
  164, 163: 	}
  165, 164: 
  166, 165: 	function depositAVAX() public payable returns (uint256 shares) {
- 167     :-		uint256 assets = msg.value;
  168, 166: 		// Check for rounding error since we round down in previewDeposit.
- 169     :-		if ((shares = previewDeposit(assets)) == 0) {
+      167:+		if ((shares = previewDeposit(msg.value)) == 0) {
  170, 168: 			revert ZeroShares();
  171, 169: 		}
  172, 170: 
- 173     :-		emit Deposit(msg.sender, msg.sender, assets, shares);
+      171:+		emit Deposit(msg.sender, msg.sender, msg.value, shares);
  174, 172: 
- 175     :-		IWAVAX(address(asset)).deposit{value: assets}();
+      173:+		IWAVAX(address(asset)).deposit{value: msg.value}();
  176, 174: 		_mint(msg.sender, shares);
- 177     :-		afterDeposit(assets, shares);
+      175:+		afterDeposit(msg.value, shares);
  178, 176: 	}
  179, 177: 
  180, 178: 	function withdrawAVAX(uint256 assets) public returns (uint256 shares) {
```

---

#### 7. **Duplicated require()/revert() checks should be refactored to a modifier or function (10 instances)**

Deployment. Gas Saved: **74 952**

Minimum Method Call. Gas Saved: **-613**

Average Method Call. Gas Saved: **-187**

Maximum Method Call. Gas Saved: **-1 439**

Overall gas change: **14 972** (NaN%)

##### - contracts/contract/Vault.sol:[48](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L48), [63](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L63), [71](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L71), [86](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L86), [95](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L95), [114](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L114), [143](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L143), [151](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L151), [172](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L172), [183](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L183)

```diff
diff --git a/contracts/contract/Vault.sol b/contracts/contract/Vault.sol
index d7b5403..a34827b 100644
--- a/contracts/contract/Vault.sol
+++ b/contracts/contract/Vault.sol
@@ -42,12 +42,22 @@ contract Vault is Base, ReentrancyGuard {
   42,  42: 		version = 1;
   43,  43: 	}
   44,  44: 
+       45:+	function validate_contract_balance(uint256 _avaxBalances, uint256 _amount) private view {
+       46:+		if (_avaxBalances < _amount) {
+       47:+			revert InsufficientContractBalance();
+       48:+		}
+       49:+	}
+       50:+
+       51:+	function not_zero_amount (uint256 _amount) private pure {
+       52:+		if (_amount == 0) {
+       53:+			revert InvalidAmount();
+       54:+		}
+       55:+	}
+       56:+
   45,  57: 	/// @notice Accept an AVAX deposit from a network contract
   46,  58: 	function depositAVAX() external payable onlyRegisteredNetworkContract {
   47,  59: 		// Valid Amount?
-  48     :-		if (msg.value == 0) {
-  49     :-			revert InvalidAmount();
-  50     :-		}
+       60:+		not_zero_amount(msg.value);
   51,  61: 		// Get contract name
   52,  62: 		string memory contractName = getContractName(msg.sender);
   53,  63: 		// Emit deposit event
@@ -60,17 +70,14 @@ contract Vault is Base, ReentrancyGuard {
   60,  70: 	/// @param amount Amount of AVAX to be withdrawn
   61,  71: 	function withdrawAVAX(uint256 amount) external onlyRegisteredNetworkContract nonReentrant {
   62,  72: 		// Valid Amount?
-  63     :-		if (amount == 0) {
-  64     :-			revert InvalidAmount();
-  65     :-		}
+       73:+		not_zero_amount(amount);
   66,  74: 		// Get the contract name the call is coming from
   67,  75: 		string memory contractName = getContractName(msg.sender);
   68,  76: 		// Emit the withdrawl event for that contract
   69,  77: 		emit AVAXWithdrawn(contractName, amount);
   70,  78: 		// Verify there are enough funds
-  71     :-		if (avaxBalances[contractName] < amount) {
-  72     :-			revert InsufficientContractBalance();
-  73     :-		}
+       79:+		validate_contract_balance(avaxBalances[contractName], amount);
+       80:+		
   74,  81: 		// Update balance
   75,  82: 		avaxBalances[contractName] = avaxBalances[contractName] - amount;
   76,  83: 		IWithdrawer withdrawer = IWithdrawer(msg.sender);
@@ -83,18 +90,15 @@ contract Vault is Base, ReentrancyGuard {
   83,  90: 	/// @param amount How many AVAX to be transferred
   84,  91: 	function transferAVAX(string memory toContractName, uint256 amount) external onlyRegisteredNetworkContract {
   85,  92: 		// Valid Amount?
-  86     :-		if (amount == 0) {
-  87     :-			revert InvalidAmount();
-  88     :-		}
+       93:+		not_zero_amount(amount);
   89,  94: 		// Make sure the contract is valid, will revert if not
   90,  95: 		getContractAddress(toContractName);
   91,  96: 		string memory fromContractName = getContractName(msg.sender);
   92,  97: 		// Emit transfer event
   93,  98: 		emit AVAXTransfer(fromContractName, toContractName, amount);
   94,  99: 		// Verify there are enough funds
-  95     :-		if (avaxBalances[fromContractName] < amount) {
-  96     :-			revert InsufficientContractBalance();
-  97     :-		}
+      100:+		validate_contract_balance(avaxBalances[fromContractName], amount);
+      101:+
   98, 102: 		// Update balances
   99, 103: 		avaxBalances[fromContractName] = avaxBalances[fromContractName] - amount;
  100, 104: 		avaxBalances[toContractName] = avaxBalances[toContractName] + amount;
@@ -111,9 +115,7 @@ contract Vault is Base, ReentrancyGuard {
  111, 115: 		uint256 amount
  112, 116: 	) external guardianOrRegisteredContract {
  113, 117: 		// Valid Amount?
- 114     :-		if (amount == 0) {
- 115     :-			revert InvalidAmount();
- 116     :-		}
+      118:+		not_zero_amount(amount);
  117, 119: 		// Make sure the network contract is valid (will revert if not)
  118, 120: 		getContractAddress(networkContractName);
  119, 121: 		// Make sure we accept this token
@@ -140,17 +142,14 @@ contract Vault is Base, ReentrancyGuard {
  140, 142: 		uint256 amount
  141, 143: 	) external onlyRegisteredNetworkContract nonReentrant {
  142, 144: 		// Valid Amount?
- 143     :-		if (amount == 0) {
- 144     :-			revert InvalidAmount();
- 145     :-		}
+      145:+		not_zero_amount(amount);
  146, 146: 		// Get contract key
  147, 147: 		bytes32 contractKey = keccak256(abi.encodePacked(getContractName(msg.sender), tokenAddress));
  148, 148: 		// Emit token withdrawn event
  149, 149: 		emit TokenWithdrawn(contractKey, address(tokenAddress), amount);
  150, 150: 		// Verify there are enough funds
- 151     :-		if (tokenBalances[contractKey] < amount) {
- 152     :-			revert InsufficientContractBalance();
- 153     :-		}
+      151:+		validate_contract_balance(tokenBalances[contractKey], amount);
+      152:+
  154, 153: 		// Update balances
  155, 154: 		tokenBalances[contractKey] = tokenBalances[contractKey] - amount;
  156, 155: 		// Get the toke ERC20 instance
@@ -169,9 +168,7 @@ contract Vault is Base, ReentrancyGuard {
  169, 168: 		uint256 amount
  170, 169: 	) external onlyRegisteredNetworkContract {
  171, 170: 		// Valid Amount?
- 172     :-		if (amount == 0) {
- 173     :-			revert InvalidAmount();
- 174     :-		}
+      171:+		not_zero_amount(amount);
  175, 172: 		// Make sure the network contract is valid (will revert if not)
  176, 173: 		getContractAddress(networkContractName);
  177, 174: 		// Get contract keys
@@ -180,9 +177,8 @@ contract Vault is Base, ReentrancyGuard {
  180, 177: 		// emit token transfer event
  181, 178: 		emit TokenTransfer(contractKeyFrom, contractKeyTo, address(tokenAddress), amount);
  182, 179: 		// Verify there are enough funds
- 183     :-		if (tokenBalances[contractKeyFrom] < amount) {
- 184     :-			revert InsufficientContractBalance();
- 185     :-		}
+      180:+		validate_contract_balance(tokenBalances[contractKeyFrom], amount);
+      181:+
  186, 182: 		// Update Balances
  187, 183: 		tokenBalances[contractKeyFrom] = tokenBalances[contractKeyFrom] - amount;
  188, 184: 		tokenBalances[contractKeyTo] = tokenBalances[contractKeyTo] + amount;
```

---

#### 8. **Instead of using a modifier you can save gas using private/internal view functions (11 instances)**

Deployment. Gas Saved: **1 957 398**

Minimum Method Call. Gas Saved: **-5 424**

Average Method Call. Gas Saved: **-6 868**

Maximum Method Call. Gas Saved: **-8 054**

Overall gas change: **50 619 (NaN%)**

##### - contracts/contract/BaseAbstract.sol:[24](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseAbstract.sol#L24), [32](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseAbstract.sol#L32), [40](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseAbstract.sol#L40), [51](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseAbstract.sol#L51), [62](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseAbstract.sol#L62), [70](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseAbstract.sol#L70), [81](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseAbstract.sol#L81)

```diff
diff --git a/contracts/contract/BaseAbstract.sol b/contracts/contract/BaseAbstract.sol
index a10b65d..6e3e074 100644
--- a/contracts/contract/BaseAbstract.sol
+++ b/contracts/contract/BaseAbstract.sol
@@ -21,69 +21,62 @@ abstract contract BaseAbstract {
   21,  21: 	Storage internal gogoStorage;
   22,  22: 
   23,  23: 	/// @dev Verify caller is a registered network contract
-  24     :-	modifier onlyRegisteredNetworkContract() {
+       24:+	function onlyRegisteredNetworkContract() internal view {
   25,  25: 		if (getBool(keccak256(abi.encodePacked("contract.exists", msg.sender))) == false) {
   26,  26: 			revert InvalidOrOutdatedContract();
   27,  27: 		}
-  28     :-		_;
   29,  28: 	}
   30,  29: 
   31,  30: 	/// @dev Verify caller is registered version of `contractName`
-  32     :-	modifier onlySpecificRegisteredContract(string memory contractName, address contractAddress) {
+       31:+	function onlySpecificRegisteredContract(string memory contractName, address contractAddress) internal view {
   33,  32: 		if (contractAddress != getAddress(keccak256(abi.encodePacked("contract.address", contractName)))) {
   34,  33: 			revert InvalidOrOutdatedContract();
   35,  34: 		}
-  36     :-		_;
   37,  35: 	}
   38,  36: 
   39,  37: 	/// @dev Verify caller is a guardian or registered network contract
-  40     :-	modifier guardianOrRegisteredContract() {
+       38:+	function guardianOrRegisteredContract() internal view {
   41,  39: 		bool isContract = getBool(keccak256(abi.encodePacked("contract.exists", msg.sender)));
   42,  40: 		bool isGuardian = msg.sender == gogoStorage.getGuardian();
   43,  41: 
   44,  42: 		if (!(isGuardian || isContract)) {
   45,  43: 			revert MustBeGuardianOrValidContract();
   46,  44: 		}
-  47     :-		_;
   48,  45: 	}
   49,  46: 
   50,  47: 	/// @dev Verify caller is a guardian or registered version of `contractName`
-  51     :-	modifier guardianOrSpecificRegisteredContract(string memory contractName, address contractAddress) {
+       48:+	function guardianOrSpecificRegisteredContract(string memory contractName, address contractAddress) internal view {
   52,  49: 		bool isContract = contractAddress == getAddress(keccak256(abi.encodePacked("contract.address", contractName)));
   53,  50: 		bool isGuardian = msg.sender == gogoStorage.getGuardian();
   54,  51: 
   55,  52: 		if (!(isGuardian || isContract)) {
   56,  53: 			revert MustBeGuardianOrValidContract();
   57,  54: 		}
-  58     :-		_;
   59,  55: 	}
   60,  56: 
   61,  57: 	/// @dev Verify caller is the guardian
-  62     :-	modifier onlyGuardian() {
+       58:+	function onlyGuardian() internal view {
   63,  59: 		if (msg.sender != gogoStorage.getGuardian()) {
   64,  60: 			revert MustBeGuardian();
   65,  61: 		}
-  66     :-		_;
   67,  62: 	}
   68,  63: 
   69,  64: 	/// @dev Verify caller is a valid multisig
-  70     :-	modifier onlyMultisig() {
+       65:+	function onlyMultisig() internal view {
   71,  66: 		int256 multisigIndex = int256(getUint(keccak256(abi.encodePacked("multisig.index", msg.sender)))) - 1;
   72,  67: 		address addr = getAddress(keccak256(abi.encodePacked("multisig.item", multisigIndex, ".address")));
   73,  68: 		bool enabled = (addr != address(0)) && getBool(keccak256(abi.encodePacked("multisig.item", multisigIndex, ".enabled")));
   74,  69: 		if (enabled == false) {
   75,  70: 			revert MustBeMultisig();
   76,  71: 		}
-  77     :-		_;
   78,  72: 	}
   79,  73: 
   80,  74: 	/// @dev Verify contract is not paused
-  81     :-	modifier whenNotPaused() {
+       75:+	function whenNotPaused() internal view {
   82,  76: 		string memory contractName = getContractName(address(this));
   83,  77: 		if (getBool(keccak256(abi.encodePacked("contract.paused", contractName)))) {
   84,  78: 			revert ContractPaused();
   85,  79: 		}
-  86     :-		_;
   87,  80: 	}
   88,  81: 
   89,  82: 	/// @dev Get the address of a network contract by name
```

##### - contracts/contract/ProtocolDAO.sol:[12](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ProtocolDAO.sol#L12)

```diff
diff --git a/contracts/contract/ProtocolDAO.sol b/contracts/contract/ProtocolDAO.sol
index d0846f6..acb83f0 100644
--- a/contracts/contract/ProtocolDAO.sol
+++ b/contracts/contract/ProtocolDAO.sol
@@ -9,18 +9,18 @@ import {Storage} from "./Storage.sol";
    9,   9: contract ProtocolDAO is Base {
   10,  10: 	error ValueNotWithinRange();
   11,  11: 
-  12     :-	modifier valueNotGreaterThanOne(uint256 setterValue) {
+       12:+	function valueNotGreaterThanOne(uint256 setterValue) private pure {
   13,  13: 		if (setterValue > 1 ether) {
   14,  14: 			revert ValueNotWithinRange();
   15,  15: 		}
-  16     :-		_;
   17,  16: 	}
   18,  17: 
   19,  18: 	constructor(Storage storageAddress) Base(storageAddress) {
   20,  19: 		version = 1;
   21,  20: 	}
   22,  21: 
-  23     :-	function initialize() external onlyGuardian {
+       22:+	function initialize() external {
+       23:+		onlyGuardian();
   24,  24: 		if (getBool(keccak256("ProtocolDAO.initialized"))) {
   25,  25: 			return;
   26,  26: 		}
@@ -64,13 +64,15 @@ contract ProtocolDAO is Base {
   64,  64: 
   65,  65: 	/// @notice Pause a contract
   66,  66: 	/// @param contractName The contract whose actions should be paused
-  67     :-	function pauseContract(string memory contractName) public onlySpecificRegisteredContract("Ocyticus", msg.sender) {
+       67:+	function pauseContract(string memory contractName) public {
+       68:+		onlySpecificRegisteredContract("Ocyticus", msg.sender);
   68,  69: 		setBool(keccak256(abi.encodePacked("contract.paused", contractName)), true);
   69,  70: 	}
   70,  71: 
   71,  72: 	/// @notice Unpause a contract
   72,  73: 	/// @param contractName The contract whose actions should be resumed
-  73     :-	function resumeContract(string memory contractName) public onlySpecificRegisteredContract("Ocyticus", msg.sender) {
+       74:+	function resumeContract(string memory contractName) public {
+       75:+		onlySpecificRegisteredContract("Ocyticus", msg.sender);
   74,  76: 		setBool(keccak256(abi.encodePacked("contract.paused", contractName)), false);
   75,  77: 	}
   76,  78: 
@@ -93,7 +95,8 @@ contract ProtocolDAO is Base {
   93,  95: 	}
   94,  96: 
   95,  97: 	/// @notice Set the amount of GGP that is in circulation
-  96     :-	function setTotalGGPCirculatingSupply(uint256 amount) public onlySpecificRegisteredContract("RewardsPool", msg.sender) {
+       98:+	function setTotalGGPCirculatingSupply(uint256 amount) public {
+       99:+		onlySpecificRegisteredContract("RewardsPool", msg.sender);
   97, 100: 		return setUint(keccak256("ProtocolDAO.TotalGGPCirculatingSupply"), amount);
   98, 101: 	}
   99, 102: 
@@ -104,7 +107,9 @@ contract ProtocolDAO is Base {
  104, 107: 	}
  105, 108: 
  106, 109: 	/// @notice Set the percentage a contract is owed for a rewards cycle
- 107     :-	function setClaimingContractPct(string memory claimingContract, uint256 decimal) public onlyGuardian valueNotGreaterThanOne(decimal) {
+      110:+	function setClaimingContractPct(string memory claimingContract, uint256 decimal) public {
+      111:+		onlyGuardian();
+      112:+		valueNotGreaterThanOne(decimal);
  108, 113: 		setUint(keccak256(abi.encodePacked("ProtocolDAO.ClaimingContractPct.", claimingContract)), decimal);
  109, 114: 	}
  110, 115: 
@@ -153,7 +158,9 @@ contract ProtocolDAO is Base {
  153, 158: 
  154, 159: 	/// @notice Set the rewards rate for validating Avalanche's p-chain
  155, 160: 	/// @dev Used for testing
- 156     :-	function setExpectedAVAXRewardsRate(uint256 rate) public onlyMultisig valueNotGreaterThanOne(rate) {
+      161:+	function setExpectedAVAXRewardsRate(uint256 rate) public {
+      162:+		onlyMultisig();
+      163:+		valueNotGreaterThanOne(rate);
  157, 164: 		setUint(keccak256("ProtocolDAO.ExpectedAVAXRewardsRate"), rate);
  158, 165: 	}
  159, 166: 
@@ -187,7 +194,8 @@ contract ProtocolDAO is Base {
  187, 194: 	/// @notice Register a new contract with Storage
  188, 195: 	/// @param addr Contract address to register
  189, 196: 	/// @param name Contract name to register
- 190     :-	function registerContract(address addr, string memory name) public onlyGuardian {
+      197:+	function registerContract(address addr, string memory name) public {
+      198:+		onlyGuardian();
  191, 199: 		setBool(keccak256(abi.encodePacked("contract.exists", addr)), true);
  192, 200: 		setAddress(keccak256(abi.encodePacked("contract.address", name)), addr);
  193, 201: 		setString(keccak256(abi.encodePacked("contract.name", addr)), name);
@@ -195,7 +203,8 @@ contract ProtocolDAO is Base {
  195, 203: 
  196, 204: 	/// @notice Unregister a contract with Storage
  197, 205: 	/// @param addr Contract address to unregister
- 198     :-	function unregisterContract(address addr) public onlyGuardian {
+      206:+	function unregisterContract(address addr) public {
+      207:+		onlyGuardian();
  199, 208: 		string memory name = getContractName(addr);
  200, 209: 		deleteBool(keccak256(abi.encodePacked("contract.exists", addr)));
  201, 210: 		deleteAddress(keccak256(abi.encodePacked("contract.address", name)));
@@ -210,7 +219,8 @@ contract ProtocolDAO is Base {
  210, 219: 		address newAddr,
  211, 220: 		string memory newName,
  212, 221: 		address existingAddr
- 213     :-	) external onlyGuardian {
+      222:+	) external {
+      223:+		onlyGuardian();
  214, 224: 		registerContract(newAddr, newName);
  215, 225: 		unregisterContract(existingAddr);
  216, 226: 	}
```

##### - contracts/contract/Storage.sol:[28](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L28)

```diff
diff --git a/contracts/contract/Storage.sol b/contracts/contract/Storage.sol
index 218542f..1a17249 100644
--- a/contracts/contract/Storage.sol
+++ b/contracts/contract/Storage.sol
@@ -25,11 +25,10 @@ contract Storage {
   25,  25: 	address public newGuardian;
   26,  26: 
   27,  27: 	/// @dev Only allow access from guardian or the latest version of a contract in the GoGoPool network
-  28     :-	modifier onlyRegisteredNetworkContract() {
+       28:+	function onlyRegisteredNetworkContract() internal view {
   29,  29: 		if (booleanStorage[keccak256(abi.encodePacked("contract.exists", msg.sender))] == false && msg.sender != guardian) {
   30,  30: 			revert InvalidOrOutdatedContract();
   31,  31: 		}
-  32     :-		_;
   33,  32: 	}
   34,  33: 
   35,  34: 	constructor() {
@@ -101,31 +100,38 @@ contract Storage {
  101, 100: 	// SET
  102, 101: 	//
  103, 102: 
- 104     :-	function setAddress(bytes32 key, address value) external onlyRegisteredNetworkContract {
+      103:+	function setAddress(bytes32 key, address value) external {
+      104:+		onlyRegisteredNetworkContract();
  105, 105: 		addressStorage[key] = value;
  106, 106: 	}
  107, 107: 
- 108     :-	function setBool(bytes32 key, bool value) external onlyRegisteredNetworkContract {
+      108:+	function setBool(bytes32 key, bool value) external {
+      109:+		onlyRegisteredNetworkContract();
  109, 110: 		booleanStorage[key] = value;
  110, 111: 	}
  111, 112: 
- 112     :-	function setBytes(bytes32 key, bytes calldata value) external onlyRegisteredNetworkContract {
+      113:+	function setBytes(bytes32 key, bytes calldata value) external {
+      114:+		onlyRegisteredNetworkContract();
  113, 115: 		bytesStorage[key] = value;
  114, 116: 	}
  115, 117: 
- 116     :-	function setBytes32(bytes32 key, bytes32 value) external onlyRegisteredNetworkContract {
+      118:+	function setBytes32(bytes32 key, bytes32 value) external {
+      119:+		onlyRegisteredNetworkContract();
  117, 120: 		bytes32Storage[key] = value;
  118, 121: 	}
  119, 122: 
- 120     :-	function setInt(bytes32 key, int256 value) external onlyRegisteredNetworkContract {
+      123:+	function setInt(bytes32 key, int256 value) external {
+      124:+		onlyRegisteredNetworkContract();
  121, 125: 		intStorage[key] = value;
  122, 126: 	}
  123, 127: 
- 124     :-	function setString(bytes32 key, string calldata value) external onlyRegisteredNetworkContract {
+      128:+	function setString(bytes32 key, string calldata value) external {
+      129:+		onlyRegisteredNetworkContract();
  125, 130: 		stringStorage[key] = value;
  126, 131: 	}
  127, 132: 
- 128     :-	function setUint(bytes32 key, uint256 value) external onlyRegisteredNetworkContract {
+      133:+	function setUint(bytes32 key, uint256 value) external {
+      134:+		onlyRegisteredNetworkContract();
  129, 135: 		uintStorage[key] = value;
  130, 136: 	}
  131, 137: 
@@ -133,31 +139,38 @@ contract Storage {
  133, 139: 	// DELETE
  134, 140: 	//
  135, 141: 
- 136     :-	function deleteAddress(bytes32 key) external onlyRegisteredNetworkContract {
+      142:+	function deleteAddress(bytes32 key) external {
+      143:+		onlyRegisteredNetworkContract();
  137, 144: 		delete addressStorage[key];
  138, 145: 	}
  139, 146: 
- 140     :-	function deleteBool(bytes32 key) external onlyRegisteredNetworkContract {
+      147:+	function deleteBool(bytes32 key) external {
+      148:+		onlyRegisteredNetworkContract();
  141, 149: 		delete booleanStorage[key];
  142, 150: 	}
  143, 151: 
- 144     :-	function deleteBytes(bytes32 key) external onlyRegisteredNetworkContract {
+      152:+	function deleteBytes(bytes32 key) external {
+      153:+		onlyRegisteredNetworkContract();
  145, 154: 		delete bytesStorage[key];
  146, 155: 	}
  147, 156: 
- 148     :-	function deleteBytes32(bytes32 key) external onlyRegisteredNetworkContract {
+      157:+	function deleteBytes32(bytes32 key) external {
+      158:+		onlyRegisteredNetworkContract();
  149, 159: 		delete bytes32Storage[key];
  150, 160: 	}
  151, 161: 
- 152     :-	function deleteInt(bytes32 key) external onlyRegisteredNetworkContract {
+      162:+	function deleteInt(bytes32 key) external {
+      163:+		onlyRegisteredNetworkContract();
  153, 164: 		delete intStorage[key];
  154, 165: 	}
  155, 166: 
- 156     :-	function deleteString(bytes32 key) external onlyRegisteredNetworkContract {
+      167:+	function deleteString(bytes32 key) external {
+      168:+		onlyRegisteredNetworkContract();
  157, 169: 		delete stringStorage[key];
  158, 170: 	}
  159, 171: 
- 160     :-	function deleteUint(bytes32 key) external onlyRegisteredNetworkContract {
+      172:+	function deleteUint(bytes32 key) external {
+      173:+		onlyRegisteredNetworkContract();
  161, 174: 		delete uintStorage[key];
  162, 175: 	}
  163, 176: 
@@ -167,13 +180,15 @@ contract Storage {
  167, 180: 
  168, 181: 	/// @param key The key for the record
  169, 182: 	/// @param amount An amount to add to the record's value
- 170     :-	function addUint(bytes32 key, uint256 amount) external onlyRegisteredNetworkContract {
+      183:+	function addUint(bytes32 key, uint256 amount) external {
+      184:+		onlyRegisteredNetworkContract();
  171, 185: 		uintStorage[key] = uintStorage[key] + amount;
  172, 186: 	}
  173, 187: 
  174, 188: 	/// @param key The key for the record
  175, 189: 	/// @param amount An amount to subtract from the record's value
- 176     :-	function subUint(bytes32 key, uint256 amount) external onlyRegisteredNetworkContract {
+      190:+	function subUint(bytes32 key, uint256 amount) external {
+      191:+		onlyRegisteredNetworkContract();
  177, 192: 		uintStorage[key] = uintStorage[key] - amount;
  178, 193: 	}
  179, 194: }
```

##### - contracts/contract/Ocyticus.sol:[15](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Ocyticus.sol#L15)

```diff
diff --git a/contracts/contract/Ocyticus.sol b/contracts/contract/Ocyticus.sol
index 41e4a35..7ebb1cc 100644
--- a/contracts/contract/Ocyticus.sol
+++ b/contracts/contract/Ocyticus.sol
@@ -12,11 +12,10 @@ contract Ocyticus is Base {
   12,  12: 
   13,  13: 	mapping(address => bool) public defenders;
   14,  14: 
-  15     :-	modifier onlyDefender() {
+       15:+	function onlyDefender() private view {
   16,  16: 		if (!defenders[msg.sender]) {
   17,  17: 			revert NotAllowed();
   18,  18: 		}
-  19     :-		_;
   20,  19: 	}
   21,  20: 
   22,  21: 	constructor(Storage storageAddress) Base(storageAddress) {
@@ -36,7 +35,8 @@ contract Ocyticus is Base {
   36,  35: 	}
   37,  36: 
   38,  37: 	/// @notice Restrict actions in important contracts
-  39     :-	function pauseEverything() external onlyDefender {
+       38:+	function pauseEverything() external {
+       39:+		onlyDefender();
   40,  40: 		ProtocolDAO dao = ProtocolDAO(getContractAddress("ProtocolDAO"));
   41,  41: 		dao.pauseContract("TokenggAVAX");
   42,  42: 		dao.pauseContract("MinipoolManager");
@@ -46,7 +46,8 @@ contract Ocyticus is Base {
   46,  46: 
   47,  47: 	/// @notice Reestablish all contract's abilities
   48,  48: 	/// @dev Multisigs will need to be enabled seperately, we dont know which ones to enable
-  49     :-	function resumeEverything() external onlyDefender {
+       49:+	function resumeEverything() external {
+       50:+		onlyDefender();
   50,  51: 		ProtocolDAO dao = ProtocolDAO(getContractAddress("ProtocolDAO"));
   51,  52: 		dao.resumeContract("TokenggAVAX");
   52,  53: 		dao.resumeContract("MinipoolManager");
@@ -54,7 +55,8 @@ contract Ocyticus is Base {
   54,  55: 	}
   55,  56: 
   56,  57: 	/// @notice Disable every multisig in the protocol
-  57     :-	function disableAllMultisigs() public onlyDefender {
+       58:+	function disableAllMultisigs() public {
+       59:+		onlyDefender();
   58,  60: 		MultisigManager mm = MultisigManager(getContractAddress("MultisigManager"));
   59,  61: 		uint256 count = mm.getCount();
   60,  62: 
```

##### - contracts/contract/tokens/TokenggAVAX.sol:[58](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L58)

```diff
diff --git a/contracts/contract/tokens/TokenggAVAX.sol b/contracts/contract/tokens/TokenggAVAX.sol
index 4f5aaf0..4028a79 100644
--- a/contracts/contract/tokens/TokenggAVAX.sol
+++ b/contracts/contract/tokens/TokenggAVAX.sol
@@ -55,11 +55,10 @@ contract TokenggAVAX is Initializable, ERC4626Upgradeable, UUPSUpgradeable, Base
   55,  55: 	/// @notice total amount of avax currently out for staking (not including any rewards)
   56,  56: 	uint256 public stakingTotalAssets;
   57,  57: 
-  58     :-	modifier whenTokenNotPaused(uint256 amt) {
+       58:+	function whenTokenNotPaused(uint256 amt) private view {
   59,  59: 		if (amt > 0 && getBool(keccak256(abi.encodePacked("contract.paused", "TokenggAVAX")))) {
   60,  60: 			revert ContractPaused();
   61,  61: 		}
-  62     :-		_;
   63,  62: 	}
   64,  63: 
   65,  64: 	/// @custom:oz-upgrades-unsafe-allow constructor
@@ -224,19 +223,23 @@ contract TokenggAVAX is Initializable, ERC4626Upgradeable, UUPSUpgradeable, Base
  224, 223: 		return shares > avail ? avail : shares;
  225, 224: 	}
  226, 225: 
- 227     :-	function previewDeposit(uint256 assets) public view override whenTokenNotPaused(assets) returns (uint256) {
+      226:+	function previewDeposit(uint256 assets) public view override returns (uint256) {
+      227:+		whenTokenNotPaused(assets);
  228, 228: 		return super.previewDeposit(assets);
  229, 229: 	}
  230, 230: 
- 231     :-	function previewMint(uint256 shares) public view override whenTokenNotPaused(shares) returns (uint256) {
+      231:+	function previewMint(uint256 shares) public view override returns (uint256) {
+      232:+		whenTokenNotPaused(shares);
  232, 233: 		return super.previewMint(shares);
  233, 234: 	}
  234, 235: 
- 235     :-	function previewWithdraw(uint256 assets) public view override whenTokenNotPaused(assets) returns (uint256) {
+      236:+	function previewWithdraw(uint256 assets) public view override returns (uint256) {
+      237:+		whenTokenNotPaused(assets);
  236, 238: 		return super.previewWithdraw(assets);
  237, 239: 	}
  238, 240: 
- 239     :-	function previewRedeem(uint256 shares) public view override whenTokenNotPaused(shares) returns (uint256) {
+      241:+	function previewRedeem(uint256 shares) public view override returns (uint256) {
+      242:+		whenTokenNotPaused(shares);
  240, 243: 		return super.previewRedeem(shares);
  241, 244: 	}
  242, 245: 
```

##### - contracts/contract/ClaimNodeOp.sol:

```diff
diff --git a/contracts/contract/ClaimNodeOp.sol b/contracts/contract/ClaimNodeOp.sol
index efcce70..089c92c 100644
--- a/contracts/contract/ClaimNodeOp.sol
+++ b/contracts/contract/ClaimNodeOp.sol
@@ -37,7 +37,8 @@ contract ClaimNodeOp is Base {
   37,  37: 	}
   38,  38: 
   39,  39: 	/// @dev Sets the total rewards for the most recent cycle
-  40     :-	function setRewardsCycleTotal(uint256 amount) public onlySpecificRegisteredContract("RewardsPool", msg.sender) {
+       40:+	function setRewardsCycleTotal(uint256 amount) public {
+       41:+		onlySpecificRegisteredContract("RewardsPool", msg.sender);
   41,  42: 		setUint(keccak256("NOPClaim.RewardsCycleTotal"), amount);
   42,  43: 	}
   43,  44: 
@@ -53,7 +54,8 @@ contract ClaimNodeOp is Base {
   53,  54: 
   54,  55: 	/// @notice Set the share of rewards for a staker as a fraction of 1 ether
   55,  56: 	/// @dev Rialto will call this
-  56     :-	function calculateAndDistributeRewards(address stakerAddr, uint256 totalEligibleGGPStaked) external onlyMultisig {
+       57:+	function calculateAndDistributeRewards(address stakerAddr, uint256 totalEligibleGGPStaked) external {
+       58:+		onlyMultisig();
   57,  59: 		Staking staking = Staking(getContractAddress("Staking"));
   58,  60: 		staking.requireValidStaker(stakerAddr);
   59,  61:
```

##### - contracts/contract/ClaimProtocolDAO.sol

```diff
diff --git a/contracts/contract/ClaimProtocolDAO.sol b/contracts/contract/ClaimProtocolDAO.sol
index 47ba849..b9cafc4 100644
--- a/contracts/contract/ClaimProtocolDAO.sol
+++ b/contracts/contract/ClaimProtocolDAO.sol
@@ -21,7 +21,8 @@ contract ClaimProtocolDAO is Base {
   21,  21: 		string memory invoiceID,
   22,  22: 		address recipientAddress,
   23,  23: 		uint256 amount
-  24     :-	) external onlyGuardian {
+       24:+	) external {
+       25:+		onlyGuardian();
   25,  26: 		Vault vault = Vault(getContractAddress("Vault"));
   26,  27: 		TokenGGP ggpToken = TokenGGP(getContractAddress("TokenGGP"));
   27,  28: 
```

##### - contracts/contract/MinipoolManager.sol

```diff
diff --git a/contracts/contract/MinipoolManager.sol b/contracts/contract/MinipoolManager.sol
index 8563580..9a366e1 100644
--- a/contracts/contract/MinipoolManager.sol
+++ b/contracts/contract/MinipoolManager.sol
@@ -198,7 +198,8 @@ contract MinipoolManager is Base, ReentrancyGuard, IWithdrawer {
  198, 198: 		uint256 duration,
  199, 199: 		uint256 delegationFee,
  200, 200: 		uint256 avaxAssignmentRequest
- 201     :-	) external payable whenNotPaused {
+      201:+	) external payable {
+      202:+		whenNotPaused();
  202, 203: 		if (nodeID == address(0)) {
  203, 204: 			revert InvalidNodeID();
  204, 205: 		}
@@ -441,7 +442,8 @@ contract MinipoolManager is Base, ReentrancyGuard, IWithdrawer {
  441, 442: 
  442, 443: 	/// @notice Re-stake a minipool, compounding all rewards recvd
  443, 444: 	/// @param nodeID 20-byte Avalanche node ID
- 444     :-	function recreateMinipool(address nodeID) external whenNotPaused {
+      445:+	function recreateMinipool(address nodeID) external {
+      446:+		whenNotPaused();
  445, 447: 		int256 minipoolIndex = onlyValidMultisig(nodeID);
  446, 448: 		requireValidStateTransition(minipoolIndex, MinipoolStatus.Prelaunch);
  447, 449: 		Minipool memory mp = getMinipool(minipoolIndex);
```

##### - contracts/contract/MultisigManager.sol

```diff
diff --git a/contracts/contract/MultisigManager.sol b/contracts/contract/MultisigManager.sol
index 06cb014..a64230e 100644
--- a/contracts/contract/MultisigManager.sol
+++ b/contracts/contract/MultisigManager.sol
@@ -32,7 +32,8 @@ contract MultisigManager is Base {
   32,  32: 
   33,  33: 	/// @notice Register a multisig. Defaults to disabled when first registered.
   34,  34: 	/// @param addr Address of the multisig that is being registered
-  35     :-	function registerMultisig(address addr) external onlyGuardian {
+       35:+	function registerMultisig(address addr) external {
+       36:+		onlyGuardian();
   36,  37: 		int256 multisigIndex = getIndexOf(addr);
   37,  38: 		if (multisigIndex != -1) {
   38,  39: 			revert MultisigAlreadyRegistered();
@@ -52,7 +53,8 @@ contract MultisigManager is Base {
   52,  53: 
   53,  54: 	/// @notice Enabling a registered multisig
   54,  55: 	/// @param addr Address of the multisig that is being enabled
-  55     :-	function enableMultisig(address addr) external onlyGuardian {
+       56:+	function enableMultisig(address addr) external {
+       57:+		onlyGuardian();
   56,  58: 		int256 multisigIndex = getIndexOf(addr);
   57,  59: 		if (multisigIndex == -1) {
   58,  60: 			revert MultisigNotFound();
@@ -65,7 +67,8 @@ contract MultisigManager is Base {
   65,  67: 	/// @notice Disabling a registered multisig
   66,  68: 	/// @param addr Address of the multisig that is being disabled
   67,  69: 	/// @dev this will prevent the multisig from completing validations. The minipool will need to be manually reassigned to a new multisig
-  68     :-	function disableMultisig(address addr) external guardianOrSpecificRegisteredContract("Ocyticus", msg.sender) {
+       70:+	function disableMultisig(address addr) external {
+       71:+		guardianOrSpecificRegisteredContract("Ocyticus", msg.sender);
   69,  72: 		int256 multisigIndex = getIndexOf(addr);
   70,  73: 		if (multisigIndex == -1) {
   71,  74: 			revert MultisigNotFound();
```

##### - contracts/contract/Ocyticus.sol

```diff
diff --git a/contracts/contract/Ocyticus.sol b/contracts/contract/Ocyticus.sol
index 71c473b..41e4a35 100644
--- a/contracts/contract/Ocyticus.sol
+++ b/contracts/contract/Ocyticus.sol
@@ -24,12 +24,14 @@ contract Ocyticus is Base {
   24,  24: 	}
   25,  25: 
   26,  26: 	/// @notice Add an address to the defender list
-  27     :-	function addDefender(address defender) external onlyGuardian {
+       27:+	function addDefender(address defender) external {
+       28:+		onlyGuardian();
   28,  29: 		defenders[defender] = true;
   29,  30: 	}
   30,  31: 
   31,  32: 	/// @notice Remove an address from the defender list
-  32     :-	function removeDefender(address defender) external onlyGuardian {
+       33:+	function removeDefender(address defender) external {
+       34:+		onlyGuardian();
   33,  35: 		delete defenders[defender];
   34,  36: 	}
   35,  37: 
```

##### - contracts/contract/Oracle.sol

```diff
diff --git a/contracts/contract/Oracle.sol b/contracts/contract/Oracle.sol
index f320f36..f845507 100644
--- a/contracts/contract/Oracle.sol
+++ b/contracts/contract/Oracle.sol
@@ -25,7 +25,8 @@ contract Oracle is Base {
   25,  25: 	}
   26,  26: 
   27,  27: 	/// @notice Set the address of the One Inch price aggregator contract
-  28     :-	function setOneInch(address addr) external onlyGuardian {
+       28:+	function setOneInch(address addr) external {
+       29:+		onlyGuardian();
   29,  30: 		setAddress(keccak256("Oracle.OneInch"), addr);
   30,  31: 	}
   31,  32: 
@@ -54,7 +55,8 @@ contract Oracle is Base {
   54,  55: 	/// @notice Set the price of GGP denominated in AVAX
   55,  56: 	/// @param price Price of GGP in AVAX
   56,  57: 	/// @param timestamp Time the price was updated
-  57     :-	function setGGPPriceInAVAX(uint256 price, uint256 timestamp) external onlyMultisig {
+       58:+	function setGGPPriceInAVAX(uint256 price, uint256 timestamp) external {
+       59:+		onlyMultisig();
   58,  60: 		uint256 lastTimestamp = getUint(keccak256("Oracle.GGPTimestamp"));
   59,  61: 		if (timestamp < lastTimestamp || timestamp > block.timestamp) {
   60,  62: 			revert InvalidTimestamp();
```


##### - contracts/contract/RewardsPool.sol

```diff
diff --git a/contracts/contract/RewardsPool.sol b/contracts/contract/RewardsPool.sol
index 20ce6cb..f3f1872 100644
--- a/contracts/contract/RewardsPool.sol
+++ b/contracts/contract/RewardsPool.sol
@@ -31,7 +31,8 @@ contract RewardsPool is Base {
   31,  31: 		version = 1;
   32,  32: 	}
   33,  33: 
-  34     :-	function initialize() external onlyGuardian {
+       34:+	function initialize() external {
+       35:+		onlyGuardian();
   35,  36: 		if (getBool(keccak256("RewardsPool.initialized"))) {
   36,  37: 			return;
   37,  38: 		}
```

##### - contracts/contract/Staking.sol

```diff
diff --git a/contracts/contract/Staking.sol b/contracts/contract/Staking.sol
index 611cce0..0c4fba1 100644
--- a/contracts/contract/Staking.sol
+++ b/contracts/contract/Staking.sol
@@ -107,14 +107,16 @@ contract Staking is Base {
  107, 107: 
  108, 108: 	/// @notice Increase the amount of AVAX a given staker is staking
  109, 109: 	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
- 110     :-	function increaseAVAXStake(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
+      110:+	function increaseAVAXStake(address stakerAddr, uint256 amount) public {
+      111:+		onlySpecificRegisteredContract("MinipoolManager", msg.sender);
  111, 112: 		int256 stakerIndex = requireValidStaker(stakerAddr);
  112, 113: 		addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxStaked")), amount);
  113, 114: 	}
  114, 115: 
  115, 116: 	/// @notice Decrease the amount of AVAX a given staker is staking
  116, 117: 	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
- 117     :-	function decreaseAVAXStake(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
+      118:+	function decreaseAVAXStake(address stakerAddr, uint256 amount) public {
+      119:+		onlySpecificRegisteredContract("MinipoolManager", msg.sender);
  118, 120: 		int256 stakerIndex = requireValidStaker(stakerAddr);
  119, 121: 		subUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxStaked")), amount);
  120, 122: 	}
@@ -130,14 +132,16 @@ contract Staking is Base {
  130, 132: 
  131, 133: 	/// @notice Increase the amount of AVAX a given staker is assigned by the protocol (for minipool creation)
  132, 134: 	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
- 133     :-	function increaseAVAXAssigned(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
+      135:+	function increaseAVAXAssigned(address stakerAddr, uint256 amount) public {
+      136:+		onlySpecificRegisteredContract("MinipoolManager", msg.sender);
  134, 137: 		int256 stakerIndex = requireValidStaker(stakerAddr);
  135, 138: 		addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssigned")), amount);
  136, 139: 	}
  137, 140: 
  138, 141: 	/// @notice Decrease the amount of AVAX a given staker is assigned by the protocol (for minipool creation)
  139, 142: 	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
- 140     :-	function decreaseAVAXAssigned(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
+      143:+	function decreaseAVAXAssigned(address stakerAddr, uint256 amount) public {
+      144:+		onlySpecificRegisteredContract("MinipoolManager", msg.sender);
  141, 145: 		int256 stakerIndex = requireValidStaker(stakerAddr);
  142, 146: 		subUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssigned")), amount);
  143, 147: 	}
@@ -153,14 +157,16 @@ contract Staking is Base {
  153, 157: 
  154, 158: 	/// @notice Increase the AVAXAssignedHighWater
  155, 159: 	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
- 156     :-	function increaseAVAXAssignedHighWater(address stakerAddr, uint256 amount) public onlyRegisteredNetworkContract {
+      160:+	function increaseAVAXAssignedHighWater(address stakerAddr, uint256 amount) public {
+      161:+		onlyRegisteredNetworkContract();
  157, 162: 		int256 stakerIndex = requireValidStaker(stakerAddr);
  158, 163: 		addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssignedHighWater")), amount);
  159, 164: 	}
  160, 165: 
  161, 166: 	/// @notice Reset the AVAXAssignedHighWater to what the current AVAXAssigned is for the staker
  162, 167: 	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
- 163     :-	function resetAVAXAssignedHighWater(address stakerAddr) public onlyRegisteredNetworkContract {
+      168:+	function resetAVAXAssignedHighWater(address stakerAddr) public {
+      169:+		onlyRegisteredNetworkContract();
  164, 170: 		int256 stakerIndex = requireValidStaker(stakerAddr);
  165, 171: 		uint256 currAVAXAssigned = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssigned")));
  166, 172: 		setUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssignedHighWater")), currAVAXAssigned);
@@ -177,14 +183,16 @@ contract Staking is Base {
  177, 183: 
  178, 184: 	/// @notice Increase the number of minipools the given staker has
  179, 185: 	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
- 180     :-	function increaseMinipoolCount(address stakerAddr) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
+      186:+	function increaseMinipoolCount(address stakerAddr) public {
+      187:+		onlySpecificRegisteredContract("MinipoolManager", msg.sender);
  181, 188: 		int256 stakerIndex = requireValidStaker(stakerAddr);
  182, 189: 		addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".minipoolCount")), 1);
  183, 190: 	}
  184, 191: 
  185, 192: 	/// @notice Decrease the number of minipools the given staker has
  186, 193: 	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
- 187     :-	function decreaseMinipoolCount(address stakerAddr) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
+      194:+	function decreaseMinipoolCount(address stakerAddr) public {
+      195:+		onlySpecificRegisteredContract("MinipoolManager", msg.sender);
  188, 196: 		int256 stakerIndex = requireValidStaker(stakerAddr);
  189, 197: 		subUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".minipoolCount")), 1);
  190, 198: 	}
@@ -201,7 +209,8 @@ contract Staking is Base {
  201, 209: 	/// @notice Set the timestamp when the staker registered for GGP rewards
  202, 210: 	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
  203, 211: 	// TODO cant use onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) since we also call from increaseMinipoolCount. Wat do?
- 204     :-	function setRewardsStartTime(address stakerAddr, uint256 time) public onlyRegisteredNetworkContract {
+      212:+	function setRewardsStartTime(address stakerAddr, uint256 time) public {
+      213:+		onlyRegisteredNetworkContract();
  205, 214: 		int256 stakerIndex = requireValidStaker(stakerAddr);
  206, 215: 		if (time > block.timestamp) {
  207, 216: 			revert InvalidRewardsStartTime();
@@ -221,14 +230,16 @@ contract Staking is Base {
  221, 230: 
  222, 231: 	/// @notice Increase the amount of GGP rewards the staker has earned and not claimed
  223, 232: 	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
- 224     :-	function increaseGGPRewards(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {
+      233:+	function increaseGGPRewards(address stakerAddr, uint256 amount) public {
+      234:+		onlySpecificRegisteredContract("ClaimNodeOp", msg.sender);
  225, 235: 		int256 stakerIndex = requireValidStaker(stakerAddr);
  226, 236: 		addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpRewards")), amount);
  227, 237: 	}
  228, 238: 
  229, 239: 	/// @notice Decrease the amount of GGP rewards the staker has earned and not claimed
  230, 240: 	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
- 231     :-	function decreaseGGPRewards(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {
+      241:+	function decreaseGGPRewards(address stakerAddr, uint256 amount) public {
+      242:+		onlySpecificRegisteredContract("ClaimNodeOp", msg.sender);
  232, 243: 		int256 stakerIndex = requireValidStaker(stakerAddr);
  233, 244: 		subUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpRewards")), amount);
  234, 245: 	}
@@ -245,7 +256,8 @@ contract Staking is Base {
  245, 256: 	/// @notice Set the most recent reward cycle number that the staker has been paid out for
  246, 257: 	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
  247, 258: 	/// @param cycleNumber The cycle that the staker was just rewarded for
- 248     :-	function setLastRewardsCycleCompleted(address stakerAddr, uint256 cycleNumber) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {
+      259:+	function setLastRewardsCycleCompleted(address stakerAddr, uint256 cycleNumber) public {
+      260:+		onlySpecificRegisteredContract("ClaimNodeOp", msg.sender);
  249, 261: 		int256 stakerIndex = requireValidStaker(stakerAddr);
  250, 262: 		setUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".lastRewardsCycleCompleted")), cycleNumber);
  251, 263: 	}
@@ -316,7 +328,8 @@ contract Staking is Base {
  316, 328: 
  317, 329: 	/// @notice Accept a GGP stake
  318, 330: 	/// @param amount The amount of GGP being staked
- 319     :-	function stakeGGP(uint256 amount) external whenNotPaused {
+      331:+	function stakeGGP(uint256 amount) external {
+      332:+		whenNotPaused();
  320, 333: 		// Transfer GGP tokens from staker to this contract
  321, 334: 		ggp.safeTransferFrom(msg.sender, address(this), amount);
  322, 335: 		_stakeGGP(msg.sender, amount);
@@ -325,7 +338,8 @@ contract Staking is Base {
  325, 338: 	/// @notice Convenience function to allow for restaking claimed GGP rewards
  326, 339: 	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
  327, 340: 	/// @param amount The amount of GGP being staked
- 328     :-	function restakeGGP(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {
+      341:+	function restakeGGP(address stakerAddr, uint256 amount) public {
+      342:+		onlySpecificRegisteredContract("ClaimNodeOp", msg.sender);
  329, 343: 		// Transfer GGP tokens from the ClaimNodeOp contract to this contract
  330, 344: 		ggp.safeTransferFrom(msg.sender, address(this), amount);
  331, 345: 		_stakeGGP(stakerAddr, amount);
@@ -355,7 +369,8 @@ contract Staking is Base {
  355, 369: 
  356, 370: 	/// @notice Allows the staker to unstake their GGP if they are over the 150% collateralization ratio
  357, 371: 	/// @param amount The amount of GGP being withdrawn
- 358     :-	function withdrawGGP(uint256 amount) external whenNotPaused {
+      372:+	function withdrawGGP(uint256 amount) external {
+      373:+		whenNotPaused();
  359, 374: 		if (amount > getGGPStake(msg.sender)) {
  360, 375: 			revert InsufficientBalance();
  361, 376: 		}
@@ -376,7 +391,8 @@ contract Staking is Base {
  376, 391: 	/// @notice Minipool Manager will call this if a minipool ended and was not in good standing
  377, 392: 	/// @param stakerAddr The C-chain address of a GGP staker in the protocol
  378, 393: 	/// @param ggpAmt The amount of GGP being slashed
- 379     :-	function slashGGP(address stakerAddr, uint256 ggpAmt) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
+      394:+	function slashGGP(address stakerAddr, uint256 ggpAmt) public {
+      395:+		onlySpecificRegisteredContract("MinipoolManager", msg.sender);
  380, 396: 		Vault vault = Vault(getContractAddress("Vault"));
  381, 397: 		decreaseGGPStake(stakerAddr, ggpAmt);
  382, 398: 		vault.transferToken("ProtocolDAO", ggp, ggpAmt);
```

##### - contracts/contract/Vault.sol

```diff
diff --git a/contracts/contract/Vault.sol b/contracts/contract/Vault.sol
index d7b5403..e218089 100644
--- a/contracts/contract/Vault.sol
+++ b/contracts/contract/Vault.sol
@@ -43,7 +43,8 @@ contract Vault is Base, ReentrancyGuard {
   43,  43: 	}
   44,  44: 
   45,  45: 	/// @notice Accept an AVAX deposit from a network contract
-  46     :-	function depositAVAX() external payable onlyRegisteredNetworkContract {
+       46:+	function depositAVAX() external payable {
+       47:+		onlyRegisteredNetworkContract();
   47,  48: 		// Valid Amount?
   48,  49: 		if (msg.value == 0) {
   49,  50: 			revert InvalidAmount();
@@ -58,7 +59,8 @@ contract Vault is Base, ReentrancyGuard {
   58,  59: 
   59,  60: 	/// @notice Withdraw an amount of AVAX to a network contract
   60,  61: 	/// @param amount Amount of AVAX to be withdrawn
-  61     :-	function withdrawAVAX(uint256 amount) external onlyRegisteredNetworkContract nonReentrant {
+       62:+	function withdrawAVAX(uint256 amount) external nonReentrant {
+       63:+		onlyRegisteredNetworkContract();
   62,  64: 		// Valid Amount?
   63,  65: 		if (amount == 0) {
   64,  66: 			revert InvalidAmount();
@@ -81,7 +83,8 @@ contract Vault is Base, ReentrancyGuard {
   81,  83: 	/// @dev No funds actually move, just bookeeping
   82,  84: 	/// @param toContractName Name of the contract fucns are being transferred to
   83,  85: 	/// @param amount How many AVAX to be transferred
-  84     :-	function transferAVAX(string memory toContractName, uint256 amount) external onlyRegisteredNetworkContract {
+       86:+	function transferAVAX(string memory toContractName, uint256 amount) external {
+       87:+		onlyRegisteredNetworkContract();
   85,  88: 		// Valid Amount?
   86,  89: 		if (amount == 0) {
   87,  90: 			revert InvalidAmount();
@@ -109,7 +112,8 @@ contract Vault is Base, ReentrancyGuard {
  109, 112: 		string memory networkContractName,
  110, 113: 		ERC20 tokenContract,
  111, 114: 		uint256 amount
- 112     :-	) external guardianOrRegisteredContract {
+      115:+	) external {
+      116:+		guardianOrRegisteredContract();
  113, 117: 		// Valid Amount?
  114, 118: 		if (amount == 0) {
  115, 119: 			revert InvalidAmount();
@@ -138,7 +142,8 @@ contract Vault is Base, ReentrancyGuard {
  138, 142: 		address withdrawalAddress,
  139, 143: 		ERC20 tokenAddress,
  140, 144: 		uint256 amount
- 141     :-	) external onlyRegisteredNetworkContract nonReentrant {
+      145:+	) external nonReentrant {
+      146:+		onlyRegisteredNetworkContract();
  142, 147: 		// Valid Amount?
  143, 148: 		if (amount == 0) {
  144, 149: 			revert InvalidAmount();
@@ -167,7 +172,8 @@ contract Vault is Base, ReentrancyGuard {
  167, 172: 		string memory networkContractName,
  168, 173: 		ERC20 tokenAddress,
  169, 174: 		uint256 amount
- 170     :-	) external onlyRegisteredNetworkContract {
+      175:+	) external {
+      176:+		onlyRegisteredNetworkContract();
  171, 177: 		// Valid Amount?
  172, 178: 		if (amount == 0) {
  173, 179: 			revert InvalidAmount();
@@ -201,11 +207,13 @@ contract Vault is Base, ReentrancyGuard {
  201, 207: 		return tokenBalances[keccak256(abi.encodePacked(networkContractName, tokenAddress))];
  202, 208: 	}
  203, 209: 
- 204     :-	function addAllowedToken(address tokenAddress) external onlyGuardian {
+      210:+	function addAllowedToken(address tokenAddress) external {
+      211:+		onlyGuardian();
  205, 212: 		allowedTokens[tokenAddress] = true;
  206, 213: 	}
  207, 214: 
- 208     :-	function removeAllowedToken(address tokenAddress) external onlyGuardian {
+      215:+	function removeAllowedToken(address tokenAddress) external {
+      216:+		onlyGuardian();
  209, 217: 		allowedTokens[tokenAddress] = false;
  210, 218: 	}
  211, 219: }
```

##### - contracts/contract/tokens/TokenggAVAX.sol

```diff 
diff --git a/contracts/contract/tokens/TokenggAVAX.sol b/contracts/contract/tokens/TokenggAVAX.sol
index dc3948d..4f5aaf0 100644
--- a/contracts/contract/tokens/TokenggAVAX.sol
+++ b/contracts/contract/tokens/TokenggAVAX.sol
@@ -139,7 +139,8 @@ contract TokenggAVAX is Initializable, ERC4626Upgradeable, UUPSUpgradeable, Base
  139, 139: 		return totalAssets_ - reservedAssets - stakingTotalAssets;
  140, 140: 	}
  141, 141: 
- 142     :-	function depositFromStaking(uint256 baseAmt, uint256 rewardAmt) public payable onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
+      142:+	function depositFromStaking(uint256 baseAmt, uint256 rewardAmt) public payable {
+      143:+		onlySpecificRegisteredContract("MinipoolManager", msg.sender);
  143, 144: 		uint256 totalAmt = msg.value;
  144, 145: 		if (totalAmt != (baseAmt + rewardAmt) || baseAmt > stakingTotalAssets) {
  145, 146: 			revert InvalidStakingDeposit();
@@ -150,7 +151,8 @@ contract TokenggAVAX is Initializable, ERC4626Upgradeable, UUPSUpgradeable, Base
  150, 151: 		IWAVAX(address(asset)).deposit{value: totalAmt}();
  151, 152: 	}
  152, 153: 
- 153     :-	function withdrawForStaking(uint256 assets) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
+      154:+	function withdrawForStaking(uint256 assets) public {
+      155:+		onlySpecificRegisteredContract("MinipoolManager", msg.sender);
  154, 156: 		if (assets > amountAvailableForStaking()) {
  155, 157: 			revert WithdrawAmountTooLarge();
  156, 158: 		}
@@ -252,5 +254,7 @@ contract TokenggAVAX is Initializable, ERC4626Upgradeable, UUPSUpgradeable, Base
  252, 254: 		totalReleasedAssets += amount;
  253, 255: 	}
  254, 256: 
- 255     :-	function _authorizeUpgrade(address newImplementation) internal override onlyGuardian {}
+      257:+	function _authorizeUpgrade(address newImplementation) internal override {
+      258:+		onlyGuardian();
+      259:+	}
  256, 260: }
```

##### - test/unit/BaseContractTest.sol

```diff
diff --git a/test/unit/BaseContractTest.sol b/test/unit/BaseContractTest.sol
index 99709a0..c5ba08c 100644
--- a/test/unit/BaseContractTest.sol
+++ b/test/unit/BaseContractTest.sol
@@ -251,36 +251,43 @@ contract BaseContractTest is Initializable, BaseTest, BaseAbstract {
  251, 251: contract MockBase is Base {
  252, 252: 	constructor(Storage _gogoStorageAddress) Base(_gogoStorageAddress) {}
  253, 253: 
- 254     :-	function onlyRegisteredNetworkContractFunction() public view onlyRegisteredNetworkContract returns (bool) {
+      254:+	function onlyRegisteredNetworkContractFunction() public view  returns (bool) {
+      255:+		onlyRegisteredNetworkContract();
  255, 256: 		return true;
  256, 257: 	}
  257, 258: 
- 258     :-	function onlySpecificRegisteredContractFunction() public view onlySpecificRegisteredContract("MinipoolManager", msg.sender) returns (bool) {
+      259:+	function onlySpecificRegisteredContractFunction() public view  returns (bool) {
+      260:+		onlySpecificRegisteredContract("MinipoolManager", msg.sender);
  259, 261: 		return true;
  260, 262: 	}
  261, 263: 
- 262     :-	function guardianOrRegisteredContractFunction() public view guardianOrRegisteredContract returns (bool) {
+      264:+	function guardianOrRegisteredContractFunction() public view returns (bool) {
+      265:+		guardianOrRegisteredContract();
  263, 266: 		return true;
  264, 267: 	}
  265, 268: 
  266, 269: 	function guardianOrSpecificRegisteredContractFunction()
  267, 270: 		public
  268, 271: 		view
- 269     :-		guardianOrSpecificRegisteredContract("MinipoolManager", msg.sender)
+      272:+		
  270, 273: 		returns (bool)
  271, 274: 	{
+      275:+		guardianOrSpecificRegisteredContract("MinipoolManager", msg.sender);
  272, 276: 		return true;
  273, 277: 	}
  274, 278: 
- 275     :-	function onlyGuardianFunction() public view onlyGuardian returns (bool) {
+      279:+	function onlyGuardianFunction() public view returns (bool) {
+      280:+		onlyGuardian();
  276, 281: 		return true;
  277, 282: 	}
  278, 283: 
- 279     :-	function onlyMultisigFunction() public view onlyMultisig returns (bool) {
+      284:+	function onlyMultisigFunction() public view returns (bool) {
+      285:+		onlyMultisig();
  280, 286: 		return true;
  281, 287: 	}
  282, 288: 
- 283     :-	function whenNotPausedFunction() public view whenNotPaused returns (bool) {
+      289:+	function whenNotPausedFunction() public view returns (bool) {
+      290:+		whenNotPaused();
  284, 291: 		return true;
  285, 292: 	}
  286, 293: }
```

---

Deployment. Gas Saved: **2 274 105**

Minimum Method Call. Gas Saved: **1 954**

Average Method Call. Gas Saved: **37 304**

Maximum Method Call. Gas Saved: **230 123**

Overall gas change: **-255 082** (NaN%)

```diff
diff --git a/original.txt b/report.txt
index a38044f..394b5f2 100644
--- a/original.txt
+++ b/report.txt
-Test result: [32mok[0m. 30 passed; 0 failed; finished in 7.82s
+Test result: [32mok[0m. 30 passed; 0 failed; finished in 8.06s
 | contracts/contract/ClaimNodeOp.sol:ClaimNodeOp contract |                 |       |        |        |         |
 |---------------------------------------------------------|-----------------|-------|--------|--------|---------|
 | Deployment Cost                                         | Deployment Size |       |        |        |         |
-| 969555                                                  | 5021            |       |        |        |         |
+| 965955                                                  | 5003            |       |        |        |         |
 | Function Name                                           | min             | avg   | median | max    | # calls |
-| calculateAndDistributeRewards                           | 7131            | 81112 | 88407  | 120850 | 22      |
-| claimAndRestake                                         | 5385            | 74705 | 117161 | 128409 | 5       |
+| calculateAndDistributeRewards                           | 7123            | 81045 | 88340  | 120736 | 22      |
+| claimAndRestake                                         | 5367            | 74772 | 117264 | 128572 | 5       |
 | getRewardsCycleTotal                                    | 1280            | 1280  | 1280   | 1280   | 7       |
-| isEligible                                              | 7127            | 9323  | 8822   | 12822  | 57      |
-| setRewardsCycleTotal                                    | 3239            | 24268 | 29239  | 33739  | 21      |
+| isEligible                                              | 7020            | 9285  | 8786   | 12786  | 57      |
+| setRewardsCycleTotal                                    | 3269            | 24297 | 29269  | 33769  | 21      |
 
 
 | contracts/contract/ClaimProtocolDAO.sol:ClaimProtocolDAO contract |                 |       |        |       |         |
 |-------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
 | Deployment Cost                                                   | Deployment Size |       |        |       |         |
-| 334933                                                            | 1755            |       |        |       |         |
+| 336739                                                            | 1764            |       |        |       |         |
 | Function Name                                                     | min             | avg   | median | max   | # calls |
-| spend                                                             | 8249            | 28013 | 18697  | 66411 | 4       |
+| spend                                                             | 8264            | 28046 | 18711  | 66500 | 4       |
 
 
 | contracts/contract/MinipoolManager.sol:MinipoolManager contract |                 |        |        |        |         |
 |-----------------------------------------------------------------|-----------------|--------|--------|--------|---------|
 | Deployment Cost                                                 | Deployment Size |        |        |        |         |
-| 3918819                                                         | 19732           |        |        |        |         |
+| 3894386                                                         | 19610           |        |        |        |         |
 | Function Name                                                   | min             | avg    | median | max    | # calls |
-| calculateGGPSlashAmt                                            | 4902            | 5902   | 4902   | 8902   | 4       |
-| canClaimAndInitiateStaking                                      | 3252            | 14333  | 15874  | 31049  | 26      |
-| cancelMinipool                                                  | 11524           | 45372  | 46547  | 75836  | 8       |
-| cancelMinipoolByMultisig                                        | 3315            | 36830  | 26512  | 80664  | 3       |
-| claimAndInitiateStaking                                         | 3295            | 94791  | 108979 | 153379 | 40      |
-| createMinipool                                                  | 8610            | 375485 | 429033 | 474533 | 122     |
-| finishFailedMinipoolByMultisig                                  | 3250            | 5659   | 5659   | 8068   | 2       |
-| getExpectedAVAXRewardsAmt                                       | 3976            | 5226   | 3976   | 16976  | 20      |
+| calculateGGPSlashAmt                                            | 4897            | 5897   | 4897   | 8897   | 4       |
+| canClaimAndInitiateStaking                                      | 3244            | 14289  | 15827  | 31002  | 26      |
+| cancelMinipool                                                  | 11503           | 45389  | 46561  | 75896  | 8       |
+| cancelMinipoolByMultisig                                        | 3307            | 36856  | 26512  | 80749  | 3       |
+| claimAndInitiateStaking                                         | 3287            | 94830  | 109025 | 153425 | 40      |
+| createMinipool                                                  | 8636            | 375556 | 429107 | 474607 | 122     |
+| finishFailedMinipoolByMultisig                                  | 3242            | 5654   | 5654   | 8066   | 2       |
+| getExpectedAVAXRewardsAmt                                       | 3961            | 5211   | 3961   | 16961  | 20      |
 | getIndexOf                                                      | 1762            | 1842   | 1762   | 8262   | 81      |
 | getMinipool                                                     | 24592           | 36781  | 42592  | 60592  | 74      |
-| getMinipoolByNodeID                                             | 26605           | 31699  | 26605  | 44605  | 53      |
+| getMinipoolByNodeID                                             | 26592           | 31686  | 26592  | 44592  | 53      |
 | getMinipoolCount                                                | 1337            | 4587   | 4587   | 7837   | 2       |
-| getMinipools                                                    | 251601          | 344064 | 344064 | 436527 | 2       |
+| getMinipools                                                    | 250631          | 342949 | 342949 | 435267 | 2       |
 | getTotalAVAXLiquidStakerAmt                                     | 1359            | 1959   | 1359   | 5359   | 10      |
 | receiveWithdrawalAVAX                                           | 184             | 184    | 184    | 184    | 87      |
-| recordStakingEnd                                                | 3267            | 89075  | 125393 | 159311 | 35      |
-| recordStakingError                                              | 3248            | 32536  | 8821   | 84808  | 6       |
-| recordStakingStart                                              | 3335            | 102470 | 112366 | 133066 | 34      |
-| recreateMinipool                                                | 7368            | 74279  | 102904 | 112566 | 3       |
-| withdrawMinipoolFunds                                           | 36999           | 50899  | 58599  | 58599  | 8       |
+| recordStakingEnd                                                | 3259            | 89109  | 125453 | 159425 | 35      |
+| recordStakingError                                              | 3240            | 32545  | 8813   | 84852  | 6       |
+| recordStakingStart                                              | 3327            | 102464 | 112361 | 133061 | 34      |
+| recreateMinipool                                                | 7386            | 74309  | 102959 | 112583 | 3       |
+| withdrawMinipoolFunds                                           | 37041           | 50941  | 58641  | 58641  | 8       |
 
 
 | contracts/contract/MultisigManager.sol:MultisigManager contract |                 |       |        |       |         |
 |-----------------------------------------------------------------|-----------------|-------|--------|-------|---------|
 | Deployment Cost                                                 | Deployment Size |       |        |       |         |
-| 696692                                                          | 3562            |       |        |       |         |
+| 657054                                                          | 3364            |       |        |       |         |
 | Function Name                                                   | min             | avg   | median | max   | # calls |
 | MULTISIG_LIMIT                                                  | 251             | 251   | 251    | 251   | 1       |
-| disableMultisig                                                 | 6103            | 11199 | 10747  | 20128 | 8       |
-| enableMultisig                                                  | 7900            | 27733 | 27933  | 27933 | 184     |
+| disableMultisig                                                 | 6136            | 11233 | 10780  | 20170 | 8       |
+| enableMultisig                                                  | 7915            | 27764 | 27964  | 27964 | 184     |
 | getCount                                                        | 1313            | 3275  | 1313   | 7813  | 27      |
 | getIndexOf                                                      | 1714            | 2114  | 1714   | 3714  | 5       |
-| getMultisig                                                     | 3050            | 4535  | 3050   | 7050  | 35      |
-| registerMultisig                                                | 5930            | 73588 | 76318  | 76318 | 196     |
-| requireNextActiveMultisig                                       | 4118            | 7242  | 4118   | 12118 | 119     |
+| getMultisig                                                     | 3039            | 4524  | 3039   | 7039  | 35      |
+| registerMultisig                                                | 5943            | 73629 | 76359  | 76359 | 196     |
+| requireNextActiveMultisig                                       | 4101            | 7228  | 4107   | 12107 | 119     |
 
 
 | contracts/contract/Ocyticus.sol:Ocyticus contract |                 |       |        |        |         |
 |---------------------------------------------------|-----------------|-------|--------|--------|---------|
 | Deployment Cost                                   | Deployment Size |       |        |        |         |
-| 569114                                            | 2831            |       |        |        |         |
+| 515663                                            | 2564            |       |        |        |         |
 | Function Name                                     | min             | avg   | median | max    | # calls |
-| addDefender                                       | 30060           | 30060 | 30060  | 30060  | 2       |
+| addDefender                                       | 30084           | 30084 | 30084  | 30084  | 2       |
 | defenders                                         | 525             | 525   | 525    | 525    | 2       |
-| disableAllMultisigs                               | 2399            | 13343 | 7348   | 30282  | 3       |
-| pauseEverything                                   | 2420            | 84853 | 121820 | 130320 | 3       |
-| removeDefender                                    | 1331            | 1331  | 1331   | 1331   | 1       |
-| resumeEverything                                  | 2442            | 9319  | 12758  | 12758  | 3       |
+| disableAllMultisigs                               | 2414            | 13313 | 7288   | 30238  | 3       |
+| pauseEverything                                   | 2435            | 84919 | 121911 | 130411 | 3       |
+| removeDefender                                    | 1350            | 1350  | 1350   | 1350   | 1       |
+| resumeEverything                                  | 2457            | 9382  | 12845  | 12845  | 3       |
 
 
 | contracts/contract/Oracle.sol:Oracle contract |                 |       |        |       |         |
 |-----------------------------------------------|-----------------|-------|--------|-------|---------|
 | Deployment Cost                               | Deployment Size |       |        |       |         |
-| 506702                                        | 2613            |       |        |       |         |
+| 508902                                        | 2624            |       |        |       |         |
 | Function Name                                 | min             | avg   | median | max   | # calls |
 | getGGPPriceInAVAX                             | 2395            | 3655  | 2395   | 8395  | 238     |
-| getGGPPriceInAVAXFromOneInch                  | 10299           | 10299 | 10299  | 10299 | 1       |
-| setGGPPriceInAVAX                             | 9717            | 52107 | 53617  | 53617 | 182     |
-| setOneInch                                    | 7921            | 20709 | 20709  | 33498 | 2       |
+| getGGPPriceInAVAXFromOneInch                  | 10294           | 10294 | 10294  | 10294 | 1       |
+| setGGPPriceInAVAX                             | 9730            | 52120 | 53630  | 53630 | 182     |
+| setOneInch                                    | 7936            | 20732 | 20732  | 33528 | 2       |
 
 
 | contracts/contract/ProtocolDAO.sol:ProtocolDAO contract |                 |        |        |        |         |
 |---------------------------------------------------------|-----------------|--------|--------|--------|---------|
 | Deployment Cost                                         | Deployment Size |        |        |        |         |
-| 1437856                                                 | 7264            |        |        |        |         |
+| 1284092                                                 | 6496            |        |        |        |         |
 | Function Name                                           | min             | avg    | median | max    | # calls |
 | getClaimingContractPct                                  | 2201            | 3896   | 4201   | 8701   | 74      |
 | getContractPaused                                       | 2214            | 2821   | 2214   | 8714   | 14      |
@@ -406,89 +430,89 @@ Test result: [32mok[0m. 30 passed; 0 failed; finished in 7.82s
 | getRewardsEligibilityMinSeconds                         | 1337            | 1793   | 1337   | 7837   | 58      |
 | getTargetGGAVAXReserveRate                              | 1336            | 2137   | 1336   | 7836   | 88      |
 | getTotalGGPCirculatingSupply                            | 1337            | 2903   | 3337   | 7837   | 30      |
-| initialize                                              | 423859          | 423859 | 423859 | 423859 | 174     |
-| pauseContract                                           | 8815            | 30166  | 34654  | 34654  | 25      |
-| registerContract                                        | 8404            | 63337  | 81232  | 82482  | 4       |
-| resumeContract                                          | 3404            | 3404   | 3404   | 3404   | 7       |
-| setClaimingContractPct                                  | 3829            | 6172   | 6391   | 10391  | 5       |
-| setExpectedAVAXRewardsRate                              | 7104            | 12675  | 13500  | 16595  | 4       |
-| setTotalGGPCirculatingSupply                            | 3321            | 9117   | 10121  | 10121  | 22      |
-| unregisterContract                                      | 7698            | 7842   | 7842   | 7987   | 2       |
-| upgradeExistingContract                                 | 8478            | 37982  | 37982  | 67487  | 2       |
+| initialize                                              | 423995          | 423995 | 423995 | 423995 | 174     |
+| pauseContract                                           | 8830            | 30195  | 34684  | 34684  | 25      |
+| registerContract                                        | 8419            | 63372  | 81274  | 82524  | 4       |
+| resumeContract                                          | 3428            | 3428   | 3428   | 3428   | 7       |
+| setClaimingContractPct                                  | 3844            | 6215   | 6445   | 10445  | 5       |
+| setExpectedAVAXRewardsRate                              | 7096            | 12694  | 13527  | 16626  | 4       |
+| setTotalGGPCirculatingSupply                            | 3351            | 9146   | 10151  | 10151  | 22      |
+| unregisterContract                                      | 7740            | 7871   | 7871   | 8002   | 2       |
+| upgradeExistingContract                                 | 8493            | 38037  | 38037  | 67582  | 2       |
 
 
 | contracts/contract/RewardsPool.sol:RewardsPool contract |                 |        |        |        |         |
 |---------------------------------------------------------|-----------------|--------|--------|--------|---------|
 | Deployment Cost                                         | Deployment Size |        |        |        |         |
-| 1143951                                                 | 5796            |        |        |        |         |
+| 1134338                                                 | 5748            |        |        |        |         |
 | Function Name                                           | min             | avg    | median | max    | # calls |
-| canStartRewardsCycle                                    | 4881            | 12933  | 13504  | 15504  | 14      |
-| getClaimingContractDistribution                         | 6419            | 10590  | 7340   | 21262  | 4       |
-| getInflationAmt                                         | 10272           | 124631 | 14272  | 459708 | 4       |
+| canStartRewardsCycle                                    | 4876            | 12924  | 13494  | 15494  | 14      |
+| getClaimingContractDistribution                         | 6417            | 10588  | 7338   | 21260  | 4       |
+| getInflationAmt                                         | 10194           | 96044  | 14194  | 345594 | 4       |
 | getInflationIntervalStartTime                           | 5293            | 5293   | 5293   | 5293   | 1       |
-| getInflationIntervalsElapsed                            | 4804            | 12304  | 12304  | 19804  | 2       |
+| getInflationIntervalsElapsed                            | 4799            | 12299  | 12299  | 19799  | 2       |
 | getRewardsCycleCount                                    | 1292            | 1392   | 1292   | 5292   | 60      |
 | getRewardsCycleStartTime                                | 1314            | 3904   | 5314   | 7814   | 11      |
 | getRewardsCycleTotalAmt                                 | 1324            | 1324   | 1324   | 1324   | 3       |
-| getRewardsCyclesElapsed                                 | 4759            | 6259   | 4759   | 10759  | 4       |
-| initialize                                              | 72755           | 72755  | 72755  | 72755  | 174     |
-| startRewardsCycle                                       | 10876           | 256692 | 289160 | 773780 | 23      |
+| getRewardsCyclesElapsed                                 | 4754            | 6254   | 4754   | 10754  | 4       |
+| initialize                                              | 72801           | 72801  | 72801  | 72801  | 174     |
+| startRewardsCycle                                       | 10871           | 250179 | 287283 | 662083 | 23      |
 
 
 | contracts/contract/Staking.sol:Staking contract |                 |        |        |        |         |
 |-------------------------------------------------|-----------------|--------|--------|--------|---------|
 | Deployment Cost                                 | Deployment Size |        |        |        |         |
-| 2368350                                         | 12048           |        |        |        |         |
+| 2137084                                         | 10893           |        |        |        |         |
 | Function Name                                   | min             | avg    | median | max    | # calls |
-| decreaseAVAXAssigned                            | 4328            | 4428   | 4328   | 6568   | 33      |
-| decreaseAVAXStake                               | 4303            | 4379   | 4303   | 5378   | 14      |
-| decreaseGGPRewards                              | 4311            | 4580   | 4311   | 5388   | 4       |
-| decreaseMinipoolCount                           | 4284            | 4421   | 4284   | 8124   | 28      |
-| getAVAXAssigned                                 | 3123            | 3296   | 3123   | 9123   | 46      |
-| getAVAXAssignedHighWater                        | 3061            | 4321   | 5061   | 5061   | 46      |
-| getAVAXStake                                    | 3176            | 3176   | 3176   | 3176   | 7       |
-| getCollateralizationRatio                       | 5156            | 14850  | 10644  | 21144  | 121     |
-| getEffectiveGGPStaked                           | 14646           | 26511  | 24320  | 33810  | 47      |
-| getGGPRewards                                   | 3101            | 3294   | 3101   | 5101   | 31      |
-| getGGPStake                                     | 3153            | 3153   | 3153   | 3153   | 15      |
+| decreaseAVAXAssigned                            | 4342            | 4442   | 4342   | 6582   | 33      |
+| decreaseAVAXStake                               | 4316            | 4393   | 4316   | 5395   | 14      |
+| decreaseGGPRewards                              | 4324            | 4594   | 4324   | 5405   | 4       |
+| decreaseMinipoolCount                           | 4298            | 4435   | 4298   | 8138   | 28      |
+| getAVAXAssigned                                 | 3110            | 3283   | 3110   | 9110   | 46      |
+| getAVAXAssignedHighWater                        | 3048            | 4308   | 5048   | 5048   | 46      |
+| getAVAXStake                                    | 3163            | 3163   | 3163   | 3163   | 7       |
+| getCollateralizationRatio                       | 5143            | 14809  | 10603  | 21103  | 121     |
+| getEffectiveGGPStaked                           | 14592           | 26406  | 24220  | 33682  | 47      |
+| getGGPRewards                                   | 3088            | 3281   | 3088   | 5088   | 31      |
+| getGGPStake                                     | 3140            | 3140   | 3140   | 3140   | 15      |
 | getIndexOf                                      | 1752            | 1752   | 1752   | 1752   | 1       |
-| getLastRewardsCycleCompleted                    | 3083            | 3355   | 3083   | 5083   | 22      |
-| getMinimumGGPStake                              | 11183           | 11183  | 11183  | 11183  | 2       |
-| getMinipoolCount                                | 3080            | 3080   | 3080   | 3080   | 32      |
-| getRewardsStartTime                             | 3079            | 3984   | 3079   | 5079   | 190     |
-| getStaker                                       | 15143           | 15143  | 15143  | 15143  | 1       |
+| getLastRewardsCycleCompleted                    | 3070            | 3342   | 3070   | 5070   | 22      |
+| getMinimumGGPStake                              | 11137           | 11137  | 11137  | 11137  | 2       |
+| getMinipoolCount                                | 3067            | 3067   | 3067   | 3067   | 32      |
+| getRewardsStartTime                             | 3066            | 3971   | 3066   | 5066   | 190     |
+| getStaker                                       | 15132           | 15132  | 15132  | 15132  | 1       |
 | getStakerCount                                  | 1382            | 3548   | 1382   | 7882   | 3       |
-| getStakers                                      | 5985            | 25065  | 17365  | 94385  | 12      |
-| getTotalGGPStake                                | 3792            | 6542   | 3792   | 14792  | 4       |
-| increaseAVAXAssigned                            | 5368            | 21558  | 27268  | 27268  | 121     |
-| increaseAVAXAssignedHighWater                   | 24967           | 25096  | 24967  | 26967  | 31      |
-| increaseAVAXStake                               | 5379            | 21206  | 27279  | 27279  | 120     |
-| increaseGGPRewards                              | 5421            | 24726  | 25321  | 29321  | 20      |
-| increaseMinipoolCount                           | 5366            | 22163  | 27266  | 29266  | 122     |
+| getStakers                                      | 5985            | 24851  | 17222  | 93527  | 12      |
+| getTotalGGPStake                                | 3771            | 6521   | 3771   | 14771  | 4       |
+| increaseAVAXAssigned                            | 5385            | 21575  | 27285  | 27285  | 121     |
+| increaseAVAXAssignedHighWater                   | 24977           | 25106  | 24977  | 26977  | 31      |
+| increaseAVAXStake                               | 5396            | 21223  | 27296  | 27296  | 120     |
+| increaseGGPRewards                              | 5438            | 24743  | 25338  | 29338  | 20      |
+| increaseMinipoolCount                           | 5383            | 22180  | 27283  | 29283  | 122     |
 | requireValidStaker                              | 1890            | 2079   | 1890   | 5877   | 21      |
-| resetAVAXAssignedHighWater                      | 4841            | 5159   | 4841   | 6051   | 19      |
-| restakeGGP                                      | 55092           | 55492  | 55092  | 56692  | 4       |
-| setLastRewardsCycleCompleted                    | 5089            | 23883  | 24989  | 24989  | 18      |
-| setRewardsStartTime                             | 3812            | 22779  | 26664  | 26664  | 99      |
-| slashGGP                                        | 34975           | 38137  | 39718  | 39718  | 3       |
-| stakeGGP                                        | 57016           | 146629 | 172113 | 201141 | 131     |
-| withdrawGGP                                     | 7238            | 24286  | 27079  | 37079  | 11      |
+| resetAVAXAssignedHighWater                      | 4860            | 5179   | 4860   | 6075   | 19      |
+| restakeGGP                                      | 55140           | 55540  | 55140  | 56740  | 4       |
+| setLastRewardsCycleCompleted                    | 5118            | 23912  | 25018  | 25018  | 18      |
+| setRewardsStartTime                             | 3729            | 22767  | 26667  | 26667  | 99      |
+| slashGGP                                        | 35044           | 38218  | 39805  | 39805  | 3       |
+| stakeGGP                                        | 57064           | 146693 | 172176 | 201219 | 131     |
+| withdrawGGP                                     | 7251            | 24309  | 27143  | 37143  | 11      |
 
 
 | contracts/contract/Storage.sol:Storage contract |                 |       |        |       |         |
 |-------------------------------------------------|-----------------|-------|--------|-------|---------|
 | Deployment Cost                                 | Deployment Size |       |        |       |         |
-| 1091363                                         | 5441            |       |        |       |         |
+| 671539                                          | 3344            |       |        |       |         |
 | Function Name                                   | min             | avg   | median | max   | # calls |
-| addUint                                         | 1184            | 17224 | 21084  | 25084 | 1030    |
+| addUint                                         | 1190            | 17230 | 21090  | 25090 | 1030    |
 | confirmGuardian                                 | 3980            | 3980  | 3980   | 3980  | 1       |
-| deleteAddress                                   | 808             | 808   | 808    | 808   | 4       |
-| deleteBool                                      | 813             | 1773  | 813    | 4653  | 4       |
-| deleteBytes                                     | 912             | 912   | 912    | 912   | 1       |
-| deleteBytes32                                   | 687             | 687   | 687    | 687   | 1       |
-| deleteInt                                       | 686             | 686   | 686    | 686   | 1       |
-| deleteString                                    | 956             | 956   | 956    | 956   | 4       |
-| deleteUint                                      | 702             | 702   | 702    | 702   | 1       |
+| deleteAddress                                   | 813             | 813   | 813    | 813   | 4       |
+| deleteBool                                      | 818             | 1778  | 818    | 4658  | 4       |
+| deleteBytes                                     | 917             | 917   | 917    | 917   | 1       |
+| deleteBytes32                                   | 692             | 692   | 692    | 692   | 1       |
+| deleteInt                                       | 691             | 691   | 691    | 691   | 1       |
+| deleteString                                    | 969             | 969   | 969    | 969   | 4       |
+| deleteUint                                      | 707             | 707   | 707    | 707   | 1       |
 | getAddress                                      | 537             | 848   | 537    | 2537  | 3638    |
 | getBool                                         | 539             | 1364  | 539    | 2539  | 1887    |
 | getBytes                                        | 1161            | 1288  | 1288   | 1416  | 2       |
@@ -496,35 +520,35 @@ Test result: [32mok[0m. 30 passed; 0 failed; finished in 7.82s
 | getGuardian                                     | 365             | 547   | 365    | 2365  | 1171    |
 | getInt                                          | 527             | 527   | 527    | 527   | 3       |
 | getString                                       | 1184            | 1953  | 1439   | 3439  | 553     |
-| getUint                                         | 549             | 1076  | 549    | 2549  | 8755    |
-| setAddress                                      | 1154            | 23147 | 23185  | 25054 | 2961    |
-| setBool                                         | 866             | 22853 | 23113  | 25113 | 3071    |
-| setBytes                                        | 25482           | 25482 | 25482  | 25482 | 1       |
-| setBytes32                                      | 742             | 18987 | 20827  | 24927 | 40      |
+| getUint                                         | 549             | 1076  | 549    | 2549  | 8754    |
+| setAddress                                      | 1160            | 23153 | 23191  | 25060 | 2961    |
+| setBool                                         | 871             | 22859 | 23119  | 25119 | 3071    |
+| setBytes                                        | 25488           | 25488 | 25488  | 25488 | 1       |
+| setBytes32                                      | 747             | 18993 | 20833  | 24933 | 40      |
 | setGuardian                                     | 22722           | 22722 | 22722  | 22722 | 1       |
-| setInt                                          | 2902            | 20469 | 25970  | 27036 | 4       |
-| setString                                       | 23503           | 23634 | 23634  | 25634 | 2325    |
-| setUint                                         | 705             | 14292 | 22881  | 24881 | 8269    |
-| subUint                                         | 947             | 1027  | 947    | 4787  | 116     |
+| setInt                                          | 2899            | 20473 | 25976  | 27042 | 4       |
+| setString                                       | 23509           | 23640 | 23640  | 25640 | 2325    |
+| setUint                                         | 710             | 14298 | 22887  | 24887 | 8269    |
+| subUint                                         | 952             | 1033  | 952    | 4792  | 116     |
 
 
 | contracts/contract/Vault.sol:Vault contract |                 |       |        |       |         |
 |---------------------------------------------|-----------------|-------|--------|-------|---------|
 | Deployment Cost                             | Deployment Size |       |        |       |         |
-| 1200490                                     | 5972            |       |        |       |         |
+| 1064351                                     | 5292            |       |        |       |         |
 | Function Name                               | min             | avg   | median | max   | # calls |
-| addAllowedToken                             | 1336            | 23442 | 23570  | 23570 | 175     |
+| addAllowedToken                             | 1356            | 23466 | 23594  | 23594 | 175     |
 | balanceOf                                   | 1149            | 1359  | 1149   | 3149  | 19      |
 | balanceOfToken                              | 1415            | 2138  | 1424   | 3424  | 25      |
-| depositAVAX()                               | 6769            | 26263 | 28669  | 30669 | 54      |
-| depositAVAX()(uint256)                      | 3500            | 19166 | 26669  | 30669 | 90      |
-| depositToken                                | 4975            | 32790 | 38630  | 55385 | 224     |
-| removeAllowedToken                          | 3607            | 7007  | 7007   | 10407 | 2       |
-| transferAVAX                                | 2060            | 10654 | 2083   | 27819 | 3       |
-| transferToken                               | 10361           | 31985 | 32261  | 43070 | 43      |
-| withdrawAVAX(uint256)                       | 14168           | 14933 | 14168  | 15768 | 23      |
-| withdrawAVAX(uint256)(uint256)              | 6656            | 14971 | 15768  | 22808 | 30      |
-| withdrawToken                               | 6819            | 27429 | 32759  | 42932 | 34      |
+| depositAVAX()                               | 3519            | 26414 | 28699  | 30699 | 67      |
+| depositAVAX()(uint256)                      | 5440            | 17893 | 26699  | 28699 | 77      |
+| depositToken                                | 5027            | 32830 | 38668  | 55433 | 224     |
+| removeAllowedToken                          | 3626            | 7026  | 7026   | 10426 | 2       |
+| transferAVAX                                | 2052            | 10673 | 2102   | 27866 | 3       |
+| transferToken                               | 10423           | 32046 | 32323  | 43132 | 43      |
+| withdrawAVAX(uint256)                       | 6672            | 14798 | 15816  | 15852 | 32      |
+| withdrawAVAX(uint256)(uint256)              | 14216           | 15313 | 14216  | 22856 | 21      |
+| withdrawToken                               | 6846            | 27496 | 32834  | 42992 | 34      |
 
 
 | contracts/contract/tokens/TokenGGP.sol:TokenGGP contract |                 |       |        |       |         |
@@ -542,20 +566,20 @@ Test result: [32mok[0m. 30 passed; 0 failed; finished in 7.82s
 | contracts/contract/tokens/TokenggAVAX.sol:TokenggAVAX contract |                 |        |        |        |         |
 |----------------------------------------------------------------|-----------------|--------|--------|--------|---------|
 | Deployment Cost                                                | Deployment Size |        |        |        |         |
-| 2850662                                                        | 14408           |        |        |        |         |
+| 2780977                                                        | 14060           |        |        |        |         |
 | Function Name                                                  | min             | avg    | median | max    | # calls |
 | allowance                                                      | 786             | 786    | 786    | 786    | 4       |
-| amountAvailableForStaking                                      | 4525            | 7975   | 8874   | 17374  | 32      |
+| amountAvailableForStaking                                      | 4497            | 7947   | 8846   | 17346  | 32      |
 | approve                                                        | 2585            | 10268  | 4685   | 22485  | 12      |
 | asset                                                          | 428             | 428    | 428    | 428    | 1       |
 | balanceOf                                                      | 571             | 571    | 571    | 571    | 41      |
 | convertToAssets                                                | 481             | 1264   | 1486   | 1486   | 31      |
 | convertToShares                                                | 1160            | 1641   | 1509   | 3509   | 17      |
 | decimals                                                       | 2381            | 2381   | 2381   | 2381   | 1       |
-| deposit                                                        | 2857            | 43226  | 27167  | 107150 | 121     |
-| depositAVAX()                                                  | 18946           | 101539 | 111538 | 121338 | 15      |
-| depositAVAX()(uint256)                                         | 2565            | 80844  | 111538 | 121338 | 26      |
-| depositFromStaking                                             | 8540            | 12828  | 12285  | 15356  | 26      |
+| deposit                                                        | 2881            | 43233  | 27173  | 107158 | 121     |
+| depositAVAX()                                                  | 2586            | 94294  | 111538 | 121338 | 23      |
+| depositAVAX()(uint256)                                         | 18946           | 80905  | 111538 | 121338 | 18      |
+| depositFromStaking                                             | 8565            | 12831  | 12287  | 15358  | 26      |
 | initialize                                                     | 3016            | 183937 | 184815 | 184815 | 208     |
 | lastRewardsAmt                                                 | 416             | 416    | 416    | 416    | 19      |
 | lastSync                                                       | 397             | 397    | 397    | 397    | 8       |
@@ -563,15 +587,15 @@ Test result: [32mok[0m. 30 passed; 0 failed; finished in 7.82s
 | maxMint                                                        | 408             | 408    | 408    | 408    | 1       |
 | maxRedeem                                                      | 3511            | 4369   | 4199   | 6209   | 5       |
 | maxWithdraw                                                    | 3529            | 4627   | 4227   | 6227   | 7       |
-| mint                                                           | 3783            | 21163  | 11686  | 89991  | 9       |
+| mint                                                           | 3798            | 21171  | 11694  | 89999  | 9       |
 | name                                                           | 3278            | 3278   | 3278   | 3278   | 1       |
-| previewDeposit                                                 | 2631            | 2974   | 2891   | 3654   | 6       |
-| previewMint                                                    | 2570            | 3018   | 2853   | 3633   | 3       |
-| previewRedeem                                                  | 2541            | 2916   | 2890   | 3656   | 6       |
-| previewWithdraw                                                | 2542            | 3796   | 3668   | 4891   | 9       |
+| previewDeposit                                                 | 2655            | 2997   | 2915   | 3669   | 6       |
+| previewMint                                                    | 2594            | 3039   | 2877   | 3648   | 3       |
+| previewRedeem                                                  | 2565            | 2939   | 2914   | 3671   | 6       |
+| previewWithdraw                                                | 2577            | 3829   | 3694   | 4926   | 9       |
 | receive                                                        | 195             | 195    | 195    | 195    | 43      |
-| redeem                                                         | 947             | 8948   | 11868  | 12493  | 10      |
-| redeemAVAX                                                     | 2665            | 22582  | 22830  | 42004  | 4       |
+| redeem                                                         | 947             | 8969   | 11892  | 12517  | 10      |
+| redeemAVAX                                                     | 2689            | 22602  | 22847  | 42024  | 4       |
 | rewardsCycleEnd                                                | 379             | 379    | 379    | 379    | 4       |
 | rewardsCycleLength                                             | 441             | 441    | 441    | 441    | 19      |
 | stakingTotalAssets                                             | 352             | 752    | 352    | 2352   | 5       |
@@ -579,10 +603,10 @@ Test result: [32mok[0m. 30 passed; 0 failed; finished in 7.82s
 | syncRewards                                                    | 488             | 29608  | 30632  | 31432  | 214     |
 | totalAssets                                                    | 778             | 1261   | 1127   | 2778   | 40      |
 | totalSupply                                                    | 375             | 375    | 375    | 375    | 11      |
-| withdraw                                                       | 3471            | 13314  | 11801  | 31352  | 11      |
-| withdrawAVAX(uint256)                                          | 3688            | 17339  | 22003  | 26328  | 3       |
-| withdrawAVAX(uint256)(uint256)                                 | 52503           | 52503  | 52503  | 52503  | 2       |
-| withdrawForStaking                                             | 16603           | 43276  | 47855  | 53855  | 38      |
+| withdraw                                                       | 3506            | 13347  | 11836  | 31387  | 11      |
+| withdrawAVAX(uint256)                                          | 3714            | 26158  | 24191  | 52538  | 4       |
+| withdrawAVAX(uint256)(uint256)                                 | 52538           | 52538  | 52538  | 52538  | 1       |
+| withdrawForStaking                                             | 16603           | 43272  | 47851  | 53851  | 38      |
 
 
 | contracts/contract/utils/OneInchMock.sol:OneInchMock contract |                 |      |        |      |         |
@@ -599,12 +623,12 @@ Test result: [32mok[0m. 30 passed; 0 failed; finished in 7.82s
 | 1086952                                                               | 5310            |        |        |         |         |
 | Function Name                                                         | min             | avg    | median | max     | # calls |
 | depositggAVAX                                                         | 27194           | 86336  | 138086 | 138086  | 15      |
-| processGGPRewards                                                     | 157172          | 460744 | 432434 | 1143124 | 12      |
-| processMinipoolEndWithRewards                                         | 181140          | 193896 | 191842 | 210525  | 14      |
-| processMinipoolEndWithoutRewards                                      | 211430          | 211430 | 211430 | 211430  | 1       |
-| processMinipoolStart                                                  | 220236          | 262768 | 270385 | 288734  | 22      |
+| processGGPRewards                                                     | 155080          | 458090 | 430159 | 1138659 | 12      |
+| processMinipoolEndWithRewards                                         | 181168          | 193927 | 191873 | 210559  | 14      |
+| processMinipoolEndWithoutRewards                                      | 211524          | 211524 | 211524 | 211524  | 1       |
+| processMinipoolStart                                                  | 220217          | 262748 | 270366 | 288715  | 22      |
 | receive                                                               | 55              | 55     | 55     | 55      | 37      |
-| setGGPPriceInAVAX                                                     | 34383           | 54268  | 54383  | 54383   | 175     |
+| setGGPPriceInAVAX                                                     | 34396           | 54281  | 54396  | 54396   | 175     |
 
 
 | contracts/contract/utils/WAVAX.sol:WAVAX contract |                 |       |        |       |         |
@@ -641,17 +665,17 @@ Test result: [32mok[0m. 30 passed; 0 failed; finished in 7.82s
 | 483833                                                                                                                      | 3872            |        |        |        |         |
 | Function Name                                                                                                               | min             | avg    | median | max    | # calls |
 | allowance                                                                                                                   | 1565            | 1565   | 1565   | 1565   | 4       |
-| amountAvailableForStaking                                                                                                   | 5298            | 8951   | 9647   | 22147  | 32      |
+| amountAvailableForStaking                                                                                                   | 5270            | 8923   | 9619   | 22119  | 32      |
 | approve                                                                                                                     | 3343            | 11026  | 5443   | 23243  | 12      |
 | asset                                                                                                                       | 1180            | 1180   | 1180   | 1180   | 1       |
 | balanceOf                                                                                                                   | 1347            | 1347   | 1347   | 1347   | 41      |
 | convertToAssets                                                                                                             | 1236            | 2019   | 2241   | 2241   | 31      |
 | convertToShares                                                                                                             | 1936            | 2417   | 2285   | 4285   | 17      |
 | decimals                                                                                                                    | 3133            | 3133   | 3133   | 3133   | 1       |
-| deposit                                                                                                                     | 9991            | 45476  | 27790  | 114429 | 121     |
-| depositAVAX()                                                                                                               | 19719           | 107512 | 118811 | 128611 | 15      |
-| depositAVAX()(uint256)                                                                                                      | 9839            | 86117  | 118811 | 128611 | 26      |
-| depositFromStaking                                                                                                          | 9410            | 13734  | 12889  | 16111  | 26      |
+| deposit                                                                                                                     | 9997            | 45483  | 27796  | 114437 | 121     |
+| depositAVAX()                                                                                                               | 9860            | 100437 | 118811 | 128611 | 23      |
+| depositAVAX()(uint256)                                                                                                      | 19719           | 85650  | 118811 | 128611 | 18      |
+| depositFromStaking                                                                                                          | 9435            | 13738  | 12891  | 16113  | 26      |
 | initialize                                                                                                                  | 10293           | 184723 | 185570 | 185570 | 208     |
 | lastRewardsAmt                                                                                                              | 1189            | 1189   | 1189   | 1189   | 19      |
 | lastSync                                                                                                                    | 1170            | 1170   | 1170   | 1170   | 8       |
@@ -659,15 +683,15 @@ Test result: [32mok[0m. 30 passed; 0 failed; finished in 7.82s
 | maxMint                                                                                                                     | 1184            | 1184   | 1184   | 1184   | 1       |
 | maxRedeem                                                                                                                   | 4287            | 6445   | 4985   | 10993  | 5       |
 | maxWithdraw                                                                                                                 | 4305            | 6332   | 5003   | 11015  | 7       |
-| mint                                                                                                                        | 11063           | 24108  | 12465  | 97270  | 9       |
+| mint                                                                                                                        | 11078           | 24117  | 12473  | 97278  | 9       |
 | name                                                                                                                        | 10536           | 10536  | 10536  | 10536  | 1       |
-| previewDeposit                                                                                                              | 3667            | 5917   | 3667   | 10931  | 6       |
-| previewMint                                                                                                                 | 3629            | 8128   | 9846   | 10910  | 3       |
-| previewRedeem                                                                                                               | 3296            | 5838   | 3645   | 10912  | 6       |
-| previewWithdraw                                                                                                             | 3297            | 5996   | 5646   | 10924  | 9       |
+| previewDeposit                                                                                                              | 3691            | 5940   | 3691   | 10946  | 6       |
+| previewMint                                                                                                                 | 3653            | 8149   | 9870   | 10925  | 3       |
+| previewRedeem                                                                                                               | 3320            | 5861   | 3669   | 10927  | 6       |
+| previewWithdraw                                                                                                             | 3332            | 6029   | 5681   | 10950  | 9       |
 | receive                                                                                                                     | 866             | 866    | 866    | 866    | 43      |
-| redeem                                                                                                                      | 1736            | 11019  | 12653  | 13278  | 10      |
-| redeemAVAX                                                                                                                  | 9921            | 26512  | 26760  | 42608  | 4       |
+| redeem                                                                                                                      | 1736            | 11039  | 12677  | 13302  | 10      |
+| redeemAVAX                                                                                                                  | 9945            | 26532  | 26777  | 42628  | 4       |
 | rewardsCycleEnd                                                                                                             | 1152            | 1152   | 1152   | 1152   | 4       |
 | rewardsCycleLength                                                                                                          | 1214            | 1214   | 1214   | 1214   | 19      |
 | stakingTotalAssets                                                                                                          | 1104            | 2804   | 1104   | 9604   | 5       |
@@ -675,30 +699,30 @@ Test result: [32mok[0m. 30 passed; 0 failed; finished in 7.82s
 | syncRewards                                                                                                                 | 1262            | 30378  | 31402  | 32202  | 214     |
 | totalAssets                                                                                                                 | 1530            | 2013   | 1879   | 3530   | 40      |
 | totalSupply                                                                                                                 | 1127            | 1127   | 1127   | 1127   | 11      |
-| withdraw                                                                                                                    | 4260            | 15267  | 12586  | 33917  | 11      |
-| withdrawAVAX(uint256)                                                                                                       | 10965           | 22397  | 22624  | 33604  | 3       |
-| withdrawAVAX(uint256)(uint256)                                                                                              | 53279           | 53279  | 53279  | 53279  | 2       |
-| withdrawForStaking                                                                                                          | 23880           | 44220  | 48628  | 54628  | 38      |
+| withdraw                                                                                                                    | 4295            | 15300  | 12621  | 33941  | 11      |
+| withdrawAVAX(uint256)                                                                                                       | 10991           | 30146  | 28140  | 53314  | 4       |
+| withdrawAVAX(uint256)(uint256)                                                                                              | 53314           | 53314  | 53314  | 53314  | 1       |
+| withdrawForStaking                                                                                                          | 23880           | 44216  | 48624  | 54624  | 38      |
 
 
 | test/unit/BaseContractTest.sol:MockBase contract |                 |       |        |       |         |
 |--------------------------------------------------|-----------------|-------|--------|-------|---------|
 | Deployment Cost                                  | Deployment Size |       |        |       |         |
-| 592360                                           | 3032            |       |        |       |         |
+| 595766                                           | 3049            |       |        |       |         |
 | Function Name                                    | min             | avg   | median | max   | # calls |
-| guardianOrRegisteredContractFunction             | 4540            | 8863  | 11014  | 11035 | 3       |
-| guardianOrSpecificRegisteredContractFunction     | 11353           | 11368 | 11368  | 11383 | 2       |
-| onlyGuardianFunction                             | 7766            | 7775  | 7775   | 7785  | 2       |
-| onlyMultisigFunction                             | 6204            | 8598  | 8176   | 11414 | 3       |
-| onlyRegisteredNetworkContractFunction            | 8030            | 8042  | 8049   | 8049  | 3       |
-| onlySpecificRegisteredContractFunction           | 8271            | 8271  | 8271   | 8271  | 1       |
-| whenNotPausedFunction                            | 4349            | 8611  | 8611   | 12873 | 2       |
+| guardianOrRegisteredContractFunction             | 4558            | 8879  | 11026  | 11053 | 3       |
+| guardianOrSpecificRegisteredContractFunction     | 11368           | 11386 | 11386  | 11404 | 2       |
+| onlyGuardianFunction                             | 7781            | 7795  | 7795   | 7809  | 2       |
+| onlyMultisigFunction                             | 6188            | 8579  | 8158   | 11392 | 3       |
+| onlyRegisteredNetworkContractFunction            | 8030            | 8048  | 8058   | 8058  | 3       |
+| onlySpecificRegisteredContractFunction           | 8286            | 8286  | 8286   | 8286  | 1       |
+| whenNotPausedFunction                            | 4364            | 8629  | 8629   | 12894 | 2       |
 
 
 | test/unit/Vault.t.sol:VaultTest contract |                 |     |        |     |         |
 |------------------------------------------|-----------------|-----|--------|-----|---------|
 | Deployment Cost                          | Deployment Size |     |        |     |         |
-| 26953416                                 | 134402          |     |        |     |         |
+| 25813312                                 | 128721          |     |        |     |         |
 | Function Name                            | min             | avg | median | max | # calls |
 | receiveWithdrawalAVAX                    | 228             | 228 | 228    | 228 | 1       |
```