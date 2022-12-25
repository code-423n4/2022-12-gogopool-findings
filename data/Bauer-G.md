# 1.Unnecessary checked arithmetic in for loop

A lot of times there is no risk that the loop counter can overflow.

Using Solidity's unchecked block saves the overflow checks.

Good:

uint len = xxxx.length;
for (uint i; i < len; ) {
    // ...

    unchecked { i++; }
}

Instances (7):
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




# 2.Use Shift Right/Left instead of Division/Multiplication if possible
Description
A division/multiplication by any number x being a power of 2 can be calculated by shifting log2(x) to the right/left.
While the DIV opcode uses 5 gas, the SHR opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

Good:

uint256 b = a >> 1


Instances (1):
File: MinipoolManager.sol

413: 		uint256 avaxHalfRewards = avaxTotalRewardAmt / 2;



