1 - MultisigManager.sol::enableMultisig() function consider a two-phase activation
==

The [enableMultisig](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MultisigManager.sol#L55) allows to enable the multisig by the guardian. The risk is that guardian can register wrong addresses with the ```registerMultisig()``` function and reach the [multisig limit](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MultisigManager.sol#L41).

Recommendation
--
Consider a two step process where the guardian register the multisig (pre-multisig address) and the pre-multisig address need to call an ```acceptEnableMultisig()``` function for the multisig activation.

2 - Native ether value lockout in the MinipoolManager.sol::receiveWithdrawalAVAX() function
==
The MinipoolManager.sol contract can receive Native Ether through [receiveWithdrawalAVAX()](https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L106) function. If someone sends accidentally/intentionally native ETH value, the eth value will be lockout because the payable functions don't control the native msg.value and is not possible to withdraw the Avax.

Recommendation
--
The ```receiveWithdrawalAVAX()``` function is called by ```TokenggAVAX.sol``` and ```Vault.sol``` so it could be possible to add a modifier that receiveWithdrawalAVAX() could be called only by TokenGggAvax.sol and Vault.sol