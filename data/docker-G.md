We know that++i saves gas more than i++.
We can't just look up the i variable，I found multiple variable++ usages.
The principle is the same. We expect to change it to ++variable

for example：
/contracts/contract/RewardsPool.sol
```
for (uint256 i = 0; i < count; i++) {
    (address addr, bool enabled) = mm.getMultisig(i);
    if (enabled) {
        enabledMultisigs[enabledCount] = addr;
        enabledCount++;
    }
}
```
maybe wo should use `++enabledCount`

contract/Stacking.sol
```
		uint256 total = 0;
		for (uint256 i = offset; i < max; i++) {
			Staker memory s = getStaker(int256(i));
			stakers[total] = s;
			total++;
		}
```

 maybe wo should use `++total`



I used local variable test to verify that ++variable really saves gas compared with variable++.
```
// SPDX-License-Identifier: GPL-3.0

pragma solidity >=0.7.0 <0.9.0;


contract IteratorTest {


    function increment() public pure{
        uint256 enabledCount;
        uint256 count = 100;

        for (uint256 i = 0; i < count; i++) {
				enabledCount++;  // ++enabledCount;
		}


    }


}

```
