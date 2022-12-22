-------------------------------------------------------------------------------------------------------------------------------
Issue : Re-entrant guard at Vault.sol #61 is not really required as internal balances are updated before sending Avax.

Code: https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L61

Fix :  Remove the re-entrancy guard as it's just increasing gas usage

-------------------------------------------------------------------------------------------------------------------------------
Issue : Re-entrant guard at Vault.sol #141 is not really required as internal balances are updated before sending Token.

Code: https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L141

Fix :  Remove the re-entrancy guard as it's just increasing gas usage

-------------------------------------------------------------------------------------------------------------------------------

Issue :  In minipool manager in function requireValidStateTransition while checking state transition is valid, In else case we are setting isValid as False. This is not required as as it's initialized as False.  This will reduce the bytecode as well reduce gas consumption for method  call

Code: https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L169

Fix :  Remove the else block in Line#168. It's redundant. That is, change Code to as below :

'''

			isValid = (to == MinipoolStatus.Prelaunch);
		} else {
			isValid = false;
		}

		if (!isValid) {
			revert InvalidStateTransition();
		}
'''
                         TO
'''

			isValid = (to == MinipoolStatus.Prelaunch);
		}

		if (!isValid) {
			revert InvalidStateTransition();
		}
'''
-------------------------------------------------------------------------------------------------------------------------------

Issue :  In minipool manager in function createMinipool, msg.value + assignmentRequest are summed. Both must be equal as per revert in MiniPoolManager.sol#213. Thus, this can be replaced with 2*msg.value. This will take less gas. Addition would invoke 2 MLOAD and 1 ADD opcode. ADD opcode takes 3 gas. And Mload aslo takes 3 gas. Totaling to 9 gas. Whereas If we multiply, It would invoke 1MLOAD and 1 MUL(5 Gas) Opcode totaling to 8 gas.

Code: https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L216

Fix :  Change Code to as below :

'''

		if (msg.value + avaxAssignmentRequest < dao.getMinipoolMinAVAXStakingAmt()) {
'''
                         TO
'''


		if (2*msg.value  < dao.getMinipoolMinAVAXStakingAmt()) {
'''

------------------------------------------------------------------------------------------------------------------------------

Issue : Internal Variable gogoStorage is being set only in constructor. Can be made immutable

Code: https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L21

Fix :  Change this piece of code to "Storage internal immutable gogoStorage;"

------------------------------------------------------------------------------------------------------------------------------