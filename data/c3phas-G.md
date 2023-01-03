Table of Contents
- [FINDINGS](#findings)
- [Using Constants or immutable on variables whose values are known or set in the constructor](#using-constants-or-immutable-on-variables-whose-values-are-known-or-set-in-the-constructor)
- [Internal/Private functions only called once can be inlined to save gas](#internalprivate-functions-only-called-once-can-be-inlined-to-save-gas)
- [Multiple accesses of a mapping/array should use a local variable cache](#multiple-accesses-of-a-mappingarray-should-use-a-local-variable-cache)
  - [Vault.sol.withdrawAVAX(): avaxBalances\[contractName\] should be cached in local storage](#vaultsolwithdrawavax-avaxbalancescontractname-should-be-cached-in-local-storage)
  - [Vault.sol.transferAVAX(): avaxBalances\[fromContractName\] should be cached in local storage](#vaultsoltransferavax-avaxbalancesfromcontractname-should-be-cached-in-local-storage)
  - [Vault.sol.withdrawToken(): tokenBalances\[contractKey\] should be cached in local storage](#vaultsolwithdrawtoken-tokenbalancescontractkey-should-be-cached-in-local-storage)
  - [Vault.sol.transferToken(): tokenBalances\[contractKeyFrom\] should be cached in local storage](#vaultsoltransfertoken-tokenbalancescontractkeyfrom-should-be-cached-in-local-storage)
- [Multiple address mappings can be combined into a single mapping of an address to a struct, where appropriate](#multiple-address-mappings-can-be-combined-into-a-single-mapping-of-an-address-to-a-struct-where-appropriate)
- [Splitting require() statements that use \&\& saves gas - (saves 8 gas per \&\&)](#splitting-require-statements-that-use--saves-gas---saves-8-gas-per-)
- [Use Shift Right/Left instead of Division/Multiplication](#use-shift-rightleft-instead-of-divisionmultiplication)
- [x += y costs more gas than x = x + y for state variables](#x--y-costs-more-gas-than-x--x--y-for-state-variables)
- [Using unchecked blocks to save gas](#using-unchecked-blocks-to-save-gas)
- [Using unchecked blocks to save gas - Increments in for loop can be unchecked  ( save 30-40 gas per loop iteration)](#using-unchecked-blocks-to-save-gas---increments-in-for-loop-can-be-unchecked---save-30-40-gas-per-loop-iteration)
- [`keccak256()` should only need to be called on a specific string literal once](#keccak256-should-only-need-to-be-called-on-a-specific-string-literal-once)
- [Boolean comparisons](#boolean-comparisons)
- [Use Custom Errors instead of Revert Strings to save Gas(~50 gas)](#use-custom-errors-instead-of-revert-strings-to-save-gas50-gas)
- [The following should have been caught by the automated script but wasn't , adding for completeness](#the-following-should-have-been-caught-by-the-automated-script-but-wasnt--adding-for-completeness)
  - [Cache storage values in memory to minimize SLOADs](#cache-storage-values-in-memory-to-minimize-sloads)
  - [TokenggAVAX.sol.depositFromStaking(): stakingTotalAssets should be cached](#tokenggavaxsoldepositfromstaking-stakingtotalassets-should-be-cached)

## FINDINGS
NB: Some functions have been truncated where necessary to just show affected parts of the code
Throughout the report some places might be denoted with audit tags to show the actual place affected.

## Using Constants or immutable on variables whose values are known or set in the constructor
Use immutable if you want to assign a permanent value at construction. Use constants if you already know the permanent value. Both get directly embedded in bytecode, saving SLOAD.The compiler does not reserve a storage slot for these variables, and every occurrence is replaced by the respective value.

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L44
```solidity
File: /contracts/contract/tokens/TokenggAVAX.sol
44:	uint32 public rewardsCycleLength;
```

The variable `rewardsCycleLength` has an hard coded value  of `14 days` defined on [Line 76](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L76)
As this value is never modified we could save a lot of gas by declaring it as a constant

```diff
diff --git a/contracts/contract/tokens/TokenggAVAX.sol b/contracts/contract/tokens/TokenggAVAX.sol
index dc3948d..c44c4f6 100644
--- a/contracts/contract/tokens/TokenggAVAX.sol
+++ b/contracts/contract/tokens/TokenggAVAX.sol
@@ -41,7 +41,7 @@ contract TokenggAVAX is Initializable, ERC4626Upgradeable, UUPSUpgradeable, Base
        uint32 public lastSync;

        /// @notice the maximum length of a rewards cycle
-       uint32 public rewardsCycleLength;
+       uint32 public constant rewardsCycleLength  = 14 days;

        /// @notice the end of the current cycle. Will always be evenly divisible by `rewardsCycleLength`.
        uint32 public rewardsCycleEnd;
@@ -73,7 +73,7 @@ contract TokenggAVAX is Initializable, ERC4626Upgradeable, UUPSUpgradeable, Base
                __ERC4626Upgradeable_init(asset, "GoGoPool Liquid Staking Token", "ggAVAX");
                __BaseUpgradeable_init(storageAddress);

-               rewardsCycleLength = 14 days;
                // Ensure it will be evenly divisible by `rewardsCycleLength`.
                rewardsCycleEnd = (block.timestamp.safeCastTo32() / rewardsCycleLength) * rewardsCycleLength;
        }
```


## Internal/Private functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

Affected code:

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L110
```solidity
File: /contracts/contract/RewardsPool.sol
110:	function increaseRewardsCycleCount() internal {
```

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L203-L207
```solidity
File: /contracts/contract/RewardsPool.sol
203:	function distributeMultisigAllotment(
204:		uint256 allotment,
205:		Vault vault,
206:		TokenGGP ggp
207:	) internal {
```

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L87
```solidity
File: /contracts/contract/Staking.sol
87:	function increaseGGPStake(address stakerAddr, uint256 amount) internal {
```

## Multiple accesses of a mapping/array should use a local variable cache

Caching a mapping's value in a local storage or calldata variable when the value is accessed multiple times saves ~42 gas per access due to not having to perform the same offset calculation every time.
**Help the Optimizer by saving a storage variable's reference instead of repeatedly fetching it**

To help the optimizer,declare a storage type variable and use it instead of repeatedly fetching the reference in a map or an array. 
As an example, instead of repeatedly calling ```someMap[someIndex]```, save its reference like this: ```SomeStruct storage someStruct = someMap[someIndex]``` and use it.

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L61-L78
### Vault.sol.withdrawAVAX(): avaxBalances\[contractName\] should be cached in local storage
```solidity
File: /contracts/contract/Vault.sol
61:	function withdrawAVAX(uint256 amount) external onlyRegisteredNetworkContract nonReentrant {

71:		if (avaxBalances[contractName] < amount) { //@audit: Initial access
72:			revert InsufficientContractBalance();
73:		}
74:		// Update balance
75:		avaxBalances[contractName] = avaxBalances[contractName] - amount;//@audit: 2nd and 3rd access
```

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L84-L101
### Vault.sol.transferAVAX(): avaxBalances\[fromContractName\] should be cached in local storage
```solidity
File: /contracts/contract/Vault.sol
84:	function transferAVAX(string memory toContractName, uint256 amount) external onlyRegisteredNetworkContract {

95:		if (avaxBalances[fromContractName] < amount) { //@audit: Initial access
96:			revert InsufficientContractBalance();
97:		}
98:		// Update balances
99:		avaxBalances[fromContractName] = avaxBalances[fromContractName] - amount;//@audit: 2nd and 3rd access
```

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L137-L160
### Vault.sol.withdrawToken(): tokenBalances\[contractKey\] should be cached in local storage
```solidity
File: /contracts/contract/Vault.sol
137:	function withdrawToken(
138:		address withdrawalAddress,
139:		ERC20 tokenAddress,
140:		uint256 amount
141:	) external onlyRegisteredNetworkContract nonReentrant {

151:		if (tokenBalances[contractKey] < amount) { //@audit: Initial access
152:			revert InsufficientContractBalance();
153:		}
154:		// Update balances
155:		tokenBalances[contractKey] = tokenBalances[contractKey] - amount;//@audit: 2nd and 3rd access
```

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L166-L189
### Vault.sol.transferToken(): tokenBalances\[contractKeyFrom\] should be cached in local storage
```solidity
File: /contracts/contract/Vault.sol
166:	function transferToken(
167:		string memory networkContractName,
168:		ERC20 tokenAddress,
169:		uint256 amount
170:	) external onlyRegisteredNetworkContract {

183:		if (tokenBalances[contractKeyFrom] < amount) { //@audit: Initial access
184:			revert InsufficientContractBalance();
185:		}
186:		// Update Balances
187:		tokenBalances[contractKeyFrom] = tokenBalances[contractKeyFrom] - amount;//@audit: 2nd and 3rd access
```


## Multiple address mappings can be combined into a single mapping of an address to a struct, where appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L35-L37
```solidity
File: /contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol
35:	mapping(address => uint256) public balanceOf;

37:	mapping(address => mapping(address => uint256)) public allowance;
```

## Splitting require() statements that use && saves gas - (saves 8 gas per &&)

Instead of using the && operator in a single require statement to check multiple conditions,using multiple require statements with 1 condition per require statement will save 8 GAS per &&
The gas difference would only be realized if the revert condition is realized(met).

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L154
```solidity
File: /contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol
154:			require(recoveredAddress != address(0) && recoveredAddress == owner, "INVALID_SIGNER");
```

```diff
diff --git a/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol b/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol
index 2ea27d6..e53ae5f 100644
--- a/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol
+++ b/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol
@@ -151,7 +151,8 @@ abstract contract ERC20Upgradeable is Initializable {
                                s
                        );

-                       require(recoveredAddress != address(0) && recoveredAddress == owner, "INVALID_SIGNER");
+                       require(recoveredAddress != address(0) , "INVALID_SIGNER");
+                       require(recoveredAddress == owner, "INVALID_SIGNER");

                        allowance[recoveredAddress][spender] = value;
                }
```

## Use Shift Right/Left instead of Division/Multiplication
A division/multiplication by any number x being a power of 2 can be calculated by shifting log2(x) to the right/left.

While the DIV opcode uses 5 gas, the SHR opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L413
```solidity
File: /contracts/contract/MinipoolManager.sol
413:		uint256 avaxHalfRewards = avaxTotalRewardAmt / 2;
```

[relevant source](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md/#g008---use-shift-rightleft-instead-of-divisionmultiplication-if-possible)

## x += y costs more gas than x = x + y for state variables

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L149
```solidity
File: /contracts/contract/tokens/TokenggAVAX.sol
149:		stakingTotalAssets -= baseAmt;
```

```diff
diff --git a/contracts/contract/tokens/TokenggAVAX.sol b/contracts/contract/tokens/TokenggAVAX.sol
index dc3948d..7aa769a 100644
--- a/contracts/contract/tokens/TokenggAVAX.sol
+++ b/contracts/contract/tokens/TokenggAVAX.sol
@@ -146,7 +146,7 @@ contract TokenggAVAX is Initializable, ERC4626Upgradeable, UUPSUpgradeable, Base
                }

                emit DepositedFromStaking(msg.sender, baseAmt, rewardAmt);
-               stakingTotalAssets -= baseAmt;
+               stakingTotalAssets = stakingTotalAssets - baseAmt;
                IWAVAX(address(asset)).deposit{value: totalAmt}();
        }
```

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L160
```solidity
File: /contracts/contract/tokens/TokenggAVAX.sol
160:		stakingTotalAssets += assets;
```

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L245
```solidity
File: /contracts/contract/tokens/TokenggAVAX.sol
245:		totalReleasedAssets -= amount;
```

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L252
```solidity
File: /contracts/contract/tokens/TokenggAVAX.sol
252:		totalReleasedAssets += amount;
```

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L184
```solidity
File: /contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol
184:		totalSupply += amount;
```

## Using unchecked blocks to save gas
Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block
[see resource](https://github.com/ethereum/solidity/issues/10695)

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimNodeOp.sol#L102
```solidity
File: /contracts/contract/ClaimNodeOp.sol
102:		uint256 restakeAmt = ggpRewards - claimAmt;
```
The operation `ggpRewards - claimAmt` cannot underflow due to the check on [Line 95](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimNodeOp.sol#L95) which ensurs that `ggpRewards` is greater than `claimAmt` before perfoming the operation
We can rewrite the above as follows.

```diff
diff --git a/contracts/contract/ClaimNodeOp.sol b/contracts/contract/ClaimNodeOp.sol
index efcce70..0e9b116 100644
--- a/contracts/contract/ClaimNodeOp.sol
+++ b/contracts/contract/ClaimNodeOp.sol
@@ -99,7 +99,10 @@ contract ClaimNodeOp is Base {
                staking.decreaseGGPRewards(msg.sender, ggpRewards);

                Vault vault = Vault(getContractAddress("Vault"));
-               uint256 restakeAmt = ggpRewards - claimAmt;
+               uint256 restakeAmt;
+               unchecked {
+                       restakeAmt = ggpRewards - claimAmt;
+               }
```

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L75
```solidity
File: /contracts/contract/Vault.sol
75:		avaxBalances[contractName] = avaxBalances[contractName] - amount;
```

The operation `avaxBalances[contractName] - amount` cannot underflow due to the check on [Line 71](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L71) that ensures that `amount` is less than `avaxBalances[contractName]`

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L99
```solidity
File:/contracts/contract/Vault.sol
99:		avaxBalances[fromContractName] = avaxBalances[fromContractName] - amount;
```
The operation `avaxBalances[fromContractName] - amount` cannot underflow due to the check on [Line 95](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L95) that ensures that `amount` is less than `avaxBalances[fromContractName]`

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L155
```solidity
File: contracts/contract/Vault.sol
155:		tokenBalances[contractKey] = tokenBalances[contractKey] - amount;
```
The operation `tokenBalances[contractKey] - amount` cannot underflow due to the check on [Line 151](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L151) that ensures that `amount` is less than `tokenBalances[contractKey]`

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L187
```solidity
File:/contracts/contract/Vault.sol
187:		tokenBalances[contractKeyFrom] = tokenBalances[contractKeyFrom] - amount;
```
The operation `tokenBalances[contractKeyFrom] - amount` cannot underflow due to the check on [Line 183](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Vault.sol#L183) that ensures that `amount` is less than `tokenBalances[contractKeyFrom]`


## Using unchecked blocks to save gas - Increments in for loop can be unchecked  ( save 30-40 gas per loop iteration)
The majority of Solidity for loops increment a uint256 variable that starts at 0. These increment operations never need to be checked for over/underflow because the variable will never reach the max number of uint256 (will run out of gas long before that happens). The default over/underflow check wastes gas in every iteration of virtually every for loop . eg.

e.g Let's work with a sample loop below.

```solidity
for(uint256 i; i < 10; i++){
//doSomething
}

```
can be written as shown below.
```solidity
for(uint256 i; i < 10;) {
  // loop logic
  unchecked { i++; }
}
```

We can also write  it as an inlined function like below.

```solidity
function inc(i) internal pure returns (uint256) {
  unchecked { return i + 1; }
}
for(uint256 i; i < 10; i = inc(i)) {
  // doSomething
}
```

**Affected code**
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L619-L625
```solidity
File: /contracts/contract/MinipoolManager.sol
619:		for (uint256 i = offset; i < max; i++) {
620:			Minipool memory mp = getMinipool(int256(i));
621:			if (mp.status == uint256(status)) {
621:				minipools[total] = mp;
622:				total++;
623:			}
624:		}
```
The above should be modified to:
```diff
diff --git a/contracts/contract/MinipoolManager.sol b/contracts/contract/MinipoolManager.sol
index 8563580..30f203c 100644
--- a/contracts/contract/MinipoolManager.sol
+++ b/contracts/contract/MinipoolManager.sol
@@ -616,12 +616,15 @@ contract MinipoolManager is Base, ReentrancyGuard, IWithdrawer {
                }
                minipools = new Minipool[](max - offset);
                uint256 total = 0;
-               for (uint256 i = offset; i < max; i++) {
+               for (uint256 i = offset; i < max;) {
                        Minipool memory mp = getMinipool(int256(i));
                        if (mp.status == uint256(status)) {
                                minipools[total] = mp;
                                total++;
                        }
+                       unchecked {
+                               ++i;
+                       }
                }
```

**Other Instances to modify**

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MultisigManager.sol#L84
```solidity
File: /contracts/contract/MultisigManager.sol
84:		for (uint256 i = 0; i < total; i++) {
```

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Ocyticus.sol#L61
```solidity
File: /contracts/contract/Ocyticus.sol
61:		for (uint256 i = 0; i < count; i++) {
```

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L74
```solidity
File: /contracts/contract/RewardsPool.sol
74:		for (uint256 i = 0; i < inflationIntervalsElapsed; i++) {

215:		for (uint256 i = 0; i < count; i++) {

230:		for (uint256 i = 0; i < enabledMultisigs.length; i++) {
```

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L428
```solidity
File:/contracts/contract/Staking.sol
428:		for (uint256 i = offset; i < max; i++) {
```
[see resource](https://github.com/ethereum/solidity/issues/10695)

## `keccak256()` should only need to be called on a specific string literal once

It should be saved to an immutable variable, and the variable used instead. If the hash is being used as a part of a function selector, the cast to `bytes4` should also only be done once

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimNodeOp.sol#L36
```solidity
File: /contracts/contract/ClaimNodeOp.sol
36:		return getUint(keccak256("NOPClaim.RewardsCycleTotal"));
```

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimNodeOp.sol#L41
```solidity
File: /contracts/contract/ClaimNodeOp.sol
41:		setUint(keccak256("NOPClaim.RewardsCycleTotal"), amount);
```

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L248
```solidity
File: /contracts/contract/MinipoolManager.sol
248:			minipoolIndex = int256(getUint(keccak256("minipool.count")));

252:			addUint(keccak256("minipool.count"), 1);

433:		subUint(keccak256("MinipoolManager.TotalAVAXLiquidStakerAmt"), avaxLiquidStakerAmt);

512:		subUint(keccak256("MinipoolManager.TotalAVAXLiquidStakerAmt"), avaxLiquidStakerAmt);

542:		return getUint(keccak256("MinipoolManager.TotalAVAXLiquidStakerAmt"));

612:		uint256 totalMinipools = getUint(keccak256("minipool.count"));

635:		return getUint(keccak256("minipool.count"));
```

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L139
```solidity
File: /contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol
139:								keccak256("Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)"),

170:					keccak256("EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)"),

172:					keccak256("1"),
```

## Boolean comparisons

Comparing to a constant (true or false) is a bit more expensive than directly checking the returned boolean value.
I suggest using if(directValue) instead of if(directValue == true) and if(!directValue) instead of if(directValue == false) here:

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseAbstract.sol#L25
```solidity
File: /contracts/contract/BaseAbstract.sol
25:		if (getBool(keccak256(abi.encodePacked("contract.exists", msg.sender))) == false) {
```

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseAbstract.sol#L74-L76
```solidity
File: /contracts/contract/BaseAbstract.sol
74:		if (enabled == false) {
75:			revert MustBeMultisig();
76:		}
```


## Use Custom Errors instead of Revert Strings to save Gas(~50 gas)
Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met)
Custom errors save ~50 gas each time they’re hit by avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas

Custom errors are defined using the error statement, which can be used inside and outside of contracts (including interfaces and libraries).
see [Source](https://blog.soliditylang.org/2021/04/21/custom-errors/)

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L127
```solidity
File: /contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol
127:		require(deadline >= block.timestamp, "PERMIT_DEADLINE_EXPIRED");

154:		require(recoveredAddress != address(0) && recoveredAddress == owner, "INVALID_SIGNER");
```

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L44
```solidity
File: /contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol
44:		require((shares = previewDeposit(assets)) != 0, "ZERO_SHARES");

103:	require((assets = previewRedeem(shares)) != 0, "ZERO_ASSETS");
```
## The following should have been caught by the automated script but wasn't , adding for completeness

### Cache storage values in memory to minimize SLOADs
The code can be optimized by minimizing the number of SLOADs.

SLOADs are expensive (100 gas after the 1st one) compared to MLOADs/MSTOREs (3 gas each). Storage values read multiple times should instead be cached in memory the first time (costing 1 SLOAD) and then read from this cache to avoid multiple SLOADs.

https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L142-L151
### TokenggAVAX.sol.depositFromStaking(): stakingTotalAssets should be cached
```solidity
File: /contracts/contract/tokens/TokenggAVAX.sol
142:	function depositFromStaking(uint256 baseAmt, uint256 rewardAmt) public payable onlySpecificRegisteredContract("MinipoolManager", msg.sender) {

144:		if (totalAmt != (baseAmt + rewardAmt) || baseAmt > stakingTotalAssets) {
145:			revert InvalidStakingDeposit();
146:		}

149:		stakingTotalAssets -= baseAmt;
```

```diff
diff --git a/contracts/contract/tokens/TokenggAVAX.sol b/contracts/contract/tokens/TokenggAVAX.sol
index dc3948d..9f131f6 100644
--- a/contracts/contract/tokens/TokenggAVAX.sol
+++ b/contracts/contract/tokens/TokenggAVAX.sol
@@ -141,12 +141,13 @@ contract TokenggAVAX is Initializable, ERC4626Upgradeable, UUPSUpgradeable, Base

        function depositFromStaking(uint256 baseAmt, uint256 rewardAmt) public payable onlySpecificRegisteredContract("MinipoolManager", msg.sender) {
                uint256 totalAmt = msg.value;
-               if (totalAmt != (baseAmt + rewardAmt) || baseAmt > stakingTotalAssets) {
+               uint256 _stakingTotalAssets = stakingTotalAssets;
+               if (totalAmt != (baseAmt + rewardAmt) || baseAmt > _stakingTotalAssets) {
                        revert InvalidStakingDeposit();
                }

                emit DepositedFromStaking(msg.sender, baseAmt, rewardAmt);
-               stakingTotalAssets -= baseAmt;
+               stakingTotalAssets = _stakingTotalAssets - baseAmt;
                IWAVAX(address(asset)).deposit{value: totalAmt}();
        }
```
