[NC-01] Incorrect versions of Solidity
solc frequently releases new compiler versions. Using an old version prevents access to new Solidity security checks. We also recommend avoiding complex pragma statement.
Deploy with any of the following Solidity versions:
0.5.16 - 0.5.17
0.6.11 - 0.6.12
0.7.5 - 0.7.6
0.8.16
The recommendations take into account:
Risks related to recent releases
Risks of complex code generation changes
Risks of new language features
Risks of known bugs
Use a simple pragma version that allows any of these versions. Consider using the latest version of Solidity for testing.
01: https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol
02: https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol

[NC-02] Dead-code
dead_code is not used in the contract, and make the code's review more difficult. Remove unused functions.
01: BaseAbstract.addUint(bytes32,uint256) (BaseAbstract.sol#191-193) is never used and should be removed
02: BaseAbstract.deleteAddress(bytes32) (BaseAbstract.sol#163-165) is never used and should be removed
03: BaseAbstract.deleteBool(bytes32) (BaseAbstract.sol#167-169) is never used and should be removed
04: BaseAbstract.deleteBytes(bytes32) (BaseAbstract.sol#171-173) is never used and should be removed
05: BaseAbstract.deleteBytes32(bytes32) (BaseAbstract.sol#175-177) is never used and should be removed
06: BaseAbstract.deleteInt(bytes32) (BaseAbstract.sol#179-181) is never used and should be removed
07: BaseAbstract.deleteString(bytes32) (BaseAbstract.sol#187-189) is never used and should be removed
08: BaseAbstract.deleteUint(bytes32) (BaseAbstract.sol#183-185) is never used and should be removed
09: BaseAbstract.getAddress(bytes32) (BaseAbstract.sol#107-109) is never used and should be removed
10: BaseAbstract.getBool(bytes32) (BaseAbstract.sol#111-113) is never used and should be removed
11: BaseAbstract.getBytes(bytes32) (BaseAbstract.sol#115-117) is never used and should be removed
12: BaseAbstract.getBytes32(bytes32) (BaseAbstract.sol#119-121) is never used and should be removed
13: BaseAbstract.getContractAddress(string) (BaseAbstract.sol#90-96) is never used and should be removed
14: BaseAbstract.getContractName(address) (BaseAbstract.sol#99-105) is never used and should be removed
15: BaseAbstract.getInt(bytes32) (BaseAbstract.sol#123-125) is never used and should be removed
16: BaseAbstract.getString(bytes32) (BaseAbstract.sol#131-133) is never used and should be removed
17: BaseAbstract.getUint(bytes32) (BaseAbstract.sol#127-129) is never used and should be removed
18: BaseAbstract.setAddress(bytes32,address) (BaseAbstract.sol#135-137) is never used and should be removed
19: BaseAbstract.setBool(bytes32,bool) (BaseAbstract.sol#139-141) is never used and should be removed
20: BaseAbstract.setBytes(bytes32,bytes) (BaseAbstract.sol#143-145) is never used and should be removed
21: BaseAbstract.setBytes32(bytes32,bytes32) (BaseAbstract.sol#147-149) is never used and should be removed
22: BaseAbstract.setInt(bytes32,int256) (BaseAbstract.sol#151-153) is never used and should be removed
23: BaseAbstract.setString(bytes32,string) (BaseAbstract.sol#159-161) is never used and should be removed
24: BaseAbstract.setUint(bytes32,uint256) (BaseAbstract.sol#155-157) is never used and should be removed
25: BaseAbstract.subUint(bytes32,uint256) (BaseAbstract.sol#195-197) is never used and should be removed

[NC-03] Boolean equality
Detects the comparison to boolean constants. Boolean constants can be used directly and do not need to be compare to true or false. Remove the equality to the boolean constant.
01: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseAbstract.sol#L25
02: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseAbstract.sol#L74

[NC-04] Functions not used internally could be marked external
01: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimNodeOp.sol#L35
02: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L547
03: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L557
04: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L566
05: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L580
06: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MultisigManager.sol#L96
07: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MultisigManager.sol#L109
08: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Ocyticus.sol#L55
09: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L48
10: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L54
11: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L66
12: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L115
13: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L120
14: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L125
15: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L134
16: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L151

[NC-05] Missing checks for address(0) when assigning values to address state variables
01: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L47
02: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L63
03: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Storage.sol#L105

[L-01] Unspecific compiler version pragma
01: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L2
02: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L2

[L-02] abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()
Use abi.encode() instead which will pad items to 32 bytes, which will prevent hash collisions (e.g. abi.encodePacked(0x123,0x456) => 0x123456 => abi.encodePacked(0x1,0x23456), but abi.encode(0x123,0x456) => 0x0...1230...456). "Unless there is a compelling reason, abi.encode should be preferred". If there is only one argument to abi.encodePacked() it can often be cast to bytes() or bytes32() instead. If all arguments are strings and or bytes, bytes.concat() should be used instead
01: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L133-L134
02: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L137-L139
03: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L168-L169

