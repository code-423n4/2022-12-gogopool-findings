## LACK OF CHECKS ADDRESS(0)
The following methods have a lack of checks if the received argument is an address, it’s good practice in order to reduce human error to check that the address specified in the constructor or initialize is different than address(0).


#### Affected Source Code
* [Base.sol:11](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Base.sol#L11)
* [ClaimNodeOp.sol:31](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimNodeOp.sol#L31)
* [MinipoolManager.sol:183](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L183)
* [MinipoolManager.sol:184](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L184)
* [Ocyticus.sol:28](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L28)
* [ProtocolDAO.sol:191](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L191)
* [Staking.sol:62](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L62)
* [Storage.sol:47](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L47)
* [Vault.sol:205](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L205)


## OPEN TODO
The code that contains “open todos” reflects that the development is not finished and that the code can change a posteriori, prior release, with or without
audit.

#### Affected Source Code
* [BaseAbstract.sol:6](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseAbstract.sol#L6)

## Internal and private functions should have an underscore prefix with mixedCase(Naming convention)
#### Affected Source Code
* [BaseUpgradeable.sol:10](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseUpgradeable.sol#L10)

    For more read...
    1. [Soliditydocs](https://docs.soliditylang.org/en/v0.8.15/style-guide.html#other-recommendations)
    2. [Solidity Style](https://www.notion.so/Solidity-Style-44daebebfbd645b0b9cbad7075ba42fe)

## Structs should be named using the CapWords style(Naming convention)
#### Affected Source Code
* [MinipoolManager.sol:82](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L82)

    For more read...
    1. [Soliditydocs](https://docs.soliditylang.org/en/v0.8.15/style-guide.html#other-recommendations)
    