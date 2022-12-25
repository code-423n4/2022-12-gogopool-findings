# 1st report for : Base.sol https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Base.sol

## 1. Uninitialized Storage contract instance

Summary: 
The Base contract's constructor accepts an address of a Storage contract instance as an argument, but it does not check whether the provided address is valid or not. As a result, the contract's `gogoStorage` variable is not guaranteed to be initialized to a valid contract instance, leading to potential vulnerabilities.

Impact: 
If the `gogoStorage` variable is not initialized to a valid contract instance, any functions that rely on it will likely fail. This could cause contract execution to halt or throw exceptions, leading to potentially harmful effects for users of the contract.

Recommendation: 
It is recommended to add a check to ensure that the provided Storage contract address is valid before initializing the `gogoStorage` variable. This can be done by calling the `isContract()` function provided by the Solidity compiler, or by calling a function on the contract instance and checking the return value.

# 2nd report for : BaseAbstract.sol https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol

## 1. Lack of input validation in `getContractAddress` function

Summary:
The `getContractAddress` function in the `BaseAbstract` contract does not properly validate the input contract name passed to it. This can potentially allow an attacker to pass an arbitrary string as the contract name, potentially leading to the execution of unintended contract functions or the retrieval of sensitive information.

Impact:
An attacker may be able to execute unintended functions or retrieve sensitive information by passing an arbitrary string as the contract name to the `getContractAddress` function. This could potentially result in loss of funds or confidentiality breaches.

Recommendation:
It is recommended to add input validation to the `getContractAddress` function to ensure that only valid contract names are accepted. This can be done by comparing the input contract name to a pre-defined list of allowed contract names or by using a regex pattern to ensure that only valid characters are present in the contract name. Additionally, it may be useful to add a `require` statement to check that the contract address returned by the function is not the zero address (0x0). This can help to prevent potential vulnerabilities caused by uninitialized contract addresses.

## 2. Uninitialized variables used in `guardianOrSpecificRegisteredContract` modifier

Summary:
The `guardianOrSpecificRegisteredContract` modifier uses two uninitialized variables, `isContract` and `isGuardian`, in its check for whether the caller is a guardian or a specific registered contract. This can lead to unexpected behavior and potentially allow unauthorized parties to bypass the intended restrictions of the modifier.

Impact:
The use of uninitialized variables in the check for whether the caller is a guardian or a specific registered contract can potentially allow unauthorized parties to bypass the intended restrictions of the modifier, leading to unintended access to protected functions.

Recommendation:
Initialize the variables `isContract` and `isGuardian` before they are used in the check. The values of these variables should be determined by checking whether the caller is a registered version of the specified contract and whether the caller is the guardian, respectively. This will ensure that the intended restrictions of the `guardianOrSpecificRegisteredContract` modifier are properly enforced.

## 3. Potential infinite loop in `getBool` function

Summary: 
The `getBool` function in the `BaseAbstract` contract may potentially enter an infinite loop if the contract address of the contract name passed as an argument is not found. This can occur if the contract does not exist or if it has been removed from the storage mapping.

Impact: 
If an infinite loop is entered, it could potentially cause the contract to become unresponsive and cause transactions to hang. This could result in a denial of service for users of the contract and potentially result in losses for the contract owner.

Recommendation: 
To fix this issue, the `getBool` function should be modified to check if the contract address of the contract name passed as an argument is found before entering the loop. If the contract address is not found, the function should return false and exit. This will prevent the possibility of entering an infinite loop and ensure that the contract functions correctly.

## 4. `getBool` type error

Summary: 
In the `guardianOrSpecificRegisteredContract` and `onlySpecificRegisteredContract` modifiers, the `getBool` function is being used to retrieve a value stored in storage. However, this function is intended to be used to retrieve a boolean value, while the value being retrieved is an address. As a result, the `getBool` function will return the wrong value, leading to incorrect behavior in the contract.

Impact: 
The `guardianOrSpecificRegisteredContract` and `onlySpecificRegisteredContract` modifiers may not function as intended, potentially allowing unauthorized calls to pass the modifier checks and execute sensitive functions.

Recommendation: 
Replace the `getBool` function calls with `getAddress` in the `guardianOrSpecificRegisteredContract` and `onlySpecificRegisteredContract` modifiers. This will correctly retrieve the stored address values and ensure that the modifier checks function as intended.

## 5. Incorrect error message thrown when calling `onlyRegisteredNetworkContract()` and `onlySpecificRegisteredContract()`

