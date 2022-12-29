## The functions `Staking.increaseGGPStake` and `Staking.decreaseGGPStake` should be private

The functions `Staking.increaseGGPStake` and `Staking.decreaseGGPStake` are only called inside the contract, therefore they should be private, instead of internal (letting them internal adds a risk of future contracts that could inherits from Staking to change staking funds inadvertently).

File: [`Staking.sol#L87`](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L87)

```solidity
      function increaseGGPStake(address stakerAddr, uint256 amount) internal {
		int256 stakerIndex = requireValidStaker(stakerAddr);
		addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpStaked")), amount);
	}

      function decreaseGGPStake(address stakerAddr, uint256 amount) internal {
		int256 stakerIndex = requireValidStaker(stakerAddr);
		subUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpStaked")), amount);
	}
```

## Check for zero address

File: [`Vault.sol#L204`]  (https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L204)

```solidity
function addAllowedToken(address tokenAddress) 
}
```
File: [`Storage.sol#L41-L48`]  (https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L41-L48)

```solidity
function setGuardian(address newAddress) external {
		// Check tx comes from current guardian
		if (msg.sender != guardian) {
			revert MustBeGuardian();
		}
		// Store new address awaiting confirmation
		newGuardian = newAddress;
}
```
function setAddress(bytes32 key, address value) external onlyRegisteredNetworkContract {
		addressStorage[key] = value;
	}

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol

	function addDefender(address defender) external onlyGuardian {
		defenders[defender] = true;
	}


Emit event
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol
	function pauseEverything() external onlyDefender {
		ProtocolDAO dao = ProtocolDAO(getContractAddress("ProtocolDAO"));
		dao.pauseContract("TokenggAVAX");
		dao.pauseContract("MinipoolManager");
		dao.pauseContract("Staking");
		disableAllMultisigs();
	}


---
## [GAS] Tautology in boolean comparisons

Boolean comparisons can be checked as `true` or `false`directly. There is no need to compare them with a `true` or `false` value. A simplified comparison can reduce gas costs.

[`BaseAbstract.sol#L25`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L25)
[`BaseAbstract.sol#L74`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L74)
[`Storage.sol#L29`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L29)

*Recommendation:* Consider removing tautology comparisons, for example, refactoring in `BaseAbstract` contract's functions:

```solidity
if (!getBool(keccak256(abi.encodePacked("contract.exists", msg.sender)))) {
     revert InvalidOrOutdatedContract();
}

if (!enabled) {
     revert MustBeMultisig();
}
```


---
## `version` state variable can be set as immutable *** >>> BaseAbstract.sol

