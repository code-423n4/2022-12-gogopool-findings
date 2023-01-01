## Summary
### Low Risk Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
|[L-01]|  Dividing before multiplying creates a rounding problem| 1 |
|[L-02]| ERC4626Upgradeable 's implmentation is not fully up to EIP-4626's specification | 1 |
|[L-03]| Front running attacks by the `onlyMultisig` | 1 |
|[L-04]| There is a risk that the `price` variable is accidentally initialized to a little  and platform loses Money| 1 |
|[L-05]| Solmate's SafeTransferLib doesn't check whether the ERC20 contract exists| 4 |
|[L-06]| A single point of failure  | 1 |
|[L-07]| Loss of precision due to rounding  | 1 |
|[L-08]| initialize() function can be called by anybody| 1 |
|[L-09]| No Storage Gap for `ERC4626Upgradeable` | 1 |
|[L-10]| Signature Malleability of EVM's ecrecover()| 1 |
|[L-11]| msg.sender not logged when depositing ERC20 token contract with `depositToken` function| 1 |
|[L-12]| Missing Event for critical parameters init and change| 10 |
|[L-13]| Use `2StepSetGuardian` instead of ` setGuardian `| 1 |
|[L-14]| Lack of Input Validation| 1 |
|[L-15]| Allows malleable SECP256K1 signatures| 1 |
|[L-16]| There is inconsistency and shadowing between `GGPSlashed` event argument names and emit argument names | 1 |

Total 16 issues

### Non-Critical Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
| [N-01]|Insufficient coverage|1|
| [N-02] |Critical Address Changes Should Use Two-step Procedure |2|
| [N-03] |Initial value check is missing in Set Functions| 7 |
| [N-04] |NatSpec comments should be increased in contracts | All Contracts |
| [N-05] |`Function writing` that does not comply with the `Solidity Style Guide` | All Conracts|
| [N-06] |Add a timelock to critical functions| 2|
| [N-07] |Solidity compiler optimizations can be problematic|  |
| [N-08] |For modern and more readable code; update import usages | 13 |
| [N-09] |Include return parameters in NatSpec comments | All Contracts |
| [N-10] |It's confusing that Emit has both `caller` and `owner` msg.sender| 1  |
| [N-11] |Long lines are not suitable for the ‘Solidity Style Guide’ | 11 |
| [N-12] |Avoid _shadowing_ `inherited state variables` | 1 |
| [N-13] |Constant values such as a call to `TENTH` should used to immutable rather than constant| 1 |
| [N-14] |Need Fuzzing test| 5 |
| [N-15] |Test environment comments and codes should not be in the main version | 3 |
| [N-16] |Use of bytes.concat() instead of abi.encodePacked()| 20 |
| [N-17] |For functions, follow Solidity standard naming conventions (internal function style rule)| 32 |
| [N-18] |Omissions in Events| 1 |
| [N-19] |Open TODOs| 3 |
| [N-20] |Mark visibility of initialize(...) functions as ``external``| 1 |
| [N-21] |Use underscores for number literals| 1 |
| [N-22] |Pragma version^0.8.17  version too recent to be trusted|  |
| [N-23] |Showing the actual values of numbers in NatSpec comments makes checking and reading code easier| 5 |
| [N-24] |Lack of event emission after critical `initialize()` function| 3 |
| [N-25] |`Empty blocks` should be _removed_ or _Emit_ something| 2 |
| [N-26] |Use `require` instead of `assert`| 1 |
| [N-27] |Implement some type of version counter that will be incremented automatically for contract upgrades| 1 |
| [N-28] |Highest risk must be specified in NatSpec comments and documentation| 1 |
| [N-29] |There is no need to cast a variable that is already an address, such as address(x)| 1 |
| [N-30] |`0 address` check for `_asset`| 1 |
| [N-31] |Repeated validation logic can be converted into a function modifier| 5 |
| [N-32] |The nonReentrant modifier should occur before all other modifiers| 2 |
| [N-33] |Tokens accidentally sent to the contract cannot be recovered| 1 |
| [N-34] |customError name inconsistency used in `onlyRegisteredNetworkContract` modifier| 1 |
| [N-35] |Remove unused codes| 3 |
| [N-36] |Lack of critical 0 address check| 1 |
| [N-37] |Change TokenGGP `mint` model| 1 |
| [N-38] |Use battle-tested multisig instead of using your own MultiSig management| 1 |

Total 38 issues

### Suggestions
| Number | Suggestion Details |
|:--:|:-------|
| [S-01] |Project Upgrade and Stop Scenario should be |

Total 1 suggestion


### [L-01] Dividing before multiplying creates a rounding problem

**Context:**
```solidity
contracts/contract/tokens/TokenggAVAX.sol:
  71  
  72: 	function initialize(Storage storageAddress, ERC20 asset) public initializer {
  73: 		__ERC4626Upgradeable_init(asset, "GoGoPool Liquid Staking Token", "ggAVAX");
  74: 		__BaseUpgradeable_init(storageAddress);
  75: 
  76: 		rewardsCycleLength = 14 days;
  77: 		// Ensure it will be evenly divisible by `rewardsCycleLength`.
  78: 		rewardsCycleEnd = (block.timestamp.safeCastTo32() / rewardsCycleLength) * rewardsCycleLength;
  79: 	}

```

**Description:**
Solidity could truncate the results, performing multiplication before division will prevent rounding/truncation in solidity math.

**Recommendation:**
Add scalars so roundings are negligible

### [L-02] ERC4626Upgradeable 's implmentation is not fully up to EIP-4626's specification


Must  return the maximum amount of shares mint would allow to be deposited to receiver and not cause a revert, which must not be higher than the actual maximum that would be accepted (it should underestimate if necessary). 

This assumes that the user has infinite assets, i.e. must not rely on `balanceOf` of asset.

```solidity

contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol:
  155  
  156: 	function maxDeposit(address) public view virtual returns (uint256) {
  157: 		return type(uint256).max;
  158: 	}
  159: 
  160: 	function maxMint(address) public view virtual returns (uint256) {
  161: 		return type(uint256).max;
  162: 	}

```

Could cause unexpected behavior in the future due to non-compliance with EIP-4626 standard.

Similar problem for Sentimentxyz ;
https://github.com/sentimentxyz/protocol/pull/235/files


### Recommended Mitigation Steps

maxMint() and maxDeposit() should reflect the limitation of maxSupply.

Consider changing maxMint() and maxDeposit() to:



```diff

 function maxMint(address) public view virtual returns (uint256) {
-        return type(uint256).max;
+      if (totalSupply >= maxSupply) return 0;
+      return maxSupply - totalSupply;
    }

 function maxDeposit(address) public view virtual returns (uint256) {
-       return type(uint256).max;
+       return convertToAssets(maxMint(address(0)));
    }


function __ERC4626Upgradeable_init(
	ERC20 _asset,
	string memory _name,
	string memory _symbol
+	 uint256 _maxSupply
) internal onlyInitializing {
-  	__ERC20Upgradeable_init(_name, _symbol, _asset.decimals());
+	 __ERC20Upgradeable_init(_name, _symbol, _asset.decimals(), _maxSupply);
	asset = _asset;
}
```


```diff
contracts/contract/tokens/TokenggAVAX.sol:
  71  
  72: 	function initialize(Storage storageAddress, ERC20 asset, uint256 _maxSupply) public initializer {
- 		__ERC4626Upgradeable_init(asset, "GoGoPool Liquid Staking Token", "ggAVAX");
+ 		 __ERC4626Upgradeable_init(asset, "GoGoPool Liquid Staking Token", "ggAVAX”, _maxSupply);
  74: 		__BaseUpgradeable_init(storageAddress);
  75: 
  76: 		rewardsCycleLength = 14 days;
  77: 		// Ensure it will be evenly divisible by `rewardsCycleLength`.
  78: 		rewardsCycleEnd = (block.timestamp.safeCastTo32() / rewardsCycleLength) * rewardsCycleLength;
  79: 	}
```

### [L-03] Front running attacks by the `onlyMultisig` 


Project has one  possible attack vectors by the `onlyMultisig`:

With the function `setGGPPriceInAVAX` the following is set: Price of GGP in AVAX 

When a user uses the platform expecting a low GGP price, the authorities can run the "setGGPPriceInAVAX" function up front and raise the price significantly. If the size is large enough, this can be a substantial amount of money.

```js
contracts/contract/Oracle.sol:
  60  
  61: 	function setGGPPriceInAVAX(uint256 price, uint256 timestamp) external onlyMultisig {
  62: 		uint256 lastTimestamp = getUint(keccak256("Oracle.GGPTimestamp"));
  63: 		if (timestamp < lastTimestamp || timestamp > block.timestamp) {
  64: 			revert InvalidTimestamp();
  65: 		}
  66: 		if (price == 0) {
  67: 			revert InvalidGGPPrice();
  68: 		}
  69: 		setUint(keccak256("Oracle.GGPPriceInAVAX"), price);
  70: 		setUint(keccak256("Oracle.GGPTimestamp"), timestamp);
  71: 		emit GGPPriceUpdated(price, timestamp);
  72: 	}
  73  }

```


