## [G-01]
File: ClaimProtocolDAO.sol

URL: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/ClaimProtocolDAO.sol#L21

Summary: invoiceID of string data type should be converted to bytes or size 32 if invoiceID is less than or equal to 32 digits or bytes in length;

Line 21 PoC: 
```
string memory invoiceID;
```
Remidiation: 
```
bytes32 memory invoiceID;
```

## [G-02]
The following text string should be assigned to a bytes32 variable to save gas. "GoGoPool Liquid Staking Token"
PoC:  __ERC4626Upgradeable_init(asset, "GoGoPool Liquid Staking Token", "ggAVAX");
URL: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/TokenggAVAX.sol#L73
Remediation: use assigned variable of butes32

## [G-03]
The following text string should be assigned to a bytes32 variable to save gas. "PERMIT_DEADLINE_EXPIRED"
require(deadline >= block.timestamp, "PERMIT_DEADLINE_EXPIRED");
URL: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L127
Remediation: use assigned variable of butes32

## [G-04]
The following text string should be assigned to a bytes32 variable to save gas. "INVALID_SIGNER"
require(recoveredAddress != address(0) && recoveredAddress == owner, "INVALID_SIGNER");
URL: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L154
Remediation: use assigned variable of butes32

## [G-05]
The following text string should be assigned to a bytes32 variable to save gas. "ZERO_SHARES"
require((shares = previewDeposit(assets)) != 0, "ZERO_SHARES");
URL: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L44
Remediation: use assigned variable of butes32

## [G-06]
The following text string should be assigned to a bytes32 variable to save gas. "ZERO_ASSETS"
require((assets = previewRedeem(shares)) != 0, "ZERO_ASSETS");
URL: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L103
Remediation: use assigned variable of butes32

## [G-07]
Use arguments instead of state variables.
PoC:  emit GGPInflated(newTokens);
URL: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L94
Remediation: Use arguments instead of state variables.

## [G-08]
Use arguments instead of state variables.
PoC:  emit ProtocolDAORewardsTransfered(daoClaimContractAllotment);
URL: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L182
Remediation: Use arguments instead of state variables

## [G-09]
Use arguments instead of state variables.
PoC:  emit MultisigRewardsTransfered(multisigClaimContractAllotment);
URL: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L187
Remediation: Use arguments instead of state variables

## [G-10]
Use arguments instead of state variables.
PoC: emit ClaimNodeOpRewardsTransfered(nopClaimContractAllotment);
URL: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/RewardsPool.sol#L192
Remediation: Use arguments instead of state variables

##[G-11]
Unsinged integers lower than 32 bytes are more costly because solidity automatically assigns 32 bytes minimum to unsigned integers. 
PoC:   uint8 public version;
URL: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/BaseAbstract.sol#L19
remediation: use uint256 and then downcast.

##[G-12]
Unsinged integers lower than 32 bytes are more costly because solidity automatically assigns 32 bytes minimum to unsigned integers. 
PoC:  uint8 public decimals;
URL: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L27
remediation: use uint256 and then downcast.

##[G-13]
Unsinged integers lower than 32 bytes are more costly because solidity automatically assigns 32 bytes minimum to unsigned integers. 
PoC:  uint8 _decimals
URL: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L56
remediation: use uint256 and then downcast.

##[G-14]
Unsinged integers lower than 32 bytes are more costly because solidity automatically assigns 32 bytes minimum to unsigned integers. 
PoC:  uint8 v,
URL: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L123
remediation: use uint256 and then downcast.

## [G-15]
Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate
File: ERC20Upgradeable.sol
line 35
mapping(address => uint256) public balanceOf;
URL: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L35
line 37
mapping(address => mapping(address => uint256)) public allowance;
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L37
If both fields are accessed in the same function, can save gas per access due to not having to recalculate the key and calculation's associated stack operations.
balanceOf and allowance are both being used in the same functions mostly consider making them a struct instead. 
Both fileds are accessed in the same function as follows:
URL: https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L92-L112
```
function transferFrom(
		address from,
		address to,
		uint256 amount
	) public virtual returns (bool) {
		uint256 allowed = allowance[from][msg.sender]; // Saves gas for limited approvals.

		if (allowed != type(uint256).max) allowance[from][msg.sender] = allowed - amount;

		balanceOf[from] -= amount;

		// Cannot overflow because the sum of all user
		// balances can't exceed the max uint256 value.
		unchecked {
			balanceOf[to] += amount;
		}

		emit Transfer(from, to, amount);

		return true;
	}
```
Remediation: use struct data type for the two mapping fields.