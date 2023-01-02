# Typos, code consistencies

## Storage.sol: Inconsistent event emission (code style)

Line 36, in the constructor, the emission comes before state change. In all other function, the emission comes after state changes and would be more consistent to keep this throughout the programme.

In addition, using the guardian variable in the event emission ensures that the log is emitting the actual change event, rather than its prediction.

I suggest a change from:
```
constructor() {
		emit GuardianChanged(address(0), msg.sender);
		guardian = msg.sender;
	}
```
to:
```
constructor() {
		
		guardian = msg.sender;
		emit GuardianChanged(address(0), guardian);
	}
```

