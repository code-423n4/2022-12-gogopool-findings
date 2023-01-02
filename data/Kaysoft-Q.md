#### [N-1] EMPTY BLOCKS SHOULD BE REMOVED OR EMIT SOMETHING.

#### Description
Code contains empty blocks

```
4 results - 3 files

contracts/contract/MinipoolManager.sol
106: function receiveWithdrawalAVAX() external payable {}

contracts/contract/TokenggAVAX.sol
225: function _authorizeUpgrade(address newImplementation) internal override onlyGuardian {}

contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol
176: function beforeWithdraw(uint256 assets, uint256 shares) internal virtual {}
178: function afterDeposit(uint256 assets, uint256 shares) internal virtual {}


```

#### Recommended Mitigation Steps
The code should be refactored so that they no longer exist or the block should do something meaningful like emitting events.


#### [NC-2] Solidity compiler optimization can be problematic

```jsx
const config: HardhatUserConfig = {
	solidity: {
		version: "0.8.17",
		settings: {
			optimizer: {
				enabled: true,
				runs: 800,
			},
		},
	},
```

### Description

Protocol has enabled optional compiler optimizations in Solidity. There have been several optimization bugs with security implications. Moreover, optimizations are actively being developed. Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them.

Therefore, it is unclear how well they are being tested and exercised. High-severity security issues due to optimization bugs have occurred in the past. A high-severity bug in the emscripten-generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG.

Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. More recently, another bug due to the incorrect caching of keccak256 was reported. A compiler audit of Solidity from November 2018 concluded that the optional optimizations may not be safe. It is likely that there are latent bugs related to optimization and that new bugs will be introduced due to future optimizations.

Exploit Scenario A latent or future bug in Solidity compiler optimizations—or in the Emscripten transpilation to solc-js—causes a security vulnerability in the contracts.

### Recommended Mitigation Steps

Short term, measure the gas savings from optimizations and carefully weigh them against the possibility of an optimization-related bug.Long term, monitor the development and adoption of Solidity compiler optimizations to access their maturity.


#### [NC-3] NatSpec comment is missing for some functions.

Files

```jsx

contracts/contract/Storage.sol
104:  function setBytes(bytes32 key, bytes calldata value) external onlyRegisteredNetworkContract {
		bytesStorage[key] = value;
	}

116: function setBytes32(bytes32 key, bytes32 value) external onlyRegisteredNetworkContract {
		bytes32Storage[key] = value;
	}

contracts/contract/ProtocolDAO.sol
23:  function initialize() external onlyGuardian {

contracts/contract/Vault.sol
204: function addAllowedToken(address tokenAddress) external onlyGuardian {
		allowedTokens[tokenAddress] = true;
	}
```

### [N-4] INCLUDE RETURN PARAMETERS IN NATSPEC COMMENTS

Files: Most of the view functions on contracts/contract/ProtocolDAO.sol and other smart contract files.

See: https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

If Return parameters are declared, you must prefix them with ”/// @return”.

Some code analysis programs do analysis by reading NatSpec details, if they can’t see the “@return” tag, they do incomplete analysis.



Recommended Mitigation Steps
Include return parameters in NatSpec comments

### [NC-01] Remove the equality to the boolean constant.

Boolean constants can be used directly and do not need to be compare to `true`
 or `false`

See <[https://github.com/crytic/slither/wiki/Detector-Documentation#boolean-equality](https://github.com/crytic/slither/wiki/Detector-Documentation#boolean-equality)>

Files:

- [BaseAbstract.sol#L25](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseAbstract.sol#L25)
-  [BaseAbstract.sol#L74](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseAbstract.sol#L74)
- [Storage.sol#L29](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L29)


### [N-5] Separate the `amt > 0` into its own modifier or an if/revert check

When the amt parameter is 0, the execution will revert with the wrong error message `ContractPaused()` even when contract is not paused.

```
contracts/contract/Tokens/TokenggAVAX.sol
59:  modifier whenTokenNotPaused(uint256 amt) {
		if (amt > 0 && getBool(keccak256(abi.encodePacked("contract.paused", "TokenggAVAX")))) {
			revert ContractPaused();
		}
		_;
	}

```

#### [N-5] THERE ARE YET TO BE COMPLETED TODOS ON THE PROJECT
- Complete the TODOs on the project because TODOs indicate unfinished work which may affect the overall project

```
constracts/contract/BaseAbstract.sol
6: // TODO remove this when dev is complete

constracts/contract/MinipoolManger.sol
412: // TODO Revisit this logic if we ever allow unequal matched funds
		uint256 avaxHalfRewards = avaxTotalRewardAmt / 2;

constracts/contract/Staking.sol
203: // TODO cant use onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) since we also call from increaseMinipoolCount. Wat do?
	function setRewardsStartTime(address stakerAddr, uint256 time) public onlyRegisteredNetworkContract {

```
#### Recommended Mitigation Steps
Complete the TODOs on the project because TODOs indicate unfinished work which may affect the overall project.

### [N-6] Remove commented code

```
constracts/contract/BaseAbstract.sol
7: // import {console} from "forge-std/console.sol";
8: // import {format} from "sol-utils/format.sol";


```

### [NC-03] Emit events for useful state changes

Use event to monitor contract activity. Events can also be listened to on the user interface and used to trigger some functions.

- [Oracle.sol#L29](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Oracle.sol#L29)
- [Ocyticus.sol#L28](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Ocyticus.sol#L28)


### [L-1] USE NAMED IMPORT 
- [Staking.sol#L4](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L4)
- [Vault.sol#L4](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L4)
- [Base.sol#L4](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Base.sol#L4)
- [BaseUpgradeable.sol#L4](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseUpgradeable.sol#L4)
- [ClaimNodeOp.sol#L4](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimNodeOp.sol#L4)
- [ClaimProtocolDAO.sol#L4](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimProtocolDAO.sol#L4)
- [MinipoolManager.sol#L4](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L4)
- [MultisigManager.sol#L4](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MultisigManager.sol#L4)
- [Ocyticus.sol#L4](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Ocyticus.sol#L4)
- [Oracle.sol#L4](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Oracle.sol#L4)
- [ProtocolDAO.sol#L4](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ProtocolDAO.sol#L4)
- [RewardsPool.sol#L4](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L4)
- [TokenggAVAX.sol#L6](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L6)


### [L-02] Remove unused input/unnamed parameter

```
contracts/contract/Tokens/TokenggAVAX.sol

241: function beforeWithdraw(
		uint256 amount,
		uint256 /* shares */
	) internal override {
		totalReleasedAssets -= amount;
	}

248: 	function afterDeposit(
		uint256 amount,
		uint256 /* shares */
	) internal override {
		totalReleasedAssets += amount;
	}

```


#### Recommended Mitigation steps
- Remove unused/unammed parameters on the `beforeWithdraw` and `afterDeposit` functions.


