## Open TODOs
Open TODOs can point to architecture or programming issues that still need to be resolved. Consider resolving them before deploying.

Here are the instances entailed:

[File: BaseAbstract.sol#L6-L8](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L6-L8)

```
// TODO remove this when dev is complete
// import {console} from "forge-std/console.sol";
// import {format} from "sol-utils/format.sol";
```
[File: Staking.sol#L203](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L203)

```
	// TODO cant use onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) since we also call from increaseMinipoolCount. Wat do?
```
[File: MinipoolManager.sol#L412](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L412)

```
		// TODO Revisit this logic if we ever allow unequal matched funds
```
## Modularity on import usages
For cleaner Solidity code in conjunction with the rule of modularity and modular programming, use named imports with curly braces instead of adopting the global import approach. Throughout the code bases, most of the imports are adopting the recommended approach but some are not. Consider synchronizing them to the recommended method.

Here are the instances entailed:

[File: Base.sol#L4](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Base.sol#L4)
[File: BaseUpgradeable.sol#L4](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseUpgradeable.sol#L4)

```diff
- import "./BaseAbstract.sol";
+ import { BaseAbstract} from "./BaseAbstract.sol";
```
[File: ClaimNodeOp.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol)

```diff
- import "./Base.sol";
+ import {Base} from "./Base.sol";
```
## Inadequate NatSpec
Solidity contracts can use a special form of comments, i.e., the Ethereum Natural Language Specification Format (NatSpec) to provide rich documentation for functions, return variables and more. Please visit the following link for further details:

https://docs.soliditylang.org/en/v0.8.16/natspec-format.html

Here is a contract instance with missing NatSpec in its entirety:

[File: BaseUpgradeable.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseUpgradeable.sol)

## Typo mistakes

[File: MinipoolManager.sol#L309](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L309)

```diff
-	/// @notice Verifies that the minipool related the the given node ID is able to a validator
+	/// @notice Verifies that the minipool related to the given node ID is able to become a validator
```
[File: MinipoolManager.sol#L556](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L556)

```diff
-	/// @return The approximate rewards the node should recieve from Avalanche for beign a validator
+	/// @return The approximate rewards the node should recieve from Avalanche for being a validator
```
## 5% annual calculated on a daily interval not fully precised
[File: ProtocolDAO.sol#L41](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L41)

The max safe integer for JavaScript without losing precision is `(2^53 – 1)`, which is [around 9 quadrillion](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number/MAX_SAFE_INTEGER#:~:text=The%20Number.,larger%20integers%2C%20consider%20using%20BigInt%20.). As such, the code line linked above that entails 19 digits will not be fully accurate.

The calculated number should be `1,000,133,680,617,113,440` instead of `1,000,133,680,617,113,500`.

Note: `BigInt((1 + 0.05) ** (1 / (365)) * 1e18)` would also yield an inaccurate figure, `1000133680617113472n`.

`dao.getInflationIntervalRate()`, i.e. 1000133680617113500, is currently used to calculate the inflation amount in the code line below:

[File: RewardsPool.sol#L75](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L75) 

```
			newTotalSupply = newTotalSupply.mulWadDown(inflationRate);
```
It has no impact in the protocol with the last three differing digits since the total supply of GGP is comparatively lowly capped at 22,500,000. It could be an issue elsewhere if the a different reward token were to be implemented with its total supply capped at a much higher value, e.g. in quadrillion like Shiba Inu.  

## Hard coded initialization
In `ProtocolDAO.sol`, `initialize()` could only be called once by `onlyGuardian` because of the bool logic in [lines 24 - 27](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L24-L27). All storage variables are hard coded with their values retrievable via the getters. Only `TotalGGPCirculatingSupply`, `ClaimingContractPct`, and `ExpectedAVAXRewardsRate` have their respective setters implemented in the contract.

Consider implementing setters for all of the storage variables initialized where possible so that the community will have more proposal options when the protocol go full DAO. Otherwise, a proposal for changing `dao.getInflationIntervalRate()`, currently hard coded to 1000133680617113500, for an example, would not be viable if there was no setter function catered for it.

## Inexpedient ternary logic
The ternary code line below:

[File: ProtocolDAO.sol#L118](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L118)

```
		return rate < 1 ether ? 1 ether : rate;
```
is deemed impractical considering 1 ether, which is 1e18, is going to make the fraction of the following arithmetic calculation always equal to 1:

[File: RewardsPool.sol#L75](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L75)

```
			newTotalSupply = newTotalSupply.mulWadDown(inflationRate);
```
With `1 ether / WAD == 1`, GGP would never be able to inflate and `ggp.totalSupply()` would forever stay at the initialized value of 18,000,000. Additionally, running a GGP rewards cycle via `startRewardsCycle()` would also be impossible because the function would readily revert on [the second if block](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L174-L176) due to [`newTokens == 0`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L92). 

There is no risk foreseeable since `InflationIntervalRate` is currently hard coded to 5 % annual. It would have to be refactored to assume something higher than 1 ether if `(rate < 1 ether) == false` if a setter for `InflationIntervalRate` was going to be implemented in the contract. 

## Custom contract pauser and resumer
First off, `pauseEverything()` and `resumeEverything()` have both their names belie their function logic catering selectively to important contracts instead of everything (or as it implies, every contract). Consider implementing a custom contract pauser and resumer in `Ocyticus.sol` so that the defenders get to work on other contracts rather than just "TokenggAVAX", "MinipoolManager", and "Staking".

Here is what the suggested functions could look like:

```solidity
	function pauseContract (string memory _contract) external onlyDefender {
		ProtocolDAO dao = ProtocolDAO(getContractAddress("ProtocolDAO"));
		dao.pauseContract(_contract);
		disableAllMultisigs();
	}

	function resumeContract (string memory _contract) external onlyDefender {
		ProtocolDAO dao = ProtocolDAO(getContractAddress("ProtocolDAO"));
		dao.resumeContract(_contract);
	}
```
## Unusual multisig logic
Conventionally, multi-signature requires the agreement of multiple people to perform an action, e.g. 6 out of 10 in the case associated with the protocol. However, `requireNextActiveMultisig()` in `MultisigManager.sol` only requires one valid, i.e registered and enabled Multisig, address to interact with `MinipoolManager.sol`. 

Additionally, the function logic of `requireNextActiveMultisig()` seems to be mostly (if not only) dealing with the first enabled multisig's index, making the other enabled indices never reachable unless the first one has been compromised and disabled. 

[File: MultisigManager.sol#L80-L91](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L80-L91)

```solidity
	function requireNextActiveMultisig() external view returns (address) {
		uint256 total = getUint(keccak256("multisig.count"));
		address addr;
		bool enabled;
		for (uint256 i = 0; i < total; i++) {
			(addr, enabled) = getMultisig(i);
			if (enabled) {
				return addr;
			}
		}
		revert NoEnabledMultisigFound();
	}
```
## Comments and codes mismatch
The comment lines below said that `2% is 0.2 ether`. It should be `2% is 0.02 ether` or `20% is 0.2 ether`.

[File: MinipoolManager.sol#L35](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L35)

```diff
-	minipool.item<index>.delegationFee = node operator specified fee (must be between 0 and 1 ether) 2% is 0.2 ether
+	minipool.item<index>.delegationFee = node operator specified fee (must be between 0 and 1 ether) 2% is 0.02 ether
```
[File: MinipoolManager.sol#L194](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L194)

```diff
-	/// @param delegationFee Percentage delegation fee in units of ether (2% is 0.2 ether)
+	/// @param delegationFee Percentage delegation fee in units of ether (20% is 0.2 ether)
```
Similarly, the comment below said that it is looking up multisig index by minipool nodeID when only the multisig address assigned to the minipool is entailed:

[File: MinipoolManager.sol#L124](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L124)

```diff
-	/// @dev Look up multisig index by minipool nodeID
+	/// @dev Look up minipool index via assigned multisig address by minipool nodeID
```
## GGP token exchange
GGP is initialized with `TotalGGPCirculatingSupply == 18_000_000 ether`. However, an exchange is needed to at least allow node operators to swap AVAX or ggAVAX into GGP. Devoid of this, no one would be able to secure any GGP to stake prior to creating a minipool. 

Additionally, with an exchange in place, liquid stakers would be able to sell the slashed GGP awarded at their discretion instead of being dictated by Protocol DAO.

## GGP tokens circulated distributions
It is not absolutely clear where and how GGP tokens are being circulated. Upon having `TokenGGP.sol` deployed, a fixed supply of 22_500_000 tokens were minted to the deployer. The protocol should clearly document when and how the initial 18_000_000 tokens were being distributed, e.g. pre-sales, airdrops, deFi exchange etc. And when the tokens got inflated, it should be transparent how the protocol was going to have proper accounting catered for contracts holding the additional GGP tokens. For instance, upon successfully executing `startRewardsCycle()` in RewardsPool.sol, how would `Vault.sol` GGP contract balance be updated to allow the transfer/withdrawal pertaining to "ClaimMultisig", "ClaimNodeOp", and "ClaimProtocolDAO"?

## Missing use for `delegationFee` 
`delegationFee` is introduced in `MinipoolManager.sol`. However, no where in the contract or the protocol could be found any use for this variable that has been included in the struct, `Minipool`. Additionally, there seems to be no boundary control on the input parameter of `delegationFee` in `createMinipool()`. This could impose a problem devoid of a yardstick to determine what percentage delegation fee in units of ether is deemed fair and appropriate on `avaxAssignmentRequest`. If `delegationFee` is not ready to be fully introduced yet, consider elaborating a brief structured plan for it in the NatSpec or the documentations since this constitutes one of the triple incentives to the node operators. 

Ironically, `MinipoolNodeCommissionFeePct` is found to be deducting a cut from `avaxHalfRewards` [prior to getting `avaxLiquidStakerRewardAmt` assigned](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L417). Apparently, the documentation might have to be updated to reflect a four fold incentives to the node operators.

## No storage gap for upgradeable contracts
Consider adding a storage gap at the end of an upgradeable contract, just in case it would entail some child contracts in the future. This would ensure no shifting down of storage in the inheritance chain.

Here is the contract instance entailed:

[File: TokenggAVAX.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol)

```diff
+ uint256[50] private __gap;
```
## Variable assignment in conditional checks
Making a variable assignment in a conditional statement deviates from the standard use and intention of the check and can easily lead to confusion.

Consider moving the needed assignment before the conditional statement by having the code lines below rewritten as follows:

[File: TokenggAVAX.sol#L169](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L169)

```diff
-		if ((shares = previewDeposit(assets)) == 0) {
+		shares = previewDeposit(assets);
+		if (shares == 0) {
```
[File: TokenggAVAX.sol#L193](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L193)

```diff
-		if ((assets = previewRedeem(shares)) == 0) {
+		assets = previewRedeem(shares);
+		if (assets == 0) {
```
## Zero value check on `withdrawAVAX() in TokenggAVAX.sol
Although `previewWithdraw()` does round up, it could still assign a zero value to `shares` if the input parameter, `assets` is accidentally entered as zero. Consider having a zero value check implemented just as it has been done so on `redeemAVAX()` which is nonetheless a side effect zero value check arising from rounding error check associated with round down issue in `previewRedeem()`.

[File: TokenggAVAX.sol#L180-L189](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L180-L189)

```diff
	function withdrawAVAX(uint256 assets) public returns (uint256 shares) {
+		if (assets == 0) {
+			revert ZeroAssets();

		shares = previewWithdraw(assets); // No need to check for rounding error, previewWithdraw rounds up.
		beforeWithdraw(assets, shares);
		_burn(msg.sender, shares);
```
## Parameterized custom errors
Consider parameterizing custom errors where deemed fit to make them more distinct.

For an example, the following custom error instance may be refactored as follows:

[File: BaseAbstract.sol#L17](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L17)

```diff
-	error MustBeGuardianOrValidContract();
+	error MustBeGuardianOrValidContract(address caller, bool authorization);
```
[File: BaseAbstract.sol#L40-L48](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L40-L48)

```diff
	modifier guardianOrRegisteredContract() {
		bool isContract = getBool(keccak256(abi.encodePacked("contract.exists", msg.sender)));
		bool isGuardian = msg.sender == gogoStorage.getGuardian();

		if (!(isGuardian || isContract)) {
-			revert MustBeGuardianOrValidContract();
+			revert MustBeGuardianOrValidContract(msg.sender, isGuardian);
		}
		_;
	}
```
## `bytes32` over `bytes`
`bytes32` typed variables are fixed-sized and can be passed between contracts. `bytes` typed variables, on the contrary, are dynamically sized and special array, i.e. a shorthand for `byte[]` according to [Solidity Documentation](https://docs.soliditylang.org/en/v0.5.10/types.html#bytes-and-strings-as-arrays). They are not value type, but can entail push (append a byte to the end), pop, and length. 

Some possibly disorienting situations are possible if `bytes` is used as a function argument and the contract successfully compiles The instances entailed in the protocol are not at risk, but care will have to be given elsewhere by always using fixed length types for any function that will be called from outside:

[File: Storage.sol#L80](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L80)

```solidity
	function getBytes(bytes32 key) external view returns (bytes memory) {
```
## Empty blocks
Function with empty block should have a comment explaining why it is empty. For instance, `receiveWithdrawalAVAX()` in MiniPoolManager.sol should be commented as receiving AVAX from TokenggAVAX.sol and Vault.sol to better portray its intended purpose:

[File: MinipoolManager.sol#L106](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L106)

```diff
+	/// @notice Receive AVAX from TokenggAVAX.sol and Vault.sol
	function receiveWithdrawalAVAX() external payable {}
```