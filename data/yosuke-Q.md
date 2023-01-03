## [YO NON-CRITICAL-1] Use requireValidStaker() istead of getIndexOf()

### Handle
yosuke

## Vulnerability details
### Impact
When getIndexOf() is used, if the argument address is not registered as a staker, it is not revert.


### Proof of Concept
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L174
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L197

### Recommended Mitigation Steps
Use requireValidStaker() istead of getIndexOf().
ex)
before
```solidity=
function getMinipoolCount(address stakerAddr) public view returns (uint256) {
    int256 stakerIndex = getIndexOf(stakerAddr);
    return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".minipoolCount")));
}
```
after
```solidity=
function getMinipoolCount(address stakerAddr) public view returns (uint256) {
    int256 stakerIndex = requireValidStaker(stakerAddr);
    return getUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".minipoolCount")));
}
```