### Recommended Mitigation Steps

Use a timelock to avoid instant changes of the parameters.

### [L-04] There is a risk that the `price` variable is accidentally initialized to a little  and platform loses Money


### Impact

There is a risk that the `GGPPrice() - Price` variables are accidentally initialized to little

In addition, it is a strong belief that it will not be 0, as it is an issue that will affect the platform revenues.

Although the value initialized ,for example 1 wei by mistake or forgetting can be changed later by `onlyMultisig` , in the first place it can be exploited by users and cause huge amount  usage

`require == 0` should not be made because of the possibility of the platform defining the `0` rate.

However, by using a safer architecture (for example, a two-stage control) in this part, it is a safer architecture to control the wrong value to be set by mistake.

```solidity

contracts/contract/Oracle.sol:
  60  
  61: 	function setGGPPriceInAVAX(uint256 price, uint256 timestamp) external onlyMultisig {
  62: 		uint256 lastTimestamp = getUint(keccak256("Oracle.GGPTimestamp"));
  63: 		if (timestamp < lastTimestamp || timestamp > block.timestamp) {
  64: 			revert InvalidTimestamp();
  65: 		}
  66: 		if (price == 0) {
  67: 			revert InvalidGGPPrice();
  68: 		}
  69: 		setUint(keccak256("Oracle.GGPPriceInAVAX"), price);
  70: 		setUint(keccak256("Oracle.GGPTimestamp"), timestamp);
  71: 		emit GGPPriceUpdated(price, timestamp);
  72: 	}
  73  }
```


### [L-05] Solmate's SafeTransferLib doesn't check whether the ERC20 contract exists

Solmate's SafeTransferLib, which is often used to interact with non-compliant/unsafe ERC20 tokens, does not check whether the ERC20 contract exists. The following code will not revert in case the token doesn't exist (yet).

This is stated in the Solmate library:
https://github.com/transmissions11/solmate/blob/main/src/utils/SafeTransferLib.sol#L9



```solidity
4 results - 2 files

contracts/contract/ClaimNodeOp.sol:
  105: 			ggp.approve(address(staking), restakeAmt);

contracts/contract/Staking.sol:
  321: 		ggp.safeTransferFrom(msg.sender, address(this), amount);
  330: 		ggp.safeTransferFrom(msg.sender, address(this), amount);
  342: 		ggp.approve(address(vault), amount);

```


### Recommended Mitigation Steps

Add a contract exist control in functions;
```js
pragma solidity >=0.8.0;

function isContract(address _addr) private returns (bool isContract) {
    isContract = _addr.code.length > 0;
}

```

### [L-06]  A single point of failure 


## Impact

The ` onlyMultisig ` role has a single point of failure and ` onlyMultisig` can use critical a few functions.

` onlyMultisig ` role in the project:
Owner is behind a multisig but changes are not behind a timelock.

With `MultisigManager.sol` the `onlyGuardian` is the sole authority in the determination of Multisig addresses, and this increases the risk of single point of failure

Even if protocol admins/developers are not malicious there is still a chance for Owner keys to be stolen. In such a case, the attacker can cause serious damage to the project due to important functions. In such a case, users who have invested in project will suffer high financial losses.


` onlyMultisig ` functions;
```js

contracts/contract/ClaimNodeOp.sol:
  55  	/// @dev Rialto will call this
  56: 	function calculateAndDistributeRewards(address stakerAddr, uint256 totalEligibleGGPStaked) external onlyMultisig {
  57  		Staking staking = Staking(getContractAddress("Staking"));

contracts/contract/Oracle.sol:
  60  
  61: 	function setGGPPriceInAVAX(uint256 price, uint256 timestamp) external onlyMultisig {
  62  		uint256 lastTimestamp = getUint(keccak256("Oracle.GGPTimestamp"));

contracts/contract/ProtocolDAO.sol:
  155  	/// @dev Used for testing
  156: 	function setExpectedAVAXRewardsRate(uint256 rate) public onlyMultisig valueNotGreaterThanOne(rate) {
  157  		setUint(keccak256("ProtocolDAO.ExpectedAVAXRewardsRate"), rate);
```

This increases the risk of ```A single point of failure```




## Tools Used
Manuel Code Review


## Recommended Mitigation Steps
Add a time lock to  critical functions. Admin-only functions that change critical parameters should emit events and have timelocks. 

Events allow capturing the changed parameters so that off-chain tools/interfaces can register such changes with timelocks that allow users to evaluate them and consider if they would like to engage/exit based on how they perceive the changes as affecting the trustworthiness of the protocol or profitability of the implemented financial services.

Also detail them in documentation and NatSpec comments


### [L-07] Loss of precision due to rounding

Loss of precision due to rounding to ` unlockedRewards ` value, the higher the value of `(rewardsCycleEnd_ - lastSync_)`, the greater the loss of precision

Add scalars so roundings are negligible


```solidity

contracts/contract/tokens/TokenggAVAX.sol:
  127  		// return unlocked rewards and stored total
  128: 		uint256 unlockedRewards = (lastRewardsAmt_ * (block.timestamp - lastSync_)) / (rewardsCycleEnd_ - lastSync_);

```


### [L-08] initialize() function can be called by anybody

`initialize()` function can be called anybody when the contract is not initialized.

More importantly, if someone else runs this function, they will have full authority because of the `__BaseUpgradeable_init` function.

Here is a definition of `initialize()` function.

```solidity

contracts/contract/tokens/TokenggAVAX.sol:
  71  
  72: 	function initialize(Storage storageAddress, ERC20 asset) public initializer {
  73: 		__ERC4626Upgradeable_init(asset, "GoGoPool Liquid Staking Token", "ggAVAX");
  74: 		__BaseUpgradeable_init(storageAddress);
  75: 
  76: 		rewardsCycleLength = 14 days;
  77: 		// Ensure it will be evenly divisible by `rewardsCycleLength`.
  78: 		rewardsCycleEnd = (block.timestamp.safeCastTo32() / rewardsCycleLength) * rewardsCycleLength;
  79: 	}

```


### Recommended Mitigation Steps

Add a control that makes `initialize()` only call the Deployer Contract or EOA;

```js
if (msg.sender != DEPLOYER_ADDRESS) {
            revert NotDeployer();
        }
```


### [L-09] No Storage Gap for `ERC4626Upgradeable`

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L11

### Impact
For upgradeable contracts, inheriting contracts may introduce new variables. In order to be able to add new variables to the upgradeable contract without causing storage collisions, a storage gap should be added to the upgradeable contract.

If no storage gap is added, when the upgradable contract introduces new variables, 
it may override the variables in the inheriting contract.

Storage gaps are a convention for reserving storage slots in a base contract, allowing future versions of that contract to use up those slots without affecting the storage layout of child contracts.
To create a storage gap, declare a fixed-size array in the base contract with an initial number of slots. 
This can be an array of uint256 so that each element reserves a 32 byte slot. Use the naming convention __gap so that OpenZeppelin Upgrades will recognize the gap:

Classification for a similar problem:
https://code4rena.com/reports/2022-05-alchemix/#m-05-no-storage-gap-for-upgradeable-contract-might-lead-to-storage-slot-collision

```js
contract Base {
    uint256 base1;
    uint256[49] __gap;
}

contract Child is Base {
    uint256 child;
}
```

Openzeppelin Storage Gaps notification:
```js
Storage Gaps
This makes the storage layouts incompatible, as explained in Writing Upgradeable Contracts. 
The size of the __gap array is calculated so that the amount of storage used by a contract 
always adds up to the same number (in this case 50 storage slots).
```

### Proof of Concept
`TokenggAVAX` contract is intended to be upgradeable, including the inherited contracts ERC4626Upgradeable and BaseUpgradeable

However, neither ERC4626Upgradeable nor the BaseUpgradeable   contract contains storage gaps. 


The `TokenggAVAX` contract contains storage variables, therefore the base contracts ERC4626Upgradeable and BaseUpgradeable  cannot be upgraded to include any additional variables because it would overwrite the variables declared in the child contract `TokenggAVAX`  This greatly limits contract upgradeability.


### Recommended Mitigation Steps
Consider adding a storage gap at the end of the upgradeable abstract contract
```js
uint256[50] private __gap;
```
### [L-10] Signature Malleability of EVM's ecrecover()

**Context:**

```solidity
1 result - 1 file

contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol:
  131  		unchecked {
  132: 			address recoveredAddress = ecrecover(
  133  				keccak256(

```
**Description:**
Description: The function calls the Solidity ecrecover() function directly to verify the given signatures. However, the ecrecover() EVM opcode allows malleable (non-unique) signatures and thus is susceptible to replay attacks.

