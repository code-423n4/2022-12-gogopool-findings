Don't initialize variables with default value


File: MinipoolManager.sol

618: 		uint256 total = 0;
File: MultisigManager.sol

84: 		for (uint256 i = 0; i < total; i++) {
File: Ocyticus.sol

61: 		for (uint256 i = 0; i < count; i++) {

File: RewardsPool.sol

74: 		for (uint256 i = 0; i < inflationIntervalsElapsed; i++) {

141: 		uint256 contractRewardsTotal = 0;

215: 		for (uint256 i = 0; i < count; i++) {

230: 		for (uint256 i = 0; i < enabledMultisigs.length; i++) {

File: Staking.sol

427: 		uint256 total = 0;
------------------------------------------

++i costs less gas than i++, especially when it's used in for-loops (--i/i-- too)
Saves 5 gas per loop



File: MinipoolManager.sol

619: 		for (uint256 i = offset; i < max; i++) {

623: 				total++;

File: MultisigManager.sol

84: 		for (uint256 i = 0; i < total; i++) {

File: Ocyticus.sol

61: 		for (uint256 i = 0; i < count; i++) {

File: RewardsPool.sol

74: 		for (uint256 i = 0; i < inflationIntervalsElapsed; i++) {

215: 		for (uint256 i = 0; i < count; i++) {

219: 				enabledCount++;

230: 		for (uint256 i = 0; i < enabledMultisigs.length; i++) {

File: Staking.sol

428: 		for (uint256 i = offset; i < max; i++) {

431: 			total++;

---------------------------------------------
 Using private rather than public for constants, saves gas
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table



File: MultisigManager.sol

27: 	uint256 public constant MULTISIG_LIMIT = 10;
