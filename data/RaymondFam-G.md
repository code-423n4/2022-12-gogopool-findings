## Private function with embedded modifier reduces contract size
Consider having the logic of a modifier embedded through a private function to reduce contract size if need be. A private visibility that saves more gas on function calls than the internal visibility is adopted because the modifier will only be making this call inside the contract.

For instance, the modifier instance below may be refactored as follows:

[File: BaseAbstract.sol#L24-L29](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L24-L29)

```diff
+    function _onlyRegisteredNetworkContract() private view {
+        if (getBool(keccak256(abi.encodePacked("contract.exists", msg.sender))) == false) {
+            revert InvalidOrOutdatedContract();
+        }
+    }

    modifier onlyRegisteredNetworkContract() {
-        if (getBool(keccak256(abi.encodePacked("contract.exists", msg.sender))) == false) {
-            revert InvalidOrOutdatedContract();
-        }
+        _onlyRegisteredNetworkContract();
        _;
    }
```
This suggestion is similar to [`onlyOwner()` and `onlyValidMultisig()`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L115-L135) implemented in `MinipoolManager.sol`.

## Unchecked SafeMath saves gas
"Checked" math, which is default in ^0.8.0 is not free. The compiler will add some overflow checks, somehow similar to those implemented by `SafeMath`. While it is reasonable to expect these checks to be less expensive than the current `SafeMath`, one should keep in mind that these checks will increase the cost of "basic math operation" that were not previously covered. This particularly concerns variable increments in for loops. When no arithmetic overflow/underflow is going to happen, `unchecked { ++i ;}` to use the previous wrapping behavior further saves gas just as in the for loop below as an example:

[File: Staking.sol#L428-L432](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L428-L432)

```diff
-        for (uint256 i = offset; i < max; i++) {
+        for (uint256 i = offset; i < max;) {
             Staker memory s = getStaker(int256(i));
             stakers[total] = s;
             total++;

+            unchecked {
+                ++i;
+            }
          }
```
## Use of named returns for local variables saves gas
You can have further advantages in term of gas cost by simply using named return values as temporary local variable.

For instance, the code block below may be refactored as follows:

[File: Staking.sol#L217-L220](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L217-L220)

```diff
-	function getGGPRewards(address stakerAddr) public view returns (uint256) {
+	function getGGPRewards(address stakerAddr) public view returns (uint256 ggpRewards) {
		int256 stakerIndex = getIndexOf(stakerAddr);
-		return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpRewards")));
+		ggpRewards = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpRewards")));
	}
```
## Unneeded functions
In `Staking.sol`, `stakeGGP()` and `restakeGGP()` that have similar/identical function logic can be discarded to save gas both on contract size and function calls. `stakerAddr` can be user specified much as it is being applied on `amount`. These parameters are going to be taken care of at the user interface after all. Additionally, there should not be any security concern removing the modifier, `onlySpecificRegisteredContract("ClaimNodeOp", msg.sender)`, while impartially applying the same set of rules on every staker.

Consider doing away with them and refactoring `_stakeGGP()` and the associated `claimAndRestake()` of `ClaimNodeOp.sol` as follows:

[File: Staking.sol#L337-L354](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L337-L354)

```diff
-	function _stakeGGP(address stakerAddr, uint256 amount) internal {
+	function _stakeGGP(address stakerAddr, uint256 amount) external whenNotPaused {
+		ggp.safeTransferFrom(msg.sender, address(this), amount);
		emit GGPStaked(stakerAddr, amount);
                ...
```
[File: ClaimNodeOp.sol#L106](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L106)

```diff
-			staking.restakeGGP(msg.sender, restakeAmt);
+			staking._stakeGGP(msg.sender, restakeAmt);
```
## Redundant checks 
In `createMinipool()` of `MinipoolManager.sol`, the second if block has already made sure that `msg.value == avaxAssignmentRequest` and `avaxAssignmentRequest >= dao.getMinipoolMinAVAXAssignment()`. As such, the third if block to make sure `msg.value + avaxAssignmentRequest >= dao.getMinipoolMinAVAXStakingAmt()` is an unnecessary check.

Consider removing the unneeded if block to save gas both on contract deployment and function calls:

[File: MinipoolManager.sol#L206-L218](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L206-L218)

```diff
		ProtocolDAO dao = ProtocolDAO(getContractAddress("ProtocolDAO"));
		if (
			// Current rule is matched funds must be 1:1 nodeOp:LiqStaker
			msg.value != avaxAssignmentRequest ||
			avaxAssignmentRequest > dao.getMinipoolMaxAVAXAssignment() ||
			avaxAssignmentRequest < dao.getMinipoolMinAVAXAssignment()
		) {
			revert InvalidAVAXAssignmentRequest();
		}

-		if (msg.value + avaxAssignmentRequest < dao.getMinipoolMinAVAXStakingAmt()) {
-			revert InsufficientAVAXForMinipoolCreation();
-		}
```
In the light of this, the following code line and function may also be removed:

[File: ProtocolDAO.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol)

```diff
- 47:		setUint(keccak256("ProtocolDAO.MinipoolMinAVAXStakingAmt"), 2_000 ether);

- 129:	/// @notice The min AVAX staking amount that is required for creating a minipool
- 130:	function getMinipoolMinAVAXStakingAmt() public view returns (uint256) {
- 131:		return getUint(keccak256("ProtocolDAO.MinipoolMinAVAXStakingAmt"));
- 132:	}
```
## Avoid comparing boolean expressions to boolean literals
Comparing a boolean value to a boolean literal incurs the `ISZERO` operation and costs more gas than using a boolean expression.

Consider having the affected code lines below refactored as follows:

[File: BaseAbstract.sol#L25](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L25)

```diff
-		if (getBool(keccak256(abi.encodePacked("contract.exists", msg.sender))) == false) {
+		if !(getBool(keccak256(abi.encodePacked("contract.exists", msg.sender)))) {
```
[File: BaseAbstract.sol#L74](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L74)

```diff
-		if (enabled == false) {
+		if (!enabled) {
```
[File: Storage.sol#L29](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L29)

```diff
-		if (booleanStorage[keccak256(abi.encodePacked("contract.exists", msg.sender))] == false && msg.sender != guardian) {
+		if (!booleanStorage[keccak256(abi.encodePacked("contract.exists", msg.sender))] && msg.sender != guardian) {
```
## `||` costs less gas than its equivalent `&&`
Rule of thumb: `(x && y)` is `(!(!x || !y))`

Even with the 10k Optimizer enabled: `||`, OR conditions cost less than their equivalent `&&`, AND conditions.

As an example, the code line instance below may be refactored as follows:

[File: BaseAbstract.sol#L73](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L73)

```diff
-		bool enabled = (addr != address(0)) && getBool(keccak256(abi.encodePacked("multisig.item", multisigIndex, ".enabled")));
+		bool enabled = (!(addr == address(0)) || !getBool(keccak256(abi.encodePacked("multisig.item", multisigIndex, ".enabled"))));
```
## Early function checks
Function logic checks should be implemented as early as possible to minimize gas wastage in case of a revert. In `RewardsPool.sol`, `inflate()` internally calls `getInflationIntervalStartTime()` on [line 84](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L84), which is also called again in the next code line via [`getInflationAmt()`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L69). The latter will, however, have a [zero value check](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L56-L59) on `getInflationIntervalStartTime()`.

Consider swapping the order of executions on line 84 and line 85:

[File: RewardsPool.sol#L84-L85](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L84-L85)
  
```diff
+		(uint256 currentTotalSupply, uint256 newTotalSupply) = getInflationAmt();
		uint256 inflationIntervalElapsedSeconds = (block.timestamp - getInflationIntervalStartTime());
-		(uint256 currentTotalSupply, uint256 newTotalSupply) = getInflationAmt();
```
## Division by 2
A division by 2 can be calculated by shifting one to the right since the div opcode uses 5 gas while SHR opcode uses 3 gas. Additionally, Solidity's division operation also includes a division-by-0 prevention by pass using shifting.

Consider using >>1 by having the code instance below refactored as follows:

[File: MinipoolManager.sol#L413](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L413)

```diff
-		uint256 avaxHalfRewards = avaxTotalRewardAmt / 2;
+		uint256 avaxHalfRewards = avaxTotalRewardAmt >> 1;
```
## Function order affects gas consumption
The order of function will also have an impact on gas consumption. Because in smart contracts, there is a difference in the order of the functions. Each position will have an extra 22 gas. The order is dependent on method ID. So, if you rename the frequently accessed function to more early method ID, you can save gas cost. Please visit the following site for further information:

https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

## Activate the optimizer
Before deploying your contract, activate the optimizer when compiling using “solc --optimize --bin sourceFile.sol”. By default, the optimizer will optimize the contract assuming it is called 200 times across its lifetime. If you want the initial contract deployment to be cheaper and the later function executions to be more expensive, set it to “ --optimize-runs=1”. Conversely, if you expect many transactions and do not care for higher deployment cost and output size, set “--optimize-runs” to a high number.

```
module.exports = {
solidity: {
version: "0.8.17",
settings: {
  optimizer: {
    enabled: true,
    runs: 1000,
  },
},
},
};
```
Please visit the following site for further information:

https://docs.soliditylang.org/en/v0.5.4/using-the-compiler.html#using-the-commandline-compiler

Here's one example of instance on opcode comparison that delineates the gas saving mechanism:

```
for !=0 before optimization
PUSH1 0x00
DUP2
EQ
ISZERO
PUSH1 [cont offset]
JUMPI

after optimization
DUP1
PUSH1 [revert offset]
JUMPI
```
Disclaimer: There have been several bugs with security implications related to optimizations. For this reason, Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them. Therefore, it is unclear how well they are being tested and exercised. High-severity security issues due to optimization bugs have occurred in the past . A high-severity bug in the emscripten -generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG. Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. Please measure the gas savings from optimizations, and carefully weigh them against the possibility of an optimization-related bug. Also, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.

## Payable access control functions costs less gas
Consider marking functions with access control as `payable`. This will save 20 gas on each call by their respective permissible callers for not needing to have the compiler check for `msg.value`.

For instance, the code line below may be refactored as follows:

[File: ProtocolDAO.sol#L107](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L107)

```diff
-	function setClaimingContractPct(string memory claimingContract, uint256 decimal) public onlyGuardian valueNotGreaterThanOne(decimal) {
+	function setClaimingContractPct(string memory claimingContract, uint256 decimal) public payable onlyGuardian valueNotGreaterThanOne(decimal) {
```
## Use storage instead of memory for structs/arrays
A storage pointer is cheaper since copying a state struct in memory would incur as many SLOADs and MSTOREs as there are slots. In another words, this causes all fields of the struct/array to be read from storage, incurring a Gcoldsload (2100 gas) for each field of the struct/array, and then further incurring an additional MLOAD rather than a cheap stack read. As such, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables will be much cheaper, involving only Gcoldsload for all associated field reads. Read the whole struct/array into a memory variable only when it is being returned by the function, passed into a function that requires memory, or if the array/struct is being read from another memory array/struct.

For instance, the code line below may be refactored as follows:

[File: Staking.sol#L429](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L429)

```diff
-			Staker memory s = getStaker(int256(i));
+			Staker storage s = getStaker(int256(i));
```
## Ternary over `if ... else`
Using ternary operator instead of the if else statement saves gas.

For instance, the code block below may be refactored as follows:

[File: Staking.sol#L389-L393](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L389-L393)

```solidity
 index != -1
     ? {
         return index;
     }
     : {
        revert StakerNotFound();
     }
```
## Non-strict inequalities are cheaper than strict ones
In the EVM, there is no opcode for non-strict inequalities (>=, <=) and two operations are performed (> + = or < + =).

As an example, consider replacing `>=` with the strict counterpart `>` in the following inequality instance:

[File: ClaimNodeOp.sol#L51](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L51)

```diff
-		return (rewardsStartTime != 0 && elapsedSecs >= dao.getRewardsEligibilityMinSeconds());
+		return (rewardsStartTime != 0 && elapsedSecs > dao.getRewardsEligibilityMinSeconds()) - 1;
```
Similarly, for an example, consider replacing `<=` with the strict counterpart `<` in the following inequality instance:

[File: MinipoolManager.sol#L318](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L318)

```diff
-		return avaxLiquidStakerAmt <= ggAVAX.amountAvailableForStaking();
+		return avaxLiquidStakerAmt < ggAVAX.amountAvailableForStaking() + 1;
```
## Unused ERC20 instance
`ggp` is declared as an immutable ERC20 instance in MinipoolManager.sol, but it is not found to be used in the contract. Its associated code excutions, e.g. `slash()`, collateralization ratio of GGP to Assigned AVAX, `calculateGGPSlashAmt()` etc are all externally dependent on ProtocolDAO.sol and Oracle.sol.

Consider having the affected code lines removed to reduce the contract size:

[File: MinipoolManager.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol)

```diff
- 13: import {TokenGGP} from "./tokens/TokenGGP.sol";

- 78:	ERC20 public immutable ggp;

- 179:		ERC20 ggp_,

-183:		ggp = ggp_;
```
## Unneeded cache
In `recordStakingEnd()` of MinipoolManager.sol, using `totalAvaxAmt` to cache the sum of two local variables has no gas saving benefit on top of it being used only once in the function logic. Because `avaxNodeOpAmt == avaxLiquidStakerAmt` in any validation cycle., the affected code lines may be refactored as follows to save gas both on contract deployment and function calls:

[File: MinipoolManager.sol#L400-L403](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L400-L403)

```diff
-		uint256 totalAvaxAmt = avaxNodeOpAmt + avaxLiquidStakerAmt;
-		if (msg.value != totalAvaxAmt + avaxTotalRewardAmt) {
+		if (msg.value != avaxNodeOpAmt * 2 + avaxTotalRewardAmt) {
			revert InvalidAmount();
		}
``` 
Similarly, the code lines below associated with `claimAndInitiateStaking()` may also be refactored as follows:

[File: MinipoolManager.sol#L341-L342](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L341-L342)

```diff
-		uint256 totalAvaxAmt = avaxNodeOpAmt + avaxLiquidStakerAmt;
-		msg.sender.safeTransferETH(totalAvaxAmt);
+		msg.sender.safeTransferETH(avaxNodeOpAmt + avaxLiquidStakerAmt);
```