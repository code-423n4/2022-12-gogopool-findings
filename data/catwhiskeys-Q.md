# 1)  Open TODOs
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment.

3 Instances:
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L6
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L412
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L203




# 2) abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()
Use abi.encode() instead which will pad items to 32 bytes, which will prevent hash collisions (e.g. abi.encodePacked(0x123,0x456) => 0x123456 => abi.encodePacked(0x1,0x23456), but abi.encode(0x123,0x456) => 0x0...1230...456). 
Unless there is a compelling reason, abi.encode should be preferred. 
If there is only one argument to abi.encodePacked() it can often be cast to bytes() or bytes32() instead. 
If all arguments are strings and or bytes, bytes.concat() should be used instead

Everywhere in the following contracts:
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol

Recommended Mitigation Steps:
Use abi.encode() instead of abi.encodePacked()




# 3) Inappropriate comments
"Dirty hack to cut unused elements off end of return value" doesn't seem to be necessary to include in the contracts

3 Instances:
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L626
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L223
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L433




# 4) Floating pragma
Instance:
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L2
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#2

Recommended Mitigation Steps:
Use precise compiler version




# 5) Function order
Functions should be ordered following the Solidity conventions.
The modifier authorized is placed at the end of the contract, while it should be placed before every other function:
Inside each contract, library or interface, use the following order:
Type declarations
State variables
Events
Modifiers
Functions

Functions should be grouped according to their visibility and ordered:
constructor
receive function (if exists)
fallback function (if exists)
external
public
internal
private

3 Instances:
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol

Recommended Mitigation Steps:
Consider reordering functions according to the solidity conventions