Summary:
The `onlyRegisteredNetworkContract()` and `onlySpecificRegisteredContract()` modifiers throw the `InvalidOrOutdatedContract` error if the calling contract is not registered as a network contract. However, the error message does not accurately describe the issue. If the calling contract is not registered, it would be more accurate to throw the `ContractNotFound` error.

Impact:
The incorrect error message could lead to confusion for developers trying to understand the issue causing the revert. It could also make it more difficult for developers to troubleshoot and fix the issue.

Recommendation:
To improve the accuracy of the error message and make it easier for developers to understand and fix the issue, the `InvalidOrOutdatedContract` error should be replaced with the `ContractNotFound` error for calls to `onlyRegisteredNetworkContract()` and `onlySpecificRegisteredContract()`.

## 6. Revert on an uninitialized Storage contract

Summary:
The contract `BaseAbstract` uses an `internal` `gogoStorage` contract of type `Storage`, which means that it is not initialized in the contract's constructor. If a function in `BaseAbstract` is called that uses `gogoStorage`, it will attempt to call a function on an uninitialized contract, which will cause the contract to revert.

Impact:
This vulnerability can lead to contract failure and loss of funds if an attacker is able to execute a function that uses `gogoStorage` before it is initialized.

Recommendation:
To fix this vulnerability, initialize `gogoStorage` in the `BaseAbstract` contract's constructor. This can be done by adding the following line to the constructor:
```
gogoStorage = new Storage();
```
# 3rd report for : MultisigManager.sol https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol

## 1. Misuse of guardianOrSpecificRegisteredContract function allows unauthorized users to disable multisig addresses

Summary: 
The function `disableMultisig` allows unauthorized users to disable multisig addresses if they are the contract address "Ocyticus". This is due to the use of the `guardianOrSpecificRegisteredContract` function, which should only allow guardians or the specified contract to execute the function.

Impact: 
This vulnerability allows unauthorized users to disable multisig addresses, potentially disrupting the functionality of the protocol and causing issues for users who rely on these addresses.

Recommendation: 
The `guardianOrSpecificRegisteredContract` function should be updated to only allow guardians to execute the `disableMultisig` function, rather than also allowing the contract "Ocyticus". The function should also include a check to ensure that the contract "Ocyticus" is indeed a registered multisig address before allowing it to disable other multisig addresses.

# 4th report for : Ocyticus.sol https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol

## 1. Incorrectly implemented pause functionality

Summary: 
The `pauseEverything()` and `resumeEverything()` functions in the `Ocyticus` contract allow an address in the `defenders` mapping to pause and resume certain contracts in the protocol. However, there is no mechanism in place to ensure that these functions are only called in emergency situations or with the consensus of multiple parties. This could allow a malicious actor with access to a `defenders` address to disrupt the operation of the protocol at will.

Impact: 
Allowing a single actor to pause and resume important contracts in the protocol could have significant negative effects on the operation and stability of the protocol. This could lead to financial losses for users and damage to the reputation of the protocol.

Recommendation: 
It is important to ensure that any functionality that can potentially disrupt the operation of the protocol is implemented in a secure and controlled manner. One way to do this would be to implement a multi-sig mechanism that requires the consensus of multiple parties before the `pauseEverything()` and `resumeEverything()` functions can be called. Alternatively, the `defenders` mapping could be replaced with a more secure mechanism for granting access to these functions, such as a voting system or a time-locked contract.

# 5th report for : Oracle.sol https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Oracle.sol

## 1. Improper handling of invalid timestamps in the setGGPPriceInAVAX function

Summary: 
The setGGPPriceInAVAX function does not properly handle invalid timestamps. It will accept a timestamp that is earlier than the last recorded timestamp, and will also accept a timestamp that is later than the current block's timestamp. This can allow an attacker to manipulate the recorded price of GGP by submitting a transaction with an invalid timestamp.

Impact: 
This vulnerability could allow an attacker to manipulate the recorded price of GGP, potentially leading to incorrect pricing for related contracts or functions that rely on this information.

Recommendation: 
The setGGPPriceInAVAX function should be modified to only accept timestamps that are greater than or equal to the last recorded timestamp, and less than or equal to the current block's timestamp. This will ensure that only valid timestamps are accepted, and prevent an attacker from manipulating the recorded price of GGP.

# 6th report for : ProtocolDAO.sol https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol

## 1. ValueNotWithinRange error not thrown when setterValue is above 1 ether

Summary: 
The ProtocolDAO contract has a modifier called `valueNotGreaterThanOne` that checks whether the input value `setterValue` is above 1 ether. If it is, the `ValueNotWithinRange` error is supposed to be thrown. However, this error is not thrown due to a missing `revert` statement in the body of the `if` statement. As a result, the check for `setterValue` being above 1 ether is ineffective.

