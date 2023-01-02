## [NC-01] Open TODO
The code that contains “open todos” reflects that the development is not finished and that the code can change a posteriori, prior release, with or without audit.

File BaseAbstract: [6-8](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L6-L8)

File MinipoolManager.sol: [412](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L412)

File Staking.sol: [203](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L203)
## [NC-02] NatSpec is missing
Not att all contracts have added NatSpec.

## [NC-03] Emit event after making of storage change
It is good practice first to make storage change and after that to emit event. It occurs in many place in contracts.
Example:
```
        emit GuardianChanged(address(0), msg.sender);
        guardian = msg.sender;
``` 
Recommended Mitigation Steps
```
    guardian = msg.sender;
    emit GuardianChanged(address(0), msg.sender);
``` 
Storage: [36-37](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L36-L37)