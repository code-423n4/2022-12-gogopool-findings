# Gas Report

The total gas saved presented here is the change in median gas usage obtained of the methods after running `forge test --gas-report`.

| Issue | Instances | Total Gas Saved |
| -------- | -------- | -------- |
| **[G-01]** Remove redundant `contractAddress` argument from modifiers | 2     | 190     |
| **[G-02]** Remove redundant zero address check from `onlyMultisig` modifier | 2 | 5819
| **[G-03]** Cache result of keccak256(...) | 1     | 173     |
| **[G-04]** Divide revert checks into separate `if` statements | 1     | 8     |



### [G-01] Remove redundant `contractAddress` argument from modifiers 
`BaseAbstract.onlySpecificRegisteredContract` and `BaseAbstract.guardianOrSpecificRegisteredContract` modifiers are only used to do checks on `msg.sender`. `contractAddress` parameter can be thus removed from the function signature, and replaced by `msg.sender` in the body.
**Gas saved:** 190
**Filename**: *contracts/contract/BaseAbstract.sol*
```solidity=32
modifier onlySpecificRegisteredContract(string memory contractName, address contractAddress) {
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L32
```solidity=51
	modifier guardianOrSpecificRegisteredContract(string memory contractName, address contractAddress) {
```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L51



### [G-02] Remove redundant zero address check from `onlyMultisig` modifier
A multisig is only enabled through `MultisigManager.enableMultisig`, and zero address or a non-existing address cannot be enabled through it. So, only checking if the multisig index is enabled, can be checked.
**Gas saved:** 5819
**Filename:** *contracts/contract/BaseAbstract.sol* 
```solidity=69
	/// @dev Verify caller is a valid multisig
	modifier onlyMultisig() {
		int256 multisigIndex = int256(getUint(keccak256(abi.encodePacked("multisig.index", msg.sender)))) - 1;
		address addr = getAddress(keccak256(abi.encodePacked("multisig.item", multisigIndex, ".address")));
		bool enabled = (addr != address(0)) && getBool(keccak256(abi.encodePacked("multisig.item", multisigIndex, ".enabled")));
		if (enabled == false) {
			revert MustBeMultisig();
		}
		_;
	}

```
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L69

The same can also be done in `MultisigManager.getMultisig`.
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L109

### [G-03] Cache result of keccak256(...)
In `MinipoolManager.recordStakingStart`, initial start time key is computed two times, and can be cached.
**Gas saved:** 173
**Filename:** *contracts/contract/MinipoolManager.sol* 
```solidity=364
		// If this is the first of many cycles, set the initialStartTime
		uint256 initialStartTime = getUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".initialStartTime")));
		if (initialStartTime == 0) {
			setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".initialStartTime")), startTime);
		}

```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L364

### [G-04] Divide revert checks into separate `if` statements
In `MinipoolManager.createMinipool`, the `msg.value` check can be rearranged to save 8 gas at runtime. 
**Gas saved:** 8
**Filename:** *contracts/contract/createMinipool.sol* 
```solidity=207
		if (
			// Current rule is matched funds must be 1:1 nodeOp:LiqStaker
			msg.value != avaxAssignmentRequest ||
			avaxAssignmentRequest > dao.getMinipoolMaxAVAXAssignment() ||
			avaxAssignmentRequest < dao.getMinipoolMinAVAXAssignment()
		) {
			revert InvalidAVAXAssignmentRequest();
		}

```
Recommended arrangement:
```solidity=207
        // Current rule is matched funds must be 1:1 nodeOp:LiqStaker
        if (msg.value != avaxAssignmentRequest) {
            revert InvalidAVAXAssignmentRequest();
        }

        if (
            avaxAssignmentRequest > dao.getMinipoolMaxAVAXAssignment()
                || avaxAssignmentRequest < dao.getMinipoolMinAVAXAssignment()
        ) {
            revert InvalidAVAXAssignmentRequest();
        }
```

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L207
