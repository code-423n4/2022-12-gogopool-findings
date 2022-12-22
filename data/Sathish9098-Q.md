## 

## [NC-1]  INCLUDE RETURN PARAMETERS IN NATSPEC COMMENTS

(https://docs.soliditylang.org/en/v0.8.15/natspec-format.html)

If Return parameters are declared, you must prefix them with ”/// @return”.

Some code analysis programs do analysis by reading NatSpec details, if they can’t see the “@return” tag, they do incomplete analysis.

Recommended Mitigation Steps
Include return parameters in NatSpec comments

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

[FILE: 12-gogopool/contracts/contract/MinipoolManager.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol)

   106:  function receiveWithdrawalAVAX() external payable {}

[FILE: 2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol)

    255:   function _authorizeUpgrade(address newImplementation) internal override onlyGuardian {}

[FILE: 2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol)

        176:  function beforeWithdraw(uint256 assets, uint256 shares) internal virtual {}

	178:  function afterDeposit(uint256 assets, uint256 shares) internal virtual {}

##
## [NC-4]  USE SAME SOLIDITY VERSIONS FOR ALL CONTRACTS . SOME CONTRACTS USING LOWER VERSIONS AND OTHER CONTRACTS USING LATEST STABLE VERSION. THIS IS NOT A BEST CODE PRACTICE.

[2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol)

     2:  pragma solidity >=0.8.0;

[FILE: 2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol)

   2:  pragma solidity >=0.8.0;

[FILE: 2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol)

     2. pragma solidity 0.8.17;

##

## [NC-5]  NON-LIBRARY/INTERFACE FILES SHOULD USE FIXED COMPILER VERSIONS, NOT FLOATING ONES

INSTANCES(2) :

pragma solidity >=0.8.0 <0.9.0;




NC-1	Return values of approve() not checked	2
NC-2	Event is missing indexed fields	23
NC-3	Constants should be defined rather than using magic numbers	1
NC-4	Functions not used internally could be marked external	57

L-1	abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()	157
L-2	Unsafe ERC20 operation(s)	2