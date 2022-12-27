# Reconsider the storage implementation

Even though I found the current storage implementation pretty interesting, I consider that it has some issues that could be improved in some grade with some changes:

## Problems
  * Pretty heavy on external calls.
  * Error prone with the current slot keys implementation.
  * Extra code between helpers and auxiliary functions.
  * Limited for custom types (structs)

### Heavy on external calls

Because all the storage used across the entire protocol (or the biggest part of the storage at least) is centralized on a single contract this implies that any storage reading has to be made through external calls. This requirement adds a lot of gas consumption to any call that requires any storage information. I understand that it's almost impossible to improve this without changing the entire protocol implementation. But I found that it's worth to mention here.

### Error prone keys implementation

Something that I thing could be improved a lot without mayor changes to the functionality is the way that the slot keys are being implemented.

There're a lot of duplicated code across the contracts that calculates the same keys for the same storage slots. This is a big security concern since it requires just a misspelled character on one of this implementations to break the functionality silently (mostly) or difficult to detect at least.

Example: Let's take for example some of the complex objects used for the minipool implementation:

```solidity
  keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".avaxNodeOpInitialAmt");
```

This calculates the key used for the initial amount for that particular protocol (through the minipoolIndex variable), the problem is that we have that same keccak calculation on multiple places and something like this:

```solidity
  keccak256(abi.encodePacked("minipool.item", minipoolIndex, "avaxNodeOpInitialAmt");
```

Looks like the same key calculation, BUT, there is a change on one of the keys. Generating a completely different key.

There're some recommendations that I'd like to mention in order to mitigate this problem:

### For simple constant keys, create a `Constants.sol` contract that can be inherited for other contracts.

Example: 

```solidity
  contract Constants {
    bytes32 private constant MULTISIG_COUNT_KEY = keccak256("multisig.count");
  }

  contract MultisigManager is Base, Constants {
    ...
    function registerMultisig(address addr) external onlyGuardian {
      ...
      uint256 index = getUint(MULTISIG_COUNT_KEY);
      ...
    }
    ...
  }
```

On this way, we can have a centralized point referencing all the keys needed for the protocol and be sure that there is no duplication or errors.

### For complex keys (where variables are used), move all the constant components to the start, convert that part on a constant key on the `Constants.sol` contract, and create helpers that concatenates those constants with the dinamic variables at the end and returns the actual storage key.


Example: 

```solidity
  contract Constants {
    bytes32 private constant MULTISIG_COUNT_KEY = keccak256("multisig.count");

    // BE SURE THAT YOU ADD A SEPARATOR AT THE END, JUST TO BE SURE THAT CANNOT BE KEY REPLICATIONS
    bytes32 private constant MINIPOOL_STATUS_PREFIX = keccak256(abi.encodePacked("minipool.item.status."));
  }

  contract MultisigManager is Base, Constants {
    ...
    function registerMultisig(address addr) external onlyGuardian {
      ...
      uint256 index = getUint(MULTISIG_COUNT_KEY);
      ...
    }
    ...
  }

  contract MinipoolManager is Constants {
    ...
    function withdrawMinipoolFunds(address nodeID) external nonReentrant {
      ...
      // INSTEAD OF THIS
      // setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".status")), uint256(MinipoolStatus.Launched));
      // DO THIS
      setUint(keccak256(abi.encodePacked(MINIPOOL_STATUS_PREFIX, minipoolIndex)), uint256(MinipoolStatus.Launched));
      ...
    }
    ...
  }
```

With these changes, it would be a lot harder to have unexpected errors.

### Extra code between helpers and auxiliary functions.

I'm a little concern about the amount of extra code required just to be able to work with this storage implementation. But I'm aware that, just like for the first point, it would be difficult to fix this (if not impossible) without big changes to the protocol implementation.

### Limited for custom types (structs)

Something that I do think could be improved is the limitations that we have for custom types.

For example, on the `MinipoolManager.sol` implementation we found that we have this defined struct:

```solidity
  struct Minipool {
		int256 index;
		address nodeID;
		uint256 status;
		uint256 duration;
		uint256 delegationFee;
		address owner;
		address multisigAddr;
		uint256 avaxNodeOpAmt;
		uint256 avaxNodeOpInitialAmt;
		uint256 avaxLiquidStakerAmt;
		// Submitted by the Rialto Oracle
		bytes32 txID;
		uint256 initialStartTime;
		uint256 startTime;
		uint256 endTime;
		uint256 avaxTotalRewardAmt;
		bytes32 errorCode;
		// Calculated in recordStakingEnd
		uint256 ggpSlashAmt;
		uint256 avaxNodeOpRewardAmt;
		uint256 avaxLiquidStakerRewardAmt;
	}
```

However, it's only being used on a external view function, for the actual storage manipulation we have functions like this one:

