1:

The startRewardsCycle() event uses the pre-update RewardsCycleTotalAmt
The event uses the pre-update RewardsCycleTotalAmt and needs to be called after inflate() to be the latest RewardsCycleTotalAmt
RewardsPool.sol
```solidity
    function startRewardsCycle() external {
        if (!canStartRewardsCycle()) {
            revert UnableToStartRewardsCycle();
        }

-       emit NewRewardsCycleStarted(getRewardsCycleTotalAmt());
..
        inflate();
+       emit NewRewardsCycleStarted(getRewardsCycleTotalAmt());        
```

2:
getMinimumGGPStake() is used to get the minimum GGPStake, theoretically use round up, to avoid not enough stake
```solidity
    function getMinimumGGPStake(address stakerAddr) public view returns (uint256) {
...
-       uint256 ggp100pct = avaxAssigned.divWadDown(ggpPriceInAvax);
+       uint256 ggp100pct = avaxAssigned.divWadUp(ggpPriceInAvax);
```    

3:upgradeExistingContract() need unregisterContract() first and then registerContract(). Avoid newAddr==existingAddr.
unregisterContract() remove newAddr
```solidity
    function upgradeExistingContract(
        address newAddr,
        string memory newName,
        address existingAddr
    ) external onlyGuardian {
+       unregisterContract(existingAddr);
        registerContract(newAddr, newName);
-       unregisterContract(existingAddr);
    }
```

4.registerContract() adds verification that both addr and name do not exist to avoid being overwritten
```solidity

    function registerContract(address addr, string memory name) public onlyGuardian {
+       require(!getBool(keccak256(abi.encodePacked("contract.exists"))))
+       require(getAddress(keccak256(abi.encodePacked("contract.address", name))==address(0)); 
        setBool(keccak256(abi.encodePacked("contract.exists", addr)), true);
        setAddress(keccak256(abi.encodePacked("contract.address", name)), addr);
        setString(keccak256(abi.encodePacked("contract.name", addr)), name);
    }

```