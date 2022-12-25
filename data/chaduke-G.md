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

G8. https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseAbstract.sol#L107-L197
eliminating these unnecessary getters and setters can save gas, one can simply call gogoStorage.getX(), gogoStorage.setX(), gogoStorage.deleteX(), and gogoStorage.addX(), and only increase readability of the contracts

G9. https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L157-L159
No need to introduce another variable ``tokenContract``, here, a type casing is sufficient:
```
ERC20(tokenAddress).safeTransfer(withdrawalAddress, amount);
```
G10. https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L353
Replacing this line with the following line can save gas since there is no need to check AGAIN check the validity of the ``stakerIndex``:
```
addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpStaked")), amount);
```

G11 https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L398-L400
Much gas is wasted because the stored index of a staker is added by 1 in the storage. AS a result, both reading and updating need to make adjustment and waste gas. To save gas, gettting rid of this add-by-1/subtract-by-1 operation and let the first staker have index 1 instead. We will not use the INDEX 0. 

G12. https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L566
Much gas is wasted because the stored index of a ``minipool``  is added by 1 in the storage. AS a result, both reading and updating need to make adjustment and waste gas. To save gas, gettting rid of this add-by-1/subtract-by-1 operation and the first ``minipool`` will have index 1 instead. We will not use the INDEX 0. 

G13 https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L149
Including  the line inside  unchecked can save gas since we know ``baseAmt <= stakingTotalAssets`` as a result of line 144.
```
unchecked{stakingTotalAssets -= baseAmt;}
```

G14: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L128
adding ``unchecked`` for the line can save gas as we know underflow and devide-by-zero  are impossible.
```
unchecked{
      uint256 unlockedRewards = (lastRewardsAmt_ * (block.timestamp - lastSync_)) / (rewardsCycleEnd_ - lastSync_);
}
```

G15: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L166-L177
abi.encodePacked() can be used here, which saves gas.
```
function computeDomainSeparator() internal view virtual returns (bytes32) {
		return
			keccak256(
				abi.encodePacked(        // @use abi.encodePacked() saves gas here
					keccak256("EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)"),
					keccak256(bytes(name)),
					keccak256("1"),
					block.chainid,
					address(this)
				)
			);
	}
```

G16. https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L154-L166
Dropping the first condition can save gas since the second condition implies the first condition, in addition, owner cannot be address 0x0 since ox0 cannot sign anything)

```
require(recoveredAddress == owner, "INVALID_SIGNER");
```

G17. https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L138-L145
abi.encodePacked() can be used here, which saves gas.
```
abi.encodePacked(
								keccak256("Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)"),
								owner,
								spender,
								value,
								nonces[owner]++,
								deadline
							)
```


G18. https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L139
Adding unchecked can save gas here, impossible for underflow
```
unchecked{
           return totalAssets_ - reservedAssets - stakingTotalAssets;
}
```

G19. https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L99
Adding unchecked can save gas here, impossible for underflow
```
uint256 nextRewardsAmt = (asset.balanceOf(address(this)) + stakingTotalAssets_) - totalReleasedAssets_ - lastRewardsAmt_;

```
G20. https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L162-L163
No need to introduce another variable ``withdrawer`` and the need of assignment stmt:
```
IWithdrawer(msg.sender).receiveWithdrawalAVAX{value: assets}();

``` 
G21: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L142-L151
There is no need to introduce the variable ``totalAmt`` and the assignment statement in line 143, we can save gas by deleting them
```
function depositFromStaking(uint256 baseAmt, uint256 rewardAmt) public payable onlySpecificRegisteredContract("MinipoolManager", msg.sender) 
		if (msg.value != (baseAmt + rewardAmt) || baseAmt > stakingTotalAssets) { // @audit: replace it with msg.value
			revert InvalidStakingDeposit();
		}

		emit DepositedFromStaking(msg.sender, baseAmt, rewardAmt);
		unchecked{stakingTotalAssets -= baseAmt;}
		IWAVAX(address(asset)).deposit{value: msg.value}();  // @audit: replace it with msg.value
	}
```

G22. https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L153
Making the function payable can save gas, as this will eliminate the code to check whether msg.value == 0, and this function will be called by ``MinipoolManager``. Therefore, no worry to send avax via this function mistakenly.

G23: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L276-L277
Moving these two lines as the first two lines of the function can save gas when the caller is not the owner for example:
```
function cancelMinipool(address nodeID) external nonReentrant {
		int256 index = requireValidMinipool(nodeID);
		onlyOwner(index);

		Staking staking = Staking(getContractAddress("Staking"));
		ProtocolDAO dao = ProtocolDAO(getContractAddress("ProtocolDAO"));
		// make sure they meet the wait period requirement
		if (block.timestamp - staking.getRewardsStartTime(msg.sender) < dao.getMinipoolCancelMoratoriumSeconds()) {
			revert CancellationTooEarly();
		}
		_cancelMinipoolAndReturnFunds(nodeID, index);
	}
```

G24. https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L61
Adding payable to the function can save gas since it will eliminate the code to check whether msg.value == 0, and the modifier ``onlySpecificRegisteredContract`` will ensure no AVAX will be sent to this function by mistake, so there is no need to check whether msg.value == 0. 

G25. https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L341-L342
No need to introduce another variable ``totalAvaxAmt`` and the assignment statement. 

```		
msg.sender.safeTransferETH(avaxNodeOpAmt + avaxLiquidStakerAmt);

```
 
g26. https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L400-L403
No need to introduce another variable ``totalAvaxAmt`` and the assignment statement. Change it to
```
		if (msg.value != avaxNodeOpAmt + avaxLiquidStakerAmt + avaxTotalRewardAmt) {
			revert InvalidAmount();
		}

```

G27. https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L141-L146
This block can be simply replaced by the following
```
return claimContractPct.mulWadDown(currentCycleRewardsTotal);

```

G28. https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L82-L100
We can simply replace L98 with the following and eliminate L84 to save gas.
```
setUint(keccak256("RewardsPool.InflationIntervalStartTime"), block.timestamp);
```

G29: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L92
Adding unchecked can save gas since underflow is impossible here:
```
uint256 newTokens = newTotalSupply - currentTotalSupply;
```