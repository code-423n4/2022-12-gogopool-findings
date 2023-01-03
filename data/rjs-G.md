# Introduction

The codebase was well structured and implemented many common gas optimizations.

⚠ It seems that some of the tests are non-deterministic, which makes it quite challenging to accurately evaluate the effect of changes as re-running even the baseline results in swings of 20% in gas costs for deployments and transactions on some (not all) of the tests. 

# Summary

| ID  | Finding | Instances |
| --- | ------- | --------- |
| G-01    |         |           |

# Gas Optimization Findings

## \[G-01\] Don't compare boolean expressions to boolean literal

### Instance 1

- Before

```solidity
contracts/contract/BaseAbstract.sol
25: 		if (getBool(keccak256(abi.encodePacked("contract.exists", msg.sender))) == false) {
```

- After

`forge test` overall gas change: -4,592

```solidity
contracts/contract/BaseAbstract.sol
25: 		if (!getBool(keccak256(abi.encodePacked("contract.exists", msg.sender)))) {
```

### Instance 2

- Before

```solidity
contracts/contract/BaseAbstract.sol
74: 		if (enabled == false) {
```

- After

`forge test` overall gas change: -444

```solidity
contracts/contract/BaseAbstract.sol
74: 		if (!enabled) {
```
---

### Instance 3

- Before

```solidity
contracts/contract/Storage.sol
29: 		if (booleanStorage[keccak256(abi.encodePacked("contract.exists", msg.sender))] == false && msg.sender != guardian) {
```

- After

`forge test` overall gas change: -2 _(very little test coverage)_

```solidity
contracts/contract/Storage.sol
29: 		if (!booleanStorage[keccak256(abi.encodePacked("contract.exists", msg.sender))] && msg.sender != guardian) {

```
---

## \[G-02\] Used named returns instead of temporary return variables

### Instance 1

Before:

```solidity
contracts/contract/BaseAbstract.sol
099: 	function getContractName(address contractAddress) internal view returns (string memory) {
100: 		string memory contractName = getString(keccak256(abi.encodePacked("contract.name", contractAddress)));
101: 		if (bytes(contractName).length == 0) {
102: 			revert ContractNotFound();
103: 		}
104: 		return contractName;
105: 	}
```

After:

`forge test` overall gas change: -13,762

```solidity
contracts/contract/BaseAbstract.sol
099: 	function getContractName(address contractAddress) internal view returns (string memory contractName) {
100: 		contractName = getString(keccak256(abi.encodePacked("contract.name", contractAddress)));
101: 		if (bytes(contractName).length == 0) {
102: 			revert ContractNotFound();
103: 		}
104: 	}
```

### Instance 2

Before:

```solidity
contracts/contract/BaseAbstract.sol
90: 	function getContractAddress(string memory contractName) internal view returns (address) {
91: 		address contractAddress = getAddress(keccak256(abi.encodePacked("contract.address", contractName)));
92: 		if (contractAddress == address(0x0)) {
93: 			revert ContractNotFound();
94: 		}
95: 		return contractAddress;
96: 	}
```

After:

`forge test` overall gas change: -8,444

```solidity
contracts/contract/
BaseAbstract.sol
90: 	function getContractAddress(string memory contractName) internal view returns (address contractAddress) {
91: 		contractAddress = getAddress(keccak256(abi.encodePacked("contract.address", contractName)));
92: 		if (contractAddress == address(0x0)) {
93: 			revert ContractNotFound();
94: 		}
95: 	}
```solidity

### Instance 3

Before:

```solidity
contracts/contract/Staking.sol
387: 	function requireValidStaker(address stakerAddr) public view returns (int256) {
388: 		int256 index = getIndexOf(stakerAddr);
389: 		if (index != -1) {
390: 			return index;
391: 		} else {
392: 			revert StakerNotFound();
393: 		}
394: 	}
```

After:

`forge test` overall gas change: -8,754

```solidity
contracts/contract/Staking.sol
387: 	function requireValidStaker(address stakerAddr) public view returns (int256 index) {
388: 		index = getIndexOf(stakerAddr);
389: 		if (index == -1) {			
390: 			revert StakerNotFound();
391: 		}
392: 	}
```

### Instance 4

`forge test` overall gas change: -118

```solidity
contracts/contract/MinipoolManager.sol
115: 	function onlyOwner(int256 minipoolIndex) private view returns (address owner) {
```

### Instance 5

`forge test` overall gas change: -727

```solidity
contracts/contract/MinipoolManager.sol
127: 	function onlyValidMultisig(address nodeID) private view returns (int256 minipoolIndex) {
```

### Instance 6

`forge test` overall gas change: -823

```solidity
contracts/contract/MinipoolManager.sol
140: 	function requireValidMinipool(address nodeID) private view returns (int256 minipoolIndex) {
```

### Instance 7

`forge test` overall gas change: -1,042

```solidity
contracts/contract/MultisigManager.sol
80: 	function requireNextActiveMultisig() external view returns (address addr) {
```

### Instance 8

`forge test` overall gas change: -305

```solidity
contracts/contract/RewardsPool.sol
133: 	function getClaimingContractDistribution(string memory claimingContract) public view returns (uint256 contractRewardsTotal) {
```

---

## \[G-03\] `x = x + y` is sometimes more efficient than `x += y`

