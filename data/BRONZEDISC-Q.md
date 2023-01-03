## QA
---

### Layout Order [1]

- The best-practices for layout within a contract is the following order: state variables, events, modifiers, constructor and functions.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol

```solidity
// state variables should come before events
27:	uint256 public constant MULTISIG_LIMIT = 10;
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol

```solidity
// constructor coming after functions
177:	constructor(
```

---

### Function Visibility [2]

- Order of Functions: Ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. Functions should be grouped according to their visibility and ordered: constructor, receive function (if exists), fallback function (if exists), public, external, internal, private. Within a grouping, place the view and pure functions last.

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/utils/WAVAX.sol

```solidity
// receive function should come before all ordinary functions
30:	receive() external payable virtual {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol

```solidity
// the receive function should come right after the constructor
82:	receive() external payable {
```


https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol

```solidity
// these view functions should come after non-view functions from the same visibility group
193:	function balanceOf(string memory networkContractName) external view returns (uint256) {
200:	function balanceOfToken(string memory networkContractName, ERC20 tokenAddress) external view returns (uint256) {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol

```solidity
// internal functions coming before non-internal ones
87:	function increaseGGPStake(address stakerAddr, uint256 amount) internal {
94:	function decreaseGGPStake(address stakerAddr, uint256 amount) internal {
337:	function _stakeGGP(address stakerAddr, uint256 amount) internal {

// external functions coming before some public ones
308:	function getEffectiveGGPStaked(address stakerAddr) external view returns (uint256) {
319:	function stakeGGP(uint256 amount) external whenNotPaused {
358:	function withdrawGGP(uint256 amount) external whenNotPaused {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol

```solidity
// internal and external functions mixed, they should be organized.
34:	function initialize() external onlyGuardian {
156:	function startRewardsCycle() external {
82:	function inflate() internal {
110:	function increaseRewardsCycleCount() internal {
203:	function distributeMultisigAllotment(
```


https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol

```solidity
// all these external functions should come before public ones, but they are mixed
23:	function initialize() external onlyGuardian {
115:	function getInflationIntervalRate() external view returns (uint256) {
181:	function getTargetGGAVAXReserveRate() external view returns (uint256) {
209:	function upgradeExistingContract(
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Oracle.sol

```solidity
// these view functions should come after all the other external ones
36:	function getGGPPriceInAVAXFromOneInch() external view returns (uint256 price, uint256 timestamp) {
46:	function getGGPPriceInAVAX() external view returns (uint256 price, uint256 timestamp) {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol

```solidity
// public functions should be declared before external ones
55:	function disableAllMultisigs() public onlyDefender {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L96-L109

```solidity
 public functions should come before external functions
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol

```solidity
// place the view and pure functions last
35:	function getRewardsCycleTotal() public view returns (uint256) {
46:	function isEligible(address stakerAddr) external view returns (bool) {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L90-L131

```solidity
  View functions are coming before non-view functions
```

---

### natSpec missing [3]

- @title: A title that should describe the contract/interface
- @author: The name of the author (for contract, interface)
- @notice: Explain to an end user what this does (for contract, interface, function, public state variable, event)
- @dev: Explain to a developer any extra details (for contract, interface, function, state variable, event)
- @param: Documents a parameter (just like in doxygen) and must be followed by parameter name (for function, event)
- @return: Documents the return variables of a contract’s function (function, public state variable)
- @inheritdoc: Copies all missing tags from the base function and must be followed by the contract name (for function, public state variable)

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/utils/WAVAX.sol

```solidity
9: contract WAVAX is ERC20("Wrapped AVAX", "AVAX", 18) {

12:	event Deposit(address indexed from, uint256 amount);

14:	event Withdrawal(address indexed to, uint256 amount);

16:	function deposit() public payable virtual {

22:	function withdraw(uint256 amount) public virtual {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/utils/RialtoSimulator.sol

```solidity
21:  contract RialtoSimulator {

31:	constructor(

49:	function depositggAVAX(uint256 amount) public {

53:	function setGGPPriceInAVAX(uint256 price, uint256 timestamp) external {

// incomplete
58:	function processMinipoolStart(address nodeID) public returns (MinipoolManager.Minipool memory) {

// incomplete
76:	function processMinipoolEndWithRewards(address nodeID) public returns (MinipoolManager.Minipool memory) {

87:	function processMinipoolEndWithoutRewards(address nodeID) public returns (MinipoolManager.Minipool memory) {

98:	function processGGPRewards() public {

127:	function isInvestor(address addr) public pure returns (bool) {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/utils/OneInchMock.sol

```solidity
6: contract OneInchMock {

9:	function setMockedRate(uint256 rate) public {

13:	function getRateToEth(IERC20 srcToken, bool useSrcWrappers) external view returns (uint256 weightedRate) {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/utils/Multicall.sol

```solidity
10:	struct Call {

15:	function aggregate(Call[] memory calls) public returns (uint256 blockNumber, bytes[] memory returnData) {

27:	function getEthBalance(address addr) public view returns (uint256 balance) {

31:	function getBlockHash(uint256 blockNumber) public view returns (bytes32 blockHash) {

35:	function getLastBlockHash() public view returns (bytes32 blockHash) {

39:	function getCurrentBlockTimestamp() public view returns (uint256 timestamp) {

43:	function getCurrentBlockDifficulty() public view returns (uint256 difficulty) {

47:	function getCurrentBlockGasLimit() public view returns (uint256 gaslimit) {

51:	function getCurrentBlockCoinBaseConstructor() public view returns (address coinbase) {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol

```solidity
15:	event Transfer(address indexed from, address indexed to, uint256 amount);

17:	event Approval(address indexed owner, address indexed spender, uint256 amount);

53:	function __ERC20Upgradeable_init(

70:	function approve(address spender, uint256 amount) public virtual returns (bool) {

78:	function transfer(address to, uint256 amount) public virtual returns (bool) {

92:	function transferFrom(

118:	function permit(

162:	function DOMAIN_SEPARATOR() public view virtual returns (bytes32) {

166:	function computeDomainSeparator() internal view virtual returns (bytes32) {

183:	function _mint(address to, uint256 amount) internal virtual {

195:	function _burn(address from, uint256 amount) internal virtual {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol

```solidity
36:	event NewRewardsCycle(uint256 indexed cycleEnd, uint256 rewardsAmt);

37:	event WithdrawnForStaking(address indexed caller, uint256 assets);

38:	event DepositedFromStaking(address indexed caller, uint256 baseAmt, uint256 rewardsAmt);

58:	modifier whenTokenNotPaused(uint256 amt) {

72:	function initialize(Storage storageAddress, ERC20 asset) public initializer {

// incomplete
113:	function totalAssets() public view override returns (uint256) {

132:	function amountAvailableForStaking() public view returns (uint256) {

142:	function depositFromStaking(uint256 baseAmt, uint256 rewardAmt) public payable onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

153:	function withdrawForStaking(uint256 assets) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

166:	function depositAVAX() public payable returns (uint256 shares) {

180:	function withdrawAVAX(uint256 assets) public returns (uint256 shares) {

191:	function redeemAVAX(uint256 shares) public returns (uint256 assets) {

// incomplete
206:	function maxWithdraw(address _owner) public view override returns (uint256) {

// incomplete
216:	function maxRedeem(address _owner) public view override returns (uint256) {

225:	function previewDeposit(uint256 assets) public view override whenTokenNotPaused(assets) returns (uint256) {

229:	function previewMint(uint256 shares) public view override whenTokenNotPaused(shares) returns (uint256) {

233:	function previewWithdraw(uint256 assets) public view override whenTokenNotPaused(assets) returns (uint256) {

237:	function previewRedeem(uint256 shares) public view override whenTokenNotPaused(shares) returns (uint256) {

241:	function beforeWithdraw(

248:	function afterDeposit(

255:	function _authorizeUpgrade(address newImplementation) internal override onlyGuardian {}
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenGGP.sol

```solidity
9:  contract TokenGGP is ERC20 {

12:	constructor() ERC20("GoGoPool Protocol", "GGP", 18) {

13:		_mint(msg.sender, TOTAL_SUPPLY);
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/interface/IWithdrawer.sol

```solidity
6:	function receiveWithdrawalAVAX() external payable;
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/interface/IWAVAX.sol

```solidity
4:  interface IWAVAX {

5:	function deposit() external payable;

7:	function transfer(address to, uint256 value) external returns (bool);

9:	function approve(address spender, uint256 amount) external returns (bool);

11:	function transferFrom(

17:	function balanceOf(address owner) external view returns (uint256);

19:	function withdraw(uint256) external;
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/interface/IOneInch.sol

```solidity
6:  interface IOneInch {

7:	function getRateToEth(ERC20 srcToken, bool useSrcWrappers) external view returns (uint256 weightedRate);
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol

```solidity
30:	event AVAXDeposited(string indexed by, uint256 amount);

31:	event AVAXTransfer(string indexed from, string indexed to, uint256 amount);

32:	event AVAXWithdrawn(string indexed by, uint256 amount);

33:	event TokenDeposited(bytes32 indexed by, address indexed tokenAddress, uint256 amount);

34:	event TokenTransfer(bytes32 indexed by, bytes32 indexed to, address indexed tokenAddress, uint256 amount);

34:	event TokenWithdrawn(bytes32 indexed by, address indexed tokenAddress, uint256 amount);

40:	constructor(Storage storageAddress) Base(storageAddress) {

204:	function addAllowedToken(address tokenAddress) external onlyGuardian {

208:	function removeAllowedToken(address tokenAddress) external onlyGuardian {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol

```solidity
7:  contract Storage {

12:	event GuardianChanged(address oldGuardian, address newGuardian);

35:	constructor() {

41:	function setGuardian(address newAddress) external {

51:	function getGuardian() external view returns (address) {

72:	function getAddress(bytes32 key) external view returns (address) {

76:	function getBool(bytes32 key) external view returns (bool) {

80:	function getBytes(bytes32 key) external view returns (bytes memory) {

84:	function getBytes32(bytes32 key) external view returns (bytes32) {

88:	function getInt(bytes32 key) external view returns (int256) {

92:	function getString(bytes32 key) external view returns (string memory) {

96:	function getUint(bytes32 key) external view returns (uint256) {

104:	function setAddress(bytes32 key, address value) external onlyRegisteredNetworkContract {

108:	function setBool(bytes32 key, bool value) external onlyRegisteredNetworkContract {

112:	function setBytes(bytes32 key, bytes calldata value) external onlyRegisteredNetworkContract {

116:	function setBytes32(bytes32 key, bytes32 value) external onlyRegisteredNetworkContract {

120:	function setInt(bytes32 key, int256 value) external onlyRegisteredNetworkContract {

124:	function setString(bytes32 key, string calldata value) external onlyRegisteredNetworkContract {

128:	function setUint(bytes32 key, uint256 value) external onlyRegisteredNetworkContract {

136:	function deleteAddress(bytes32 key) external onlyRegisteredNetworkContract {

140:	function deleteBool(bytes32 key) external onlyRegisteredNetworkContract {

144:	function deleteBytes(bytes32 key) external onlyRegisteredNetworkContract {

148:	function deleteBytes32(bytes32 key) external onlyRegisteredNetworkContract {

152:	function deleteInt(bytes32 key) external onlyRegisteredNetworkContract {

156:	function deleteString(bytes32 key) external onlyRegisteredNetworkContract {

160:	function deleteUint(bytes32 key) external onlyRegisteredNetworkContract {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol

```solidity
40:	event GGPStaked(address indexed from, uint256 amount);

41:	event GGPWithdrawn(address indexed to, uint256 amount);

60:	constructor(Storage storageAddress, ERC20 ggp_) Base(storageAddress) {

// incomplete
66:	function getTotalGGPStake() public view returns (uint256) {
72:	function getStakerCount() public view returns (uint256) {
80:	function getGGPStake(address stakerAddr) public view returns (uint256) {
87:	function increaseGGPStake(address stakerAddr, uint256 amount) internal {
103:	function getAVAXStake(address stakerAddr) public view returns (uint256) {
110:	function increaseAVAXStake(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
117:	function decreaseAVAXStake(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
126:	function getAVAXAssigned(address stakerAddr) public view returns (uint256) {
133:	function increaseAVAXAssigned(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
140:	function decreaseAVAXAssigned(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
139:	function getAVAXAssignedHighWater(address stakerAddr) public view returns (uint256) {
156:	function increaseAVAXAssignedHighWater(address stakerAddr, uint256 amount) public onlyRegisteredNetworkContract {
173:	function getMinipoolCount(address stakerAddr) public view returns (uint256) {
196:	function getRewardsStartTime(address stakerAddr) public view returns (uint256) {
217:	function getGGPRewards(address stakerAddr) public view returns (uint256) {
224:	function increaseGGPRewards(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {
231:	function decreaseGGPRewards(address stakerAddr, uint256 amount) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {
240:	function getLastRewardsCycleCompleted(address stakerAddr) public view returns (uint256) {
308:	function getEffectiveGGPStaked(address stakerAddr) external view returns (uint256) {
387:	function requireValidStaker(address stakerAddr) public view returns (int256) {
398:	function getIndexOf(address stakerAddr) public view returns (int256) {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol

```solidity
24:	event GGPInflated(uint256 newTokens);

25:	event NewRewardsCycleStarted(uint256 totalRewardsAmt);

26:	event ClaimNodeOpRewardsTransfered(uint256 value);

27:	event ProtocolDAORewardsTransfered(uint256 value);

28:	event MultisigRewardsTransfered(uint256 value);

30:	constructor(Storage storageAddress) Base(storageAddress) {

34:	function initialize() external onlyGuardian {

// incomplete
105:	function getRewardsCycleCount() public view returns (uint256) {
115:	function getRewardsCycleStartTime() public view returns (uint256) 
120:	function getRewardsCycleTotalAmt() public view returns (uint256) {
125:	function getRewardsCyclesElapsed() public view returns (uint256) {
151:	function canStartRewardsCycle() public view returns (bool) {
```


https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol

```solidity
12:	modifier valueNotGreaterThanOne(uint256 setterValue) {

19:	constructor(Storage storageAddress) Base(storageAddress) {

23:	function initialize() external onlyGuardian {

// incomplete
61:	function getContractPaused(string memory contractName) public view returns (bool) {
86:	function getRewardsCycleSeconds() public view returns (uint256) {
91:	function getTotalGGPCirculatingSupply() public view returns (uint256) {
96:	function setTotalGGPCirculatingSupply(uint256 amount) public onlySpecificRegisteredContract("RewardsPool", msg.sender) {
102:	function getClaimingContractPct(string memory claimingContract) public view returns (uint256) {
107:	function setClaimingContractPct(string memory claimingContract, uint256 decimal) public onlyGuardian valueNotGreaterThanOne(decimal) {
130:	function getMinipoolMinAVAXStakingAmt() public view returns (uint256) {
135:	function getMinipoolNodeCommissionFeePct() public view returns (uint256) {
140:	function getMinipoolMaxAVAXAssignment() public view returns (uint256) {
145:	function getMinipoolMinAVAXAssignment() public view returns (uint256) {
150:	function getMinipoolCancelMoratoriumSeconds() public view returns (uint256) {
156:	function setExpectedAVAXRewardsRate(uint256 rate) public onlyMultisig valueNotGreaterThanOne(rate) {
161:	function getExpectedAVAXRewardsRate() public view returns (uint256) {
168:	function getMaxCollateralizationRatio() public view returns (uint256) {
173:	function getMinCollateralizationRatio() public view returns (uint256) {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Oracle.sol

```solidity
21:	event GGPPriceUpdated(uint256 indexed price, uint256 timestamp);

23:	constructor(Storage storageAddress) Base(storageAddress) {

// incomplete
28:	function setOneInch(address addr) external onlyGuardian {
```


https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol

```solidity
22:	constructor(Storage storageAddress) Base(storageAddress) {

// incomplete
27:	function addDefender(address defender) external onlyGuardian {

// incomplete
32:	function removeDefender(address defender) external onlyGuardian {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol

```solidity
23:	event DisabledMultisig(address indexed multisig, address actor);

24:	event EnabledMultisig(address indexed multisig, address actor);

25:	event RegisteredMultisig(address indexed multisig, address actor);

29:	constructor(Storage storageAddress) Base(storageAddress) {


```


https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol

```solidity
75:	event GGPSlashed(address indexed nodeID, uint256 ggp);

76:	event MinipoolStatusChanged(address indexed nodeID, MinipoolStatus indexed status);

106:	function receiveWithdrawalAVAX() external payable {}

177:	constructor(

// incomplete
644:	function getMinipoolCount() public view returns (uint256) {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimProtocolDAO.sol

```solidity
13:	event GGPTokensSentByDAOProtocol(string invoiceID, address indexed from, address indexed to, uint256 amount);

15:	constructor(Storage storageAddress) Base(storageAddress) {

// incomplete
20:	function spend(
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol

```solidity
29:	constructor(Storage storageAddress, ERC20 ggp_) Base(storageAddress) {

// incomplete, missing @return
35:	function getRewardsCycleTotal() public view returns (uint256) {

// incomplete, missing @param
40:	function setRewardsCycleTotal(uint256 amount) public onlySpecificRegisteredContract("RewardsPool", msg.sender) {

// incomplete
46:	function isEligible(address stakerAddr) external view returns (bool) {

// incomplete
56:	function calculateAndDistributeRewards(address stakerAddr, uint256 totalEligibleGGPStaked) external onlyMultisig {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseUpgradeable.sol

```solidity
9:  contract BaseUpgradeable is Initializable, BaseAbstract {

10:	function __BaseUpgradeable_init(Storage gogoStorageAddress) internal onlyInitializing {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol

```solidity

//  incomplete natspec
32:	modifier onlySpecificRegisteredContract(string memory contractName, address contractAddress) {

//  incomplete natspec
51:	modifier guardianOrSpecificRegisteredContract(string memory contractName, address contractAddress) {

//  incomplete natspec
90:	function getContractAddress(string memory contractName) internal view returns (address) {

//  incomplete natspec
99:	function getContractName(address contractAddress) internal view returns (string memory) {

107:	function getAddress(bytes32 key) internal view returns (address) {

111:	function getBool(bytes32 key) internal view returns (bool) {

115:	function getBytes(bytes32 key) internal view returns (bytes memory) {

119:	function getBytes32(bytes32 key) internal view returns (bytes32) {

123:	function getInt(bytes32 key) internal view returns (int256) {

127:	function getUint(bytes32 key) internal view returns (uint256) {

131:	function getString(bytes32 key) internal view returns (string memory) {

135:	function setAddress(bytes32 key, address value) internal {

139:	function setBool(bytes32 key, bool value) internal {

143:	function setBytes(bytes32 key, bytes memory value) internal {

147:	function setBytes32(bytes32 key, bytes32 value) internal {

151:	function setInt(bytes32 key, int256 value) internal {

155:	function setUint(bytes32 key, uint256 value) internal {

159:	function setString(bytes32 key, string memory value) internal {

163:	function deleteAddress(bytes32 key) internal {

167:	function deleteBool(bytes32 key) internal {

171:	function deleteBytes(bytes32 key) internal {

175:	function deleteBytes32(bytes32 key) internal {

179:	function deleteInt(bytes32 key) internal {

183:	function deleteUint(bytes32 key) internal {

187:	function deleteString(bytes32 key) internal {

191:	function addUint(bytes32 key, uint256 amount) internal {

195:	function subUint(bytes32 key, uint256 amount) internal {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Base.sol

```solidity
7:  abstract contract Base is BaseAbstract {
```

---

### State variable and function names [4]

- Variables should be named according to their specifications
  - private and internal `variables` should preppend with `underline`
  - private and internal `functions` should preppend with `underline`
  - constant state variables should be UPPER_CASE

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/utils/RialtoSimulator.sol

```solidity
// internal functions missing a prefixed `_`
24:	MinipoolManager internal minipoolMgr;
25:	ClaimNodeOp internal nopClaim;
26:	RewardsPool internal rewardsPool;
27:	Staking internal staking;
28:	Oracle internal oracle;
29:	TokenggAVAX internal ggAVAX;
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol

```solidity
// for internal state variables use _varName, also avoid upper-case for non constant or immutable
43:	uint256 internal INITIAL_CHAIN_ID;

// for internal state variables use _varName, also avoid upper-case for non constant or immutable
45:	bytes32 internal INITIAL_DOMAIN_SEPARATOR;

// internal functions missing a prefixed `_`
166:	function computeDomainSeparator() internal view virtual returns (bytes32) {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol

```solidity
// internal functions missing a prefixed `_`
241:	function beforeWithdraw(
248:	function afterDeposit(
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol

```solidity
15:	mapping(bytes32 => address) private addressStorage;
16:	mapping(bytes32 => bool) private booleanStorage;
17:	mapping(bytes32 => bytes) private bytesStorage;
18:	mapping(bytes32 => bytes32) private bytes32Storage;
19:	mapping(bytes32 => int256) private intStorage;
20:	mapping(bytes32 => string) private stringStorage;
21:	mapping(bytes32 => uint256) private uintStorage;

24:	address private guardian;
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol

```solidity
// internal functions missing a prefixed `_`
87:	function increaseGGPStake(address stakerAddr, uint256 amount) internal {
94:	function decreaseGGPStake(address stakerAddr, uint256 amount) internal {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol

```solidity
// internal functions missing a prefixed `_`
82:	function inflate() internal {
110:	function increaseRewardsCycleCount() internal {
203:	function distributeMultisigAllotment(
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol

```solidity
// private and internal `functions` should preppend with `underline`
115:	function onlyOwner(int256 minipoolIndex) private view returns (address) {

127:	function onlyValidMultisig(address nodeID) private view returns (int256) {

140:	function requireValidMinipool(address nodeID) private view returns (int256) {

152:	function requireValidStateTransition(int256 minipoolIndex, MinipoolStatus to) private view {

670:	function slash(int256 index) private {

687:	function resetMinipoolData(int256 index) private {
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol

```solidity
// should be prefixed with underline
21:	Storage internal gogoStorage;

// private and internal `functions` should preppend with `underline`
90:	function getContractAddress(string memory contractName) internal view returns (address) {

99:	function getContractName(address contractAddress) internal view returns (string memory) {

107:	function getAddress(bytes32 key) internal view returns (address) {

111:	function getBool(bytes32 key) internal view returns (bool) {

115:	function getBytes(bytes32 key) internal view returns (bytes memory) {

119:	function getBytes32(bytes32 key) internal view returns (bytes32) {

123:	function getInt(bytes32 key) internal view returns (int256) {

127:	function getUint(bytes32 key) internal view returns (uint256) {

131:	function getString(bytes32 key) internal view returns (string memory) {

135:	function setAddress(bytes32 key, address value) internal {

139:	function setBool(bytes32 key, bool value) internal {

143:	function setBytes(bytes32 key, bytes memory value) internal {

147:	function setBytes32(bytes32 key, bytes32 value) internal {

151:	function setInt(bytes32 key, int256 value) internal {

155:	function setUint(bytes32 key, uint256 value) internal {

159:	function setString(bytes32 key, string memory value) internal {

163:	function deleteAddress(bytes32 key) internal {

167:	function deleteBool(bytes32 key) internal {

171:	function deleteBytes(bytes32 key) internal {

175:	function deleteBytes32(bytes32 key) internal {

179:	function deleteInt(bytes32 key) internal {

183:	function deleteUint(bytes32 key) internal {

187:	function deleteString(bytes32 key) internal {

191:	function addUint(bytes32 key, uint256 amount) internal {

195:	function subUint(bytes32 key, uint256 amount) internal {
```

---

### Version [5]

- Pragma versions should be standardized and avoid floating pragma `( ^ )`

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol

```solidity
2: pragma solidity >=0.8.0;
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol

```solidity
2: pragma solidity >=0.8.0;
```

---


### Observations [6]

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/utils/WAVAX.sol

```solidity
// typo
14:	event Withdrawal(address indexed to, uint256 amount);

// event being called before transfer happening
25:		emit Withdrawal(msg.sender, amount);
```


https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol

```solidity
// event emitting is coming before the guardian assignment
35:   constructor() {
36:    emit GuardianChanged(address(0), msg.sender);
37:    guardian = msg.sender;
38:   }
```