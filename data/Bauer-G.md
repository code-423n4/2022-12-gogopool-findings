# Unnecessary checked arithmetic in for loop

A lot of times there is no risk that the loop counter can overflow.

Using Solidity's unchecked block saves the overflow checks.

Good:

uint len = xxxx.length;
for (uint i; i < len; ) {
    // ...

    unchecked { i++; }
}

Instances (10):
File: MinipoolManager.sol

619: 		for (uint256 i = offset; i < max; i++) {

File: MultisigManager.sol

84:                 for (uint256 i = 0; i < total; i++) {

File: Ocyticus.sol

61:                 for (uint256 i = 0; i < count; i++) {


File: RewardsPool.sol

74:                   for (uint256 i = 0; i < inflationIntervalsElapsed; i++) {

215:                  for (uint256 i = 0; i < count; i++) {

230:                 for (uint256 i = 0; i < count; i++) {


File: Staking.sol

428:                  for (uint256 i = offset; i < max; i++) {




