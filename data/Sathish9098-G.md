##

## [GAS]  DON’T COMPARE BOOLEAN EXPRESSIONS TO BOOLEAN LITERALS

CAN USE   if (<x> == true) => if (<x>), if (<x> == false) => if (!<x>)

POSSIBLE TO SAVE 11 GAS PER EVERY CONDITION CHECKS 

[FILE: 2022-12-gogopool/contracts/contract/BaseAbstract.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol)

         25:     if (getBool(keccak256(abi.encodePacked("contract.exists", msg.sender))) == false) {

        74:      if (enabled == false) {

##

## [GAS-2] Use uint256 instead of uint8 . uint256 consumes less gas than uint8 .

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

[FILE: 2022-12-gogopool/contracts/contract/BaseAbstract.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol)

         19:   uint8 public version;

[2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol)

       27:   uint8 public decimals;

##

## [GAS-3]  BYTES VARIABLES ARE MORE EFFICIENT THAN STRING VARIBLES 

 If data can fit into 32 bytes, then you should use bytes32 datatype rather than bytes or strings as it is cheaper in solidity

[FILE: 2022-12-gogopool/contracts/contract/BaseAbstract.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol)

        82:  string memory contractName = getContractName(address(this));

## [GAS-4]  DIVISION BY TWO SHOULD USE BIT SHIFTING

<x> / 2 is the same as <x> >> 1. While the compiler uses the SHR opcode to accomplish both, the version that uses division incurs an overhead of 20 gas due to JUMPs to and from a compiler utility function that introduces checks which can be avoided by using unchecked {} around the division by two

  [FILE: 12-gogopool/contracts/contract/MinipoolManager.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol)


      413:   uint256 avaxHalfRewards = avaxTotalRewardAmt / 2;

##

## [GAS-5]   ++I/I++ OR --I/I-- SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} OR  UNCHECKED{--I}/UNCHECKED{I--}WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop

[FILE: 12-gogopool/contracts/contract/MinipoolManager.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol)

         619:  for (uint256 i = offset; i < max; i++) {

        623:   total++;

[File: 2022-12-gogopool/contracts/contract/MultisigManager.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol)

      84:   for (uint256 i = 0; i < total; i++) {

[File: 2022-12-gogopool/contracts/contract/Ocyticus.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol)

         61:  for (uint256 i = 0; i < count; i++) {

[FILE: 2022-12-gogopool/contracts/contract/RewardsPool.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol)

        215:  for (uint256 i = 0; i < count; i++) {

       219:   enabledCount++;

      230:    for (uint256 i = 0; i < enabledMultisigs.length; i++) {

[2022-12-gogopool/contracts/contract/Staking.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol)

      428:  for (uint256 i = offset; i < max; i++) {

      431:  total++;

##

## [GAS-6]  USING STORAGE INSTEAD OF MEMORY FOR STRUCTS/ARRAYS SAVES GAS

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct


[FILE: 12-gogopool/contracts/contract/MinipoolManager.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol)

     620:  Minipool memory mp = getMinipool(int256(i));

##

## [GAS- 7] CAN MAKE THE VARIABLE OUTSIDE THE LOOP TO SAVE GAS

Make it outside and only use it inside.

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

## [GAS-8]  NOT USING THE NAMED RETURN VARIABLES WHEN A FUNCTION RETURNS, WASTES DEPLOYMENT GAS

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

## [GAS-9]  Instead of using operator && on single REQUIRE or IF statements  . Using multiple REQUIRE or  IF checks can save more gas. Its possible to save 8 gas . 

[2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol)

          154:  require(recoveredAddress != address(0) && recoveredAddress == owner, "INVALID_SIGNER");

[FILE: 2022-12-gogopool/contracts/contract/RewardsPool.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol)

          142:  if (claimContractPct > 0 && currentCycleRewardsTotal > 0) {   

[FILE: 2022-12-gogopool/contracts/contract/Storage.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol)

          29:    if (booleanStorage[keccak256(abi.encodePacked("contract.exists", msg.sender))] == false && msg.sender != guardian) {

[FILE: 2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol)

         59:   if (amt > 0 && getBool(keccak256(abi.encodePacked("contract.paused", "TokenggAVAX")))) {

##

## [GAS-10]  ADD UNCHECKED {} FOR SUBTRACTIONS WHERE THE OPERANDS CANNOT UNDERFLOW BECAUSE OF A PREVIOUS REQUIRE() OR IF-STATEMENT . This saves 30-40 gas

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

## [ GAS-11]  <X> += <Y> AND <X>-=<Y> COSTS MORE GAS THAN <X> = <X> + <Y> AND <X>=<X>-<Y> FOR STATE VARIABLES . FOR EVERY CALL CAN SAVE 13 GAS 

[FILE: 2022-12-gogopool/contracts/contract/tokens/TokenggAVAX.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol)

          149:     stakingTotalAssets -= baseAmt;

          160:     stakingTotalAssets += assets;

          245:     totalReleasedAssets -= amount;

         252:     totalReleasedAssets += amount;

[2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol)

       79:  balanceOf[msg.sender] -= amount;

       84:  balanceOf[to] += amount;

      101:  balanceOf[from] -= amount;

      106:  balanceOf[to] += amount;

      184:  totalSupply += amount;
 
      189:  balanceOf[to] += amount;

      196:   balanceOf[from] -= amount;

      201:   totalSupply -= amount;



































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