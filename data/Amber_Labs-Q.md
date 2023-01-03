# Report
​
## Gas Optimizations
​
### [GAS-1] Staking.stakeGGP()/restakeGGP() could transfer tokens to Vault directly
​
```solidity
 function stakeGGP(uint256 amount) external whenNotPaused {
     // Transfer GGP tokens from staker to this contract
     ggp.safeTransferFrom(msg.sender, address(this), amount);
     _stakeGGP(msg.sender, amount);
 }
```
​
```solidity
 function _stakeGGP(address stakerAddr, uint256 amount) internal {
     emit GGPStaked(stakerAddr, amount);
​
     // Deposit GGP tokens from this contract to vault
     Vault vault = Vault(getContractAddress("Vault"));
     ggp.approve(address(vault), amount);
     vault.depositToken("Staking", ggp, amount);
```
​
The two-phase GGP transfer could be simiplified to one GGP transfer which directly sends GGP to Vault. Two external calls could be optimized if we applied this patch: GGP.approve(), GGP.transferFrom(). However, this also leads to some code refactory efforts.
​
ClaimNodeOp.claimAndRestake() has a similiar issue of withdrawing tokens out from Vault and sending it back to Vault thru Staking with an extra GGP.approve() in the middle.
​
```solidity
 function claimAndRestake(uint256 claimAmt) external {
     Staking staking = Staking(getContractAddress("Staking"));
     uint256 ggpRewards = staking.getGGPRewards(msg.sender);
     if (ggpRewards == 0) {
         revert NoRewardsToClaim();
     }
     if (claimAmt > ggpRewards) {
         revert InvalidAmount();
     }
​
     staking.decreaseGGPRewards(msg.sender, ggpRewards);
​
     Vault vault = Vault(getContractAddress("Vault"));
     uint256 restakeAmt = ggpRewards - claimAmt;
     if (restakeAmt > 0) {
         vault.withdrawToken(address(this), ggp, restakeAmt);
         ggp.approve(address(staking), restakeAmt);
         staking.restakeGGP(msg.sender, restakeAmt);
     }
```
​
## Low Issues
​
### [L-1] TokenggAVAX may take away more than expected assets from users in depositAVAX()/withdrawAVAX() 
​
Since previewDeposit() rounds down and previewWithdraw() rounds up, more than expected assets/shares would be taken from the user. We should consider return extra AVAX in depositAVAX() and mint just enough GG tokens. Also, sends less AVAX to the user and burn just enough GG tokens owned by the user.
​
```solidity
 function depositAVAX() public payable returns (uint256 shares) {
     uint256 assets = msg.value;
     // Check for rounding error since we round down in previewDeposit.
     if ((shares = previewDeposit(assets)) == 0) {
         revert ZeroShares();
     }
​
     emit Deposit(msg.sender, msg.sender, assets, shares);
​
     IWAVAX(address(asset)).deposit{value: assets}();
     _mint(msg.sender, shares);
     afterDeposit(assets, shares);
 }
​
 function withdrawAVAX(uint256 assets) public returns (uint256 shares) {
     shares = previewWithdraw(assets); // No need to check for rounding error, previewWithdraw rounds up.
     beforeWithdraw(assets, shares);
     _burn(msg.sender, shares);
​
     emit Withdraw(msg.sender, msg.sender, msg.sender, assets, shares);
​
     IWAVAX(address(asset)).withdraw(assets);
     msg.sender.safeTransferETH(assets);
 }
```