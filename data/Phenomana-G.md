https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol

To optimize the gas usage of this smart contract, there are a few things that you can consider:

Use the bytes32 type instead of string for the name and symbol fields, as it is more gas efficient.
Consider using the SafeERC20 contract from the OpenZeppelin library for the ERC20 functionality, as it includes gas-efficient implementations of the ERC20 functions.
Make sure to mark the functions that don't modify the contract state with the view or pure keywords, as this will allow the EVM to optimize the gas usage for these functions.
Consider using the unchecked keyword for the transfer and transferFrom functions, as it allows the EVM to skip the overflow check, which can save gas. However, be aware that this can result in unintended behavior if the sum of the balances exceeds the maximum value of a uint256 (2^256 - 1).
Use the Initializable contract from the OpenZeppelin library for the Initializable functionality, as it provides a gas-efficient implementation for upgradeability.
Consider using the SafeMath library from the OpenZeppelin library for arithmetic operations, as it includes gas-efficient implementations of common arithmetic functions and automatically handles overflow and underflow conditions.
Consider using a struct to store the name, symbol, and decimals fields, as this can reduce the storage cost and save gas.
Consider using an array or mapping to store the balanceOf and allowance fields, as this can reduce the storage cost and save gas.
Use the chainid and blockhash built-in functions instead of calling computeDomainSeparator, as this will be more gas-efficient.
Consider using the SafeERC2612 contract from the OpenZeppelin library for the EIP-2612 functionality, as it includes a gas-efficient implementation of the EIP-2612 functions.
It is also a good idea to test the gas usage of the contract on the Ethereum network using a tool such as Remix or Truffle to see how it performs in a real-world scenario and identify any potential issues.