Impact: 
The impact of this vulnerability is limited as it only affects the `pauseContract` and `unpauseContract` functions. These functions can still be used as intended, but the check for `setterValue` being above 1 ether is not enforced. This could potentially allow a malicious actor to bypass the intended limits on these functions, but it is unlikely to have a significant impact on the overall functioning of the contract.

Recommendation: 
To fix this vulnerability, the `revert` statement should be added to the body of the `if` statement in the `valueNotGreaterThanOne` modifier. This will ensure that the `ValueNotWithinRange` error is properly thrown when `setterValue` is above 1 ether.

## 2. Reentrancy vulnerability in the ProtocolDAO contract

Summary: 
The `ProtocolDAO` contract contains a reentrancy vulnerability in the `pauseContract` function. The `pauseContract` function allows a caller to pause any contract by calling `setBool` with the key `keccak256(abi.encodePacked("contract.paused", contractName))` and the value `true`. However, the contract being paused may call back into the `ProtocolDAO` contract before the state is updated, allowing an attacker to call functions in the paused contract multiple times before the contract is actually paused. This can potentially allow an attacker to drain funds or manipulate state in the paused contract.

Impact: 
An attacker could use this vulnerability to drain funds or manipulate state in any contract that can be paused using the ProtocolDAO contract.

Recommendation: 
To fix this vulnerability, the `pauseContract` function should use a mutex or some other method to ensure that the contract being paused is not able to call back into the `ProtocolDAO` contract before the state is updated.

## 3. Incorrect type for inflation rate

Summary: 
The `setUint` function call on line 41 of the provided code sets the value of the `ProtocolDAO.InflationIntervalRate` key to a value that is much larger than the maximum value that can be stored in a `uint256 (2^256 - 1)`. This will cause the value to be stored incorrectly, potentially leading to incorrect calculations or error messages later on.

Impact: 
Incorrect calculations or errors when using the inflation rate value stored in the contract's storage. This could have consequences for the contract's users and potentially lead to financial losses.

Recommendation: 
The value being set for the `ProtocolDAO.InflationIntervalRate` key should be a `uint256`. To avoid this issue, the value should be explicitly cast to a `uint256` using the `uint256` type before being passed to the `setUint` function. Alternatively, the value could be divided by a power of 10 to bring it within the range of a `uint256`.

# 7th report for : RewardsPool.sol https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol

## 1. IncorrectRewardsDistribution error thrown when distribution exceeds total rewards

Summary: 
The contract has an error condition in the "distributeRewards" function that throws an IncorrectRewardsDistribution error if the distribution amount exceeds the total rewards amount. However, the condition that checks if the distribution amount is less than the total rewards amount is written incorrectly, causing the error to be thrown when the distribution amount is actually equal to or less than the total rewards amount.

Impact: 
If the distribution amount is equal to or less than the total rewards amount, the "distributeRewards" function will not execute as expected and will throw an IncorrectRewardsDistribution error. This will prevent the intended distribution of rewards to the specified recipients.

Recommendation: 
The condition that checks if the distribution amount is less than the total rewards amount should be updated to use the "less than or equal to" operator ( <= ) instead of the "less than" operator ( < ). This will allow the "distributeRewards" function to execute as intended when the distribution amount is equal to the total rewards amount.

# 8th report for : Storage.sol https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol

## 1. Potential vulnerability in `setGuardian` function allows unauthorized change of guardian address.

Summary:

The `setGuardian` function allows anyone who is the current guardian to change the guardian address to a new address. This could potentially be exploited by an attacker to gain full control over the contract.

Impact:

If an attacker is able to successfully change the guardian address to their own, they would have full control over the contract and could potentially cause significant financial losses for the contract's users.

Recommendation : 

To address this vulnerability, the `setGuardian` function should be modified to only allow the guardian address to be changed by a trusted third party or through a secure and transparent process. This would ensure that only authorized parties are able to change the guardian address, reducing the risk of unauthorized control of the contract.

## 2. Potential vulnerability in `onlyRegisteredNetworkContract` modifier allows unauthorized disruption of contract functions.

Summary:

The `onlyRegisteredNetworkContract` modifier checks the boolean value stored at the keccak256 hash of `contract.exists` and the address of the contract calling the function. If this value is false, it will revert the function with the `InvalidOrOutdatedContract` error. This could potentially be exploited by an attacker who is able to change the value stored at this key to false, allowing them to disrupt the contract's functions.

Impact:

