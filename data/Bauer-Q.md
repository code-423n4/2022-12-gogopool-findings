# No same value input control

	function setGuardian(address newAddress) external {
		// Check tx comes from current guardian
		if (msg.sender != guardian) {
			revert MustBeGuardian();
		}
		// Store new address awaiting confirmation
		newGuardian = newAddress;
	}

Recommended Mitigation Steps:
Add code like this; if (newAddress == guardian revert ADDRESS_SAME();

