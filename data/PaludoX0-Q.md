## LOW
#### Avoid check of amt > 0 in modifier whenTokenNotPaused 
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L58
Check of `amt > 0` in `modifier whenTokenNotPaused` is misleading because user will not understand if contract is paused if he is trying to call functions with amt == 0
Moreover will burn some gas for nothing because the user cannot interact with token in any case if contract is paused.
Suggest to remove the check


#### Constructor variables are not validated
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Base.sol#L9
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/MinipoolManager.sol#L177
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimNodeOp.sol#L29

Address inputs  in constructor should be validated, at least in Base.sol which is inherited by most of the protocol contracts


#### restakeGGP() doesn't have whenNotPaused() modifier
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/Contracts/contract/Staking.sol#328
At line 319 `stakeGGP()` has `whenNotPaused()` modifier, while `restakeGGP()` has not. Suggest to add the same modifier to `restakeGGP()`.


#### getStakers() view function doesn't return  avaxAssignedHighWater
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/Contracts/contract/Staking.sol#L405
In staking.getStaker the following staker inforamtion is not returned:
`staker.avaxAssignedHighWater = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssignedHighWater")));`


#### No Storage Gap for Contract BaseUpgradeable.sol 
BaseUpgradeable is intended to be upgradeable and for upgradeable contracts, there must be storage gap to "allow developers to freely add new state variables in the future without compromising the storage compatibility with existing deployments" 
Missing storage gaps for upgradeable contracts might lead to storage slot collision
Recommend adding appropriate storage gap at the end of upgradeable contracts such as the below. Please reference OpenZeppelin upgradeable contract templates.
`uint256[50] private __gap;`



## Informational

#### MinipoolMaxAVAXAssignment should be 1.000 avax
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ProtocolDAO.sol#L49
As explained in documentation and discord chat, at the beginning the min and max Avax assignment will be 1000 ether, while in ProtocolDAO initializiation MinipoolMaxAVAXAssignment is set to 10.000 avax


#### Different compiler versions 
/home/ubuntu_distro/code4rena/2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L2
/home/ubuntu_distro/code4rena/2022-12-gogopool/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L2
Mixing pragma is not recommended. Different compiler versions have different meanings and behaviors. 
As a result, depending on the compiler version selected for any given file, deployed contracts may have security issues
