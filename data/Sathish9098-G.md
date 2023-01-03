##

## [GAS-1]  DON’T COMPARE BOOLEAN EXPRESSIONS TO BOOLEAN LITERALS

CAN USE   if (<x> == true) => if (<x>), if (<x> == false) => if (!<x>)

POSSIBLE TO SAVE 11 GAS PER EVERY CONDITION CHECKS 

> Instances (2) :

[FILE: 2022-12-gogopool/contracts/contract/BaseAbstract.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol)

         25:     if (getBool(keccak256(abi.encodePacked("contract.exists", msg.sender))) == false) {

         74:      if (enabled == false) {

##

## [GAS-2]  DIVISION BY TWO SHOULD USE BIT SHIFTING

<x> / 2 is the same as <x> >> 1. While the compiler uses the SHR opcode to accomplish both, the version that uses division incurs an overhead of 20 gas due to JUMPs to and from a compiler utility function that introduces checks which can be avoided by using unchecked {} around the division by two

> Instances (1) :

  [FILE: 12-gogopool/contracts/contract/MinipoolManager.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol)


      413:   uint256 avaxHalfRewards = avaxTotalRewardAmt / 2;

##

## [GAS-3]   ++I/I++ OR --I/I-- SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} OR  UNCHECKED{--I}/UNCHECKED{I--}WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop

> Instances (6) :

[FILE: 12-gogopool/contracts/contract/MinipoolManager.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol)

         619:  for (uint256 i = offset; i < max; i++) {

[File: 2022-12-gogopool/contracts/contract/MultisigManager.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol)

       84:   for (uint256 i = 0; i < total; i++) {

[File: 2022-12-gogopool/contracts/contract/Ocyticus.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol)

        61:  for (uint256 i = 0; i < count; i++) {

[FILE: 2022-12-gogopool/contracts/contract/RewardsPool.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol)

       215:  for (uint256 i = 0; i < count; i++) {

      230:    for (uint256 i = 0; i < enabledMultisigs.length; i++) {

[2022-12-gogopool/contracts/contract/Staking.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol)

      428:  for (uint256 i = offset; i < max; i++) {


##

## [GAS- 4] CAN MAKE THE VARIABLE OUTSIDE THE LOOP TO SAVE GAS

Make it outside and only use it inside.

>Instances (2)

[FILE: 12-gogopool/contracts/contract/MinipoolManager.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol)

         619:      for (uint256 i = offset; i < max; i++) {
			Minipool memory mp = getMinipool(int256(i));     //@Audit Variable outside the loop
			if (mp.status == uint256(status)) {
				minipools[total] = mp;
				total++;
                 }

[2022-12-gogopool/contracts/contract/Staking.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol)

         428:    for (uint256 i = offset; i < max; i++) {
			Staker memory s = getStaker(int256(i));    //@Audit Variable outside the loop
			stakers[total] = s;
			total++;
		}

##

## [GAS-5]  NOT USING THE NAMED RETURN VARIABLES WHEN A FUNCTION RETURNS, WASTES DEPLOYMENT GAS

>Instances (1)

[FILE: 2022-12-gogopool/contracts/contract/RewardsPool.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol)

       66:  function getInflationAmt() public view returns (uint256 currentTotalSupply, uint256 newTotalSupply) {
		ProtocolDAO dao = ProtocolDAO(getContractAddress("ProtocolDAO"));
		uint256 inflationRate = dao.getInflationIntervalRate();
		uint256 inflationIntervalsElapsed = getInflationIntervalsElapsed();
		currentTotalSupply = dao.getTotalGGPCirculatingSupply();
		newTotalSupply = currentTotalSupply;

		// Compute inflation for total inflation intervals elapsed
		for (uint256 i = 0; i < inflationIntervalsElapsed; i++) {
			newTotalSupply = newTotalSupply.mulWadDown(inflationRate);
		}
		return (currentTotalSupply, newTotalSupply);
	}

##

## [GAS-6]  Instead of using operator && on single REQUIRE  . Using multiple REQUIRE  can save more gas. Its possible to save 8 gas . 

[2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol)

>Instances (1)

          154:  require(recoveredAddress != address(0) && recoveredAddress == owner, "INVALID_SIGNER");

##

## [GAS-7]  ADD UNCHECKED {} FOR SUBTRACTIONS WHERE THE OPERANDS CANNOT UNDERFLOW BECAUSE OF A PREVIOUS REQUIRE() OR IF-STATEMENT . This saves 30-40 gas

>Instances (4)

[FILE: 2022-12-gogopool/contracts/contract/Vault.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol)

           71:   if (avaxBalances[contractName] < amount) {
           75:   avaxBalances[contractName] = avaxBalances[contractName] - amount;  //@AUDIT UNCHECKED {} FOR SUBTRACTIONS

           95:   if (avaxBalances[fromContractName] < amount) {
           99:   avaxBalances[fromContractName] = avaxBalances[fromContractName] - amount;   //@AUDIT UNCHECKED {} FOR SUBTRACTIONS

          151:  if (tokenBalances[contractKey] < amount) {
          152:  tokenBalances[contractKey] = tokenBalances[contractKey] - amount;    //@AUDIT UNCHECKED {} FOR SUBTRACTIONS

          183:  if (tokenBalances[contractKeyFrom] < amount) {
          187: tokenBalances[contractKeyFrom] = tokenBalances[contractKeyFrom] - amount;    //@AUDIT UNCHECKED {} FOR SUBTRACTIONS

##

## [GAS-8] Use uint256 instead of uint8 . uint256 consumes less gas than uint8 .

A smart contract's gas consumption can be higher if developers use items that are less than 32 bytes in size because the Ethereum Virtual Machine can only handle 32 bytes at a time. In order to increase the element's size to the necessary size, the EVM has to perform additional operations. 

> Before Change:

pragma solidity ^0.8.13;
import "./Test.sol";
contract Testr {
uint8 public version;
Test testContract;
    }

As per remix gas reports execution cost is 95097

> After change:


pragma solidity ^0.8.13;
import "./Test.sol";
contract Testr {
uint256 public version;
Test testContract;
    }

After change the execution cost is 92079. 

SO CLEARLY AFTER THE CHANGE THE EXECUTION COST IS REDUCED .

> Instances (2) :

[FILE: 2022-12-gogopool/contracts/contract/BaseAbstract.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol)

         19:   uint8 public version;

[2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol)

       27:   uint8 public decimals;

##

## [GAS-9]  BYTES VARIABLES ARE MORE EFFICIENT THAN STRING VARIBLES 

 If data can fit into 32 bytes, then you should use bytes32 datatype rather than bytes or strings as it is cheaper in solidity

[FILE: 2022-12-gogopool/contracts/contract/BaseAbstract.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol)

        82:  string memory contractName = getContractName(address(this));


##

## [GAS-10]  USING STORAGE INSTEAD OF MEMORY FOR STRUCTS/ARRAYS SAVES GAS

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct


[FILE: 12-gogopool/contracts/contract/MinipoolManager.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol)

     620:  Minipool memory mp = getMinipool(int256(i));































