# 1. Uncheck arithmetics operations that can’t underflow/overflow

Solidity version 0.8+ has an implicit overflow and underflow check on unsigned integers.

When an overflow or an underflow isn’t possible, some gas can be saved using an unchecked block.

For example, the code at [Staking.sol#L428-L432](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L428-L432) can be refactored to as:

```solidity
File: contract/Staking.sol
for (uint256 i = offset; i < max;) {
        Staker memory s = getStaker(int256(i));
        stakers[total] = s;
        total++;
        unchecked {
            ++i;
        }
}
```

There is 1 instance of this issue:

- [Staking.sol#L428-L432](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L428-L432)

# 2. Use named return values as local variables

Using named return values as temporary local variables can help to save on gas costs.

For example, the code at [Staking.sol#L217-L220](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L217-L220) could be refactored as follows:

```solidity
File: contract/Staking.sol
function getGGPRewards(address stakerAddr) public view returns (uint256 ggpRewards) {
  int256 stakerIndex = getIndexOf(stakerAddr);
  ggpRewards = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpRewards")));
}
```

Affected line of code:

- [Staking.sol#L217-L220](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L217-L220)

# 3. Use `||` Operator to Save on Gas Costs

Using the `||` operator typically costs less gas than using the equivalent `&&` operator.

This is because `(x && y)` is equivalent to `(!(!x || !y))`, and the `||` operator typically requires less gas to execute than the `&&` operator.

Even with the 10k Optimizer enabled, it is generally more efficient to use `||` for OR conditions and `&&` for AND conditions.

For example, the code at [BaseAbstract.sol#L73](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L73) could be refactored as follows:

```solidity
Line 73:    bool enabled = (!(addr == address(0)) || !getBool(keccak256(abi.encodePacked("multisig.item", multisigIndex, ".enabled"))));
```

# 4. Save gas with `payable` functions

Marking functions as `payable` will be cheaper (by ~20 gas) than using non-`payable` functions because the Solidity compiler inserts a check into non-`payable` functions that requires `msg.value` to be zero.

For instance the code at [ProtocolDAO.sol#L107](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L107) can be refactored as:

```solidity
Line 107:    function setClaimingContractPct(string memory claimingContract, uint256 decimal) public payable onlyGuardian valueNotGreaterThanOne(decimal) {
```

> **Note**: Although this optimization can save gas, it is important to be aware of the security considerations involving Ether held in contracts that it introduces.
  More information on this topic can be found in the [Solidity Compiler Discussion](https://github.com/ethereum/solidity/issues/12539).

Affected line of code:

- [ProtocolDAO.sol#L107](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L107)

# 5. Using `Storage` Instead of `Memory` for Structs/Arrays Saves Gas

When fetching data from a `storage` location, assigning the data to a `memory` variable causes all fields of the struct/array to be read from `storage`, which incurs a Gcoldsload (2100 gas) for each field of the struct/array.
If the fields are read from the new `memory` variable, they incur an additional `MLOAD` rather than a cheap stack read. Instead of declaring the variable with the `memory` keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incurring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires `memory`, or if the array/struct is being read from another `memory` array/struct.

As an example, The code at [Staking.sol#L429](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L429) can be refactored as follows:

```solidity
Line 429:    Staker storage s = getStaker(int256(i));
```

Affected line of code:

- [Staking.sol#L429](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L429)

# 6. `>`/`<` is cheaper than `>=`/`<=`

Using the `>`/`<` operator requires fewer opcodes and therefore costs less gas than using the `>=`/`<=` operator. This is demonstrated in the tweet linked below:

https://twitter.com/GalloDaSballo/status/1543729467465629696

This optimization can be applied to the following lines of code:

- [ClaimNodeOp.sol#L51](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L51)
- [MinipoolManager.sol#L318](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L318)

# 7. Avoid re-initializing default values to save gas

On [line 155](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L155), the `isValid` variable is created as a `bool` type and by default the initial value when created is `false`. However, in the `else` statement on [lines 168-170](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L168-L170), `isValid` is re-initialized to `false` again, despite the value already being `false` and as a result it will cost more gas.

To avoid unnecessary gas costs, consider removing [lines 168-170](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L168-L170).

```solidity
File: contract/MinipoolManager.sol
Line 155:    bool isValid;

Line 168:    } else {
Line 169:           isValid = false;
Line 170:    }
```

Affected lines of code:

- [MinipoolManager.sol#L168-L170](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L168-L170)

# 8. Chained ternary operators are more gas-efficient than `else`-`if`

In certain cases, chaining ternary operators can be more gas efficient than using `else`-`if` statements.

As an example on [lines 157-170](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L157-L170) you would refactor it as such

```solidity
File: contract/MinipoolManager.sol
isValid = (currentStatus == MinipoolStatus.Prelaunch)
            ? (to == MinipoolStatus.Launched || to == MinipoolStatus.Canceled)
            : (currentStatus == MinipoolStatus.Launched)
            ? (to == MinipoolStatus.Staking || to == MinipoolStatus.Error)
            : (currentStatus == MinipoolStatus.Staking)
            ? (to == MinipoolStatus.Withdrawable || to == MinipoolStatus.Error)
            : (currentStatus == MinipoolStatus.Withdrawable ||
                currentStatus == MinipoolStatus.Error)
            ? (to == MinipoolStatus.Finished || to == MinipoolStatus.Prelaunch)
            : (currentStatus == MinipoolStatus.Finished ||
                currentStatus == MinipoolStatus.Canceled)
            ? (to == MinipoolStatus.Prelaunch)
            : false;
```

Affected line of code:

- [MinipoolManager.sol#L157-L170](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L157-L170)