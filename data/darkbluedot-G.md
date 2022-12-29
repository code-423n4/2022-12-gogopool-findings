Gas Reports 

Severity (low) : 

Report : There seems to be lots of instances where variables have been created just to be used for once . This takes unnecessary amounts of storage that can be prevented . This saves some gas cost . 

Some of the examples of this are below : 

Base.sol - None 

BaseAbstract.sol -

Line 25 : the comparison can just be changed to ! to save some gas 
```
if (getBool(keccak256(abi.encodePacked("contract.exists", msg.sender))) == false)  ```

```
``` if (!getBool(keccak256(abi.encodePacked("contract.exists", msg.sender))))```

This will save one additional transaction  


Line 41 : the variable  isContract and isGuardian have no need and can be directly added in the if statement 

``` bool isContract = getBool(keccak256(abi.encodePacked("contract.exists", msg.sender)));
		bool isGuardian = msg.sender == gogoStorage.getGuardian();  

```

They can be directly added to the if statement 

```
if(!(getBool(keccak256(abi.encodePacked("contract.exists", msg.sender)) || msg.sender == gogoStorage.getGuardian())){

}
```

Line 55 : same as above , useless unit are created only to be used once . 


ClaimNodeOp.sol 

Line 51 : instead of storing the elapsed time in a variable , since its getting used only once , we can directly compute the value in the return statement itself 

``` return (rewardsStartTime != 0 && elapsedSecs >= dao.getRewardsEligibilityMinSeconds()); ```

This can be made as 

 ``` return (rewardsStartTime != 0 && (block.timestamp - rewardsStartTime)>= dao.getRewardsEligibilityMinSeconds()); ```

MinipoolManager.sol 

Line 131 : 
Since assigedMultiSig is only used once in the function, there is no need for initializing it in the variable. Instead, we can directly call the function in the if condition to check and then revert 
```

		address assignedMultisig = getAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".multisigAddr")));
		if (msg.sender != assignedMultisig) {
			revert InvalidMultisigAddress();
		}
```
This can be written as : 
```
	if (msg.sender != getAddress(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".multisigAddr")))) {
			revert InvalidMultisigAddress();
		}
```
This will save the initialization cost of the uint 

Many more instances of this can be found in the code .
 
Line 169 : 
lid is assigned false in the if and else and then reverted with another  , we cad directly revert from the else statement , this will help lessen the computations 


In MultisigManager.sol

Line 36 : 

Multisigindex is used once but still its saved in a uint. You can directly get the value from the function getIndexOf() to check in the if condition . 
