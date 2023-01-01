## [QA-01] MISSING CHECKS FOR ADDRESS(0X0)

Lack of zero-address validation on address parameters may lead to transaction reverts, waste gas, require resubmission of transactions and may even force contract redeployments in certain cases within the protocol.

```
ClaimNodeOp.sol

46:   function isEligible(address stakerAddr) external view returns (bool) {
56:   function calculateAndDistributeRewards(address stakerAddr, uint256 totalEligibleGGPStaked) external onlyMultisig {

Storage.sol
41: function setGuardian(address newAddress) external {
```

#### Recommended Mitigation Steps

Consider adding explicit zero-address validation on input parameters of address type.

## [QA-02] EMPTY BLOCKS SHOULD BE REMOVED OR EMIT SOMETHING

#### Code contains empty block

```
MinipoolManager.sol
106: 	function receiveWithdrawalAVAX() external payable {}
```

#### Recommended Mitigation Steps

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

## [QA-03] FOR FUNCTIONS, FOLLOW SOLIDITY STANDARD NAMING CONVENTIONS
The above functions don’t follow Solidity’s standard naming convention:
```
MinipoolManager.sol
115: function onlyOwner(int256 minipoolIndex) private view returns (address) {
127: function onlyValidMultisig(address nodeID) private view returns (int256) {
140: function requireValidMinipool(address nodeID) private view returns (int256) {
152: function requireValidStateTransition(int256 minipoolIndex, MinipoolStatus to) private view {
```
* internal and private functions : the mixedCase format starting with an underscore (_mixedCase starting with an underscore)

## [QA-04] Use latest Solidity version and stable pragma statement
```
ERC20Upgradeable.sol
ERC4626Upgradeable.sol
```
Solidity pragma versioning should be upgraded to latest available version and use a stable pragma statement .