```solidity
  function getMinipool(int256 index) public view returns (Minipool memory mp) {
		mp.index = index;
		mp.nodeID = getAddress(keccak256(abi.encodePacked("minipool.item", index, ".nodeID")));
		mp.status = getUint(keccak256(abi.encodePacked("minipool.item", index, ".status")));
		mp.duration = getUint(keccak256(abi.encodePacked("minipool.item", index, ".duration")));
		mp.delegationFee = getUint(keccak256(abi.encodePacked("minipool.item", index, ".delegationFee")));
		mp.owner = getAddress(keccak256(abi.encodePacked("minipool.item", index, ".owner")));
		mp.multisigAddr = getAddress(keccak256(abi.encodePacked("minipool.item", index, ".multisigAddr")));
		mp.avaxNodeOpAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxNodeOpAmt")));
		mp.avaxLiquidStakerAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxLiquidStakerAmt")));
		mp.txID = getBytes32(keccak256(abi.encodePacked("minipool.item", index, ".txID")));
		mp.initialStartTime = getUint(keccak256(abi.encodePacked("minipool.item", index, ".initialStartTime")));
		mp.startTime = getUint(keccak256(abi.encodePacked("minipool.item", index, ".startTime")));
		mp.endTime = getUint(keccak256(abi.encodePacked("minipool.item", index, ".endTime")));
		mp.avaxTotalRewardAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxTotalRewardAmt")));
		mp.errorCode = getBytes32(keccak256(abi.encodePacked("minipool.item", index, ".errorCode")));
		mp.avaxNodeOpInitialAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxNodeOpInitialAmt")));
		mp.avaxNodeOpRewardAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxNodeOpRewardAmt")));
		mp.avaxLiquidStakerRewardAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".avaxLiquidStakerRewardAmt")));
		mp.ggpSlashAmt = getUint(keccak256(abi.encodePacked("minipool.item", index, ".ggpSlashAmt")));
	}
```

This could be improved a lot just adding a custom mapping on storage (with some custom helpers) using that custom struct that we have defined on the contract.

```solidity
  import {MinipoolManager} from './MinipoolManager.sol'; // I'd move this struct to the `Constant.sol` contract as well.

  contract Storage is IStorage {
    mapping(uint256 => MinipoolManager.Minipool) private _miniPools;

    function getMinipool(uint256 index) external view onlyRegisteredNetworkContract returns(MinipoolManager.Minipool memory)  {
      return _miniPools[index];
    }

    function delMinipool(uint256 index) external onlyRegisteredNetworkContract {
      delete _miniPools[index];
    }

    function setMinipool(uint256 index, MinipoolManager.Minipool calldata minipool) external onlyRegisteredNetworkContract {
      _miniPools[index] = minipool;
    }
  }

  abstract contract BaseAbstract {
    function getMinipool(bytes32 key) internal view returns (Minipool memory mp) {
      return gogoStorage.getMinipool(key);
    }
}

  contract MinipoolManager is Base, ReentrancyGuard, IWithdrawer {
    function getMinipool(uint256 index) public view returns (Minipool memory mp) {
      return super.getMinipool(index);
    }
  }
```

This will improve the code quality a lot making it shorter, more readable and less error prone.


# Add `address(0)` validations to avoid unexpected behaviours

## Where

* [Base.sol#L9](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/Base.sol#L9) - `_gogoStorageAddress` must not be `address(0)`
* [ClaimNodeOp.sol#L31](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/ClaimNodeOp.sol#L31
) - `gpt_` must not be `address(0)`


# Simplify if condition

## Where
* [BaseAbstract.sol#L44](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/BaseAbstract.sol#L44)
* [BaseAbstract.sol#L55](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/BaseAbstract.sol#L55)


`!(isGuardian || isContract)` is the same than `!isGuardian && !isContract` but the second option is more readable.

# Remove unused imports on `ClaimNodeOp.sol`

## Where
* [ClaimNodeOp.sol#L5
](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/ClaimNodeOp.sol#L5)
* [ClaimNodeOp.sol#L10
](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/ClaimNodeOp.sol#L10
)

# An invalid call to `initialize()` should revert the transaction

## Where
[ProtocolDAO.sol#L24-L26](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/ProtocolDAO.sol#L24-L26)

If someone tries to reinitialize the contract, the transaction should revert instead of return the execution silently.

# Remove unnecesary getter and use public for the storage definition instead

## Where

[Storage.sol#L51-L53](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/Storage.sol#L51-L53)

Remove the getter and use `address public guardian;` instead.

# Shadowed variables should be renamed

## Where

[/tokens/TokenggAVAX.sol#L72-L79](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/tokens/TokenggAVAX.sol#L72-L79)

`asset` is shadowing a storage variable.

```diff
-   function initialize(Storage storageAddress, ERC20 asset) public initializer {
-     __ERC4626Upgradeable_init(asset, "GoGoPool Liquid Staking Token", "ggAVAX");
+   function initialize(Storage storageAddress, ERC20 asset_) public initializer {
+     __ERC4626Upgradeable_init(asset_, "GoGoPool Liquid Staking Token", "ggAVAX");
      __BaseUpgradeable_init(storageAddress);

      rewardsCycleLength = 14 days;
      // Ensure it will be evenly divisible by `rewardsCycleLength`.
      rewardsCycleEnd = (block.timestamp.safeCastTo32() / rewardsCycleLength) * rewardsCycleLength;
    }
```

# Use same pragma version across all the contracts

## Where

* [/tokens/upgradeable/ERC20Upgradeable.sol#L2](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/tokens/upgradeable/ERC20Upgradeable.sol#L2)
* [/tokens/upgradeable/ERC4626Upgradeable.sol#L2](https://github.com/code-423n4/2022-12-gogopool/blob/1c30b320b7105e57c92232408bc795b6d2dfa208/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L2)

Use `pragma solidity 0.8.17` just like the rest of the contracts.