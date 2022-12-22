##

## [GAS]  DON’T COMPARE BOOLEAN EXPRESSIONS TO BOOLEAN LITERALS

CAN USE   if (<x> == true) => if (<x>), if (<x> == false) => if (!<x>)

POSSIBLE TO SAVE 11 GAS PER EVERY CONDITION CHECKS 

[FILE: 2022-12-gogopool/contracts/contract/BaseAbstract.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol)

         25:  if (getBool(keccak256(abi.encodePacked("contract.exists", msg.sender))) == false) {

##

## [GAS-2] Use uint256 instead of uint8 . uint256 consumes less gas than uint8 As per remix gas reports possible to save 12 gas 

A smart contract's gas consumption can be higher if developers use items that are less than 32 bytes in size because the Ethereum Virtual Machine can only handle 32 bytes at a time. In order to increase the element's size to the necessary size, the EVM has to perform additional operations. 

[FILE: 2022-12-gogopool/contracts/contract/BaseAbstract.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol)

         19:   uint8 public version;
































GAS-1	Use assembly to check for address(0)	3
GAS-2	array[index] += amount is cheaper than array[index] = array[index] + amount (or related variants)	10
GAS-3	Using bools for storage incurs overhead	3
GAS-4	Cache array length outside of loop	1
GAS-5	State variables should be cached in stack variables rather than re-reading them from storage	2
GAS-6	Use calldata instead of memory for function arguments that do not get mutated	14
GAS-7	Don't initialize variables with default value	8
GAS-8	++i costs less gas than i++, especially when it's used in for-loops (--i/i-- too)	10
GAS-9	Using private rather than public for constants, saves gas	1
GAS-10	Use != 0 instead of > 0 for unsigned integer comparison	10
GAS-11	internal functions not called by the contract should be removed	8