
## [L-1] USE OF `block.timestamp`

### Description
Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the *[Entropy Illusion](https://hacken.io/discover/most-common-smart-contract-vulnerabilities/#Entropy_Illusion)* for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

### ✅ Recommendation
Use oracle instead of `block.timestamp`

### 🔍 Findings:
| | |
|---|---|
|examples/2022-12-gogopool/tree/main/contracts/contract/ClaimNodeOp.sol#L49|[uint256 elapsedSecs = (block.timestamp - rewardsStartTime);](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ClaimNodeOp.sol#L49 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L226|[staking.setRewardsStartTime(msg.sender, block.timestamp);](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L226 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L279|[if (block.timestamp - staking.getRewardsStartTime(msg.sender) < dao.getMinipoolCancelMoratoriumSeconds()) {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L279 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L356|[if (startTime > block.timestamp) {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L356 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L394|[if (endTime <= startTime || endTime > block.timestamp) {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L394 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/Oracle.sol#L13|[Oracle.GGPTimestamp = block.timestamp of last update to GGP price](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Oracle.sol#L13 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/Oracle.sol#L40|[timestamp = block.timestamp;](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Oracle.sol#L40 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/Oracle.sol#L59|[if (timestamp < lastTimestamp || timestamp > block.timestamp) {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Oracle.sol#L59 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/RewardsPool.sol#L40|[setUint(keccak256("RewardsPool.RewardsCycleStartTime"), block.timestamp);](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/RewardsPool.sol#L40 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/RewardsPool.sol#L41|[setUint(keccak256("RewardsPool.InflationIntervalStartTime"), block.timestamp);](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/RewardsPool.sol#L41 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/RewardsPool.sol#L60|[return (block.timestamp - startTime) / dao.getInflationIntervalSeconds();](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/RewardsPool.sol#L60 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/RewardsPool.sol#L84|[uint256 inflationIntervalElapsedSeconds = (block.timestamp - getInflationIntervalStartTime());](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/RewardsPool.sol#L84 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/RewardsPool.sol#L128|[return (block.timestamp - startTime) / dao.getRewardsCycleSeconds();](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/RewardsPool.sol#L128 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/RewardsPool.sol#L164|[setUint(keccak256("RewardsPool.RewardsCycleStartTime"), block.timestamp);](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/RewardsPool.sol#L164 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L206|[if (time > block.timestamp) {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L206 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L78|[rewardsCycleEnd = (block.timestamp.safeCastTo32() / rewardsCycleLength) * rewardsCycleLength;](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L78 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L89|[uint32 timestamp = block.timestamp.safeCastTo32();](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L89 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L120|[if (block.timestamp >= rewardsCycleEnd_) {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L120 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L128|[uint256 unlockedRewards = (lastRewardsAmt_ * (block.timestamp - lastSync_)) / (rewardsCycleEnd_ - lastSync_);](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L128 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L127|[require(deadline >= block.timestamp, "PERMIT_DEADLINE_EXPIRED");](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L127 )|
---

## [L-2] `ecrecover()` NOT CHECKED FOR SIGNER ADDRESS OF ZERO

### Description
The `ecrecover()` function returns an address of zero when the signature does not match. This can cause problems if address zero is ever the owner of assets, and someone uses the permit function on address zero. 

### ✅ Recommendation
You should check whether the return value is address(0) or not in terms of the security.

### 🔍 Findings:
| | |
|---|---|
|examples/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L132|[address recoveredAddress = ecrecover(](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L132 )|
---

## [L-3] `_safeMint()` SHOULD BE USED RATHER THAN `_mint()` WHEREVER POSSIBLE

### Description
`_safeMint()` is used along with [IERC721Receiver](https://docs.openzeppelin.com/contracts/2.x/api/token/erc721#IERC721Receiver) that checks if you are sending the minted token to a Contract that is capable to manage NFTs or not. This is to prevent tokens to be lost.

### ✅ Recommendation
Change from` _mint` to `_safeMint`.

### 🔍 Findings:
| | |
|---|---|
|examples/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenGGP.sol#L13|[_mint(msg.sender, TOTAL_SUPPLY);](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenGGP.sol#L13 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L176|[_mint(msg.sender, shares);](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L176 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L183|[function _mint(address to, uint256 amount) internal virtual {](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L183 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L49|[_mint(receiver, shares);](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L49 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L62|[_mint(receiver, shares);](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L62 )|
---

## [L-4] OPEN TODOS

### Description
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

### ✅ Recommendation
Implement TODO and remove.

### 🔍 Findings:
| | |
|---|---|
|examples/2022-12-gogopool/tree/main/contracts/contract/BaseAbstract.sol#L6|[// TODO remove this when dev is complete](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/BaseAbstract.sol#L6 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L412|[// TODO Revisit this logic if we ever allow unequal matched funds](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L412 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L203|[// TODO cant use onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) since we also call from increaseMinipoolCount. Wat do?](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L203 )|
---

## [N-1] CONSIDER ADDING  CHECKS FOR SIGNATURE MALLEABILITY

### Description
Consider adding checks for signature malleability

### ✅ Recommendation
Use OpenZeppelin's ECDSA contract rather than calling ecrecover() directly

### 🔍 Findings:
| | |
|---|---|
|examples/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L132|[address recoveredAddress = ecrecover(](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L132 )|
---

## [N-2] USE SCIENTIFIC NOTATION (e.g.`1e18`) RATHER THAN LARGE MULTIPLES OF 10 (e.g. 1000000)

### Description
Use scientific notation (e.g.`1e18`) rather than large multiples of 10, that could lead to errors.

### ✅ Recommendation
Use scientific notation instead of large multiples of 10.

### 🔍 Findings:
| | |
|---|---|
|examples/2022-12-gogopool/tree/main/contracts/contract/ProtocolDAO.sol#L49|[setUint(keccak256("ProtocolDAO.MinipoolMaxAVAXAssignment"), 10_000 ether);](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ProtocolDAO.sol#L49 )|
---


## [N-3] LINES ARE TOO LONG

### Description
Usually lines in source code are limited to 80 characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length

### ✅ Recommendation
Reduce number of characters per line to improve readability.

### 🔍 Findings:
| | |
|---|---|
|examples/2022-12-gogopool/tree/main/contracts/contract/ProtocolDAO.sol#L41|[setUint(keccak256("ProtocolDAO.InflationIntervalRate"), 1000133680617113500); // 5%!a(MISSING)nnual calculated on a daily interval - Calculate in js example: let dailyInflation = web3.utils.toBN((1 + 0.05) ** (1 / (365)) * 1e18);](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ProtocolDAO.sol#L41 )|
---

## [N-4] FOR MODERN AND MORE READABLE CODE, UPDATE IMPORT USAGES

### Description
Solidity code is also cleaner in another way that might not be noticeable. Only import what you need Specific imports with curly braces allow us to apply this rule better.

### ✅ Recommendation
Import like this: `import {contract1 , contract2} from "filename.sol";`

### 🔍 Findings:
| | |
|---|---|
|examples/2022-12-gogopool/tree/main/contracts/contract/Base.sol#L4|[import "./BaseAbstract.sol";](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Base.sol#L4 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/BaseUpgradeable.sol#L4|[import "./BaseAbstract.sol";](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/BaseUpgradeable.sol#L4 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/ClaimNodeOp.sol#L4|[import "./Base.sol";](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ClaimNodeOp.sol#L4 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/ClaimProtocolDAO.sol#L4|[import "./Base.sol";](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ClaimProtocolDAO.sol#L4 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L4|[import "./Base.sol";](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L4 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/MultisigManager.sol#L4|[import "./Base.sol";](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MultisigManager.sol#L4 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/Ocyticus.sol#L4|[import "./Base.sol";](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Ocyticus.sol#L4 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/Oracle.sol#L4|[import "./Base.sol";](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Oracle.sol#L4 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/ProtocolDAO.sol#L4|[import "./Base.sol";](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ProtocolDAO.sol#L4 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/RewardsPool.sol#L4|[import "./Base.sol";](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/RewardsPool.sol#L4 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L4|[import "./Base.sol";](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L4 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/Vault.sol#L4|[import "./Base.sol";](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Vault.sol#L4 )|
|examples/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L6|[import "../BaseUpgradeable.sol";](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L6 )|
---
