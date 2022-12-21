## [L-01]
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L225-L227

Block timestamp: Use of "block.timestamp": "block.timestamp" can be influenced by miners to a certain degree. That means that a miner can "choose" the block.timestamp, to a certain degree, to change the outcome of a transaction in the mined block.

PoC
if (staking.getRewardsStartTime(msg.sender) == 0) {
			staking.setRewardsStartTime(msg.sender, block.timestamp -1);
		}

## [L-02]
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L279-L281 

Use of "block.timestamp": "block.timestamp" can be influenced by miners to a certain degree. That means that a miner can "choose" the block.timestamp, to a certain degree, to change the outcome of a transaction in the mined block.

PoC
if (block.timestamp - 1 - staking.getRewardsStartTime(msg.sender) < dao.getMinipoolCancelMoratoriumSeconds()) {
			revert CancellationTooEarly();
		}

## [L-03]
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L356 

Block timestamp: Use of "block.timestamp": "block.timestamp" can be influenced by miners to a certain degree. That means that a miner can "choose" the block.timestamp, to a certain degree, to change the outcome of a transaction in the mined block.

PoC
if (startTime > block.timestamp - 1) {
			revert InvalidStartTime();
		}

## [L-04]
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L394

Block timestamp: Use of "block.timestamp": "block.timestamp" can be influenced by miners to a certain degree. That means that a miner can "choose" the block.timestamp, to a certain degree, to change the outcome of a transaction in the mined block.

PoC
if (endTime <= startTime || endTime > block.timestamp - 1) {

## [L-05]
Use else if instead of || OR

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L157-L170

PoC
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
			isValid = false;
		}

## [L-06]
Use else if instead of || OR

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L207-L214

PoC
if (
			// Current rule is matched funds must be 1:1 nodeOp:LiqStaker
			msg.value != avaxAssignmentRequest ||
			avaxAssignmentRequest > dao.getMinipoolMaxAVAXAssignment() ||
			avaxAssignmentRequest < dao.getMinipoolMinAVAXAssignment()
		) {
			revert InvalidAVAXAssignmentRequest();
		}

## [L-07]
Use else if instead of || OR

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L394

PoC
if (endTime <= startTime || endTime > block.timestamp) {
			revert InvalidEndTime();
		}

## [L-08]
Use else if instead of || OR

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L614

PoC
if (max > totalMinipools || limit == 0) {
			max = totalMinipools;
		}

## [L-09]
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Oracle.sol#L40 

Block timestamp:
Use of "block.timestamp": "block.timestamp" can be influenced by miners to a certain degree. That means that a miner can "choose" the block.timestamp, to a certain degree, to change the outcome of a transaction in the mined block.
Pos: 40:14:

PoC
function getGGPPriceInAVAXFromOneInch() external view returns (uint256 price, uint256 timestamp) {
		TokenGGP ggp = TokenGGP(getContractAddress("TokenGGP"));
		IOneInch oneinch = IOneInch(getAddress(keccak256("Oracle.OneInch")));
		price = oneinch.getRateToEth(ggp, false);
		timestamp = block.timestamp -1;
	}

## [L-10]
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Oracle.sol#L59 

Block timestamp:
Use of "block.timestamp": "block.timestamp" can be influenced by miners to a certain degree. That means that a miner can "choose" the block.timestamp, to a certain degree, to change the outcome of a transaction in the mined block.
Pos: 59:47:

PoC
function setGGPPriceInAVAX(uint256 price, uint256 timestamp) external onlyMultisig {
		uint256 lastTimestamp = getUint(keccak256("Oracle.GGPTimestamp"));
		if (timestamp < lastTimestamp || timestamp > block.timestamp -1) {
			revert InvalidTimestamp();
		}
		if (price == 0) {
			revert InvalidGGPPrice();
		}

## [L-11]
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L40 

Block timestamp:
Use of "block.timestamp": "block.timestamp" can be influenced by miners to a certain degree. That means that a miner can "choose" the block.timestamp, to a certain degree, to change the outcome of a transaction in the mined block.
Pos: 40:58:

PoC
setUint(keccak256("RewardsPool.RewardsCycleStartTime"), block.timestamp -1);

## [L-12]
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L41 

Block timestamp:
Use of "block.timestamp": "block.timestamp" can be influenced by miners to a certain degree. That means that a miner can "choose" the block.timestamp, to a certain degree, to change the outcome of a transaction in the mined block.
Pos: 41:63:

PoC
setUint(keccak256("RewardsPool.InflationIntervalStartTime"), block.timestamp -1);

## [L-13]
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L60 

Block timestamp:
Use of "block.timestamp": "block.timestamp" can be influenced by miners to a certain degree. That means that a miner can "choose" the block.timestamp, to a certain degree, to change the outcome of a transaction in the mined block.
Pos: 60:10:

PoC
return (block.timestamp -1 -startTime) / dao.getInflationIntervalSeconds();

## [L-14]
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L84 

Block timestamp:
Use of "block.timestamp": "block.timestamp" can be influenced by miners to a certain degree. That means that a miner can "choose" the block.timestamp, to a certain degree, to change the outcome of a transaction in the mined block.
Pos: 84:45:

PoC
uint256 inflationIntervalElapsedSeconds = (block.timestamp -1 - getInflationIntervalStartTime());