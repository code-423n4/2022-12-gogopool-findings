G1. https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseAbstract.sol#L25
NO need to compare a bool value with ``false``
```
if (!booleanStorage[keccak256(abi.encodePacked("contract.exists", msg.sender))] && msg.sender != guardian) {
			revert InvalidOrOutdatedContract();
		}
```

G2. https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseAbstract.sol#L40-L48
Applying the short-circuit can save gas - the second condition might not need to be evaluated sometimes
```
modifier guardianOrRegisteredContract() {
		bool isContract = getBool(keccak256(abi.encodePacked("contract.exists", msg.sender)));
                if (!isContract)) {
			revert MustBeGuardianOrValidContract();
		}

		bool isGuardian = msg.sender == gogoStorage.getGuardian();
		if (!isGuardian) {
			revert MustBeGuardianOrValidContract();
		}
		_;
	}
```

G3. https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseAbstract.sol#L51-L59
Applying the short-circuit can save gas - the second condition might not need to be evaluated sometimes
```
modifier guardianOrSpecificRegisteredContract(string memory contractName, address contractAddress) {
		bool isContract = contractAddress == getAddress(keccak256(abi.encodePacked("contract.address", contractName)));
		if (!isContract) {
			revert MustBeGuardianOrValidContract();
		}

		bool isGuardian = msg.sender == gogoStorage.getGuardian();

		if (!isGuardian) {
			revert MustBeGuardianOrValidContract();
		}
		_;
	}

```

G4. https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L152-L175
Moving revert stmt to the default case can save gas:
```
function requireValidStateTransition(int256 minipoolIndex, MinipoolStatus to) private view {
		bytes32 statusKey = keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status"));
		MinipoolStatus currentStatus = MinipoolStatus(getUint(statusKey));
		bool isValid;

		if (currentStatus == MinipoolStatus.Prelaunch) {
			isValid = (to == MinipoolStatus.Launched || to == MinipoolStatus.Canceled);
		} else if (currentStatus == MinipoolStatus.Launched) {
			isValid = (to == MinipoolStatus.Staking || to == MinipoolStatus.Error);
		} else if (currentStatus == MinipoolStatus.Staking) {
			isValid = (to == MinipoolStatus.Withdrawable || to == MinipoolStatus.Error);
		} else if (currentStatus == MinipoolStatus.Withdrawable || currentStatus == MinipoolStatus.Error) {
			isValid = (to == MinipoolStatus.Finished || to == MinipoolStatus.Prelaunch);
		} else if (currentStatus == MinipoolStatus.Finished || currentStatus == MinipoolStatus.Canceled) {
			// Once a node is finished/canceled, if they re-validate they go back to beginning state
			isValid = (to == MinipoolStatus.Prelaunch);
		} else {
			revert InvalidStateTransition();
		}
	}
```

G5. https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L75
Impossible to underflow due to previous check, so we can put it inside the ``unchecked`` to reduce gas.
```
unchecked{
                avaxBalances[fromContractName] = avaxBalances[fromContractName] - amount;
		avaxBalances[toContractName] = avaxBalances[toContractName] + amount;
}
```

G6. https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L155
Impossible to underflow due to previous check, so we can put it inside the ``unchecked`` to reduce gas.
```
unchecked{
           tokenBalances[contractKey] = tokenBalances[contractKey] - amount;
}
```
G7. https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L187
Impossible to underflow due to previous check, so we can put it inside the ``unchecked`` to reduce gas.
```
unchecked{
         tokenBalances[contractKeyFrom] = tokenBalances[contractKeyFrom] - amount;
}
```

G7. https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseAbstract.sol#L107-L197
eliminating these unnecessary getters and setters can save gas, one can simply call gogoStorage.getX(), gogoStorage.setX(), gogoStorage.deleteX(), and gogoStorage.addX(), and only increase readability of the contracts

G8. https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L157-L159
No need to introduce another variable ``tokenContract``, here, a type casing is sufficent:
```
ERC20(tokenAddress).safeTransfer(withdrawalAddress, amount);
```
G8. https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L346-L352
Revising the order of statements can save gas.
```
if (stakerIndex == -1) {
			// create index for the new staker
			addUint(keccak256("staker.count"); // we exchanged the order of these two stmts here
			stakerIndex = int256(getUint(keccak256("staker.count")));
			setUint(keccak256(abi.encodePacked("staker.index", stakerAddr)), uint256(stakerIndex)); // @no need to add 1 here
			setAddress(keccak256(abi.encodePacked("staker.item", stakerIndex, ".stakerAddr")), stakerAddr);
		}
```

G8. https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L353
Replacing this line with the following line can save gas since there is no need to check AGAIN check the validity of the ``stakerIndex``:
```
addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpStaked")), amount);
```