If an attacker is able to successfully change the value stored at this key to false, they could potentially disrupt the contract's functions. This could lead to issues with the contract's operations and potentially cause financial losses for the contract's users.

Recommendation:

To address this vulnerability, the `onlyRegisteredNetworkContract` modifier should be modified to use a more secure method of verifying the contract's status. This could include using a trusted third party to verify the contract's status, or implementing a secure and transparent process for updating the contract's status. This would ensure that only authorized parties are able to disrupt the contract's functions, reducing the risk of unauthorized disruptions.

# 9th report for : Vault.sol https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol

## 1. Reentrancy vulnerability in `Vault` contract

Summary: 
The `Vault` contract contains a reentrancy vulnerability in the `withdrawAVAX` function. The `IWithdrawer` contract is called with a call to `receiveWithdrawalAVAX`, which could potentially perform further calls to the `Vault` contract, leading to an infinite loop. This could allow an attacker to drain the contract of its AVAX balance.

Impact: 
An attacker could exploit this vulnerability to drain the `Vault` contract of its AVAX balance, leading to financial loss for the contract owner.

Recommendation: 
To fix this vulnerability, the call to `IWithdrawer` should be made using a `transfer` or `send` function, which do not allow for reentrancy attacks. Alternatively, the `ReentrancyGuard` contract could be used to protect against reentrancy attacks.

# 10th report for : TokenggAVAX.sol https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol

## 1. Uninitialized `lastSync` and `rewardsCycleEnd` variables

Summary: 
The `lastSync` and `rewardsCycleEnd` variables in the code are not initialized before they are used in the `updateCycleEnd` function. This can lead to unexpected behavior and potential vulnerabilities.

Impact: 
The uninitialized values of `lastSync` and `rewardsCycleEnd` could potentially result in incorrect calculations and unintended behavior in the contract. This could lead to incorrect rewards distributions, incorrect staking deposits and withdrawals, and potentially even allow malicious actors to exploit the contract.

Recommendation: 
Initialize the `lastSync` and `rewardsCycleEnd` variables before they are used in the `updateCycleEnd` function. This can be done by setting their values in the contract's constructor or by adding an initializer function to set their values. It is also recommended to add appropriate checks and safeguards to prevent unauthorized access to these variables.

## 2. Interface check missing in `withdrawForStaking` function

Summary: 
The `withdrawForStaking` function in the `TokenggAVAX` contract is missing a check to ensure that the `_withdrawer` contract implements the `IWithdrawer` interface before calling the `withdraw` function. This could allow an attacker to pass in a malicious contract that does not implement the `IWithdrawer` interface, potentially leading to an exception being thrown and disrupting the contract's functionality.

Impact: 
An attacker could exploit this vulnerability by passing in a malicious contract as the `_withdrawer` parameter in the `withdrawForStaking` function. This could potentially lead to an exception being thrown and disrupting the contract's functionality.

Recommendation: 
To address this vulnerability, add a check to ensure that the `_withdrawer` contract implements the `IWithdrawer` interface before calling the `withdraw` function. This can be done using the `require` function to ensure that the `IWithdrawer` contract has been correctly implemented by the `_withdrawer` contract.

# 11th report for : ERC20Upgradeable.sol https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol

## 1. Uncontrolled ERC20 token transfer vulnerability

Summary: 
The function `transfer` in the contract `ERC20Upgradeable` does not have any restrictions on who can call it, allowing anyone to transfer the tokens of any user.
This can lead to unauthorized transfer of tokens and potential financial loss for the token holders.

Impact: 
This vulnerability allows anyone to transfer the tokens of any user, leading to unauthorized transfer of tokens and potential financial loss for the token holders.

Recommendation: 
The function `transfer` should include a check to ensure that only the owner of the tokens being transferred is able to initiate the transfer. This can be done by adding a require statement at the beginning of the function, such as `require(msg.sender == from, "Only the owner of the tokens can initiate a transfer");`.

## 2. Reentrancy Attack in ERC20Upgradeable Contract

Summary:

The `transferFrom` function in the `ERC20Upgradeable` contract is vulnerable to reentrancy attacks. A malicious contract can repeatedly call the `transferFrom` function with an increasing nonce value and receive an increasing amount of tokens from the contract. This can potentially result in a loss of funds for the contract.

Impact:

A successful reentrancy attack on the ERC20Upgradeable contract can result in a loss of funds for the contract and its users.

Recommendation:

To mitigate the risk of reentrancy attacks, it is recommended to implement the `checks-effects-interactions` pattern in the `transferFrom` function. This can be achieved by first checking the allowance and balance of the `from` address, and then updating the allowance and balance values. This ensures that the `transferFrom` function is not vulnerable to reentrancy attacks.

