## [G-01]
File: ClaimProtocolDAO.sol

URL: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimProtocolDAO.sol#L21

Summary: invoiceID of string data type should be converted to bytes or size 32 if invoiceID is less than or equal to 32 digits or bytes in length;

Line 21 PoC: 
```
{string} memory invoiceID;
```
Remidiation: 
```
{bytes32} memory invoiceID;
```

## [G-02]
File: MinipoolManager.sol

URL: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L78 

Summary: Gas requirement of function MinipoolManager.ggp is infinite: If the gas requirement of a function is higher than the block gas limit, it cannot be executed. Line 78. 

PoC:
```
ERC20 public immutable ggp;
```

Remidiation: Please avoid loops in your functions or actions that modify large areas of storage (this includes clearing or copying arrays in storage)

## [G-03]
File: MinipoolManager.sol

URL: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L79 

Summary: 
Gas requirement of function MinipoolManager.ggAVAX is infinite: If the gas requirement of a function is higher than the block gas limit, it cannot be executed. Please avoid loops in your functions or actions that modify large areas of storage (this includes clearing or copying arrays in storage)
Line: 79

PoC:
```
TokenggAVAX public immutable ggAVAX;
```

Remidiation: Please avoid loops in your functions or actions that modify large areas of storage (this includes clearing or copying arrays in storage)

## [G-04]
File: MinipoolManager.sol

URL: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L196 

Summary: 
Gas requirement of function MinipoolManager.createMinipool is infinite: If the gas requirement of a function is higher than the block gas limit, it cannot be executed. Please avoid loops in your functions or actions that modify large areas of storage (this includes clearing or copying arrays in storage)
Line: 196

PoC:
```
function createMinipool(
		address nodeID,
		uint256 duration,
		uint256 delegationFee,
		uint256 avaxAssignmentRequest
	)
```

Remidiation: Please avoid loops in your functions or actions that modify large areas of storage (this includes clearing or copying arrays in storage)

## [G-05]
File: MinipoolManager.sol

URL: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L273 

Summary: 
Gas requirement of function MinipoolManager.cancelMinipool is infinite: If the gas requirement of a function is higher than the block gas limit, it cannot be executed. Please avoid loops in your functions or actions that modify large areas of storage (this includes clearing or copying arrays in storage)
Line: 273

PoC:
```
function cancelMinipool(address nodeID) external nonReentrant {
		Staking staking = Staking(getContractAddress("Staking"));
		ProtocolDAO dao = ProtocolDAO(getContractAddress("ProtocolDAO"));
		int256 index = requireValidMinipool(nodeID);
		onlyOwner(index);
		// make sure they meet the wait period requirement
		if (block.timestamp - staking.getRewardsStartTime(msg.sender) < dao.getMinipoolCancelMoratoriumSeconds()) {
			revert CancellationTooEarly();
		}
		_cancelMinipoolAndReturnFunds(nodeID, index);
	}
```

Remidiation: Please avoid loops in your functions or actions that modify large areas of storage (this includes clearing or copying arrays in storage)

## [G-06]
File: MinipoolManager.sol

URL: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L287 

Summary: 
Gas requirement of function MinipoolManager.withdrawMinipoolFunds is infinite: If the gas requirement of a function is higher than the block gas limit, it cannot be executed. Please avoid loops in your functions or actions that modify large areas of storage (this includes clearing or copying arrays in storage)
Line: 287

PoC:
```
function withdrawMinipoolFunds(address nodeID) external nonReentrant {
		int256 minipoolIndex = requireValidMinipool(nodeID);
		address owner = onlyOwner(minipoolIndex);
		requireValidStateTransition(minipoolIndex, MinipoolStatus.Finished);
		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Finished));

		uint256 avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpAmt")));
		uint256 avaxNodeOpRewardAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpRewardAmt")));
		uint256 totalAvaxAmt = avaxNodeOpAmt + avaxNodeOpRewardAmt;

		Staking staking = Staking(getContractAddress("Staking"));
		staking.decreaseAVAXStake(owner, avaxNodeOpAmt);

		Vault vault = Vault(getContractAddress("Vault"));
		vault.withdrawAVAX(totalAvaxAmt);
		owner.safeTransferETH(totalAvaxAmt);
	}
```

Remidiation: Please avoid loops in your functions or actions that modify large areas of storage (this includes clearing or copying arrays in storage)

## [G-07]
File: MinipoolManager.sol

URL: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L313 

Summary: 
Gas requirement of function MinipoolManager.canClaimAndInitiateStaking is infinite: If the gas requirement of a function is higher than the block gas limit, it cannot be executed. Please avoid loops in your functions or actions that modify large areas of storage (this includes clearing or copying arrays in storage)
Line: 313

PoC:
```
function canClaimAndInitiateStaking(address nodeID) external view returns (bool) {
		int256 minipoolIndex = onlyValidMultisig(nodeID);
		requireValidStateTransition(minipoolIndex, MinipoolStatus.Launched);

		uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));
		return avaxLiquidStakerAmt <= ggAVAX.amountAvailableForStaking();
	}
```

Remidiation: Please avoid loops in your functions or actions that modify large areas of storage (this includes clearing or copying arrays in storage)

## [G-08]
File: MinipoolManager.sol

URL: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L324 

Summary: 
Gas requirement of function MinipoolManager.claimAndInitiateStaking is infinite: If the gas requirement of a function is higher than the block gas limit, it cannot be executed. Please avoid loops in your functions or actions that modify large areas of storage (this includes clearing or copying arrays in storage)
Line: 324

PoC:
```
function claimAndInitiateStaking(address nodeID) external {
		int256 minipoolIndex = onlyValidMultisig(nodeID);
		requireValidStateTransition(minipoolIndex, MinipoolStatus.Launched);

		uint256 avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpAmt")));
		uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));

		// Transfer funds to this contract and then send to multisig
		ggAVAX.withdrawForStaking(avaxLiquidStakerAmt);
		addUint(keccak256("MinipoolManager.TotalAVAXLiquidStakerAmt"), avaxLiquidStakerAmt);

		setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Launched));
		emit MinipoolStatusChanged(nodeID, MinipoolStatus.Launched);

		Vault vault = Vault(getContractAddress("Vault"));
		vault.withdrawAVAX(avaxNodeOpAmt);

		uint256 totalAvaxAmt = avaxNodeOpAmt + avaxLiquidStakerAmt;
		msg.sender.safeTransferETH(totalAvaxAmt);
	}
```

Remidiation: Please avoid loops in your functions or actions that modify large areas of storage (this includes clearing or copying arrays in storage)