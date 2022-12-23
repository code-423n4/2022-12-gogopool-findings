- in Ocyticus constructor, the inherited Base contract, lacks assigning 'version' variable.

		contract Ocyticus is Base {
			...
		constructor(Storage storageAddress) Base(storageAddress) {
			// needs to assign 'version' variable
			defenders[msg.sender] = true;
		}
