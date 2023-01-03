
# Missing checks on functions
### Summary
No check of `gogoStorageAddress` not being 0 before assigning it to `gogoStorage`

### Impact
Most of `BaseAbstract.sol` functions not working because of wrong address at `gogoStorage` 

### Details
Similar issues with `ggp` in:
[ClaimNodeOp](https://github.com/code-423n4/2022-12-gogopool/blob/3fbb3c89b3f656a13431705590f25a64b4468916/contracts/contract/ClaimNodeOp.sol#L31)
[Staking](https://github.com/code-423n4/2022-12-gogopool/blob/b918009eb949705992cf21e4d516c469dbae223b/contracts/contract/Staking.sol#L62)

and the `gogoStorage` from the summary
[BaseUpgradeable](https://github.com/code-423n4/2022-12-gogopool/blob/b918009eb949705992cf21e4d516c469dbae223b/contracts/contract/BaseUpgradeable.sol#L11)
[Base](https://github.com/code-423n4/2022-12-gogopool/blob/b918009eb949705992cf21e4d516c469dbae223b/contracts/contract/Base.sol#L11)

### Recommended steps

```diff
	constructor(Storage storageAddress, ERC20 ggp_) Base(storageAddress) {
		+ require(address(ggp_) != address(0));
		version = 1;
		ggp = ggp_;
	}
```

```diff
	function __BaseUpgradeable_init(Storage gogoStorageAddress) internal onlyInitializing {
		+ require(address(gogoStorageAddress) != address(0));
        gogoStorage = Storage(gogoStorageAddress);
	}
```
