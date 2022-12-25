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