## 3. ERC20Upgradeable contract allows for unlimited approvals

Summary:
The ERC20Upgradeable contract allows users to grant unlimited approvals to other users to transfer their tokens. This can be exploited by malicious users to drain the balance of a victim's account.

Impact:
If a malicious user is able to grant themselves unlimited approvals, they can transfer all of the victim's tokens to themselves, leading to financial loss for the victim.

Recommendation:
To fix this vulnerability, the ERC20Upgradeable contract should check that the amount being approved is not equal to the maximum uint256 value. This can be done by adding a simple check in the `approve()` function:
```
function approve(address spender, uint256 amount) public virtual returns (bool) {
    require(amount != type(uint256).max, "Cannot grant unlimited approvals");

    allowance[msg.sender][spender] = amount;

    emit Approval(msg.sender, spender, amount);

    return true;
}
```
Alternatively, the contract could include a mechanism to allow users to update or revoke their approvals, such as a `modifyApproval()` function that allows users to set a new approval amount or set it to zero. This would give users more control over their approvals and prevent unlimited approvals from being granted.

# 12th report for : ERC4626Upgradeable.sol https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol

## 1. Depositing a 0 amount of assets results in an error

Summary:
The `deposit` function in the `ERC4626Upgradeable` contract includes a requirement that the number of shares returned by the `previewDeposit` function is not 0. However, the `previewDeposit` function does not handle the case where the input amount of assets is 0, which leads to a requirement failure and an error when attempting to deposit 0 assets.

Impact:
This vulnerability allows a malicious actor to cause the `deposit` function to throw an error and fail when attempting to deposit 0 assets. This could potentially disrupt the intended functionality of the contract and cause issues for users trying to deposit 0 assets.

Recommendation:
To fix this vulnerability, the `previewDeposit` function should handle the case where the input amount of assets is 0 and return 0 shares in this case. The requirement in the `deposit` function that checks that the number of shares returned by `previewDeposit` is not 0 should also be removed to allow for the deposit of 0 assets.

## 2. Possible Re-entrancy Attack in ERC4626Upgradeable Contract

Summary: 
The ERC4626Upgradeable contract contains a potential re-entrancy vulnerability in the `deposit` and `mint` functions. The contract calls the `safeTransferFrom` function of the `asset` contract before calling the `_mint` function, which could allow an attacker to re-enter the contract through the `safeTransferFrom` function if it is not implemented correctly. This could potentially allow an attacker to manipulate the balance and share distribution of the ERC4626Upgradeable contract.

Impact: 
If exploited, this vulnerability could allow an attacker to manipulate the balance and share distribution of the ERC4626Upgradeable contract, potentially leading to financial losses for users of the contract.

Recommendation: 
To fix this vulnerability, the `_mint` function should be called before the `safeTransferFrom` function in the `deposit` and `mint` functions. The `safeTransferFrom` function should also be implemented with a mutex to prevent re-entrancy attacks.

## 3. Uninitialized Variable in ERC4626Upgradeable Contract

Summary:
The ERC4626Upgradeable contract contains an uninitialized variable in the deposit and mint functions, which can potentially cause unintended behavior in the contract.

Impact:
If the assets variable is not properly initialized before being passed to the safeTransferFrom function, it could result in unexpected behavior, such as transferring a different amount of assets than intended. This could potentially lead to financial losses for the users of this contract.

Recommendation:
It is recommended to initialize the assets variable before passing it to the safeTransferFrom function in the deposit and mint functions. This can be done by assigning a value to the assets variable before it is used.

Example:
```
function deposit(uint256 assets, address receiver) public virtual returns (uint256 shares) {
// Initialize assets variable
assets = 0;

// Check for rounding error since we round down in previewDeposit.
require((shares = previewDeposit(assets)) != 0, "ZERO_SHARES");

// Need to transfer before minting or ERC777s could reenter.
asset.safeTransferFrom(msg.sender, address(this), assets);

_mint(receiver, shares);

emit Deposit(msg.sender, receiver, assets, shares);

afterDeposit(assets, shares);
}

function mint(uint256 shares, address receiver) public virtual returns (uint256 assets) {
// Initialize assets variable
assets = 0;

assets = previewMint(shares); // No need to check for rounding error, previewMint rounds up.

// Need to transfer before minting or ERC777s could reenter.
asset.safeTransferFrom(msg.sender, address(this), assets);

_mint(receiver, shares);

emit Deposit(msg.sender, receiver, assets, shares);

afterDeposit(assets, shares);
}
```