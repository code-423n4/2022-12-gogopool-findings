https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol

while managing multisig contract , guardian can add different multisig addresses to the storage , that's while there is a maximum number of how much MULTISIG addresses a contract may contain .
it is also possible to disable or enable a specefic multisig . 
that's while its not possible to unregister a multisig , if a guardian was planning to register 10 mutlisg to the contract and he ended up adding a wrong address accidently , then there is no way that the guardian be able to add those 10 to the contract  . he can only add 9 more . 


recommendation : consider adding a function to unregister an address 