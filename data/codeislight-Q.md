- in Ocyticus constructor, the inherited Base contract, lacks assigning 'version' variable.
```
contract Ocyticus is Base {
	...
	constructor(Storage storageAddress) Base(storageAddress) {
		// assign 1 to 'version' variable
		defenders[msg.sender] = true;
	}
```