Although a replay attack seems not possible for this contract, I recommend using the battle-tested OpenZeppelin's ECDSA library.

*Recommendation:**
Use the ecrecover function from OpenZeppelin's ECDSA library for signature verification. (Ensure using a version > 4.7.3 for there was a critical bug >= 4.1.0 < 4.7.3).

### [L-11]  msg.sender not logged when depositing ERC20 token contract with `depositToken` function

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Vault.sol#L108-L131

### Impact
ERC20 token contract can be deposited with the `depositToken()` function.

With the following part of the code, the ERC20 transfer from msg.sender to the contract takes place;
`tokenContract.safeTransferFrom(msg.sender, address(this), amount);`

The token deposit record is kept with the following record, but there is no msg.sender here;

`bytes32 contractKey = keccak256(abi.encodePacked(networkContractName, address(tokenContract)));`


Whereas `withdrawToken()` function has msg.sender record

`bytes32 contractKey = keccak256(abi.encodePacked(getContractName(msg.sender), tokenAddress));`




```js
contracts/contract/Vault.sol:
  107  	/// @param amount How many tokens being deposited
  108: 	function depositToken(
  109: 		string memory networkContractName,
  110: 		ERC20 tokenContract,
  111: 		uint256 amount
  112: 	) external guardianOrRegisteredContract {
  113: 		// Valid Amount?
  114: 		if (amount == 0) {
  115: 			revert InvalidAmount();
  116: 		}
  117: 		// Make sure the network contract is valid (will revert if not)
  118: 		getContractAddress(networkContractName);
  119: 		// Make sure we accept this token
  120: 		if (!allowedTokens[address(tokenContract)]) {
  121: 			revert InvalidToken();
  122: 		}
  123: 		// Get contract key
  124: 		bytes32 contractKey = keccak256(abi.encodePacked(networkContractName, address(tokenContract)));
  125: 		// Emit token transfer event
  126: 		emit TokenDeposited(contractKey, address(tokenContract), amount);
  127: 		// Send tokens to this address now, safeTransfer will revert if it fails
  128: 		tokenContract.safeTransferFrom(msg.sender, address(this), amount);
  129: 		// Update balances
  130: 		tokenBalances[contractKey] = tokenBalances[contractKey] + amount;
  131: 	}

```

### [L-12] Missing Event for critical parameters init and change

**Context:**
```solidity
10 results
contracts/contract/ClaimNodeOp.sol:
  28  
  29: 	constructor(Storage storageAddress, ERC20 ggp_) Base(storageAddress) {
  30: 		version = 1;
  31: 		ggp = ggp_;
  32: 	}
  33  

contracts/contract/Staking.sol:
  59  
  60: 	constructor(Storage storageAddress, ERC20 ggp_) Base(storageAddress) {
  61: 		version = 1;
  62: 		ggp = ggp_;
  63: 	}

contracts/contract/MinipoolManager.sol:
  176  
  177: 	constructor(
  178: 		Storage storageAddress,
  179: 		ERC20 ggp_,
  180: 		TokenggAVAX ggAVAX_
  181: 	) Base(storageAddress) {
  182: 		version = 1;
  183: 		ggp = ggp_;
  184: 		ggAVAX = ggAVAX_;
  185: 	}

contracts/contract/ClaimProtocolDAO.sol:
  14  
  15: 	constructor(Storage storageAddress) Base(storageAddress) {
  16: 		version = 1;
  17: 	}
  18  

contracts/contract/MultisigManager.sol:
  28  
  29: 	constructor(Storage storageAddress) Base(storageAddress) {
  30: 		version = 1;
  31: 	}
  32  

contracts/contract/Oracle.sol:
  22  
  23: 	constructor(Storage storageAddress) Base(storageAddress) {
  24: 		version = 1;
  25: 	}
  26  

contracts/contract/ProtocolDAO.sol:
  18  
  19: 	constructor(Storage storageAddress) Base(storageAddress) {
  20: 		version = 1;
  21: 	}
  22  

contracts/contract/RewardsPool.sol:
  29  
  30: 	constructor(Storage storageAddress) Base(storageAddress) {
  31: 		version = 1;
  32: 	}
  33  

contracts/contract/Vault.sol:
  40  
  41: 	constructor(Storage storageAddress) Base(storageAddress) {
  42: 		version = 1;
  43: 	}


contracts/contract/Storage.sol:
  40  	// Initiate transfer of guardianship to a new address
  41: 	function setGuardian(address newAddress) external {
  42: 		// Check tx comes from current guardian
  43: 		if (msg.sender != guardian) {
  44: 			revert MustBeGuardian();
  45: 		}
  46: 		// Store new address awaiting confirmation
  47: 		newGuardian = newAddress;
  48: 	}

```

**Description:**
Events help non-contract tools to track changes, and events prevent users from being surprised by changes

**Recommendation:**
Add Event-Emit

### [L-13] Use `2StepSetGuardian` instead of ` setGuardian `

```solidity
contracts/contract/Storage.sol:
  40  	// Initiate transfer of guardianship to a new address
  41: 	function setGuardian(address newAddress) external {
  42: 		// Check tx comes from current guardian
  43: 		if (msg.sender != guardian) {
  44: 			revert MustBeGuardian();
  45: 		}
  46: 		// Store new address awaiting confirmation
  47: 		newGuardian = newAddress;
  48: 	}
```


**Description:**
``` setGuardian ``` function is used to change `guardian` address

Use a 2 structure which is safer. 

**Recommendation:**
Use 2-stage guardian address transfer :


```js
abstract contract 2StepSetGuardian {
    address private _pendingGuardian;

    // Guardian address
    address private guardian;
    address public newGuardian;
    event GuardianTransferStarted(address indexed previousGuardian, address indexed newGuardian);

    /**
     * @dev Returns the address of the pending Guardian.
     */
    function pendingGuardian() public view returns (address) {
        return _pendingGuardian;
    }

    /**
     * @dev Starts the Guardian transfer of the contract to a new account. Replaces the pending transfer if there is one.
     * Can only be called by the current Guardian.
     */
    function transferGuardian(address newGuardian) public onlyGuardian {
        _pendingGuardian = newGuardian;
        emit GuardianTransferStarted(guardian(), newGuardian);
    }

    /**
     * @dev Transfers Guardian of the contract to a new account (`newGuardian`) and deletes any pending Guardian.
     * Internal function without access restriction.
     */
    function _transferGuardian(address newGuardian) internal {
        delete _pendingGuardian;
        transferGuardian(newGuardian);
    }

    /**
     * @dev The new Guardian accepts the Guardian transfer.
     */
    function accept Guardian() external {
        address sender = _msgSender();
        require(pendingGuardian() == sender, " 2StepSetGuardian : caller is not the new Guardian");
        _transferGuardian(sender);
    }
}
```


### [L-14] Lack of Input Validation

For defence-in-depth purpose, it is recommended to perform additional validation against the amount that the user is attempting to deposit, mint, withdraw and redeem to ensure that the submitted amount is valid.

