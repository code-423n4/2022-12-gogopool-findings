

### [L01] require() should be used instead of assert()


#### Findings:
```
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::83 => assert(msg.sender == address(asset));
```






### [L02] Missing checks for `address(0x0)` when assigning values to `address` state variables


#### Findings:
```
2022-12-gogopool/contracts/contract/ClaimNodeOp.sol::31 => ggp = ggp_;
2022-12-gogopool/contracts/contract/MinipoolManager.sol::183 => ggp = ggp_;
2022-12-gogopool/contracts/contract/MinipoolManager.sol::184 => ggAVAX = ggAVAX_;
2022-12-gogopool/contracts/contract/Staking.sol::62 => ggp = ggp_;
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol::58 => name = _name;
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol::59 => symbol = _symbol;
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol::60 => decimals = _decimals;
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol::35 => asset = _asset;
```



### [L03] `initialize` functions can be front-run

#### Impact
See [this](https://github.com/code-423n4/2021-10-badgerdao-findings/issues/40) finding from a prior badger-dao contest for details
#### Findings:
```
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::23 => function initialize() external onlyGuardian {
2022-12-gogopool/contracts/contract/RewardsPool.sol::34 => function initialize() external onlyGuardian {
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::72 => function initialize(Storage storageAddress, ERC20 asset) public initializer {
```



### [L04] Upgradeable contract is missing a `__gap[50]` storage variable to allow for new storage variables in later versions

#### Impact
See [this](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps) link for a description of this storage variable. While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.
#### Findings:
```
2022-12-gogopool/contracts/contract/BaseUpgradeable.sol::9 => contract BaseUpgradeable is Initializable, BaseAbstract {
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::24 => contract TokenggAVAX is Initializable, ERC4626Upgradeable, UUPSUpgradeable, BaseUpgradeable {
```





### [L05] approve should be replaced with safeApprove or safeIncreaseAllowance() / safeDecreaseAllowance()

#### Impact
approve is subject to a known front-running attack. Consider using safeApprove instead:
#### Findings:
```
2022-12-gogopool/contracts/contract/ClaimNodeOp.sol::105 => ggp.approve(address(staking), restakeAmt);
2022-12-gogopool/contracts/contract/Staking.sol::342 => ggp.approve(address(vault), amount);
```



### [L06] `_safeMint()` should be used rather than `_mint()` wherever possible

#### Impact
_mint() is discouraged in favor of _safeMint() which ensures that the recipient is either an EOA or implements IERC721Receiver. Both OpenZeppelin and solmate have versions of this function
#### Findings:
```
2022-12-gogopool/contracts/contract/tokens/TokenGGP.sol::13 => _mint(msg.sender, TOTAL_SUPPLY);
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::176 => _mint(msg.sender, shares);
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol::49 => _mint(receiver, shares);
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol::62 => _mint(receiver, shares);
```




### `Non-Critical Issues`

### [N01] Adding a return statement when the function defines a named return variable, is redundant


#### Findings:
```
2022-12-gogopool/contracts/contract/BaseAbstract.sol::95 => return contractAddress;
2022-12-gogopool/contracts/contract/BaseAbstract.sol::104 => return contractName;
2022-12-gogopool/contracts/contract/MinipoolManager.sol::120 => return owner;
2022-12-gogopool/contracts/contract/MinipoolManager.sol::134 => return minipoolIndex;
2022-12-gogopool/contracts/contract/MinipoolManager.sol::146 => return minipoolIndex;
2022-12-gogopool/contracts/contract/MultisigManager.sol::87 => return addr;
2022-12-gogopool/contracts/contract/RewardsPool.sol::146 => return contractRewardsTotal;
2022-12-gogopool/contracts/contract/Staking.sol::390 => return index;
2022-12-gogopool/contracts/contract/Storage.sol::52 => return guardian;
```



### [N02] constants should be defined rather than using magic numbers



#### Findings:
```
2022-12-gogopool/contracts/contract/MinipoolManager.sol::560 => return (avaxAmt.mulWadDown(rate) * duration) / 365 days;
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::34 => setUint(keccak256("ProtocolDAO.TotalGGPCirculatingSupply"), 18_000_000 ether);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::41 => setUint(keccak256("ProtocolDAO.InflationIntervalRate"), 1000133680617113500); // 5% annual calculated on a daily interval - Calculate in js example: let dailyInflation = web3.utils.toBN((1 + 0.05) ** (1 / (365)) * 1e18);
2022-12-gogopool/contracts/contract/tokens/TokenGGP.sol::10 => uint256 private constant TOTAL_SUPPLY = 22_500_000 ether;
2022-12-gogopool/contracts/contract/tokens/TokenGGP.sol::12 => constructor() ERC20("GoGoPool Protocol", "GGP", 18) {
```


### [N03] Variable names that consist of all capital letters should be reserved for `const`/`immutable `variables

#### Impact
If the variable needs to be different based on which class it comes from, a view/pure function should be used instead (e.g. like [this](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/76eee35971c2541585e05cbf258510dda7b2fbc6/contracts/token/ERC20/extensions/draft-IERC20Permit.sol#L59)).
#### Findings:
```
2022-12-gogopool/contracts/contract/BaseAbstract.sol::21 => Storage internal gogoStorage;
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol::43 => uint256 internal INITIAL_CHAIN_ID;
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol::45 => bytes32 internal INITIAL_DOMAIN_SEPARATOR;
```



### [N04] Event is missing `indexed` fields

#### Impact
Each event should use three indexed fields if there are three or more fields
#### Findings:
```
2022-12-gogopool/contracts/contract/ClaimProtocolDAO.sol::13 => event GGPTokensSentByDAOProtocol(string invoiceID, address indexed from, address indexed to, uint256 amount);
2022-12-gogopool/contracts/contract/Storage.sol::12 => event GuardianChanged(address oldGuardian, address newGuardian);
2022-12-gogopool/contracts/contract/Vault.sol::31 => event AVAXTransfer(string indexed from, string indexed to, uint256 amount);
2022-12-gogopool/contracts/contract/Vault.sol::33 => event TokenDeposited(bytes32 indexed by, address indexed tokenAddress, uint256 amount);
2022-12-gogopool/contracts/contract/Vault.sol::35 => event TokenWithdrawn(bytes32 indexed by, address indexed tokenAddress, uint256 amount);
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::36 => event NewRewardsCycle(uint256 indexed cycleEnd, uint256 rewardsAmt);
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::38 => event DepositedFromStaking(address indexed caller, uint256 baseAmt, uint256 rewardsAmt);
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol::15 => event Transfer(address indexed from, address indexed to, uint256 amount);
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol::17 => event Approval(address indexed owner, address indexed spender, uint256 amount);
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol::19 => event Deposit(address indexed caller, address indexed owner, uint256 assets, uint256 shares);
```


### [N05] Open TODOs

#### Impact
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment
#### Findings:
```
2022-12-gogopool/contracts/contract/BaseAbstract.sol::6 => // TODO remove this when dev is complete
2022-12-gogopool/contracts/contract/MinipoolManager.sol::412 => // TODO Revisit this logic if we ever allow unequal matched funds
2022-12-gogopool/contracts/contract/Staking.sol::203 => // TODO cant use onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) since we also call from increaseMinipoolCount. Wat do?
```




### [N06] `public` functions not called by the contract should be declared `external` instead

#### Impact
Contracts are allowed to override their parents’ functions and change the visibility from public to external .
#### Findings:
```
2022-12-gogopool/contracts/contract/ClaimNodeOp.sol::35 => function getRewardsCycleTotal() public view returns (uint256) {
2022-12-gogopool/contracts/contract/ClaimNodeOp.sol::40 => function setRewardsCycleTotal(uint256 amount) public onlySpecificRegisteredContract("RewardsPool", msg.sender) {
2022-12-gogopool/contracts/contract/MinipoolManager.sol::541 => function getTotalAVAXLiquidStakerAmt() public view returns (uint256) {
2022-12-gogopool/contracts/contract/MinipoolManager.sol::547 => function calculateGGPSlashAmt(uint256 avaxRewardAmt) public view returns (uint256) {
2022-12-gogopool/contracts/contract/MinipoolManager.sol::557 => function getExpectedAVAXRewardsAmt(uint256 duration, uint256 avaxAmt) public view returns (uint256) {
2022-12-gogopool/contracts/contract/MinipoolManager.sol::566 => function getIndexOf(address nodeID) public view returns (int256) {
2022-12-gogopool/contracts/contract/MinipoolManager.sol::572 => function getMinipoolByNodeID(address nodeID) public view returns (Minipool memory mp) {
2022-12-gogopool/contracts/contract/MinipoolManager.sol::580 => function getMinipool(int256 index) public view returns (Minipool memory mp) {
2022-12-gogopool/contracts/contract/MinipoolManager.sol::611 => ) public view returns (Minipool[] memory minipools) {
2022-12-gogopool/contracts/contract/MinipoolManager.sol::634 => function getMinipoolCount() public view returns (uint256) {
2022-12-gogopool/contracts/contract/MultisigManager.sol::96 => function getIndexOf(address addr) public view returns (int256) {
2022-12-gogopool/contracts/contract/MultisigManager.sol::102 => function getCount() public view returns (uint256) {
2022-12-gogopool/contracts/contract/MultisigManager.sol::109 => function getMultisig(uint256 index) public view returns (address addr, bool enabled) {
2022-12-gogopool/contracts/contract/Ocyticus.sol::55 => function disableAllMultisigs() public onlyDefender {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::61 => function getContractPaused(string memory contractName) public view returns (bool) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::67 => function pauseContract(string memory contractName) public onlySpecificRegisteredContract("Ocyticus", msg.sender) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::73 => function resumeContract(string memory contractName) public onlySpecificRegisteredContract("Ocyticus", msg.sender) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::81 => function getRewardsEligibilityMinSeconds() public view returns (uint256) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::86 => function getRewardsCycleSeconds() public view returns (uint256) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::91 => function getTotalGGPCirculatingSupply() public view returns (uint256) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::96 => function setTotalGGPCirculatingSupply(uint256 amount) public onlySpecificRegisteredContract("RewardsPool", msg.sender) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::102 => function getClaimingContractPct(string memory claimingContract) public view returns (uint256) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::107 => function setClaimingContractPct(string memory claimingContract, uint256 decimal) public onlyGuardian valueNotGreaterThanOne(decimal) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::123 => function getInflationIntervalSeconds() public view returns (uint256) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::130 => function getMinipoolMinAVAXStakingAmt() public view returns (uint256) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::135 => function getMinipoolNodeCommissionFeePct() public view returns (uint256) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::140 => function getMinipoolMaxAVAXAssignment() public view returns (uint256) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::145 => function getMinipoolMinAVAXAssignment() public view returns (uint256) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::150 => function getMinipoolCancelMoratoriumSeconds() public view returns (uint256) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::156 => function setExpectedAVAXRewardsRate(uint256 rate) public onlyMultisig valueNotGreaterThanOne(rate) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::161 => function getExpectedAVAXRewardsRate() public view returns (uint256) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::168 => function getMaxCollateralizationRatio() public view returns (uint256) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::173 => function getMinCollateralizationRatio() public view returns (uint256) {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::190 => function registerContract(address addr, string memory name) public onlyGuardian {
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::198 => function unregisterContract(address addr) public onlyGuardian {
2022-12-gogopool/contracts/contract/RewardsPool.sol::48 => function getInflationIntervalStartTime() public view returns (uint256) {
2022-12-gogopool/contracts/contract/RewardsPool.sol::54 => function getInflationIntervalsElapsed() public view returns (uint256) {
2022-12-gogopool/contracts/contract/RewardsPool.sol::66 => function getInflationAmt() public view returns (uint256 currentTotalSupply, uint256 newTotalSupply) {
2022-12-gogopool/contracts/contract/RewardsPool.sol::105 => function getRewardsCycleCount() public view returns (uint256) {
2022-12-gogopool/contracts/contract/RewardsPool.sol::115 => function getRewardsCycleStartTime() public view returns (uint256) {
2022-12-gogopool/contracts/contract/RewardsPool.sol::120 => function getRewardsCycleTotalAmt() public view returns (uint256) {
2022-12-gogopool/contracts/contract/RewardsPool.sol::125 => function getRewardsCyclesElapsed() public view returns (uint256) {
2022-12-gogopool/contracts/contract/RewardsPool.sol::134 => function getClaimingContractDistribution(string memory claimingContract) public view returns (uint256) {
2022-12-gogopool/contracts/contract/RewardsPool.sol::151 => function canStartRewardsCycle() public view returns (bool) {
2022-12-gogopool/contracts/contract/Staking.sol::66 => function getTotalGGPStake() public view returns (uint256) {
2022-12-gogopool/contracts/contract/Staking.sol::72 => function getStakerCount() public view returns (uint256) {
2022-12-gogopool/contracts/contract/Staking.sol::80 => function getGGPStake(address stakerAddr) public view returns (uint256) {
2022-12-gogopool/contracts/contract/Staking.sol::103 => function getAVAXStake(address stakerAddr) public view returns (uint256) {
2022-12-gogopool/contracts/contract/Staking.sol::110 => function increaseAVAXStake(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
2022-12-gogopool/contracts/contract/Staking.sol::117 => function decreaseAVAXStake(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
2022-12-gogopool/contracts/contract/Staking.sol::126 => function getAVAXAssigned(address stakerAddr) public view returns (uint256) {
2022-12-gogopool/contracts/contract/Staking.sol::133 => function increaseAVAXAssigned(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
2022-12-gogopool/contracts/contract/Staking.sol::140 => function decreaseAVAXAssigned(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
2022-12-gogopool/contracts/contract/Staking.sol::149 => function getAVAXAssignedHighWater(address stakerAddr) public view returns (uint256) {
2022-12-gogopool/contracts/contract/Staking.sol::156 => function increaseAVAXAssignedHighWater(address stakerAddr, uint256 amount) public onlyRegisteredNetworkContract {
2022-12-gogopool/contracts/contract/Staking.sol::163 => function resetAVAXAssignedHighWater(address stakerAddr) public onlyRegisteredNetworkContract {
2022-12-gogopool/contracts/contract/Staking.sol::173 => function getMinipoolCount(address stakerAddr) public view returns (uint256) {
2022-12-gogopool/contracts/contract/Staking.sol::180 => function increaseMinipoolCount(address stakerAddr) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
2022-12-gogopool/contracts/contract/Staking.sol::187 => function decreaseMinipoolCount(address stakerAddr) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
2022-12-gogopool/contracts/contract/Staking.sol::196 => function getRewardsStartTime(address stakerAddr) public view returns (uint256) {
2022-12-gogopool/contracts/contract/Staking.sol::204 => function setRewardsStartTime(address stakerAddr, uint256 time) public onlyRegisteredNetworkContract {
2022-12-gogopool/contracts/contract/Staking.sol::217 => function getGGPRewards(address stakerAddr) public view returns (uint256) {
2022-12-gogopool/contracts/contract/Staking.sol::224 => function increaseGGPRewards(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {
2022-12-gogopool/contracts/contract/Staking.sol::231 => function decreaseGGPRewards(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {
2022-12-gogopool/contracts/contract/Staking.sol::240 => function getLastRewardsCycleCompleted(address stakerAddr) public view returns (uint256) {
2022-12-gogopool/contracts/contract/Staking.sol::248 => function setLastRewardsCycleCompleted(address stakerAddr, uint256 cycleNumber) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {
2022-12-gogopool/contracts/contract/Staking.sol::256 => function getMinimumGGPStake(address stakerAddr) public view returns (uint256) {
2022-12-gogopool/contracts/contract/Staking.sol::269 => function getCollateralizationRatio(address stakerAddr) public view returns (uint256) {
2022-12-gogopool/contracts/contract/Staking.sol::286 => function getEffectiveRewardsRatio(address stakerAddr) public view returns (uint256) {
2022-12-gogopool/contracts/contract/Staking.sol::328 => function restakeGGP(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {
2022-12-gogopool/contracts/contract/Staking.sol::379 => function slashGGP(address stakerAddr, uint256 ggpAmt) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
2022-12-gogopool/contracts/contract/Staking.sol::387 => function requireValidStaker(address stakerAddr) public view returns (int256) {
2022-12-gogopool/contracts/contract/Staking.sol::398 => function getIndexOf(address stakerAddr) public view returns (int256) {
2022-12-gogopool/contracts/contract/Staking.sol::405 => function getStaker(int256 stakerIndex) public view returns (Staker memory staker) {
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::72 => function initialize(Storage storageAddress, ERC20 asset) public initializer {
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::88 => function syncRewards() public {
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::113 => function totalAssets() public view override returns (uint256) {
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::132 => function amountAvailableForStaking() public view returns (uint256) {
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::142 => function depositFromStaking(uint256 baseAmt, uint256 rewardAmt) public payable onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::153 => function withdrawForStaking(uint256 assets) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::166 => function depositAVAX() public payable returns (uint256 shares) {
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::180 => function withdrawAVAX(uint256 assets) public returns (uint256 shares) {
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::191 => function redeemAVAX(uint256 shares) public returns (uint256 assets) {
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::206 => function maxWithdraw(address _owner) public view override returns (uint256) {
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::216 => function maxRedeem(address _owner) public view override returns (uint256) {
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::225 => function previewDeposit(uint256 assets) public view override whenTokenNotPaused(assets) returns (uint256) {
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::229 => function previewMint(uint256 shares) public view override whenTokenNotPaused(shares) returns (uint256) {
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::233 => function previewWithdraw(uint256 assets) public view override whenTokenNotPaused(assets) returns (uint256) {
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::237 => function previewRedeem(uint256 shares) public view override whenTokenNotPaused(shares) returns (uint256) {
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol::70 => function approve(address spender, uint256 amount) public virtual returns (bool) {
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol::78 => function transfer(address to, uint256 amount) public virtual returns (bool) {
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol::96 => ) public virtual returns (bool) {
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol::126 => ) public virtual {
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol::162 => function DOMAIN_SEPARATOR() public view virtual returns (bytes32) {
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol::27 => ERC20 public asset;
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol::42 => function deposit(uint256 assets, address receiver) public virtual returns (uint256 shares) {
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol::56 => function mint(uint256 shares, address receiver) public virtual returns (uint256 assets) {
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol::73 => ) public virtual returns (uint256 shares) {
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol::95 => ) public virtual returns (uint256 assets) {
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol::118 => function totalAssets() public view virtual returns (uint256);
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol::120 => function convertToShares(uint256 assets) public view virtual returns (uint256) {
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol::126 => function convertToAssets(uint256 shares) public view virtual returns (uint256) {
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol::132 => function previewDeposit(uint256 assets) public view virtual returns (uint256) {
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol::136 => function previewMint(uint256 shares) public view virtual returns (uint256) {
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol::142 => function previewWithdraw(uint256 assets) public view virtual returns (uint256) {
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol::148 => function previewRedeem(uint256 shares) public view virtual returns (uint256) {
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol::156 => function maxDeposit(address) public view virtual returns (uint256) {
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol::160 => function maxMint(address) public view virtual returns (uint256) {
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol::164 => function maxWithdraw(address owner) public view virtual returns (uint256) {
2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol::168 => function maxRedeem(address owner) public view virtual returns (uint256) {
```



### [N07] Numeric values having to do with time should use time units for readability

#### Impact
There are units for seconds, minutes, hours, days, and weeks
#### Findings:
```
2022-12-gogopool/contracts/contract/MinipoolManager.sol::34 => minipool.item<index>.duration = requested validation duration in seconds (performed as 14 day cycles)
2022-12-gogopool/contracts/contract/MinipoolManager.sol::193 => /// @param duration Requested validation period in seconds
2022-12-gogopool/contracts/contract/MinipoolManager.sol::554 => /// @param duration The length of validation in seconds
2022-12-gogopool/contracts/contract/MinipoolManager.sol::560 => return (avaxAmt.mulWadDown(rate) * duration) / 365 days;
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::30 => setUint(keccak256("ProtocolDAO.RewardsEligibilityMinSeconds"), 14 days);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::33 => setUint(keccak256("ProtocolDAO.RewardsCycleSeconds"), 28 days); // The time in which a claim period will span in seconds - 28 days by default
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::40 => setUint(keccak256("ProtocolDAO.InflationIntervalSeconds"), 1 days);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::52 => setUint(keccak256("ProtocolDAO.MinipoolCancelMoratoriumSeconds"), 5 days);
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::79 => /// @notice Get how many seconds a node must be registered for rewards to be eligible for the rewards cycle
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::80 => /// @return uint256 The min number of seconds to be considered eligible
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::85 => /// @notice Get how many seconds in a rewards cycle
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::121 => /// @notice How many seconds to calculate inflation at
2022-12-gogopool/contracts/contract/ProtocolDAO.sol::122 => /// @return uint256 how many seconds to calculate inflation at
2022-12-gogopool/contracts/contract/RewardsPool.sol::168 => //      since inflation is on a 1 day interval and it needs at least one cycle since last calculation
2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol::76 => rewardsCycleLength = 14 days;
```







