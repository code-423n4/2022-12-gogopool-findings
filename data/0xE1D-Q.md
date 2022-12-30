Inside contract ERC20Upgradeable.sol, method transferFrom(address from, address to, uint256 amount) can be changed to transferFrom(address from, address to, uint256 memory amount).

You can use the memory keyword for variables and function parameters to reduce gas costs because memory variables are stored in a temporary area of the contract's memory, which is cheaper to access than storage.