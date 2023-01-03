# 1. inadequate NatSpec

Contracts can use the Ethereum Natural Language Specification Format (NatSpec) to provide detailed documentation for functions, return variables, and other elements of the contract. This is done using a special type of comment within the contract code.

Here is an example of what a NatSpec comment would look like in a contract:

```solidity
/// @notice This is a NatSpec comment that provides
/// a brief description of the `__BaseUpgradeable_init` function.
function __BaseUpgradeable_init(Storage gogoStorageAddress) internal onlyInitializing {
  ...
}
```

NatSpec comments start with `///` and follow a specific format that includes tags such as `@notice` and `@param` to indicate the type of information being provided. You can find more information about NatSpec and its usage in Solidity contracts at the following link:

- [NatSpec Format](https://docs.soliditylang.org/en/v0.8.16/natspec-format.html)

This is present in 1 file:

- [BaseUpgradeable.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseUpgradeable.sol)

# 2. Improve the Readability by following modularity principles

To make your Solidity code more modern and readable, it is recommended to update your import statements to only include the specific contracts or objects that you need.

This practice, known as modular programming, helps to keep the code cleaner and more organized.

To avoid unnecessarily polluting the source code with unused contracts or objects, it is recommended to use specific imports with curly braces. An example of this syntax is shown below:

```solidity
import {contract1, contract2} from "filename.sol";
```

By using specific imports in this way, you can ensure that your code follows the principles of modularity and is as clear and maintainable as possible.

Affected lines of code:

- [Base.sol#L4](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Base.sol#L4)

- [BaseUpgradeable.sol#L4](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseUpgradeable.sol#L4)

- [ClaimNodeOp.sol#L4](https://github.com/code-423n4/2022-12-gogopool/blob/-main/contracts/contract/ClaimNodeOp.sol#L4)

# 3. Open TODOs

Open TODOs can point to architecture or programming issues that still need to be resolved. Consider resolving them before deploying.

Affected lines of code:

- [BaseAbstract.sol#L6-L8](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L6-L8)
- [Staking.sol#L203](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L203)
- [MinipoolManager.sol#L412](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L412)

# 4. Add Storage Gap to Upgradeable Contracts

It is recommended to add a storage gap at the end of an upgradeable contract to ensure that any future child contracts do not cause a shift in storage in the inheritance chain. This can be done by including the following line of code:

```solidity
uint256[50] private __gap;
```

This can be applied to the following contract:

- [TokenggAVAX.sol](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol)

This storage gap can be added to the end of the contract file. By adding a storage gap in this way, you can ensure that the storage layout of your contract remains stable and consistent even if it is extended with additional functionality in the future.

# 5. Limit Line Length for Readability

It is generally a good practice to keep lines of code within 80 characters in length, although some flexibility is allowed and it is reasonable to allow lines to be up to 120 characters in certain cases. On modern screens, it is even possible to go beyond this limit.

However, for the sake of readability and ease of use, it is recommended to split lines when they reach a length of 164 characters or more, as this is the point at which GitHub will introduce a scroll bar to view the code.

This recommendation applies to the following line of code:

- [ProtocolDAO.sol#L41](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ProtocolDAO.sol#L41)

By following this guidance, you can help to make your code more readable and easier to work with.

# 6. Typos

1. [contract/ClaimNodeOp.sol](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimNodeOp.sol)

   - [ClaimNodeOp.sol#L45](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimNodeOp.sol#L45)

     ```solidity
     line 45:    /// @dev Eligiblity: time in protocol (secs) > RewardsEligibilityMinSeconds. Rialto will call this.
     ```

     - Consider making the following change:
       - `Eligiblity` To `Eligibility` ([Line 45](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimNodeOp.sol#L45))

2. [NFTEDA/NFTEDA.sol](https://github.com/with-backed/papr/blob/9528f2711ff0c1522076b9f93fba13f88d5bd5e6/src/NFTEDA/NFTEDA.sol)

   - [MinipoolManager.sol#L606](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L606)
   - [MinipoolManager.sol#L556](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L556)
   - [MinipoolManager.sol#L480](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L480)
   - [MinipoolManager.sol#L442](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L442)
   - [MinipoolManager.sol#L411](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L411)
   - [MinipoolManager.sol#L347](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L347)

     ```solidity
     Line 606:    /// @return minipools in the protocol that adhear to the paramaters

     Line 556:    /// @return The approximate rewards the node should recieve from Avalanche for beign a validator

     Line 480:    /// @notice A staking error occured while registering the node as a validator

     Line 442:    /// @notice Re-stake a minipool, compounding all rewards recvd

     Line 411:    // Calculate rewards splits (these will all be zero if no rewards were recvd)

     Line 347:	  /// @param txID The ID of the transaction that successfully registered the node with Avalanche to become a validater
     ```

     - Consider making the following change:
       - `paramaters` To `parameters` ([Line 606](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L606))
       - `recieve` To `receive` ([Line 556](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L556))
       - `recvd` To `received` ([Line 411](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L411), [Line 442](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L442))
       - `occured` To `occurred` ([Line 480](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L480))
       - `validater` To `validator` ([Line 347](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L347))
