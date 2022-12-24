## Summary<a name="Summary">

### Low Risk Issues
| |Issue|Contexts|
|-|:-|:-:|
| [LOW&#x2011;1](#LOW&#x2011;1) | Missing Checks for Address(0x0)  | 2 |
| [LOW&#x2011;2](#LOW&#x2011;2) | Use `_safeMint` instead of `_mint` | 3 |
| [LOW&#x2011;3](#LOW&#x2011;3) | `decimals()` not part of ERC20 standard | 1 |
| [LOW&#x2011;4](#LOW&#x2011;4) | Init functions are susceptible to front-running | 3 |
| [LOW&#x2011;5](#LOW&#x2011;5) | Vulnerable To Cross-chain Replay Attacks Due To Static DOMAIN_SEPARATOR | 1 |
| [LOW&#x2011;6](#LOW&#x2011;6) | The `nonReentrant` modifier should occur before all other modifiers | 2 |
| [LOW&#x2011;7](#LOW&#x2011;7) | Use of `ecrecover` is susceptible to signature malleability | 1 |
| [LOW&#x2011;8](#LOW&#x2011;8) | `require()` should be used instead of `assert()` | 1 |
| [LOW&#x2011;9](#LOW&#x2011;9) | Missing/Incomplete `nonReentrant` Modifiers | 5 |
| [LOW&#x2011;10](#LOW&#x2011;10) | `TokenggAVAX` shares can be sent to the zero address | 1 |
| [LOW&#x2011;11](#LOW&#x2011;11) | Guardian can add itself as a Defender | 1 |

Total: 21 contexts over 11 issues

### Non-critical Issues
| |Issue|Contexts|
|-|:-|:-:|
| [NC&#x2011;1](#NC&#x2011;1) | Critical Changes Should Use Two-step Procedure | 23 |
| [NC&#x2011;2](#NC&#x2011;2) | Use a more recent version of Solidity | 2 |
| [NC&#x2011;3](#NC&#x2011;3) | Redundant Cast | 2 |
| [NC&#x2011;4](#NC&#x2011;4) | Public Functions Not Called By The Contract Should Be Declared External Instead | 5 |
| [NC&#x2011;5](#NC&#x2011;5) | Missing event for critical parameter change | 22 |
| [NC&#x2011;6](#NC&#x2011;6) | Implementation contract may not be initialized | 14 |
| [NC&#x2011;7](#NC&#x2011;7) | Use of Block.Timestamp | 7 |
| [NC&#x2011;8](#NC&#x2011;8) | Non-usage of specific imports | 13 |
| [NC&#x2011;9](#NC&#x2011;9) | Lines are too long | 1 |
| [NC&#x2011;10](#NC&#x2011;10) | Use `bytes.concat()` | 284 |
| [NC&#x2011;11](#NC&#x2011;11) | Open TODOs | 3 |
| [NC&#x2011;12](#NC&#x2011;12) | No need to initialize uints to zero | 3 |

Total: 379 contexts over 12 issues

## Low Risk Issues

### <a href="#Summary">[LOW&#x2011;1]</a><a name="LOW&#x2011;1"> Missing Checks for Address(0x0) 

Lack of zero-address validation on address parameters may lead to transaction reverts, waste gas, require resubmission of transactions and may even force contract redeployments in certain cases within the protocol.

#### <ins>Proof Of Concept</ins>


```solidity
41: function setGuardian: address newAddress
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L41

```solidity
104: function setAddress: address value
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L104



#### <ins>Recommended Mitigation Steps</ins>

Consider adding explicit zero-address validation on input parameters of address type.





### <a href="#Summary">[LOW&#x2011;2]</a><a name="LOW&#x2011;2"> Use `_safeMint` instead of `_mint`

According to openzepplin's ERC721, the use of `_mint` is discouraged, use _safeMint whenever possible.
https://docs.openzeppelin.com/contracts/3.x/api/token/erc721#ERC721-_mint-address-uint256-

#### <ins>Proof Of Concept</ins>


```solidity
176: _mint(msg.sender, shares);
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L176

```solidity
49: _mint(receiver, shares);
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L49



#### <ins>Recommended Mitigation Steps</ins>

Use `_safeMint` whenever possible instead of `_mint`



### <a href="#Summary">[LOW&#x2011;3]</a><a name="LOW&#x2011;3"> `decimals()` not part of ERC20 standard

`decimals()` is not part of the official ERC20 standard and might fail for tokens that do not implement it. While in practice it is very unlikely, as usually most of the tokens implement it, this should still be considered as a potential issue.

#### <ins>Proof Of Concept</ins>


```solidity
34: __ERC20Upgradeable_init(_name, _symbol, _asset.decimals()
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L34






### <a href="#Summary">[LOW&#x2011;4]</a><a name="LOW&#x2011;4"> Init functions are susceptible to front-running

Most contracts use an init pattern (instead of a constructor) to initialize contract parameters. Unless these are enforced to be atomic with contact deployment via deployment script or factory contracts, they are susceptible to front-running race conditions where an attacker/griefer can front-run (cannot access control because admin roles are not initialized) to initially with their own (malicious) parameters upon detecting (if an event is emitted) which the contract deployer has to redeploy wasting gas and risking other transactions from interacting with the attacker-initialized contract.

Many init functions do not have an explicit event emission which makes monitoring such scenarios harder. All of them have re-init checks; while many are explicit some (those in auction contracts) have implicit reinit checks in initAccessControls() which is better if converted to an explicit check in the main init function itself.
(Details reference credit to: code-423n4/2021-09-sushimiso-findings#64)

#### <ins>Proof Of Concept</ins>


```solidity
function __BaseUpgradeable_init(Storage gogoStorageAddress) internal onlyInitializing {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/BaseUpgradeable.sol#L10

```solidity
function __ERC20Upgradeable_init(
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L53

```solidity
function __ERC4626Upgradeable_init(
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L29



#### <ins>Recommended Mitigation Steps</ins>

Ensure atomic calls to init functions along with deployment via robust deployment scripts or factory contracts. Emit explicit events for initializations of contracts. Enforce prevention of re-initializations via explicit setting/checking of boolean initialized variables in the main init function instead of downstream function checks.



### <a href="#Summary">[LOW&#x2011;5]</a><a name="LOW&#x2011;5"> Vulnerable To Cross-chain Replay Attacks Due To Static DOMAIN_SEPARATOR

The protocol calculates the chainid it should assign during its execution and permanently stores it in an immutable variable. Should Ethereum fork in the feature, the chainid will change however the one used by the permits will not enabling a user to use any new permits on both chains thus breaking the token on the forked chain permanently.

Please consult EIP1344 for more details: https://eips.ethereum.org/EIPS/eip-1344#rationale

#### <ins>Proof Of Concept</ins>

```solidity
62: INITIAL_CHAIN_ID = block.chainid;
63: INITIAL_DOMAIN_SEPARATOR = computeDomainSeparator();
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L62-L63



#### <ins>Recommended Mitigation Steps</ins>

The mitigation action that should be applied is the calculation of the chainid dynamically on each permit invocation. As a gas optimization, the deployment pre-calculated hash for the permits can be stored to an immutable variable and a validation can occur on the permit function that ensure the current chainid is equal to the one of the cached hash and if not, to re-calculate it on the spot.




### <a href="#Summary">[LOW&#x2011;6]</a><a name="LOW&#x2011;6"> The `nonReentrant` modifier should occur before all other modifiers

Currently the `nonReentrant` modifier is not the first to occur, it should occur before all other modifiers.

#### <ins>Proof Of Concept</ins>


```solidity
61: function withdrawAVAX(uint256 amount) external onlyRegisteredNetworkContract nonReentrant {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Vault.sol#L61

```solidity
137: function withdrawToken(
		address withdrawalAddress,
		ERC20 tokenAddress,
		uint256 amount
	) external onlyRegisteredNetworkContract nonReentrant {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Vault.sol#L137



#### <ins>Recommended Mitigation Steps</ins>

Re-sort modifiers so that the `nonReentrant` modifier occurs first.



### <a href="#Summary">[LOW&#x2011;7]</a><a name="LOW&#x2011;7"> Use of `ecrecover` is susceptible to signature malleability

The built-in EVM precompile `ecrecover` is susceptible to signature malleability, which could lead to replay attacks.
References:  https://swcregistry.io/docs/SWC-117,  https://swcregistry.io/docs/SWC-121, and  https://medium.com/cryptronics/signature-replay-vulnerabilities-in-smart-contracts-3b6f7596df57.
While this is not immediately exploitable, this may become a vulnerability if used elsewhere.

#### <ins>Proof Of Concept</ins>


```solidity
118: function permit(
		address owner,
		address spender,
		uint256 value,
		uint256 deadline,
		uint8 v,
		bytes32 r,
		bytes32 s
	) public virtual {
		require(deadline >= block.timestamp, "PERMIT_DEADLINE_EXPIRED");

		// Unchecked because the only math done is incrementing
		// the owner's nonce which cannot realistically overflow.
		unchecked {
			address recoveredAddress = ecrecover(
				keccak256(
					abi.encodePacked(
						"\x19\x01",
						DOMAIN_SEPARATOR(),
						keccak256(
							abi.encode(
								keccak256("Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)"),
								owner,
								spender,
								value,
								nonces[owner]++,
								deadline
							)
						)
					)
				),
				v,
				r,
				s
			);

			require(recoveredAddress != address(0) && recoveredAddress == owner, "INVALID_SIGNER");

			allowance[recoveredAddress][spender] = value;
		}

		emit Approval(owner, spender, value);
	}
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L118-L160



#### Recommended Mitigation Steps
Consider using OpenZeppelin’s ECDSA library (which prevents this malleability) instead of the built-in function.
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/cryptography/ECDSA.sol#L138-L149



### <a href="#Summary">[LOW&#x2011;8]</a><a name="LOW&#x2011;8"> `require()` Should Be Used Instead Of `assert()`

Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction's available gas rather than returning it, as `require()`/`revert()` do. `assert()` should be avoided even past solidity version 0.8.0 as its <a href="https://docs.soliditylang.org/en/v0.8.14/control-structures.html#panic-via-assert-and-error-via-require">documentation</a> states that "The assert function creates an error of type Panic(uint256). ... Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix".

#### <ins>Proof Of Concept</ins>


```solidity
83: assert(msg.sender == address(asset));
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L83



### <a href="#Summary">[LOW&#x2011;9]</a><a name="LOW&#x2011;9"> Missing/Incomplete `nonReentrant` Modifiers

The following functions are missing the `nonReentrant` modifier although some other public/external functions does use reentrancy modifer. Even though I did not find a way to exploit it, it seems like those functions should have the nonReentrant modifier as the other functions have it as well.

#### <ins>Proof Of Concept</ins>

```solidity
function createMinipool(
		address nodeID,
		uint256 duration,
		uint256 delegationFee,
		uint256 avaxAssignmentRequest
	) external payable whenNotPaused {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L196

```solidity
function claimAndInitiateStaking(address nodeID) external {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L324

```solidity
function recordStakingStart(
		address nodeID,
		bytes32 txID,
		uint256 startTime
	) external {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L349

```solidity
function recordStakingEnd(
		address nodeID,
		uint256 endTime,
		uint256 avaxTotalRewardAmt
	) external payable {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L385

```solidity
function recreateMinipool(address nodeID) external whenNotPaused {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L444

```solidity
function depositToken(
		string memory networkContractName,
		ERC20 tokenContract,
		uint256 amount
	) external guardianOrRegisteredContract {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Vault.sol#L108


### <a href="#Summary">[LOW&#x2011;10]</a><a name="LOW&#x2011;10"> `TokenggAVAX` shares can be sent to the zero address

Sending shares to the zero address puts them out of the circulation–such shares won't ever be redeemed for the underlying assets. However, they'll continue accrue rewards, which means other stakers will have (probably only slightly) diminished rewards. This affects all stakers in the protocol.

The `TokenggAVAX.sol` contract inherits from `ERC4626Upgradeable` which inherits from `ERC20Upgradeable`, which allows transfers to the zero address:

```solidity
	function transfer(address to, uint256 amount) public virtual returns (bool) {
		balanceOf[msg.sender] -= amount;

		// Cannot overflow because the sum of all user
		// balances can't exceed the max uint256 value.
		unchecked {
			balanceOf[to] += amount;
		}

		emit Transfer(msg.sender, to, amount);

		return true;
	}
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L78-L90

```solidity
	function transferFrom(
		address from,
		address to,
		uint256 amount
	) public virtual returns (bool) {
		uint256 allowed = allowance[from][msg.sender]; // Saves gas for limited approvals.

		if (allowed != type(uint256).max) allowance[from][msg.sender] = allowed - amount;

		balanceOf[from] -= amount;

		// Cannot overflow because the sum of all user
		// balances can't exceed the max uint256 value.
		unchecked {
			balanceOf[to] += amount;
		}

		emit Transfer(from, to, amount);

		return true;
	}
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L92-L112

Since the ERC20 standard doesn't define a tokens burning mechanism, the community have ended up transferring tokens to the zero address to burn them. `ERC20Upgradeable` implementation also follows the convention by setting the to address of the Transfer event to the zero address when burning tokens:

```solidity
	function _burn(address from, uint256 amount) internal virtual {
		balanceOf[from] -= amount;

		// Cannot underflow because a user's balance
		// will never be larger than the total supply.
		unchecked {
			totalSupply -= amount;
		}

		emit Transfer(from, address(0), amount);
	}
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L195-L205


#### Recommended Mitigation Steps
Consider disallowing transfers to the zero address in the `transfer` and `transferFrom` functions of `ERC20Upgradeable`. While this, of course, won't protect from users "burning" their tokens on other non-controlled addresses (like 0x01, 0x02, etc. or the 0xdead address), this will significantly reduce the impact of lost tokens because instead of sending to the zero address (which is the community accepted way of burning tokens) stakers will be forced to call the burn function.


### <a href="#Summary">[LOW&#x2011;11]</a><a name="LOW&#x2011;11"> Guardian can add itself as a Defender

According to the documentation: 
```
Protocol emergency pause feature, such that any one of a specified group of "Defenders" can pause the entire protocol should that be necessary. The Defenders will be a different set of users from Guardians, and their only permission will be the ability to call the Pause function. Only the Guardian can add and remove Defenders
```

Guardians are in charge of the Defenders and only Defender have the pause permission. However, the current implementation allows the Guardian to have excessive privileges by adding itself as a Defender as it should be avoided.


#### <ins>Proof Of Concept</ins>
```solidity
	function addDefender(address defender) external onlyGuardian {
		defenders[defender] = true;
	}
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L27-L29


#### Recommended Mitigation Steps
Do not allow Guardian to add itself as a Defender.




## Non Critical Issues

### <a href="#Summary">[NC&#x2011;1]</a><a name="NC&#x2011;1"> Critical Changes Should Use Two-step Procedure

The critical procedures should be two step process.

See similar findings in previous Code4rena contests for reference:
https://code4rena.com/reports/2022-06-illuminate/#2-critical-changes-should-use-two-step-procedure

#### <ins>Proof Of Concept</ins>

```solidity
135: function setAddress(bytes32 key, address value) internal {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/BaseAbstract.sol#L135

```solidity
139: function setBool(bytes32 key, bool value) internal {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/BaseAbstract.sol#L139

```solidity
143: function setBytes(bytes32 key, bytes memory value) internal {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/BaseAbstract.sol#L143

```solidity
147: function setBytes32(bytes32 key, bytes32 value) internal {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/BaseAbstract.sol#L147

```solidity
151: function setInt(bytes32 key, int256 value) internal {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/BaseAbstract.sol#L151

```solidity
155: function setUint(bytes32 key, uint256 value) internal {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/BaseAbstract.sol#L155

```solidity
159: function setString(bytes32 key, string memory value) internal {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/BaseAbstract.sol#L159

```solidity
40: function setRewardsCycleTotal(uint256 amount) public onlySpecificRegisteredContract("RewardsPool", msg.sender) {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ClaimNodeOp.sol#L40

```solidity
28: function setOneInch(address addr) external onlyGuardian {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Oracle.sol#L28

```solidity
57: function setGGPPriceInAVAX(uint256 price, uint256 timestamp) external onlyMultisig {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Oracle.sol#L57

```solidity
96: function setTotalGGPCirculatingSupply(uint256 amount) public onlySpecificRegisteredContract("RewardsPool", msg.sender) {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ProtocolDAO.sol#L96

```solidity
107: function setClaimingContractPct(string memory claimingContract, uint256 decimal) public onlyGuardian valueNotGreaterThanOne(decimal) {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ProtocolDAO.sol#L107

```solidity
156: function setExpectedAVAXRewardsRate(uint256 rate) public onlyMultisig valueNotGreaterThanOne(rate) {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ProtocolDAO.sol#L156

```solidity
204: function setRewardsStartTime(address stakerAddr, uint256 time) public onlyRegisteredNetworkContract {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L204

```solidity
248: function setLastRewardsCycleCompleted(address stakerAddr, uint256 cycleNumber) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L248

```solidity
41: function setGuardian(address newAddress) external {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L41

```solidity
104: function setAddress(bytes32 key, address value) external onlyRegisteredNetworkContract {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L104

```solidity
108: function setBool(bytes32 key, bool value) external onlyRegisteredNetworkContract {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L108

```solidity
112: function setBytes(bytes32 key, bytes calldata value) external onlyRegisteredNetworkContract {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L112

```solidity
116: function setBytes32(bytes32 key, bytes32 value) external onlyRegisteredNetworkContract {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L116

```solidity
120: function setInt(bytes32 key, int256 value) external onlyRegisteredNetworkContract {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L120

```solidity
124: function setString(bytes32 key, string calldata value) external onlyRegisteredNetworkContract {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L124

```solidity
128: function setUint(bytes32 key, uint256 value) external onlyRegisteredNetworkContract {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L128



#### <ins>Recommended Mitigation Steps</ins>

Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.



### <a href="#Summary">[NC&#x2011;2]</a><a name="NC&#x2011;2"> Use a more recent version of Solidity

<a href="https://blog.soliditylang.org/2021/04/21/solidity-0.8.4-release-announcement/">0.8.4</a>:
bytes.concat() instead of abi.encodePacked(<bytes>,<bytes>)

<a href="https://blog.soliditylang.org/2022/02/16/solidity-0.8.12-release-announcement/">0.8.12</a>: 
string.concat() instead of abi.encodePacked(<str>,<str>)

<a href="https://blog.soliditylang.org/2022/03/16/solidity-0.8.13-release-announcement/">0.8.13</a>: 
Ability to use using for with a list of free functions

<a href="https://blog.soliditylang.org/2022/05/18/solidity-0.8.14-release-announcement/">0.8.14</a>:

ABI Encoder: When ABI-encoding values from calldata that contain nested arrays, correctly validate the nested array length against calldatasize() in all cases.
Override Checker: Allow changing data location for parameters only when overriding external functions.

<a href="https://blog.soliditylang.org/2022/06/15/solidity-0.8.15-release-announcement/">0.8.15</a>:

Code Generation: Avoid writing dirty bytes to storage when copying bytes arrays.
Yul Optimizer: Keep all memory side-effects of inline assembly blocks.

<a href="https://blog.soliditylang.org/2022/08/08/solidity-0.8.16-release-announcement/">0.8.16</a>:

Code Generation: Fix data corruption that affected ABI-encoding of calldata values represented by tuples: structs at any nesting level; argument lists of external functions, events and errors; return value lists of external functions. The 32 leading bytes of the first dynamically-encoded value in the tuple would get zeroed when the last component contained a statically-encoded array.

<a href="https://blog.soliditylang.org/2022/09/08/solidity-0.8.17-release-announcement/">0.8.17</a>:

Yul Optimizer: Prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call.

#### <ins>Proof Of Concept</ins>


```solidity
pragma solidity >=0.8.0;
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L2

```solidity
pragma solidity >=0.8.0;
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L2



#### <ins>Recommended Mitigation Steps</ins>

Consider updating to a more recent solidity version.



### <a href="#Summary">[NC&#x2011;3]</a><a name="NC&#x2011;3"> Redundant Cast

The type of the variable is the same as the type to which the variable is being cast

#### <ins>Proof Of Concept</ins>


```solidity
11: Storage(gogoStorageAddress)
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/BaseUpgradeable.sol#L11

```solidity
157: ERC20(tokenAddress)
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Vault.sol#L157








### <a href="#Summary">[NC&#x2011;4]</a><a name="NC&#x2011;4"> Public Functions Not Called By The Contract Should Be Declared External Instead

Contracts are allowed to override their parents’ functions and change the visibility from external to public.

#### <ins>Proof Of Concept</ins>


```solidity
function setClaimingContractPct(string memory claimingContract, uint256 decimal) public onlyGuardian valueNotGreaterThanOne(decimal) {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ProtocolDAO.sol#L107

```solidity
function setExpectedAVAXRewardsRate(uint256 rate) public onlyMultisig valueNotGreaterThanOne(rate) {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ProtocolDAO.sol#L156

```solidity
function syncRewards() public {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L88

```solidity
function permit(
		address owner,
		address spender,
		uint256 value,
		uint256 deadline,
		uint8 v,
		bytes32 r,
		bytes32 s
	) public virtual {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L118

```solidity
function deposit(uint256 assets, address receiver) public virtual returns (uint256 shares) {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L42






### <a href="#Summary">[NC&#x2011;5]</a><a name="NC&#x2011;5"> Missing event for critical parameter change

When changing state variables events are not emitted. Emitting events allows monitoring activities with off-chain monitoring tools.

#### <ins>Proof Of Concept</ins>


```solidity
135: function setAddress(bytes32 key, address value) internal {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/BaseAbstract.sol#L135

```solidity
139: function setBool(bytes32 key, bool value) internal {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/BaseAbstract.sol#L139

```solidity
143: function setBytes(bytes32 key, bytes memory value) internal {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/BaseAbstract.sol#L143

```solidity
147: function setBytes32(bytes32 key, bytes32 value) internal {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/BaseAbstract.sol#L147

```solidity
151: function setInt(bytes32 key, int256 value) internal {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/BaseAbstract.sol#L151

```solidity
155: function setUint(bytes32 key, uint256 value) internal {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/BaseAbstract.sol#L155

```solidity
159: function setString(bytes32 key, string memory value) internal {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/BaseAbstract.sol#L159

```solidity
40: function setRewardsCycleTotal(uint256 amount) public onlySpecificRegisteredContract("RewardsPool", msg.sender) {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ClaimNodeOp.sol#L40

```solidity
28: function setOneInch(address addr) external onlyGuardian {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Oracle.sol#L28

```solidity
96: function setTotalGGPCirculatingSupply(uint256 amount) public onlySpecificRegisteredContract("RewardsPool", msg.sender) {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ProtocolDAO.sol#L96

```solidity
107: function setClaimingContractPct(string memory claimingContract, uint256 decimal) public onlyGuardian valueNotGreaterThanOne(decimal) {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ProtocolDAO.sol#L107

```solidity
156: function setExpectedAVAXRewardsRate(uint256 rate) public onlyMultisig valueNotGreaterThanOne(rate) {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ProtocolDAO.sol#L156

```solidity
204: function setRewardsStartTime(address stakerAddr, uint256 time) public onlyRegisteredNetworkContract {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L204

```solidity
248: function setLastRewardsCycleCompleted(address stakerAddr, uint256 cycleNumber) public onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L248

```solidity
41: function setGuardian(address newAddress) external {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L41

```solidity
104: function setAddress(bytes32 key, address value) external onlyRegisteredNetworkContract {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L104

```solidity
108: function setBool(bytes32 key, bool value) external onlyRegisteredNetworkContract {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L108

```solidity
112: function setBytes(bytes32 key, bytes calldata value) external onlyRegisteredNetworkContract {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L112

```solidity
116: function setBytes32(bytes32 key, bytes32 value) external onlyRegisteredNetworkContract {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L116

```solidity
120: function setInt(bytes32 key, int256 value) external onlyRegisteredNetworkContract {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L120

```solidity
124: function setString(bytes32 key, string calldata value) external onlyRegisteredNetworkContract {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L124

```solidity
128: function setUint(bytes32 key, uint256 value) external onlyRegisteredNetworkContract {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L128





### <a href="#Summary">[NC&#x2011;6]</a><a name="NC&#x2011;6"> Implementation contract may not be initialized

OpenZeppelin recommends that the initializer modifier be applied to constructors. 
Per OZs Post implementation contract should be initialized to avoid potential griefs or exploits.
https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680/5

#### <ins>Proof Of Concept</ins>


```solidity
9: constructor(Storage _gogoStorageAddress)
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Base.sol#L9

```solidity
29: constructor(Storage storageAddress, ERC20 ggp_) Base(storageAddress)
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ClaimNodeOp.sol#L29

```solidity
15: constructor(Storage storageAddress) Base(storageAddress)
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ClaimProtocolDAO.sol#L15

```solidity
177: constructor(
		Storage storageAddress,
		ERC20 ggp_,
		TokenggAVAX ggAVAX_
	) Base(storageAddress)
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L177

```solidity
29: constructor(Storage storageAddress) Base(storageAddress)
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MultisigManager.sol#L29

```solidity
22: constructor(Storage storageAddress) Base(storageAddress)
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Ocyticus.sol#L22

```solidity
23: constructor(Storage storageAddress) Base(storageAddress)
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Oracle.sol#L23

```solidity
19: constructor(Storage storageAddress) Base(storageAddress)
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ProtocolDAO.sol#L19

```solidity
30: constructor(Storage storageAddress) Base(storageAddress)
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/RewardsPool.sol#L30

```solidity
60: constructor(Storage storageAddress, ERC20 ggp_) Base(storageAddress)
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L60

```solidity
35: constructor()
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Storage.sol#L35

```solidity
41: constructor(Storage storageAddress) Base(storageAddress)
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Vault.sol#L41

```solidity
66: constructor()
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L66

```solidity
12: constructor() ERC20("GoGoPool Protocol", "GGP", 18)
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenGGP.sol#L12





### <a href="#Summary">[NC&#x2011;7]</a><a name="NC&#x2011;7"> Use of Block.Timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.
References: SWC ID: 116

#### <ins>Proof Of Concept</ins>


```solidity
279: if (block.timestamp - staking.getRewardsStartTime(msg.sender) < dao.getMinipoolCancelMoratoriumSeconds()) {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L279

```solidity
356: if (startTime > block.timestamp) {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L356

```solidity
394: if (endTime <= startTime || endTime > block.timestamp) {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L394

```solidity
59: if (timestamp < lastTimestamp || timestamp > block.timestamp) {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Oracle.sol#L59

```solidity
206: if (time > block.timestamp) {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L206

```solidity
120: if (block.timestamp >= rewardsCycleEnd_) {
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L120

```solidity
127: require(deadline >= block.timestamp, "PERMIT_DEADLINE_EXPIRED");
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L127



#### <ins>Recommended Mitigation Steps</ins>
Block timestamps should not be used for entropy or generating random numbers—i.e., they should not be the deciding factor (either directly or through some derivation) for winning a game or changing an important state.

Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, or enforcing expiry dates. It is sometimes recommended to use block.number and an average block time to estimate times; with a 10 second block time, 1 week equates to approximately, 60480 blocks. Thus, specifying a block number at which to change a contract state can be more secure, as miners are unable to easily manipulate the block number.



### <a href="#Summary">[NC&#x2011;8]</a><a name="NC&#x2011;8"> Non-usage of specific imports

The current form of relative path import is not recommended for use because it can unpredictably pollute the namespace.
Instead, the Solidity docs recommend specifying imported symbols explicitly.
https://docs.soliditylang.org/en/v0.8.15/layout-of-source-files.html#importing-other-source-files

A good example:
```solidity
import {OwnableUpgradeable} from "openzeppelin-contracts-upgradeable/contracts/access/OwnableUpgradeable.sol";
import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
import {SafeCastLib} from "solmate/utils/SafeCastLib.sol";
import {ERC20} from "solmate/tokens/ERC20.sol";
import {IProducer} from "src/interfaces/IProducer.sol";
import {GlobalState, UserState} from "src/Common.sol";
```

#### <ins>Proof Of Concept</ins>


```solidity
4: import "./BaseAbstract.sol";

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Base.sol#L4

```solidity
4: import "./BaseAbstract.sol";

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/BaseUpgradeable.sol#L4

```solidity
4: import "./Base.sol";

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ClaimNodeOp.sol#L4

```solidity
4: import "./Base.sol";

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ClaimProtocolDAO.sol#L4

```solidity
4: import "./Base.sol";

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L4

```solidity
4: import "./Base.sol";

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MultisigManager.sol#L4

```solidity
4: import "./Base.sol";

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Ocyticus.sol#L4

```solidity
4: import "./Base.sol";

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Oracle.sol#L4

```solidity
4: import "./Base.sol";

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ProtocolDAO.sol#L4

```solidity
4: import "./Base.sol";

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/RewardsPool.sol#L4

```solidity
4: import "./Base.sol";

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L4

```solidity
4: import "./Base.sol";

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Vault.sol#L4

```solidity
6: import "../BaseUpgradeable.sol";

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/tokens/TokenggAVAX.sol#L6




#### <ins>Recommended Mitigation Steps</ins>

Use specific imports syntax per solidity docs recommendation.



### <a href="#Summary">[NC&#x2011;9]</a><a name="NC&#x2011;9"> Lines are too long

Usually lines in source code are limited to 80 characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length
Reference: https://docs.soliditylang.org/en/v0.8.10/style-guide.html#maximum-line-length

#### <ins>Proof Of Concept</ins>

```solidity
41: // 5% annual calculated on a daily interval - Calculate in js example: let dailyInflation = web3.utils.toBN((1 + 0.05) ** (1 / (365)) * 1e18);
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/ProtocolDAO.sol#L41





### <a href="#Summary">[NC&#x2011;10]</a><a name="NC&#x2011;10"> Use `bytes.concat()`

Solidity version 0.8.4 introduces `bytes.concat()` (vs `abi.encodePacked(<bytes>,<bytes>)`)

#### <ins>Proof Of Concept</ins>

Examples of use found in:
```solidity
25: if (getBool(keccak256(abi.encodePacked("contract.exists", msg.sender)
```

```solidity
116: address owner = getAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".owner")
```

Many other uses can be found throughout the project's contracts such as:
https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/BaseAbstract.sol
https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol


#### <ins>Recommended Mitigation Steps</ins>

Use `bytes.concat()` and upgrade to at least Solidity version 0.8.4 if required. 



### <a href="#Summary">[NC&#x2011;11]</a><a name="NC&#x2011;11"> Open TODOs

An open TODO is present. It is recommended to avoid open TODOs as they may indicate programming errors that still need to be fixed.

#### <ins>Proof Of Concept</ins>


```solidity
6: // TODO remove this when dev is complete
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/BaseAbstract.sol#L6

```solidity
412: // TODO Revisit this logic if we ever allow unequal matched funds
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L412

```solidity
203: // TODO cant use onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) since we also call from increaseMinipoolCount. Wat do?
```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L203






### <a href="#Summary">[NC&#x2011;12]</a><a name="NC&#x2011;12"> No need to initialize uints to zero

There is no need to initialize `uint` variables to zero as their default value is `0`

#### <ins>Proof Of Concept</ins>

```solidity
618: uint256 total = 0;

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/MinipoolManager.sol#L618

```solidity
141: uint256 contractRewardsTotal = 0;

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/RewardsPool.sol#L141

```solidity
427: uint256 total = 0;

```

https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts/contract/Staking.sol#L427






