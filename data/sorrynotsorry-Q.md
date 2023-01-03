# QA (LOW & NON-CRITICAL)

## [L-01] Zero price sanitation
Oracle contract has `getGGPPriceInAVAXFromOneInch` function to request off-chain prices through 1inch API.
We're not able to see how this function is called and exercised on the back end and for various validations.
However, there is no zero price sanitation in the function on the protocol level. It calls `getRateToEth` method of the API and directly assigns the price and the timestamp variables returned.
When we check the 1inch contract [here](https://github.com/1inch/spot-price-aggregator/blob/master/contracts/OffchainOracle.sol), it's also confirmed that there is no zero price sanitation as well.
The zero price sanitation is only carried out in `getGGPPriceInAVAX` and `setGGPPriceInAVAX` functions.
```solidity
	function getGGPPriceInAVAXFromOneInch() external view returns (uint256 price, uint256 timestamp) {
		TokenGGP ggp = TokenGGP(getContractAddress("TokenGGP"));
		IOneInch oneinch = IOneInch(getAddress(keccak256("Oracle.OneInch")));
		price = oneinch.getRateToEth(ggp, false); 
		timestamp = block.timestamp;
	}
```
It's recommended to implement a zero price check due to avoid possible erratic values to be recorded.


## [L-02] setGGPPriceInAVAX is frontrunnable
Oracle contract's `setGGPPriceInAVAX` is frontrunnable. The bots seeking for this function signature can accordingly take arbitrage positions where it can affect the existing GGP price and many times can cause a manipulated atomic flash-dump and slashing action taken by the MiniPoolManagers who might be running these bots as well.
```solidity
	function setGGPPriceInAVAX(uint256 price, uint256 timestamp) external onlyMultisig { 
		uint256 lastTimestamp = getUint(keccak256("Oracle.GGPTimestamp"));
		if (timestamp < lastTimestamp || timestamp > block.timestamp) { 
			revert InvalidTimestamp();
		}
		if (price == 0) {
			revert InvalidGGPPrice();
		}
		setUint(keccak256("Oracle.GGPPriceInAVAX"), price);
		setUint(keccak256("Oracle.GGPTimestamp"), timestamp);
		emit GGPPriceUpdated(price, timestamp);
	}
 ```
A manipulated slashing can be avoided in `slash()` function by checking the block.timestamp is not equal to `timestamp` in which the price was updated in `setGGPPriceInAVAX` function.

## [L-03] setGGPPriceInAVAX does not validate the stale price
As above, `setGGPPriceInAVAX` checks the `lastTimestamp` should be less than the `timestamp` in which the price was updated. It also checks the timestamp should be less than block.timestamp, else the function reverts. 
There is no information on how frequently the price & timestamp are to be recorded. According to the function, if the price is 0, it reverts and the protocol will not have a healthy price set for GGP for that call. In a scenario of the Oracle feed malfunctions and suffering due to long downtimes, the protocol will be forced to use the stale price which was recorded a long time before the interruption.  In turbulent market conditions, this might lead to a big price difference between the existing price and the most recently recorded one.

E.g. Staking contract'S `contgetMinimumGGPStake` requires staker's minimum GGP stake to collateralize their minipools, based on the current GGP price. The function calls `getGGPPriceInAVAX` to return the GGP price in AVAX which `setGGPPriceInAVAX` has set the value. If the returned price is stale, the collateralization will not be true which in the end may cause inflated or fewer rewards than it should be.

It's recommended to set a timestamp validation for the set intervals. Accordingly, it can be avoided to feed the protocol on a stale price. 

## [L-04] ProtocolDAO contract design
ProtocolDAO is the heart of the protocol settings. If any settings are required to be changed or required to be added in a new version, the contract is serving for it. The settings are set utilizing hardcoded text. However, it's less prone to possible errors if using structs rather than hardcoded strings. Since the mapping will store whatever is being provided and there is no confirmation mechanism regarding what's being stored, it might go downwards in terms of changing the settings, especially for the times if something goes wrong (either on the Avalanche chain or on the protocol level).
It's recommended to use a struct library contract or emit the settings to confirm the set values.

## [L-05] slashGGP is frontrunnable
Staking contract's slashGGP is frontrunnable. A staker who is not in good standing can monitor the mempool for the MinipoolManager's `slash` call. S/he can increase GGP position to be over the 150% collateralization ratio, call `withdrawGGP`, and exit with a less token loss which the slashing will cause more if slashed.

## [L-06] Modifier logic in Storage contract
Storage contract's `onlyRegisteredNetworkContract` allows the calls whether the callee is a registered contract `and` is the `guardian`. However, the NATSPEC states the call to come from `guardian` or the latest version of a contract.
```solidity
/// @dev Only allow access from guardian or the latest version of a contract in the GoGoPool network
	modifier onlyRegisteredNetworkContract() {
		if (booleanStorage[keccak256(abi.encodePacked("contract.exists", msg.sender))] == false && msg.sender != guardian) { 
			revert InvalidOrOutdatedContract();
		}
		_;
	}
```
## [L-07] Underflow in Storage's subUint function
Storage contract's `subUint` function subtracts the amount from the mapping without validating the amount is =< than the value.
```solidity
	/// @param key The key for the record
	/// @param amount An amount to subtract from the record's value
	function subUint(bytes32 key, uint256 amount) external onlyRegisteredNetworkContract {
		uintStorage[key] = uintStorage[key] - amount;
	}
```
For sure this will not underflow due to the compiler version, however, it will revert for the calling instances if the substracted amount is greater than the value.

Instances are scoped in;
[BaseAbstract](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseAbstract.sol#L195-L197)
[MiniPoolManager-1](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L433)
[MiniPoolManager-2](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L512)
[Staking-1](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L96)
[Staking-2](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L119)
[Staking-3](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L142)
[Staking-4](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L189)
[Staking-5](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L233)