Note that this is only true when not `unchecked`, and also depends on call patterns. Every change should be carefully evaluated.

### Instance 1

Before:

```solidity
contracts/contract/tokens/TokenggAVAX.sol
252: 		totalReleasedAssets += amount;
```

After:

`forge test` overall gas change: -2,296

```solidity
contracts/contract/tokens/TokenggAVAX.sol
252: 		totalReleasedAssets = totalReleasedAssets + amount;
```

### Instance 2

Before:

```solidity
contracts/contract/tokens/TokenggAVAX.sol
160: 		stakingTotalAssets += assets;
```

After:

`forge test` overall gas change: -589

```solidity
contracts/contract/tokens/TokenggAVAX.sol
160: 		stakingTotalAssets = stakingTotalAssets + assets;
```

---

## \[G-04\] `x = x - y` is more efficient than `x -= y`

### Instance 1

Before:

```solidity
contracts/contract/tokens/TokenggAVAX.sol
149: 		stakingTotalAssets -= baseAmt;
```

After:

`forge test` overall gas change: -384

```solidity
contracts/contract/tokens/TokenggAVAX.sol
149: 		stakingTotalAssets = stakingTotalAssets - baseAmt;
```

### Instance 2

Before:

```solidity
contracts/contract/tokens/TokenggAVAX.sol
245: 		totalReleasedAssets -= amount;
```

After:

`forge test` overall gas change: -305

```solidity
contracts/contract/tokens/TokenggAVAX.sol
245: 		totalReleasedAssets = totalReleasedAssets- amount;
```

### Instance 3

Before:

```solidity
contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol
79: 		balanceOf[msg.sender] -= amount;
```

After:

`forge test` overall gas change: -159 (with some smaller increases for some tests)

```solidity
contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol
79: 		balanceOf[msg.sender] = balanceOf[msg.sender] - amount;
```

---

## \[G-05\] Use `unchecked` if possible for increment/decrement

### Instance 1

In the following code, the loop count is bounded by `[offset, max)` and therefore `total`, initialized to `0`, will never overflow on line `623`.

Before:

```solidity
contracts/contract/MinipoolManager.sol
618: 		uint256 total = 0;
619: 		for (uint256 i = offset; i < max; i++) {
620: 			Minipool memory mp = getMinipool(int256(i));
621: 			if (mp.status == uint256(status)) {
622: 				minipools[total] = mp;
623: 				total++;
624: 			}
625: 		}
```

After:

`forge test` overall gas change: -870

```solidity
contracts/contract/MinipoolManager.sol
618: 		uint256 total = 0;
619: 		for (uint256 i = offset; i < max; i++) {
620: 			Minipool memory mp = getMinipool(int256(i));
621: 			if (mp.status == uint256(status)) {
622: 				minipools[total] = mp;
623: 				unchecked {
624: 					total++;
625: 				}
626: 			}
627: 		}
```

### Instance 2

Before:

```solidity
contracts/contract/RewardsPool.sol
215: 		for (uint256 i = 0; i < count; i++) {
216: 			(address addr, bool enabled) = mm.getMultisig(i);
217: 			if (enabled) {
218: 				enabledMultisigs[enabledCount] = addr;
219: 				enabledCount++;
220: 			}
221: 		}
```

After:

`forge test` overall gas change: -1,239

```solidity
contracts/contract/RewardsPool.sol
215: 		for (uint256 i = 0; i < count; i++) {
216: 			(address addr, bool enabled) = mm.getMultisig(i);
217: 			if (enabled) {
218: 				enabledMultisigs[enabledCount] = addr;
219: 				unchecked {
220: 				  enabledCount++;
221: 				}
222: 			}
223: 		}
```

### Instance 3

Before:

```solidity
contracts/contract/Staking.sol
427: 		uint256 total = 0;
428: 		for (uint256 i = offset; i < max; i++) {
429: 			Staker memory s = getStaker(int256(i));
430: 			stakers[total] = s;
431: 			total++;
432: 		}
```

After:

`forge test` overall gas change: -1,089

```solidity
contracts/contract/RewardsPool.sol
215: 		for (uint256 i = 0; i < count; i++) {
216: 			(address addr, bool enabled) = mm.getMultisig(i);
217: 			if (enabled) {
218: 				enabledMultisigs[enabledCount] = addr;
219: 				unchecked {
220: 				  enabledCount++;
221: 				}
222: 			}
223: 		}
```


--

# NOT RECOMMENDED Gas Optimization Findings

I have included this section as I have noticed a trend for gas reports to include findings that are not, in fact, quantifiable or valid in all instances.

## \[XG-01\] NOT RECOMMENDED: Use `external` instead of `public` where possible

For example, `ProtocolDAO` has `pauseContract()` which is only ever used externally. There are other such instances in the codebase.

Changing the function from `public` to `external` yields no benefit, because the `external` vs. `public` optimization was a historical `solc` issue that was actually related to `memory` vs `calldata` parameters.

---

## \[XG-02\] NOT RECOMMENDED: `x = x - y` is NOT ALWAYS more efficient than `x -= y`

### Instance 1

Before:

```solidity
contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol
196: 		balanceOf[from] -= amount;
```

After:

`forge test` overall gas change: +2,768

```solidity
contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol
196: 		balanceOf[from] = balanceOf[from] - amount;
```