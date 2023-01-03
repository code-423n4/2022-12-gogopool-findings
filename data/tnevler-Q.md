# Report
## Low ##
### [L-1]: Check offset>max is required
**Context:**

1. ```minipools = new Minipool[](max - offset);``` [L617](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L617)
1. ```stakers = new Staker[](max - offset);``` [L426](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L426)

**Description:**
If offset is greater than max it will be not clear why reverts. Add condition if offset>max then revert with message that offset bigger than stakers/minipools amount.

## Non-Critical Issues ##
### [N-1]: Function defines a named return variable but then it uses return statements
**Context:**

1. ```return getMinipool(index);``` [L574](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L574) 
1. ```return (currentTotalSupply, newTotalSupply);``` [L77](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L77) 

**Recommendation:**

Choose named return variable or return statement. It is unnecessary to use both.

### [N-2]: Wrong order of functions
**Context:**

1. ```constructor(``` [L177](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L177) (constructor can not go after private function)
1. MinipoolManager.sol [L115-L175](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L115-L175) (private functions can not go before costructor)

**Description:**

According to [official solidity documentation](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions) functions should be grouped according to their visibility and ordered:

+ constructor

+ receive function (if exists)

+ fallback function (if exists)

+ external

+ public

+ internal

+ private

Within a grouping, place the view and pure functions last.

**Recommendation:**

Put the functions in the correct order according to the documentation.

### [N-3]: NatSpec is missing
**Context:**

1. [BaseUpgradeable.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseUpgradeable.sol)
1. [BaseAbstract.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol) (23 functions are missing NatSpec)
1. ```function initialize() external onlyGuardian {``` [L23](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L23) 
1. [Storage.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol) (24 functions are missing NatSpec)
1. ```function addAllowedToken(address tokenAddress) external onlyGuardian {``` [L204](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L204) 
1. ```function removeAllowedToken(address tokenAddress) external onlyGuardian {``` [L208](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L208) 
1. [TokenggAVAX.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol) (14 functions are missing NatSpec)
1. [ERC20Upgradeable.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol) (9 functions are missing NatSpec)
1. [ERC4626Upgradeable.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol)

### [N-4]: Typos
**Context:**

1. ```/// @notice The node commision fee for running the hardware for the minipool``` [L134](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L134) (Change **commision** to **commission**)
1. ```// Emit the withdrawl event for that contract``` [L68](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L68) (Change **withdrawl** to **withdrawn**)
1. ```/// @dev No funds actually move, just bookeeping``` [L81](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L81) (Change **bookeeping** to **bookkeeping**)
1. ```/// @param toContractName Name of the contract fucns are being transferred to``` [L82](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L82) (Change **fucns** to **funds**)
1. ```/// @param withdrawalAddress Address that will recieve the token``` [L134](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L134) (Change **recieve** to **receive**)
1. ```// The constructor is exectued only when creating implementation contract``` [L67](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L67) (Change **exectued** to **executed**)

### [N-5]: TODO
**Context:**

1. ```// TODO remove this when dev is complete``` [L6](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L6) 
1. ```// TODO Revisit this logic if we ever allow unequal matched funds``` [L412](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L412) 
1. ```// TODO cant use onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) since we also call from increaseMinipoolCount. Wat do?``` [L203](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L203) 

### [N-6]: Line is too long
**Context:**

1. ```bool enabled = (addr != address(0)) && getBool(keccak256(abi.encodePacked("multisig.item", multisigIndex, ".enabled")));``` [L73](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L73) 
1. ```minipool.item<index>.ggpSlashAmt = amt of ggp bond that was slashed if necessary (expected reward amt = avaxLiquidStakerAmt * x%/yr / ggpPriceInAvax)``` [L53](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L53) 
1. ```/// @notice Accept AVAX deposit from node operator to create a Minipool. Node Operator must be staking GGP. Open to public.``` [L191](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L191) 
1. ```/// @notice Withdraw function for a Node Operator to claim all AVAX funds they are due (original AVAX staked, plus any AVAX rewards)``` [L285](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L285) 
1. ```uint256 avaxNodeOpRewardAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpRewardAmt")));``` [L294](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L294) 
1. ```uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));``` [L317](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L317) 
1. ```/// @notice Removes the AVAX associated with a minipool from the protocol to stake it on Avalanche and register the node as a validator``` [L321](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L321) 
1. ```uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));``` [L329](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L329) 
1. ```uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));``` [L371](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L371) 
1. ```uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));``` [L399](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L399) 
1. ```uint256 avaxLiquidStakerRewardAmt = avaxHalfRewards - avaxHalfRewards.mulWadDown(dao.getMinipoolNodeCommissionFeePct());``` [L417](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L417) 
1. ```setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerRewardAmt")), avaxLiquidStakerRewardAmt);``` [L421](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L421) 
1. ```uint256 avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxLiquidStakerAmt")));``` [L490](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L490) 
1. ```/// @dev this will prevent the multisig from completing validations. The minipool will need to be manually reassigned to a new multisig``` [L67](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L67) 
1. ```setUint(keccak256("ProtocolDAO.RewardsCycleSeconds"), 28 days); // The time in which a claim period will span in seconds - 28 days by default``` [L33](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L33) 
1. ```setUint(keccak256("ProtocolDAO.InflationIntervalRate"), 1000133680617113500); // 5% annual calculated on a daily interval - Calculate in js example: let dailyInflation = web3.utils.toBN((1 + 0.05) ** (1 / (365)) * 1e18);``` [L41](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L41) 
1. ```function setTotalGGPCirculatingSupply(uint256 amount) public onlySpecificRegisteredContract("RewardsPool", msg.sender) {``` [L96](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L96) 
1. ```function setClaimingContractPct(string memory claimingContract, uint256 decimal) public onlyGuardian valueNotGreaterThanOne(decimal) {``` [L107](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L107) 
1. ```if (daoClaimContractAllotment + nopClaimContractAllotment + multisigClaimContractAllotment > getRewardsCycleTotalAmt()) {``` [L174](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L174) 
1. ```function increaseAVAXStake(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {``` [L110](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L110) 
1. ```function decreaseAVAXStake(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {``` [L117](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L117) 
1. ```function increaseAVAXAssigned(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {``` [L133](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L133) 
1. ```function decreaseAVAXAssigned(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {``` [L140](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L140) 
1. ```function increaseGGPRewards(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {``` [L224](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L224) 
1. ```function decreaseGGPRewards(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {``` [L231](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L231) 
1. ```function setLastRewardsCycleCompleted(address stakerAddr, uint256 cycleNumber) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {``` [L248](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L248) 
1. ```/// @dev (saves a large amount of gas this way through not needing a double token transfer via a network contract first)``` [L104](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L104) 
1. ```uint256 nextRewardsAmt = (asset.balanceOf(address(this)) + stakingTotalAssets_) - totalReleasedAssets_ - lastRewardsAmt_;``` [L99](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L99) 
1. ```function depositFromStaking(uint256 baseAmt, uint256 rewardAmt) public payable onlySpecificRegisteredContract("MinipoolManager", msg.sender) {``` [L142](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L142) 
1. ```event Withdraw(address indexed caller, address indexed receiver, address indexed owner, uint256 assets, uint256 shares);``` [L21](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L21) 

**Description:**

Maximum suggested line length is [120 characters](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-length).