[OpenZeppelinTokenizedVault.sol#L9](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/7c75b8aa89073376fb67d78a40f6d69331092c94/contracts/token/ERC20/extensions/ERC20TokenizedVault.sol#L95)


```diff

contracts/contract/tokens/TokenggAVAX.sol:
  165  
  166: 	function depositAVAX() public payable returns (uint256 shares) {
  167: 		uint256 assets = msg.value;
  168: 		// Check for rounding error since we round down in previewDeposit.
  169: 		if ((shares = previewDeposit(assets)) == 0) {
  170: 			revert ZeroShares();
  171: 		}
+	        	 require(amount <= maxDeposit(receiver), "deposit more than max");
  172: 
  173: 		emit Deposit(msg.sender, msg.sender, assets, shares);
  174: 
  175: 		IWAVAX(address(asset)).deposit{value: assets}();
  176: 		_mint(msg.sender, shares);
  177: 		afterDeposit(assets, shares);
  178: 	}

```


```diff
contracts/contract/tokens/TokenggAVAX.sol:
  190  
  191: 	function redeemAVAX(uint256 shares) public returns (uint256 assets) {
  192: 		// Check for rounding error since we round down in previewRedeem.
  193: 		if ((assets = previewRedeem(shares)) == 0) {
  194: 			revert ZeroAssets();
  195: 		}
+               		require(shares <= maxRedeem(owner), "redeem more than max");
  196: 		beforeWithdraw(assets, shares);
  197: 		_burn(msg.sender, shares);
  198: 
  199: 		emit Withdraw(msg.sender, msg.sender, msg.sender, assets, shares);
  200: 
  201: 		IWAVAX(address(asset)).withdraw(assets);
  202: 		msg.sender.safeTransferETH(assets);
  203: 	}
```

```diff
contracts/contract/tokens/TokenggAVAX.sol:
  179  
  180: 	function withdrawAVAX(uint256 assets) public returns (uint256 shares) {
  181: 		shares = previewWithdraw(assets); // No need to check for rounding error, previewWithdraw rounds up.
  182: 		beforeWithdraw(assets, shares);
+              		 require(assets <= maxWithdraw(owner), "withdraw more than max");
  183: 		_burn(msg.sender, shares);
  184: 
  185: 		emit Withdraw(msg.sender, msg.sender, msg.sender, assets, shares);
  186: 
  187: 		IWAVAX(address(asset)).withdraw(assets);
  188: 		msg.sender.safeTransferETH(assets);
  189: 	}

```
### [L-15] Allows malleable SECP256K1 signatures

Here, the ecrecover() method doesn't check the s range.

Homestead (EIP-2) added this limitation, however the precompile remained unaltered. The majority of libraries, including OpenZeppelin, do this check.
Since an order can only be confirmed once and its hash is saved, there doesn't seem to be a serious danger in existing use cases.

https://github.com/OpenZeppelin/openzeppelin-contracts/blob/7201e6707f6631d9499a569f492870ebdd4133cf/contracts/utils/cryptography/ECDSA.sol#L138-L149


```solidity

contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol:
  117  
  118: 	function permit(
  119: 		address owner,
  120: 		address spender,
  121: 		uint256 value,
  122: 		uint256 deadline,
  123: 		uint8 v,
  124: 		bytes32 r,
  125: 		bytes32 s
  126: 	) public virtual {
  127: 		require(deadline >= block.timestamp, "PERMIT_DEADLINE_EXPIRED");
  128: 
  129: 		// Unchecked because the only math done is incrementing
  130: 		// the owner's nonce which cannot realistically overflow.
  131: 		unchecked {
  132: 			address recoveredAddress = ecrecover(
  133: 				keccak256(
  134: 					abi.encodePacked(
  135: 						"\x19\x01",
  136: 						DOMAIN_SEPARATOR(),
  137: 						keccak256(
  138: 							abi.encode(
  139: 								keccak256("Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)"),
  140: 								owner,
  141: 								spender,
  142: 								value,
  143: 								nonces[owner]++,
  144: 								deadline
  145: 							)
  146: 						)
  147: 					)
  148: 				),
  149: 				v,
  150: 				r,
  151: 				s
  152: 			);
  153: 
  154: 			require(recoveredAddress != address(0) && recoveredAddress == owner, "INVALID_SIGNER");
  155: 
  156: 			allowance[recoveredAddress][spender] = value;
  157: 		}
  158: 
  159: 		emit Approval(owner, spender, value);
  160: 	}

```
### [L-16] There is inconsistency and shadowing between `GGPSlashed` event argument names and emit argument names.

`GGPSlashed` event argument names: `nodeID` and `ggp`
`GGPSlashed` emit   argument names: `nodeID` and ` slashGGPAmt `

`ggp` is also used as state variable name, this shadows it (It doesn't create security issue directly now, but may cause confusion and security issues in the future due to upgradeability)

Best practice; Event argument names and emit argument names are expected to be the same.

```diff
contracts/contract/MinipoolManager.sol:
  74  
- 75: 	event GGPSlashed(address indexed nodeID, uint256 ggp);
+ 75: 	event GGPSlashed(address indexed nodeID, uint256 slashGGPAmt);

  77: 
  78: 	ERC20 public immutable ggp;



  676: 	function slash(int256 index) private {
            		// ......
            		address nodeID = getAddress(keccak256(abi.encodePacked("minipool.item", index, ".nodeID")));
 682: 		uint256 slashGGPAmt = calculateGGPSlashAmt(expectedAVAXRewardsAmt);
		
  685: 		emit GGPSlashed(nodeID, slashGGPAmt);
  689: 	}


```


### [N-01] Insufficient coverage

**Description:**
The test coverage rate of the project is 76%. Testing all functions is best practice in terms of security criteria.

```js

| File                                                         | % Lines           | % Statements       | % Branches       | % Funcs          |
|--------------------------------------------------------------|-------------------|--------------------|------------------|------------------|
| contracts/contract/BaseAbstract.sol                          | 74.19% (23/31)    | 75.76% (25/33)     | 100.00% (4/4)    | 68.00% (17/25)   |
| contracts/contract/BaseUpgradeable.sol                       | 0.00% (0/1)       | 0.00% (0/1)        | 100.00% (0/0)    | 0.00% (0/1)      |
| contracts/contract/ClaimNodeOp.sol                           | 97.62% (41/42)    | 98.21% (55/56)     | 75.00% (12/16)   | 100.00% (5/5)    |
| contracts/contract/ClaimProtocolDAO.sol                      | 100.00% (6/6)     | 100.00% (8/8)      | 100.00% (2/2)    | 100.00% (1/1)    |
| contracts/contract/MinipoolManager.sol                       | 98.87% (262/265)  | 99.14% (345/348)   | 79.63% (43/54)   | 100.00% (27/27)  |
| contracts/contract/MultisigManager.sol                       | 100.00% (32/32)   | 100.00% (38/38)    | 100.00% (10/10)  | 100.00% (7/7)    |
| contracts/contract/Ocyticus.sol                              | 100.00% (19/19)   | 100.00% (24/24)    | 50.00% (1/2)     | 100.00% (5/5)    |
| contracts/contract/Oracle.sol                                | 94.12% (16/17)    | 95.00% (19/20)     | 83.33% (5/6)     | 100.00% (4/4)    |
| contracts/contract/ProtocolDAO.sol                           | 60.78% (31/51)    | 62.26% (33/53)     | 0.00% (0/2)      | 96.00% (24/25)   |
| contracts/contract/RewardsPool.sol                           | 91.36% (74/81)    | 93.69% (104/111)   | 65.00% (13/20)   | 92.86% (13/14)   |
| contracts/contract/Staking.sol                               | 99.23% (129/130)  | 99.46% (184/185)   | 94.44% (17/18)   | 97.30% (36/37)   |
| contracts/contract/Storage.sol                               | 93.94% (31/33)    | 93.94% (31/33)     | 50.00% (2/4)     | 100.00% (26/26)  |
| contracts/contract/Vault.sol                                 | 94.55% (52/55)    | 95.31% (61/64)     | 86.36% (19/22)   | 100.00% (10/10)  |
| contracts/contract/tokens/TokenggAVAX.sol                    | 94.94% (75/79)    | 95.60% (87/91)     | 100.00% (16/16)  | 94.44% (17/18)   |
| contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol   | 100.00% (31/31)   | 100.00% (33/33)    | 100.00% (6/6)    | 100.00% (9/9)    |
| contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol | 90.48% (38/42)    | 90.91% (40/44)     | 100.00% (12/12)  | 70.59% (12/17)   |
| Total                                                        | 77.07% (911/1182) | 79.94% (1152/1441) | 74.55% (167/224) | 76.32% (232/304) |
```
Due to its capacity, test coverage is expected to be 100%.


### [N-02] Critical Address Changes Should Use Two-step Procedure

The critical procedures should be two step process.


```solidity

contracts/contract/BaseAbstract.sol:
  134  
  135: 	function setAddress(bytes32 key, address value) internal {
  136: 		gogoStorage.setAddress(key, value);
  137: 	}

contracts/contract/Storage.sol:
  103  
  104: 	function setAddress(bytes32 key, address value) external onlyRegisteredNetworkContract {
  105: 		addressStorage[key] = value;
  106: 	}


```

Recommended Mitigation Steps
Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.


### [N-03] Initial value check is missing in Set Functions

**Context:**


```js

7 results


contracts/contract/BaseAbstract.sol:
  135: 	function setAddress(bytes32 key, address value) internal {
  136: 		gogoStorage.setAddress(key, value);
  137: 	}
  138: 
  139: 	function setBool(bytes32 key, bool value) internal {
  140: 		gogoStorage.setBool(key, value);
  141: 	}
  142: 
  143: 	function setBytes(bytes32 key, bytes memory value) internal {
  144: 		gogoStorage.setBytes(key, value);
  145: 	}
  146: 
  147: 	function setBytes32(bytes32 key, bytes32 value) internal {
  148: 		gogoStorage.setBytes32(key, value);
  149: 	}
  150: 
  151: 	function setInt(bytes32 key, int256 value) internal {
  152: 		gogoStorage.setInt(key, value);
  153: 	}
  154: 
  155: 	function setUint(bytes32 key, uint256 value) internal {
  156: 		gogoStorage.setUint(key, value);
  157: 	}
  158: 
  159: 	function setString(bytes32 key, string memory value) internal {
  160: 		gogoStorage.setString(key, value);
  161: 	}
  162: 
```



Checking whether the current value and the new value are the same should be added


### [N-04] NatSpec comments should be increased in contracts

**Context:**
All Contracts

**Description:**
It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation.
In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.
https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

**Recommendation:**
NatSpec comments should be increased in contracts


### [N-05] `Function writing` that does not comply with the `Solidity Style Guide`

**Context:**
All Contracts

**Description:**
Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

Functions should be grouped according to their visibility and ordered:

 constructor
receive function (if exists)
fallback function (if exists)
external
public
internal
private
within a grouping, place the view and pure functions last

### [N-06] Add a timelock to critical functions

It is a good practice to give time for users to react and adjust to critical changes. A timelock provides more guarantees and reduces the level of trust required, thus decreasing risk for users. It also indicates that the project is legitimate (less risk of a malicious owner making a sandwich attack on a user).
Consider adding a timelock to:

```solidity

contracts/contract/Storage.sol:
  40  	// Initiate transfer of guardianship to a new address
  41: 	function setGuardian(address newAddress) external {
  42: 		// Check tx comes from current guardian
  43: 		if (msg.sender != guardian) {
  44: 			revert MustBeGuardian();
  45: 		}
  46: 		// Store new address awaiting confirmation
  47: 		newGuardian = newAddress;
  48: 	}


 104: 	function setAddress(bytes32 key, address value) external onlyRegisteredNetworkContract {
  105: 		addressStorage[key] = value;
  106: 	}

```

### [N-07] Solidity compiler optimizations can be problematic

```js
hardhat.config.ts:
  34  
  35: const config: HardhatUserConfig = {
  36: 	solidity: {
  37: 		version: "0.8.17",
  38: 		settings: {
  39: 			optimizer: {
  40: 				enabled: true,
  41: 				runs: 800,
  42: 			},
  43  		},
```

**Description:**
Protocol has enabled optional compiler optimizations in Solidity.
There have been several optimization bugs with security implications. Moreover, optimizations are actively being developed. Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them. 

Therefore, it is unclear how well they are being tested and exercised.
High-severity security issues due to optimization bugs have occurred in the past. A high-severity bug in the emscripten-generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG. 

Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. More recently, another bug due to the incorrect caching of keccak256 was reported.
A compiler audit of Solidity from November 2018 concluded that the optional optimizations may not be safe.
It is likely that there are latent bugs related to optimization and that new bugs will be introduced due to future optimizations.

Exploit Scenario
A latent or future bug in Solidity compiler optimizations—or in the Emscripten transpilation to solc-js—causes a security vulnerability in the contracts.


**Recommendation:**
Short term, measure the gas savings from optimizations and carefully weigh them against the possibility of an optimization-related bug.
Long term, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.

### [N-08] For modern and more readable code; update import usages

**Context:**

```solidity
13 results - 13 files

contracts/contract/Base.sol:
  4: import "./BaseAbstract.sol";

contracts/contract/BaseUpgradeable.sol:
  4: import "./BaseAbstract.sol";

contracts/contract/ClaimNodeOp.sol:
  4: import "./Base.sol";

contracts/contract/ClaimProtocolDAO.sol:
  4: import "./Base.sol";

contracts/contract/MinipoolManager.sol:
  4: import "./Base.sol";

contracts/contract/MultisigManager.sol:
  4: import "./Base.sol";

contracts/contract/Ocyticus.sol: 
  4: import "./Base.sol";

contracts/contract/Oracle.sol:
  4: import "./Base.sol";

contracts/contract/ProtocolDAO.sol:
  4: import "./Base.sol";

contracts/contract/RewardsPool.sol:
  4: import "./Base.sol";

contracts/contract/Staking.sol:
  4: import "./Base.sol";

contracts/contract/Vault.sol:
  4: import "./Base.sol";

contracts/contract/tokens/TokenggAVAX.sol:
  6: import "../BaseUpgradeable.sol";

```


**Description:**
Solidity code is also cleaner in another way that might not be noticeable: the struct Point. We were importing it previously with global import but not using it. The Point struct `polluted the source code` with an unnecessary object we were not using because we did not need it. 
This was breaking the rule of modularity and modular programming: `only import what you need` Specific imports with curly braces allow us to apply this rule better.

**Recommendation:**
`import {contract1 , contract2} from "filename.sol";`

A good example from the ArtGobblers project;
```js
import {Owned} from "solmate/auth/Owned.sol";
import {ERC721} from "solmate/tokens/ERC721.sol";
import {LibString} from "solmate/utils/LibString.sol";
import {MerkleProofLib} from "solmate/utils/MerkleProofLib.sol";
import {FixedPointMathLib} from "solmate/utils/FixedPointMathLib.sol";
import {ERC1155, ERC1155TokenReceiver} from "solmate/tokens/ERC1155.sol";
import {toWadUnsafe, toDaysWadUnsafe} from "solmate/utils/SignedWadMath.sol";
```
### [N-09] Include return parameters in NatSpec comments

**Context:**
All Contracts

**Description:**
It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation. In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.

https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

**Recommendation:**
Include return parameters in NatSpec comments

_Recommendation  Code Style: (from Uniswap3)_
```js
    /// @notice Adds liquidity for the given recipient/tickLower/tickUpper position
    /// @dev The caller of this method receives a callback in the form of IUniswapV3MintCallback#uniswapV3MintCallback
    /// in which they must pay any token0 or token1 owed for the liquidity. The amount of token0/token1 due depends
    /// on tickLower, tickUpper, the amount of liquidity, and the current price.
    /// @param recipient The address for which the liquidity will be created
    /// @param tickLower The lower tick of the position in which to add liquidity
    /// @param tickUpper The upper tick of the position in which to add liquidity
    /// @param amount The amount of liquidity to mint
    /// @param data Any data that should be passed through to the callback
    /// @return amount0 The amount of token0 that was paid to mint the given amount of liquidity. Matches the value in the callback
    /// @return amount1 The amount of token1 that was paid to mint the given amount of liquidity. Matches the value in the callback
    function mint(
        address recipient,
        int24 tickLower,
        int24 tickUpper,
        uint128 amount,
        bytes calldata data
    ) external returns (uint256 amount0, uint256 amount1);
```

### [N-10] It's confusing that Emit has both `caller` and `owner` msg.sender

The `depositAVAX` function has a working where only msg.sender can deposit AVAX.
However, the two msg.senders in the `emit` part are not needed, so updating the event-emit definition avoids confusion.

```solidity
contracts/contract/tokens/TokenggAVAX.sol:
  165  
  166: 	function depositAVAX() public payable returns (uint256 shares) {
  167: 		uint256 assets = msg.value;
  168: 		// Check for rounding error since we round down in previewDeposit.
  169: 		if ((shares = previewDeposit(assets)) == 0) {
  170: 			revert ZeroShares();
  171: 		}
  172: 
  173: 		emit Deposit(msg.sender, msg.sender, assets, shares);

```

## [N-11] Long lines are not suitable for the ‘Solidity Style Guide’

**Context:**
[MinipoolManager.sol#L490](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L490)
[MinipoolManager.sol#L451](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L451)
[MinipoolManager.sol#L371](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L371)
[MinipoolManager.sol#L674](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MinipoolManager.sol#L674)
[BaseAbstract.sol#L73](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L73)
[Staking.sol#L110](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L110)
[Staking.sol#L248](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L248)
[Staking.sol#L379](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L379)
[Staking.sol#L248](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L248)
[Staking.sol#L133](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L133)
[RewardsPool.sol#L174](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L174)


**Description:**
It is generally recommended that lines in the source code should not exceed 80-120 characters. Today's screens are much larger, so in some cases it makes sense to expand that. The lines above should be split when they reach that length, as the files will most likely be on GitHub and GitHub always uses a scrollbar when the length is more than 164 characters.

(why-is-80-characters-the-standard-limit-for-code-width)[https://softwareengineering.stackexchange.com/questions/148677/why-is-80-characters-the-standard-limit-for-code-width]

**Recommendation:**
Multiline output parameters and return statements should follow the same style recommended for wrapping long lines found in the Maximum Line Length section.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html#introduction

```js
thisFunctionCallIsReallyLong(
    longArgument1,
    longArgument2,
    longArgument3
);
```

## [N-12] Avoid _shadowing_ `inherited state variables`

**Context:**
```solidity
contracts/contract/tokens/TokenggAVAX.sol:
  71  
  72: 	function initialize(Storage storageAddress, ERC20 asset) public initializer {
  73: 		__ERC4626Upgradeable_init(asset, "GoGoPool Liquid Staking Token", "ggAVAX");
  74: 		__BaseUpgradeable_init(storageAddress);
  75: 
  76: 		rewardsCycleLength = 14 days;
  77: 		// Ensure it will be evenly divisible by `rewardsCycleLength`.
  78: 		rewardsCycleEnd = (block.timestamp.safeCastTo32() / rewardsCycleLength) * rewardsCycleLength;
  79: 	}


contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol:
  26  
  27: 	ERC20 public asset;

```
`asset` is shadowed, 

The argument name `asset` in the `initialize` function and `asset` as the state variable in `ERC4626Upgradeable.sol` have the same name and create shadowing

**Recommendation:**
Avoid using variables with the same name, including inherited in the same contract, if used, it must be specified in the NatSpec comments.

### [N-13] Constant values such as a call to `TENTH` should used to immutable rather than constant

There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts.

While it doesn't save any gas because the compiler knows that developers often make this mistake, it's still best to use the right tool for the task at hand.

The value of 0.1 ether is not a directly calculated value, it is calculated in wei and written to the code base, that is, a calculation is made, so it must be defined with immutable


```solidity
contracts/contract/Staking.sol:
  55  
  56: 	uint256 internal constant TENTH = 0.1 ether;

```

### [N-14] Need Fuzzing test

**Context:**

```solidity
5 results - 1 file

contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol:
   82  		// balances can't exceed the max uint256 value.
   83: 		unchecked {
   84  			balanceOf[to] += amount;

  104  		// balances can't exceed the max uint256 value.
  105: 		unchecked {
  106  			balanceOf[to] += amount;

  128  
  129: 		// Unchecked because the only math done is incrementing
  130  		// the owner's nonce which cannot realistically overflow.
  131: 		unchecked {
  132  			address recoveredAddress = ecrecover(

  187  		// balances can't exceed the max uint256 value.
  188: 		unchecked {
  189  			balanceOf[to] += amount;

  199  		// will never be larger than the total supply.
  200: 		unchecked {
  201  			totalSupply -= amount;

```

**Description:**
In total 5 contracts, 37 unchecked are used, the functions used are critical. For this reason, there must be fuzzing tests in the tests of the project. Not seen in tests.

**Recommendation:**
Use should fuzzing test like Echidna.

As Alberto Cuesta Canada said:
_Fuzzing is not easy, the tools are rough, and the math is hard, but it is worth it. Fuzzing gives me a level of confidence in my smart contracts that I didn’t have before. Relying just on unit testing anymore and poking around in a testnet seems reckless now._

https://medium.com/coinmonks/smart-contract-fuzzing-d9b88e0b0a05


### [N-15] Test environment comments and codes should not be in the main version


```solidity
3 results - 2 files

contracts/contract/BaseAbstract.sol:
  7: // import {console} from "forge-std/console.sol";
  8  // import {format} from "sol-utils/format.sol";

contracts/contract/utils/RialtoSimulator.sol:
  12: // import {console} from "forge-std/console.sol";

```
### [N-16] Use of bytes.concat() instead of abi.encodePacked()

```solidity

20 results - 4 files

contracts/contract/BaseAbstract.sol:
   24  	modifier onlyRegisteredNetworkContract() {
   25: 		if (getBool(keccak256(abi.encodePacked("contract.exists", msg.sender))) == false) {
   26  			revert InvalidOrOutdatedContract();

   32  	modifier onlySpecificRegisteredContract(string memory contractName, address contractAddress) {
   33: 		if (contractAddress != getAddress(keccak256(abi.encodePacked("contract.address", contractName)))) {
   34  			revert InvalidOrOutdatedContract();

   40  	modifier guardianOrRegisteredContract() {
   41: 		bool isContract = getBool(keccak256(abi.encodePacked("contract.exists", msg.sender)));
   42  		bool isGuardian = msg.sender == gogoStorage.getGuardian();

   51  	modifier guardianOrSpecificRegisteredContract(string memory contractName, address contractAddress) {
   52: 		bool isContract = contractAddress == getAddress(keccak256(abi.encodePacked("contract.address", contractName)));
   53  		bool isGuardian = msg.sender == gogoStorage.getGuardian();

   82  		string memory contractName = getContractName(address(this));
   83: 		if (getBool(keccak256(abi.encodePacked("contract.paused", contractName)))) {
   84  			revert ContractPaused();

   90  	function getContractAddress(string memory contractName) internal view returns (address) {
   91: 		address contractAddress = getAddress(keccak256(abi.encodePacked("contract.address", contractName)));
   92  		if (contractAddress == address(0x0)) {

   99  	function getContractName(address contractAddress) internal view returns (string memory) {
  100: 		string memory contractName = getString(keccak256(abi.encodePacked("contract.name", contractAddress)));
  101  		if (bytes(contractName).length == 0) {

contracts/contract/ProtocolDAO.sol:
   61  	function getContractPaused(string memory contractName) public view returns (bool) {
   62: 		return getBool(keccak256(abi.encodePacked("contract.paused", contractName)));
   63  	}

   67  	function pauseContract(string memory contractName) public onlySpecificRegisteredContract("Ocyticus", msg.sender) {
   68: 		setBool(keccak256(abi.encodePacked("contract.paused", contractName)), true);
   69  	}

   73  	function resumeContract(string memory contractName) public onlySpecificRegisteredContract("Ocyticus", msg.sender) {
   74: 		setBool(keccak256(abi.encodePacked("contract.paused", contractName)), false);
   75  	}

  190  	function registerContract(address addr, string memory name) public onlyGuardian {
  191: 		setBool(keccak256(abi.encodePacked("contract.exists", addr)), true);
  192: 		setAddress(keccak256(abi.encodePacked("contract.address", name)), addr);
  193: 		setString(keccak256(abi.encodePacked("contract.name", addr)), name);
  194  	}

  199  		string memory name = getContractName(addr);
  200: 		deleteBool(keccak256(abi.encodePacked("contract.exists", addr)));
  201: 		deleteAddress(keccak256(abi.encodePacked("contract.address", name)));
  202: 		deleteString(keccak256(abi.encodePacked("contract.name", addr)));
  203  	}

contracts/contract/Storage.sol:
  28  	modifier onlyRegisteredNetworkContract() {
  29: 		if (booleanStorage[keccak256(abi.encodePacked("contract.exists", msg.sender))] == false && msg.sender != guardian) {
  30  			revert InvalidOrOutdatedContract();

contracts/contract/tokens/TokenggAVAX.sol:
   58  	modifier whenTokenNotPaused(uint256 amt) {
   59: 		if (amt > 0 && getBool(keccak256(abi.encodePacked("contract.paused", "TokenggAVAX")))) {
   60  			revert ContractPaused();

  206  	function maxWithdraw(address _owner) public view override returns (uint256) {
  207: 		if (getBool(keccak256(abi.encodePacked("contract.paused", "TokenggAVAX")))) {
  208  			return 0;

  216  	function maxRedeem(address _owner) public view override returns (uint256) {
  217: 		if (getBool(keccak256(abi.encodePacked("contract.paused", "TokenggAVAX")))) {
  218  			return 0;

```
Rather than using `abi.encodePacked` for appending bytes, since version 0.8.4, bytes.concat() is enabled

Since version 0.8.4 for appending bytes, bytes.concat() can be used instead of abi.encodePacked(,)

### [N-17] For functions, follow Solidity standard naming conventions (internal function style rule)

**Context:**
```solidity

32 results - 4 files

contracts/contract/BaseAbstract.sol:
   90: 	function getContractAddress(string memory contractName) internal view returns (address) {
   99: 	function getContractName(address contractAddress) internal view returns (string memory) {
  107: 	function getAddress(bytes32 key) internal view returns (address) {
  111: 	function getBool(bytes32 key) internal view returns (bool) {
  115: 	function getBytes(bytes32 key) internal view returns (bytes memory) {
  119: 	function getBytes32(bytes32 key) internal view returns (bytes32) {
  123: 	function getInt(bytes32 key) internal view returns (int256) {
  127: 	function getUint(bytes32 key) internal view returns (uint256) {
  131: 	function getString(bytes32 key) internal view returns (string memory) {
  135: 	function setAddress(bytes32 key, address value) internal {
  139: 	function setBool(bytes32 key, bool value) internal {
  143: 	function setBytes(bytes32 key, bytes memory value) internal {
  147: 	function setBytes32(bytes32 key, bytes32 value) internal {
  151: 	function setInt(bytes32 key, int256 value) internal {
  155: 	function setUint(bytes32 key, uint256 value) internal {
  159: 	function setString(bytes32 key, string memory value) internal {
  163: 	function deleteAddress(bytes32 key) internal {
  167: 	function deleteBool(bytes32 key) internal {
  171: 	function deleteBytes(bytes32 key) internal {
  175: 	function deleteBytes32(bytes32 key) internal {
  179: 	function deleteInt(bytes32 key) internal {
  183: 	function deleteUint(bytes32 key) internal {
  187: 	function deleteString(bytes32 key) internal {
  191: 	function addUint(bytes32 key, uint256 amount) internal {
  195: 	function subUint(bytes32 key, uint256 amount) internal {

contracts/contract/RewardsPool.sol:
   82: 	function inflate() internal {
  110: 	function increaseRewardsCycleCount() internal {

contracts/contract/Staking.sol:
  87: 	function increaseGGPStake(address stakerAddr, uint256 amount) internal {
  94: 	function decreaseGGPStake(address stakerAddr, uint256 amount) internal {

contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol:
   43: 	uint256 internal INITIAL_CHAIN_ID;
   45: 	bytes32 internal INITIAL_DOMAIN_SEPARATOR;
  166: 	function computeDomainSeparator() internal view virtual returns (bytes32) {
```


**Description:**
The above codes don't follow Solidity's standard naming convention,

internal and private functions : the mixedCase format starting with an underscore (_mixedCase starting with an underscore)

### [N-18] Omissions in Events

Throughout the codebase, events are generally emitted when sensitive changes are made to the contracts. However, some events are missing important parameters

The events should include the new value and old value where possible:

```solidity

1 result - 1 file

contracts/contract/Oracle.sol:
  70  		setUint(keccak256("Oracle.GGPTimestamp"), timestamp);
  71: 		emit GGPPriceUpdated(price, timestamp);
```
### [N-19] Open TODOs

**Context:**
```solidity
3 results - 3 files

contracts/contract/BaseAbstract.sol:
  6: // TODO remove this when dev is complete

contracts/contract/MinipoolManager.sol:
  412: 		// TODO Revisit this logic if we ever allow unequal matched funds

contracts/contract/Staking.sol:
  203: 	// TODO cant use onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) since we also call from increaseMinipoolCount. Wat do?

```

**Recommendation:**
Use temporary TODOs as you work on a feature, but make sure to treat them before merging. Either add a link to a proper issue in your TODO, or remove it from the code.

### [N-20] Mark visibility of initialize(...) functions as ``external``

```solidity
contracts/contract/tokens/TokenggAVAX.sol:
  71  
  72: 	function initialize(Storage storageAddress, ERC20 asset) public initializer {
  73: 		__ERC4626Upgradeable_init(asset, "GoGoPool Liquid Staking Token", "ggAVAX");
  74: 		__BaseUpgradeable_init(storageAddress);
  75: 
  76: 		rewardsCycleLength = 14 days;
  77: 		// Ensure it will be evenly divisible by `rewardsCycleLength`.
  78: 		rewardsCycleEnd = (block.timestamp.safeCastTo32() / rewardsCycleLength) * rewardsCycleLength;
  79: 	}


```

**Description:**
If someone wants to extend via inheritance, it might make more sense that the overridden initialize(...) function calls the internal {...}_init function, not the parent public initialize(...) function.

External instead of public would give more the sense of the initialize(...) functions to behave like a constructor (only called on deployment, so should only be called externally)

Security point of view, it might be safer so that it cannot be called internally by accident in the child contract 

It might cost a bit less gas to use external over public 


It is possible to override a function from external to public (= "opening it up") ✅
but it is not possible to override a function from public to external (= "narrow it down"). ❌

For above reasons you can change initialize(...) to external


https://github.com/OpenZeppelin/openzeppelin-contracts/issues/3750

### [N-21] Use underscores for number literals

```diff
contracts/contract/ProtocolDAO.sol:
- 41: setUint(keccak256("ProtocolDAO.InflationIntervalRate"), 1000133680617113500); 
+ 41: setUint(keccak256("ProtocolDAO.InflationIntervalRate"), 1_000_133_680_617_113_500); 

```
**Description:**
 There is   occasions where certain number have been hardcoded, either in variable or in the code itself. Large numbers can become hard to read.

**Recommendation:**
Consider using underscores for number literals to improve its readability.

### [N-22] Pragma version^0.8.17  version too recent to be trusted.

https://github.com/ethereum/solidity/blob/develop/Changelog.md
0.8.17 (2022-09-08)
0.8.16 (2022-08-08)
0.8.15 (2022-06-15)
0.8.10 (2021-11-09)


Unexpected bugs can be reported in recent versions;
Risks related to recent releases
Risks of complex code generation changes
Risks of new language features
Risks of known bugs

Use a non-legacy and more battle-tested version
Use 0.8.10

### [N-23] Showing the actual values of numbers in NatSpec comments makes checking and reading code easier

```diff
5 results - 2 files

contracts/contract/ProtocolDAO.sol:
- 30: 		setUint(keccak256("ProtocolDAO.RewardsEligibilityMinSeconds"), 14 days);
+ 30: 		setUint(keccak256("ProtocolDAO.RewardsEligibilityMinSeconds"), 14 days); // 14 Days:  1209600 (14 * 24 * 60 * 60 )


- 33: 		setUint(keccak256("ProtocolDAO.RewardsCycleSeconds"), 28 days); 
+ 33: 		setUint(keccak256("ProtocolDAO.RewardsCycleSeconds"), 28 days); // 28 Days:  2419600 (28 * 24 * 60 * 60 )

- 40: 		setUint(keccak256("ProtocolDAO.InflationIntervalSeconds"), 1 days);
+ 40: 		setUint(keccak256("ProtocolDAO.InflationIntervalSeconds"), 1 days); // 1 Days:  86400 (1 * 24 * 60 * 60 )

- 52: 		setUint(keccak256("ProtocolDAO.MinipoolCancelMoratoriumSeconds"), 5 days);
+ 52: 		setUint(keccak256("ProtocolDAO.MinipoolCancelMoratoriumSeconds"), 5 days); // 5 Days:  432000 (5 * 24 * 60 * 60 )

contracts/contract/tokens/TokenggAVAX.sol:
- 76: 		rewardsCycleLength = 14 days;
+ 76: 		rewardsCycleLength = 14 days; // 14 Days:  1209600 (14 * 24 * 60 * 60 )


```

### [N-24] Lack of event emission after critical `initialize()` function

To record the init parameters for off-chain monitoring and transparency reasons, please consider emitting an event after the `initialize()` function:

```solidity
3 results - 3 files

contracts/contract/ProtocolDAO.sol:
  22  
  23: 	function initialize() external onlyGuardian {
  24  		if (getBool(keccak256("ProtocolDAO.initialized"))) {

contracts/contract/RewardsPool.sol:
  33  
  34: 	function initialize() external onlyGuardian {
  35  		if (getBool(keccak256("RewardsPool.initialized"))) {

contracts/contract/tokens/TokenggAVAX.sol:
  71  
  72: 	function initialize(Storage storageAddress, ERC20 asset) public initializer {
  73  		__ERC4626Upgradeable_init(asset, "GoGoPool Liquid Staking Token", "ggAVAX");
```

### [N-25] `Empty blocks` should be _removed_ or _Emit_ something

**Description:**
Code contains empty block

```solidity

2 results - 2 files

contracts/contract/MinipoolManager.sol:
  106: 	function receiveWithdrawalAVAX() external payable {}

contracts/contract/utils/RialtoSimulator.sol:
  47: 	receive() external payable {}

```

**Recommendation:**
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

### [N-26] Use `require` instead of `assert`

```solidity
contracts/contract/tokens/TokenggAVAX.sol:
  82  	receive() external payable {
  83: 		assert(msg.sender == address(asset));
  84  	}
```

**Description:**
Assert should not be used except for tests, `require` should be used

Prior to Solidity 0.8.0, pressing a confirm consumes the remainder of the process's available gas instead of returning it, as request()/revert() did.

assert() and ruqire();
The big difference between the two is that the `assert()`function when false, uses up all the remaining gas and reverts all the changes made.
Meanwhile, a  `require()` function when false, also reverts back all the changes made to the contract but does refund all the remaining gas fees we offered to pay.
This is the most common Solidity function used by developers for debugging and error handling.

Assertion() should be avoided even after solidity version 0.8.0, because its documentation states "The Assert function generates an error of type Panic(uint256).
Code that works properly should never Panic, even on invalid external input. If this happens, you need to fix it in your contract. there's a mistake".

### [N-27] Implement some type of version counter that will be incremented automatically for contract upgrades

As part of the upgradeability of  Proxies , the contract can be upgraded multiple times, where it is a systematic approach to record the version of each upgrade.

```js
1 result - 1 file

contracts/contract/tokens/TokenggAVAX.sol:
  254  
  255: 	function _authorizeUpgrade(address newImplementation) internal override onlyGuardian {}
  256  }

```
I suggest implementing some kind of version counter that will be incremented automatically when you upgrade the contract.

**Recommendation:**
```js

uint256 public authorizeUpgradeCounter;

function _authorizeUpgrade(address newImplementation) internal override onlyGuardian {
	authorizeUpgradeCounter+=1;
}


    }
```

### [N-28] Highest risk must be specified in NatSpec comments and documentation

```solidity

contracts/contract/tokens/TokenggAVAX.sol:
  254  
  255: 	function _authorizeUpgrade(address newImplementation) internal override onlyGuardian {}

```

**Description:**
Although the UUPS model has many advantages and the proposed proxy model is currently the UUPS model, there are some caveats we should be aware of before applying it to a real-world project.

One of the main attention: Since upgrades are done through the implementation contract with the help of the ```upgradeTo``` method, there is a high risk of new implementations such as excluding the ```upgradeTo``` method, which can permanently kill the smart contract's ability to upgrade. Also, this pattern is somewhat complicated to implement compared to other proxy patterns.

**Recommendation:**
Therefore, this highest risk must be found in NatSpec comments and documents.

### [N-29] There is no need to cast a variable that is already an address, such as address(x)

```diff
contracts/contract/Vault.sol:
- 124: 		bytes32 contractKey = keccak256(abi.encodePacked(networkContractName, address(tokenContract)));
+ 124: 		bytes32 contractKey = keccak256(abi.encodePacked(networkContractName, tokenContract));

```
**Description:**
There is no need to cast a variable that is already an address, such as address(tokenContract) tokenContract also address.

**Recommendation:**
Use directly variable.


### [N-30] `0 address` check for `_asset`

**Context:**

```solidity
contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol:
  28  
  29: 	function __ERC4626Upgradeable_init(
  30: 		ERC20 _asset,
  31: 		string memory _name,
  32: 		string memory _symbol
  33: 	) internal onlyInitializing {
  34: 		__ERC20Upgradeable_init(_name, _symbol, _asset.decimals());
  35: 		asset = _asset;
  36: 	}

```

**Description:**
Also check of the address to protect the code from 0x0 address  problem just in case. This is best practice or instead of suggesting that they verify address != 0x0, you could add some good NatSpec comments explaining what is valid and what is invalid and what are the implications of accidentally using an invalid address.

**Recommendation:**
like this;
`if (_asset == address(0)) revert ADDRESS_ZERO();`

### [N-31] Repeated validation logic can be converted into a function modifier

If a query or logic is repeated over many lines, using a modifier improves the readability and reusability of the code


```solidity
5 results - 4 files

contracts/contract/BaseAbstract.sol:
  73: 		bool enabled = (addr != address(0)) && getBool(keccak256(abi.encodePacked("multisig.item", multisigIndex, ".enabled")));

  92: 		if (contractAddress == address(0x0)) {

contracts/contract/MinipoolManager.sol:
  202: 		if (nodeID == address(0)) {

contracts/contract/MultisigManager.sol:
  111: 		enabled = (addr != address(0)) && getBool(keccak256(abi.encodePacked("multisig.item", index, ".enabled")));

contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol:
  154: 			require(recoveredAddress != address(0) && recoveredAddress == owner, "INVALID_SIGNER");


```


### [N-32] The nonReentrant modifier should occur before all other modifiers

Best practice for the re-entry issue is to have non-entrancy come first in the modifier order.

This rule is not applied in the following two functions;

```solidity
2 results - 1 file

contracts/contract/Vault.sol:
   61: 	function withdrawAVAX(uint256 amount) external onlyRegisteredNetworkContract nonReentrant {
  141: 	) external onlyRegisteredNetworkContract nonReentrant {

```

### [N-33] Tokens accidentally sent to the contract cannot be recovered


It can't be recovered if the tokens accidentally arrive at the contract address, which has happened to many popular projects, so I recommend adding a recovery code to your critical contracts.

### Recommended Mitigation Steps

Add this code:

```solidity
 /**
  * @notice Sends ERC20 tokens trapped in contract to external address
  * @dev Onlyowner is allowed to make this function call
  * @param account is the receiving address
  * @param externalToken is the token being sent
  * @param amount is the quantity being sent
  * @return boolean value indicating whether the operation succeeded.
  *
 */
  function rescueERC20(address account, address externalToken, uint256 amount) public onlyOwner returns (bool) {
    IERC20(externalToken).transfer(account, amount);
    return true;
  }
}

```



### [N-34] customError name inconsistency used in `onlyRegisteredNetworkContract` modifier

CustomError used in `onlyRegisteredNetworkContract` modifier has `or` (InvalidOrOutdatedContract) in the name while && (and) is used in the code;

```solidity

contracts/contract/Storage.sol:
  26  
  27: 	/// @dev Only allow access from guardian or the latest version of a contract in the GoGoPool network
  28: 	modifier onlyRegisteredNetworkContract() {
  29: 		if (booleanStorage[keccak256(abi.encodePacked("contract.exists", msg.sender))] == false && msg.sender != guardian) {
  30: 			revert InvalidOrOutdatedContract();
  31: 		}
  32: 		_;
  33: 	}

```
 
### [N-35] Remove unused codes

They custom Errors are not used anywhere in the project
Unused codes cause confusion and waste of gas 

```solidity

contracts/contract/Vault.sol:
  29: 	error InvalidNetworkContract();
  30: 	error TokenTransferFailed();
  31: 	error VaultTokenWithdrawalFailed();

```
 

### [N-36] Lack of critical 0 address check

`withdrawalAddress` is the address argument and there is no 0 address control, so the withdrawn token may be sent to 0 addresses by mistake;

```diff
contracts/contract/Vault.sol:
  136  	/// @param amount Number of tokens to be withdrawn
  137: 	function withdrawToken(
  138: 		address withdrawalAddress,
  139: 		ERC20 tokenAddress,
  140: 		uint256 amount
  141: 	) external onlyRegisteredNetworkContract nonReentrant {
  142: 		// Valid Amount?
  143: 		if (amount == 0) {
  144: 			revert InvalidAmount();
  145: 		}
+		if (withdrawalAddress == address(0)) {
+			revert InvalidAddress();
+		}
  146: 		// Get contract key
  147: 		bytes32 contractKey = keccak256(abi.encodePacked(getContractName(msg.sender), tokenAddress));
  148: 		// Emit token withdrawn event
  149: 		emit TokenWithdrawn(contractKey, address(tokenAddress), amount);
  150: 		// Verify there are enough funds
  151: 		if (tokenBalances[contractKey] < amount) {
  152: 			revert InsufficientContractBalance();
  153: 		}
  154: 		// Update balances
  155: 		tokenBalances[contractKey] = tokenBalances[contractKey] - amount;
  156: 		// Get the toke ERC20 instance
  157: 		ERC20 tokenContract = ERC20(tokenAddress);
  158: 		// Withdraw to the withdrawal address, , safeTransfer will revert if it fails
  159: 		tokenContract.safeTransfer(withdrawalAddress, amount);
  160: 	}
```
 


### [N-37] Change TokenGGP `mint` model

Instead of having a fixed Total Supply of TokenGGP and minting the entire Total Supply to a single account, use the minting model at the time of transaction, so Total Supply will only be as much as the token in use and there will be no tokens minted in a single address.


```solidity
contracts/contract/tokens/TokenGGP.sol:
   8  
   9: contract TokenGGP is ERC20 {
  10: 	uint256 private constant TOTAL_SUPPLY = 22_500_000 ether;
  11: 
  12: 	constructor() ERC20("GoGoPool Protocol", "GGP", 18) {
  13: 		_mint(msg.sender, TOTAL_SUPPLY);
  14: 	}
  15: }

```
 

One advantage of using the constant TOTAL_SUPPLY in the TokenGGP contract is that it allows you to specify the total supply of tokens in a single place, which can make it easier to maintain and understand the contract.

One disadvantage is that the total supply of tokens is hard-coded into the contract and cannot be changed later. This means that if the total supply needs to be changed for any reason (e.g., to issue more tokens or to burn existing tokens), the contract will need to be modified and redeployed. This can be time-consuming and costly.

Another potential disadvantage is that the use of a constant value for the total supply may make the contract less flexible. For example, if the total supply needs to be determined dynamically based on some external factor (e.g., the total amount of funds raised in a crowdfunding campaign), the contract will need to be modified to accommodate this.

Finally, using a constant value for the total supply may make it more difficult to implement certain features or behaviors that depend on the total supply, such as vesting or token burns. These features may require additional logic to be implemented in the contract, which could increase its complexity and gas cost.



### [N-38] Use battle-tested multisig instead of using your own MultiSig management

https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol


The use of Multisig is a very critical architecture for single failure and centrality risks, so it is better to use battle-tested and accepted security risk-free architectures rather than using such important architectures with projects' own implementations.

 

The Consensys multisig wallet is a simple but cleanly-written implementation of a multisig wallet. Its code is easy to understand and use, and contains all the common functions a multisig contract would need. However, this wallet is not regularly maintained and development has since continued under the Gnosis multisig wallet. The Consensys version is still widely used, and its biggest wallet deployment contains over 150,000 ETH at the time of writing.
Multisig source code: https://github.com/ConsenSysMesh/MultiSigWallet


https://medium.com/mycrypto/introduction-to-multisig-contracts-33d5b25134b2



### [S-01] Project Upgrade and Stop Scenario should be

At the start of the project, the system may need to be stopped or upgraded, I suggest you have a script beforehand and add it to the documentation.
This can also be called an " EMERGENCY STOP (CIRCUIT BREAKER) PATTERN ".

https://github.com/maxwoe/solidity_patterns/blob/master/security/EmergencyStop.sol


