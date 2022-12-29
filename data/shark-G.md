## 1. Use `>>` rather than division by 2

A division by 2 can be calculated by shifting to the right.

While the SHR (shift right) opcode only uses 3 gas, the DIV opcode uses 5 gas. Furthermore, Solidity's division operation also includes division-by-0 prevention which is bypassed using shifting.

File: `MinipoolManager.sol` [Line 413](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L413)

```solidity
    uint256 avaxHalfRewards = avaxTotalRewardAmt / 2;
```

Consider refactoring the above code to:

```solidity
        uint256 avaxHalfRewards = avaxTotalRewardAmt >> 1;
```

## 2. Functions with access control marked as `payable` cost less gas

Functions with access control will cost less gas when marked as `payable` (assuming the caller has correct permissions). This is because the compiler doesn't need to check for `msg.value`, which saves approximately **20 gas** per call.

For example:

File: `ProtocolDAO.sol` [Line 190](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L190)

```solidity
    function registerContract(address addr, string memory name) public onlyGuardian {
```

Since the above function has the access control `onlyGuardian`, it can be marked `payable` to save gas:

```solidity
    function registerContract(address addr, string memory name) public payable onlyGuardian {
```

## 3. Using `storage` instead of `memory` for structs/arrays saves gas

When retrieving data from a `storage` location, assigning the data to a `memory` variable will cause all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new `memory` variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declaring the variable with the memory keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incurring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires `memory`, or if the array/struct is being read from another `memory` array/struct.

For example, the following structs can be changed to `storage` instead of `memory` to save gas:

File: `Staking.sol` [Line 429](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L429)

File: `MinipoolManager.sol` [Line 620](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L620)

## 4. Unnecessary boolean literal compare

You will save gas on deployment by not comparing boolean literals (i.e. `true` and `false`)

Here are some examples of this issue:

File: `Storage.sol` [Line 29](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L29)

File: `BaseAbstract.sol` [Line 25](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L25)

File: `BaseAbstract.sol` [Line 74](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L74)

## 5. Unchecked maths saves gas

Use the `unchecked` keyword to avoid redundant arithmetic checks when an underflow/overflow cannot happen.

For example:

File: `MultisigManager.sol` [Line 84-89](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L84-L89)

```solidity
        for (uint256 i = 0; i < total; i++) {
            (addr, enabled) = getMultisig(i);
            if (enabled) {
                return addr;
            }
        }
```

`i++` will never overflow here.

## 6. Unnecessary check

In [`createMinipool()`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L196), [the third if block](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L216) is an unnecessary check, as [the second if block](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L207-L212) already checks it.

As such, removing the redundant if block will save gas:

File: `MinipoolManager.sol` [Line 216-218](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L216-L218)

## 7. `<` costs less gas than `<=`

`<` cost less gas since it requires fewer opcodes than `<=`. This is the same for `>=`

For instance:

File: `MinipoolManager.sol` [Line 394](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L394)

```solidity
        if (endTime <= startTime || endTime > block.timestamp) {
```

The above could be refactored to:

```solidity
        if (endTime < startTime + 1 || endTime > block.timestamp) {
```

## 8. Unused `delegationFee` field in struct

In [`MinipoolManager.sol`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol), the `Minipool` struct has a field that is unused ([Line 87](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L87))

Because this will cost extra gas, consider either removing it, or making use of it.
