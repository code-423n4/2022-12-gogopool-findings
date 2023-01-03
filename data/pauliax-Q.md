* Anyone can send AVAX to the ```MinipoolManager``` contract. Consider applying the ```onlyRegisteredNetworkContract``` modifier.
```solidity
  function receiveWithdrawalAVAX() external payable {}
```

* ```NewRewardsCycleStarted``` event is emitted too early because the ```inflate``` will change the total amount:
```solidity
  function startRewardsCycle() external {
    ...

    emit NewRewardsCycleStarted(getRewardsCycleTotalAmt());

    ...
    inflate();
```
```solidity
  function inflate() internal {
    ...
    setUint(keccak256("RewardsPool.RewardsCycleTotalAmt"), newTokens);
  }
```

* You do not need to inherit ```Initializable``` here because ```ERC20Upgradeable is Initializable```:
```solidity
  abstract contract ERC4626Upgradeable is Initializable, ERC20Upgradeable
```

* Contracts do not account for deflationary (or other weird) ERC20 tokens, for instance, when transferring it could check the actual balance (after-before) that was transferred, e.g. ```Vault```:
```solidity
  // Send tokens to this address now, safeTransfer will revert if it fails
  tokenContract.safeTransferFrom(msg.sender, address(this), amount);
  // Update balances
  tokenBalances[contractKey] = tokenBalances[contractKey] + amount;
```
```ERC4626Upgradeable```:
```solidity
  // Need to transfer before minting or ERC777s could reenter.
  asset.safeTransferFrom(msg.sender, address(this), assets);
  
  _mint(receiver, shares);
```

* ```allowedTokens``` is private and no events are emitted when changing the status making it a bit more complicated to find if a token is allowed or not: 
```solidity
  mapping(address => bool) private allowedTokens;

  function addAllowedToken(address tokenAddress) external onlyGuardian {
	allowedTokens[tokenAddress] = true;
  }

  function removeAllowedToken(address tokenAddress) external onlyGuardian {
	allowedTokens[tokenAddress] = false;
  }
```

* When creating a minipool, it does not actually verify: "node operator specified fee (must be between 0 and 1 ether) 2% is 0.2 ether":
```solidity
  setUint(keccak256(abi.encodePacked("minipool.item", minipoolIndex, ".delegationFee")), delegationFee);
```

* There is no priority on which pool will receive the ```avaxLiquidStakerAmt``` match first meaning new node operators that come after you may still get matched earlier if the Rialto multisig decides so. There should be objective priority criteria, e.g. higher GGP stake, or first come, first serve, etc. to give every node operator a transparent way to compete for funds. 

* When the protocol is paused, all the multisigs are disabled:
```solidity
  function pauseEverything() external onlyDefender {
	ProtocolDAO dao = ProtocolDAO(getContractAddress("ProtocolDAO"));
	dao.pauseContract("TokenggAVAX");
	dao.pauseContract("MinipoolManager");
	dao.pauseContract("Staking");
	disableAllMultisigs();
  }
```
However, it is still possible to call ```startRewardsCycle``` in the ```RewardsPool```, however, the execution will revert because the enabled count is 0:
```solidity
  uint256 tokensPerMultisig = allotment / enabledCount;
```
I think it would be nice to apply ```whenNotPaused``` modifier.

* ```ProtocolDAO``` and ```RewardsPool``` contracts contain ```initialize``` function which is callable by ```onlyGuardian```. Other parts of the codebase depend on the initialization parameters, however, they do not check if the aforementioned contracts were initialized. While not very likely, there is no guarantee the guardian will initialize the protocol in time but I assume it will be handled with automated scripts, so no big issue here.

* ```finishFailedMinipoolByMultisig``` is supposed to let the multisig move a minipool from the error state to the finished state, yet a ```Withdrawable``` state can also be transitioned to ```Finished```. This would discard the owner's stake and rewards.
```solidity
  function finishFailedMinipoolByMultisig(address nodeID) external {
    int256 minipoolIndex = onlyValidMultisig(nodeID);
    requireValidStateTransition(minipoolIndex, MinipoolStatus.Finished);
```

* Missing ```avaxAssignedHighWater``` field:
```solidity
  function getStaker(int256 stakerIndex) public view returns (Staker memory staker) {
    staker.ggpStaked = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpStaked")));
    staker.avaxAssigned = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssigned")));
    staker.avaxStaked = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxStaked")));
    staker.stakerAddr = getAddress(keccak256(abi.encodePacked("staker.item", stakerIndex, ".stakerAddr")));
    staker.minipoolCount = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".minipoolCount")));
    staker.rewardsStartTime = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".rewardsStartTime")));
    staker.ggpRewards = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".ggpRewards")));
    staker.lastRewardsCycleCompleted = getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".lastRewardsCycleCompleted")));
  }
```

*  All the supply of GGP is minted upfront, I wonder why can't it mint on demand with privileged ```_mint``` functions?
```solidity
  contract TokenGGP is ERC20 {
    uint256 private constant TOTAL_SUPPLY = 22_500_000 ether;

    constructor() ERC20("GoGoPool Protocol", "GGP", 18) {
      _mint(msg.sender, TOTAL_SUPPLY);
    }
  }
```
Also, theoretically, the governance can change ```getContractAddress("TokenGGP")``` and thus escape this limitation.

* Todos are left in nearly production code:
```solidity
  // Calculate rewards splits (these will all be zero if no rewards were recvd)
  // TODO Revisit this logic if we ever allow unequal matched funds
  uint256 avaxHalfRewards = avaxTotalRewardAmt / 2;
```
```solidity
  // TODO cant use onlySpecificRegisteredContract("ClaimNodeOp", msg.sender) since we also call from increaseMinipoolCount. Wat do?
  function setRewardsStartTime(address stakerAddr, uint256 time) public onlyRegisteredNetworkContract
```