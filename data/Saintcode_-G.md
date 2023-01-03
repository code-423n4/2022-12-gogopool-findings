## USE CALLDATA INSTEAD OF MEMORY

When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for-loop to copy each index of the `calldata` to the `memory` index. Each iteration of this for-loop costs at least 60 gas (i.e. `60 * <mem_array>.length`). Using `calldata` directly, obliviates the need for such a loop in the contract code and runtime execution.

When arguments are read-only on external functions, the data location should be `calldata`

Instances:
-BaseAbstract.sol lines 90, 143, 159
-ClaimProtocolDAO.sol line 21
-ProtocolDAO.sol lines 61, 67, 73, 102, 107, 190, 211
-RewardsPool.sol line 134
-Vault.sol lines 84, 109, 167, 193, 200

## <ARRAY>.LENGTH SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A FOR-LOOP

Reading array length at each iteration of the loop consumes more gas than necessary.
In the best case scenario (length read on a memory variable), caching the array length in the stack saves around 3 gas per iteration. In the worst case scenario (external calls at each iteration), the amount of gas wasted can be massive.

Consider storing the array’s length in a variable before the for-loop, and use this new variable instead

Instances:
-RewardsPool.sol line 230

## <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y> FOR STATE VARIABLES

Instances:
-TokenggAVAX.sol lines 160, 252
-ERC20Upgradeable.sol lines 84, 106, 184, 196

## `++I` INSTEAD OF `I++` (OR USE ASSEMBLY WHEN APPLICABLE)

Use `++i` instead of ` i++`. This is especially useful in for loops but this optimization can be used anywhere in your code. 

Instances:
-MinipoolManager.sol lines 619, 623
-Ocyticus.sol line 61
-RewardsPool.sol lines 74, 215, 219, 230
-Staking.sol lines 428, 431
-ERC20Upgradeable.sol line 143


## USE MULTIPLE `REQUIRE()` STATMENTS INSTEAD OF `REQUIRE(EXPRESSION && EXPRESSION && ...)`

Intances:
-ERC20Upgradeable.sol line 154

## IT COSTS MORE GAS TO INITIALIZE VARIABLES WITH THEIR DEFAULT VALUE THAN LETTING THE DEFAULT VALUE BE APPLIED

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

As an example: for (uint256 i = 0; i < numIterations; ++i) { should be replaced with for (uint256 i; i < numIterations; ++i) {

Instances:
-MiniPoolManager.sol line 169


## `++I/I++` SHOULD BE `UNCHECKED{++I}`/`UNCHECKED{I++}` WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop
`
   for (uint256 i = 0; i < orders.length; /** NOTE: Removed i++ **/ ) {
           // Do the thing
           // Unchecked pre-increment is cheapest
           unchecked { ++i; }   
}  `

Instances:
-MinipoolManager.sol lines 619, 623
-Ocyticus.sol line 61
-RewardsPool.sol lines 74, 215, 219, 230
-Staking.sol lines 428, 431


## USE A MORE RECENT VERSION OF SOLIDITY

Instances:
-ERC20Upgradeable.sol line 2
-ERC4Upgradeable.sol line 2

## USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE() STRINGS TO SAVE GAS
Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they’re hitby avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas

Instances:
-ERC20Upgradeable.sol lines 127, 154
-ERC4Upgradeable.sol lines 44, 103

## USING BOOLS FOR STORAGE INCURS OVERHEAD
Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write
back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past

Instances:
-Ocyticus.sol line 13
-Vault.sol line 39

## DON’T COMPARE BOOLEAN EXPRESSIONS TO BOOLEAN LITERALS
Example:
Instead of `if (getBool(keccak256(abi.encodePacked("contract.exists", msg.sender))) == false)`, write `!getBool(keccak256(abi.encodePacked("contract.exists", msg.sender)))`.

Instances:
-BaseAbstract.sol lines 25, 74
-Storage.sol line 29

## FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED PAYABLE

If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are `CALLVALUE`(2),`DUP1`(3),`ISZERO`(3),`PUSH2`(3),`JUMPI`(10),`PUSH1`(3),`DUP1`(3),`REVERT`(0),`JUMPDEST`(1),`POP`(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

Instances:
All functions guarded with `onlyGuardian` modifier.