## [NAZ-L1] `receive()` Function Should Emit An Event
**Severity**: Low
**Context**: [`TokenggAVAX.sol#L82`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L82)

**Description**:
Consider emitting an event inside this function with `msg.sender` and `msg.value` as the parameters. This would make it easier to track incoming ether transfers.

**Recommendation**:
Consider adding events to the `receive()` functions. 


## [NAZ-L2] Missing Events In Initialize Functions
**Severity**: Low
**Context**: [`ProtocolDAO.sol#L23`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L23), [`RewardsPool.sol#L34`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L34), [`TokenggAvax.sol#L72`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L72)

**Description**:
None of the initialize functions emit emit init-specific events. They all however have the initializer modifier (from Initializable) so that they can be called only once. Off-chain monitoring of calls to these critical functions is not possible.

**Recommendation**:
It is recommended to emit events in your initialization functions.


## [NAZ-N1] Unused Errors
**Severity**: Informational
**Context**: [`Vault.sol#L26-L28`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L26-L28)

**Description**:
There is no point to having these errors in the contract and should be removed or used.

**Recommendation**:
Consider removing the unused errors or using them.

## [NAZ-N2] Code Contains Empty Blocks
**Severity**: Informational
**Context**: [`MinipoolManager.sol#L160`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L160), [`TokenggAVAX.sol#L255`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/TokenggAVAX.sol#L255), [`ERC4626Upgradeable.sol#L176-L178`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L176-L178)

**Description**:
It's best practice that when there is an empty block, to add a comment in the block explaining why it's empty.

**Recommendation**:
Consider adding `/* Comment on why */` to the empty blocks.


## [NAZ-N3] Line Length
**Severity**: Informational
**Context**: [`BaseAbstract.sol#L73`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L73), [`MinipoolManager.sol#L53`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L53), [`MinipoolManager.sol#L191`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L191), [`MinipoolManager.sol#L285`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L285), [`MinipoolManager.sol#L294`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L294), [`MinipoolManager.sol#L317`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L317), [`MinipoolManager.sol#L321`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L321), [`MinipoolManager.sol#L329`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L329), [`MinipoolManager.sol#L371`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L371), [`MinipoolManager.sol#L399`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L399), [`MinipoolManager.sol#L417`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L417), [`MinipoolManager.sol#L421`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L421), [`MinipoolManager.sol#L432`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L432), [`MinipoolManager.sol#L490`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L490), [`MinipoolManager.sol#L598`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L598), [`MultisigManager.sol#L67`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L67), [`ProtocolDAO.sol#L33`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L33), [`ProtocolDAO.sol#L41`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L41), [`ProtocolDAO.sol#L96`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L96), [`ProtocolDAO.sol#L107`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L107), [`ProtocolDAO.sol#L174`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/ProtocolDAO.sol#L174), [`Staking.sol#L110`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L110), [`Staking.sol#L117`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L117), [`Staking.sol#L133`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L133), [`Staking.sol#L140`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L140), [`Staking.sol#L180`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L180), [`Staking.sol#L187`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L187), [`Staking.sol#L203`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L203), [`Staking.sol#L224`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L224), [`Staking.sol#L231`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L231), [`Staking.sol#L248`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L248), [`Staking.sol#L328`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L328), [`Staking.sol#L379`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L379), [`Staking.sol#L413`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L413), [`Vault.sol#L104`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L104)

**Description**:
Max line length must be no more than 120 but many lines are extended past this length.

**Recommendation**:
Consider cutting down the line length below 120.


## [NAZ-N4] TODOs Left In The Code
**Severity**: Informational
**Context**: [`BaseAbstract.sol#L6`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L6), [`MinipoolManager.sol#L412`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L412), [`Staking.sol#L203`](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L203)

**Description**:
There should never be any TODOs in the code when deploying.

**Recommendation**:
Consider finishing the TODOs before deploying.


## [NAZ-N5] Missing or Incomplete NatSpec
**Severity**: Informational
**Context**: [`All Contracts`](https://github.com/code-423n4/2022-12-gogopool/tree/main/contracts)

**Description**:
Some functions are missing @notice/@dev NatSpec comments for the function, @param for all/some of their parameters and @return for return values. Given that NatSpec is an important part of code documentation, this affects code comprehension, auditability and usability.

**Recommendation**:
Consider adding in full NatSpec comments for all functions to have complete code documentation for future use.