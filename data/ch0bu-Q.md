## 1. Open TODOs

Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment.

```
6	// TODO remove this when dev is complete
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol
```
412      // TODO Revisit this logic if we ever allow unequal matched funds
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol
```
203      // TODO cant use onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) since we also call from increaseMini
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol


## 2. The `nonReentrant modifier` should occur before all other modifiers

This is a best-practice to protect against reentrancy in other modifiers.

```
61       function withdrawAVAX(uint256 amount) external onlyRegisteredNetworkContract nonReentrant {
141      ) external onlyRegisteredNetworkContract nonReentrant {
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol


## 3. Variable names that consist of all capital letters should be reserved for `constant`/`immutable variables`

If the variable needs to be different based on which class it comes from, a `view`/`pure` function should be used instead. Like this for example:
- https://github.com/OpenZeppelin/openzeppelin-contracts/blob/76eee35971c2541585e05cbf258510dda7b2fbc6/contracts/token/ERC20/extensions/draft-IERC20Permit.sol#L59

```
43	uint256 internal INITIAL_CHAIN_ID;
45	bytes32 internal INITIAL_DOMAIN_SEPARATOR;
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol


## 4. Avoid nested `if` statements

For better readability and analysis it is better to avoid nested `if` blocks.

```
76	if (msg.sender != owner) {
77		uint256 allowed = allowance[owner][msg.sender]; // Saves gas for limited approvals.
79		if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;
80	}
...
96	if (msg.sender != owner) {
97		uint256 allowed = allowance[owner][msg.sender]; // Saves gas for limited approvals.
99		if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;
100	}
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol


## 5. `_safeMint()` should be used rather than `_mint()` wherever possible

`_mint()` is [discouraged](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271) in favor of `_safeMint()` which ensures that the recipient is either an EOA or implements `IERC721Receiver`. Both OpenZeppelin and solmate have versions of this function so that NFTs aren’t lost if they’re minted to contracts that cannot transfer them back out.

```
49       _mint(receiver, shares);
62       _mint(receiver, shares);
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol
```
176      _mint(msg.sender, shares);
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol
```
13       _mint(msg.sender, TOTAL_SUPPLY);
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenGGP.sol


## 6. `require()` should be used instead of `assert()`

Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction’s available gas rather than returning it, as `require()`/`revert()` do. `assert()` should be avoided even past solidity version 0.8.0 as its [documentation](https://docs.soliditylang.org/en/v0.8.14/control-structures.html#panic-via-assert-and-error-via-require) states that “The assert function creates an error of type Panic(uint256). … Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix”.

```
83       assert(msg.sender == address(asset));
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol


## 7. Upgradeable contract is missing a `__gap[50]` storage variable to allow for new storage variables in later versions

See the following link for a description of this storage variable. While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.

- https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps

```
9        contract BaseUpgradeable is Initializable, BaseAbstract {
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseUpgradeable.sol
```
10       abstract contract ERC20Upgradeable is Initializable {
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol
```
11       abstract contract ERC4626Upgradeable is Initializable, ERC20Upgradeable {
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol
```
24       contract TokenggAVAX is Initializable, ERC4626Upgradeable, UUPSUpgradeable, BaseUpgradeable {
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol


## 8. Immutable addresses lack zero-address check

Constructors should check the address written in an immutable address variable is not the zero address.

```
58	ERC20 public immutable ggp;
60	constructor(Storage storageAddress, ERC20 ggp_) Base(storageAddress) {
61		version = 1;
62		ggp = ggp_;
63	}
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol
```
78	ERC20 public immutable ggp;
79	TokenggAVAX public immutable ggAVAX;
177	constructor(
178		Storage storageAddress,
179		ERC20 ggp_,
180		TokenggAVAX ggAVAX_
181	) Base(storageAddress) {
182		version = 1;
183		ggp = ggp_;
184		ggAVAX = ggAVAX_;
185	}
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol
```
27	ERC20 public immutable ggp;
29	constructor(Storage storageAddress, ERC20 ggp_) Base(storageAddress) {
30		version = 1;
31		ggp = ggp_;
32	}
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol
