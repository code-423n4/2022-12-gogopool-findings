In Storage.sol, if a guardian loses access to their account or privatekey, the role cannot be restored.
Through "modifier onlyRegisteredNetworkContract()" functionality can still be executed, but there is no way to get back the guardian role.

Possible Solution:
Implement a function that returns the guardian role to the deployer. He can work together with the rightfull guardian and give him the role once again.

Could look like this:
```
	function restoreGuardian() external onlyRegisteredNetworkContract{ 
                 // If the guardian is lost, return the role to the deployer
		newGuardian = deployer; //deployer would have to be stored when constructor is called
	}
```