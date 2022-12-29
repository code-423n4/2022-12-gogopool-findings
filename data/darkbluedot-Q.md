Quality Assurance : 

Severity non - critical
Report : 

The code is not properly following the design patterns usually followed in solidity .
The proper use of Guard checks and modifiers has not been done . This degrades the quality of the code as well as putting the smart contract with security vulnerabilities. 

For Example in MinipoolManager.sol , 
The guard checks on the functions onlyOwner and onlyValidMultisig can be put using modifiers that can also be reused . This ensures readability of the code .
