gas optimizations

file:https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Ocyticus.sol


The following code

```
for (uint256 i = 0; i < count; i++) {
			(addr, enabled) = mm.getMultisig(i);
			if (enabled) {
				mm.disableMultisig(addr);
			}
		}
```

Can be modified to

```
for (uint256 i = 0; i < count;) {
	(addr, enabled) = mm.getMultisig(i);
	if (enabled) {
		mm.disableMultisig(addr);
	}

         unchecked {
             ++i;
         }
}
```





