In ClaimProtocolDAO.sol `https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ClaimProtocolDAO.sol`.

It seems that the vault.withdrawToken() function (in the spend() function) without any error checking. 
It means that if the vault.withdrawToken() function fails or returns an error for any reason, it is not handled by the spend() function. 

For example, if the vault.withdrawToken() function fails to transfer the token (GGP), the spend() function will not be able to detect the failure and will continue to execute as if the transfer was successful.

---

In TokenggAVAX.sol `https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L88-#L109`, 
there is no error handling for handling cases where nextRewardsAmt overflows a uint192. 
It may cause unexpected result if the contract is used in a way that causes nextRewardsAmt to exceed the maximum value that can be stored in a uint192.

---

In MinipoolManager.sol `https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L342`

The function transfers funds to msg.sender without any checks on the recipient's address. 
It would be great if adding checking to ensure that the recipient is a valid address.



