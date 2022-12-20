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