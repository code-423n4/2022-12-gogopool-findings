# NON-CRITICAL FINDINGS :

------------------------------------------------------------------------------------------------------------------------------------------------------------

## 

## [NC-1]  INCLUDE RETURN PARAMETERS IN NATSPEC COMMENTS

(https://docs.soliditylang.org/en/v0.8.15/natspec-format.html)

If Return parameters are declared, you must prefix them with ”/// @return”.

Some code analysis programs do analysis by reading NatSpec details, if they can’t see the “@return” tag, they do incomplete analysis.

Recommended Mitigation Steps

Include return parameters in NatSpec comments

> Instances(3) :

[FILE: 12-gogopool/contracts/contract/MinipoolManager.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol)

       /// @notice Gets the minipool information from the node ID
	/// @param nodeID 20-byte Avalanche node ID
	function getMinipoolByNodeID(address nodeID) public view returns (Minipool memory mp) {
		int256 index = getIndexOf(nodeID);
		return getMinipool(index);
	}


       /// @notice Calculates how much GGP should be slashed given an expected avaxRewardAmt
	/// @param avaxRewardAmt The amount of AVAX that should have been awarded to the validator by Avalanche
	function calculateGGPSlashAmt(uint256 avaxRewardAmt) public view returns (uint256) {
		Oracle oracle = Oracle(getContractAddress("Oracle"));
		(uint256 ggpPriceInAvax, ) = oracle.getGGPPriceInAVAX();
		return avaxRewardAmt.divWadDown(ggpPriceInAvax);
	}

       /// @notice Get the total amount of AVAX from liquid stakers that is being used for minipools
	/// @dev Get the total AVAX *actually* withdrawn from ggAVAX and sent to Rialto
	function getTotalAVAXLiquidStakerAmt() public view returns (uint256) {
		return getUint(keccak256("MinipoolManager.TotalAVAXLiquidStakerAmt"));
	}

##

## [NC-2]  NATSPEC IS MISSING

Description
NatSpec is missing for the following functions , constructor and events:

> Instances(30):

[FILE: 12-gogopool/contracts/contract/MinipoolManager.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol)

        106:  function receiveWithdrawalAVAX() external payable {}   

               constructor(
		Storage storageAddress,
		ERC20 ggp_,
		TokenggAVAX ggAVAX_
	       ) Base(storageAddress) {
		version = 1;
		ggp = ggp_;
		ggAVAX = ggAVAX_;
	       }


        event GGPSlashed(address indexed nodeID, uint256 ggp);
	event MinipoolStatusChanged(address indexed nodeID, MinipoolStatus indexed status);


[FILE: 2022-12-gogopool/contracts/contract/BaseAbstract.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol)


        function getAddress(bytes32 key) internal view returns (address) {
		return gogoStorage.getAddress(key);
	}

	function getBool(bytes32 key) internal view returns (bool) {
		return gogoStorage.getBool(key);
	}

	function getBytes(bytes32 key) internal view returns (bytes memory) {
		return gogoStorage.getBytes(key);
	}

	function getBytes32(bytes32 key) internal view returns (bytes32) {
		return gogoStorage.getBytes32(key);
	}

	function getInt(bytes32 key) internal view returns (int256) {
		return gogoStorage.getInt(key);
	}

	function getUint(bytes32 key) internal view returns (uint256) {
		return gogoStorage.getUint(key);
	}

	function getString(bytes32 key) internal view returns (string memory) {
		return gogoStorage.getString(key);
	}

	function setAddress(bytes32 key, address value) internal {
		gogoStorage.setAddress(key, value);
	}

	function setBool(bytes32 key, bool value) internal {
		gogoStorage.setBool(key, value);
	}

	function setBytes(bytes32 key, bytes memory value) internal {
		gogoStorage.setBytes(key, value);
	}

	function setBytes32(bytes32 key, bytes32 value) internal {
		gogoStorage.setBytes32(key, value);
	}

	function setInt(bytes32 key, int256 value) internal {
		gogoStorage.setInt(key, value);
	}

	function setUint(bytes32 key, uint256 value) internal {
		gogoStorage.setUint(key, value);
	}

	function setString(bytes32 key, string memory value) internal {
		gogoStorage.setString(key, value);
	}

	function deleteAddress(bytes32 key) internal {
		gogoStorage.deleteAddress(key);
	}

	function deleteBool(bytes32 key) internal {
		gogoStorage.deleteBool(key);
	}

	function deleteBytes(bytes32 key) internal {
		gogoStorage.deleteBytes(key);
	}

	function deleteBytes32(bytes32 key) internal {
		gogoStorage.deleteBytes32(key);
	}

	function deleteInt(bytes32 key) internal {
		gogoStorage.deleteInt(key);
	}

	function deleteUint(bytes32 key) internal {
		gogoStorage.deleteUint(key);
	}

	function deleteString(bytes32 key) internal {
		gogoStorage.deleteString(key);
	}

	function addUint(bytes32 key, uint256 amount) internal {
		gogoStorage.addUint(key, amount);
	}

	function subUint(bytes32 key, uint256 amount) internal {
		gogoStorage.subUint(key, amount);
	}

[File: 2022-12-gogopool/contracts/contract/ClaimNodeOp.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol)         

         constructor(Storage storageAddress, ERC20 ggp_) Base(storageAddress) {
		version = 1;
		ggp = ggp_;
	}

       25:  event GGPRewardsClaimed(address indexed to, uint256 amount);

[File: 2022-12-gogopool/contracts/contract/MultisigManager.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol)

        event DisabledMultisig(address indexed multisig, address actor);
	event EnabledMultisig(address indexed multisig, address actor);
	event RegisteredMultisig(address indexed multisig, address actor);

##

## [NC-3]  EMPTY BLOCKS SHOULD BE REMOVED OR EMIT SOMETHING

> Instances(4)

[FILE: 12-gogopool/contracts/contract/MinipoolManager.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol)

   106:  function receiveWithdrawalAVAX() external payable {}

[FILE: 2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol)

    255:   function _authorizeUpgrade(address newImplementation) internal override onlyGuardian {}

[FILE: 2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol)

        176:  function beforeWithdraw(uint256 assets, uint256 shares) internal virtual {}

	178:  function afterDeposit(uint256 assets, uint256 shares) internal virtual {}

##

## [NC-4]  NON-LIBRARY/INTERFACE FILES SHOULD USE FIXED COMPILER VERSIONS, NOT FLOATING ONES

INSTANCES(2) :

pragma solidity >=0.8.0 <0.9.0;

##

## [NC-5] NOT USING THE NAMED RETURN VARIABLES ANYWHERE IN THE FUNCTION IS CONFUSING

Consider changing the variable to be an unnamed one

> Instances(1) : 

[FILE: 2022-12-gogopool/contracts/contract/RewardsPool.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol)

       66:  function getInflationAmt() public view returns (uint256 currentTotalSupply, uint256 newTotalSupply) {
		ProtocolDAO dao = ProtocolDAO(getContractAddress("ProtocolDAO"));
		uint256 inflationRate = dao.getInflationIntervalRate();
		uint256 inflationIntervalsElapsed = getInflationIntervalsElapsed();
		currentTotalSupply = dao.getTotalGGPCirculatingSupply();
		newTotalSupply = currentTotalSupply;

		// Compute inflation for total inflation intervals elapsed
		for (uint256 i = 0; i < inflationIntervalsElapsed; i++) {
			newTotalSupply = newTotalSupply.mulWadDown(inflationRate);
		}
		return (currentTotalSupply, newTotalSupply);
	}

##

## [NC-6]  OMISSIONS IN EVENTS

Throughout the codebase, events are generally emitted when sensitive changes are made to the contracts. However, some events are missing important parameters

The events should include the new value and old value where possible. In MinipoolManager Contract for imporatant updates no events are emitted for newminipool creations, withdraw operations etc

[FILE: 12-gogopool/contracts/contract/MinipoolManager.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol)

##

## [NC-7] COMMENTED CODE

[File: 2022-12-gogopool/contracts/contract/MinipoolManager.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol)

/*
	Data Storage Schema
	NodeIDs are 20 bytes so can use Solidity 'address' as storage type for them
	NodeIDs can be added, but never removed. If a nodeID submits another validation request,
		it will overwrite the old one (only allowed for specific statuses).

	MinipoolManager.TotalAVAXLiquidStakerAmt = total for all active minipools (Prelaunch/Launched/Staking)

	minipool.count = Starts at 0 and counts up by 1 after a node is added.

	minipool.index<nodeID> = <index> of nodeID
	minipool.item<index>.nodeID = nodeID used as primary key (NOT the ascii "Node-123..." but the actual 20 bytes)
	minipool.item<index>.status = enum
	minipool.item<index>.duration = requested validation duration in seconds (performed as 14 day cycles)
	minipool.item<index>.delegationFee = node operator specified fee (must be between 0 and 1 ether) 2% is 0.2 ether
	minipool.item<index>.owner = owner address
	minipool.item<index>.multisigAddr = which Rialto multisig is assigned to manage this validation
	minipool.item<index>.avaxNodeOpAmt = avax deposited by node operator (for this cycle)
	minipool.item<index>.avaxNodeOpInitialAmt = avax deposited by node operator for the **first** validation cycle
	minipool.item<index>.avaxLiquidStakerAmt = avax deposited by users and assigned to this nodeID

	// Submitted by the Rialto oracle
	minipool.item<index>.txID = transaction id of the AddValidatorTx
	minipool.item<index>.initialStartTime = actual time the **first** validation cycle was started
	minipool.item<index>.startTime = actual time validation was started
	minipool.item<index>.endTime = actual time validation was finished
	minipool.item<index>.avaxTotalRewardAmt = Actual total avax rewards paid by avalanchego to the TSS P-chain addr
	minipool.item<index>.errorCode = bytes32 that encodes an error msg if something went wrong during launch of minipool

	// Calculated in recordStakingEnd()
	minipool.item<index>.avaxNodeOpRewardAmt
	minipool.item<index>.avaxLiquidStakerRewardAmt
	minipool.item<index>.ggpSlashAmt = amt of ggp bond that was slashed if necessary (expected reward amt = avaxLiquidStakerAmt * x%/yr / ggpPriceInAvax)
*/

##

## [NC-8]  INCONSISTENT SOLIDITY VERSIONS

> Description

Different Solidity compiler versions are used throughout Src repositories. The following contracts mix versions

[2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol)

     2:  pragma solidity >=0.8.0;

[FILE: 2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol)

   2:  pragma solidity >=0.8.0;

[FILE: 2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol)

     2. pragma solidity 0.8.17;



---------------------------------------------------------------------------------------------------------------------------------------------------------
# LOW RISK FINDINGS :

--------------------------------------------------------------------------------------------------------------------------------------------------------
##

## [L-1] MIXING AND OUTDATED COMPILER

[2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol)

     2:  pragma solidity >=0.8.0;

[FILE: 2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol)

   2:  pragma solidity >=0.8.0;

[FILE: 2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol)

     2. pragma solidity 0.8.17;

The pragma version used are:

pragma solidity ^0.8.0;
Note that mixing pragma is not recommended. Because different compiler versions have different meanings and behaviors, it also significantly raises maintenance costs. As a result, depending on the compiler version selected for any given file, deployed contracts may have security issues.

The minimum required version must be 0.8.17; otherwise, contracts will be affected by the following important bug fixes:

0.8.14:

ABI Encoder: When ABI-encoding values from calldata that contain nested arrays, correctly validate the nested array length against calldatasize() in all cases.
Override Checker: Allow changing data location for parameters only when overriding external functions.
0.8.15

Code Generation: Avoid writing dirty bytes to storage when copying bytes arrays.
Yul Optimizer: Keep all memory side-effects of inline assembly blocks.
0.8.16

Code Generation: Fix data corruption that affected ABI-encoding of calldata values represented by tuples: structs at any nesting level; argument lists of external functions, events and errors; return value lists of external functions. The 32 leading bytes of the first dynamically-encoded value in the tuple would get zeroed when the last component contained a statically-encoded array.
0.8.17

Yul Optimizer: Prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call.
Apart from these, there are several minor bug fixes and improvements

##

## [L2]  USE NAMED IMPORTS INSTEAD OF PLAIN IMPORTS

> Instances (10)

[FIL: 2022-12-gogopool/contracts/contract/BaseUpgradeable.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseUpgradeable.sol)

  4:  import "./BaseAbstract.sol";

[FILE: 2022-12-gogopool/contracts/contract/ClaimNodeOp.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol)

  4:  import "./Base.sol";

#

##[ L-3]  LACK OF NO REENTRANT MODIFIER 

The  methods cancelMinipoolByMultisig() and recordStakingStart() do not have the noReentrant modifier and make calls to an external contract that can take advantage of and call these methods again




