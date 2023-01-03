### [G-1] Use function instead of modifiers 

replace modifier with private view function
EG.

``` solidity
24: -- deleted @audit-info modifier onlyRegisteredNetworkContract() {
++ Added @audit-info function onlyRegisteredNetworkContract() private view {
	if (getBool(keccak256(abi.encodePacked("contract.exists", msg.sender))) == false) {
		revert InvalidOrOutdatedContract();
	}
}

File: c:\Users\pc\Desktop\code\2022-12-gogopool\contracts\contract\Staking.sol
156: 	function increaseAVAXAssignedHighWater(address stakerAddr, uint256 amount) public (onlyRegisteredNetworkContract  --deleted @audit-info ) {
            ++ Added   onlyRegisteredNetworkContract (); @audit-info
157: 		int256 stakerIndex = requireValidStaker(stakerAddr);
158: 		addUint(keccak256(abi.encodePacked("staker.item", stakerIndex, ".avaxAssignedHighWater")), amount);
159: 	}
160: 

```
BaseAbstract 
``` solidity
File: c:\Users\pc\Desktop\code\2022-12-gogopool\contracts\contract\BaseAbstract.sol
24: 	modifier onlyRegisteredNetworkContract() {
25: 		if (getBool(keccak256(abi.encodePacked("contract.exists", msg.sender))) == false) {
26: 			revert InvalidOrOutdatedContract();
27: 		}
28: 		_;
29: 	}



File: c:\Users\pc\Desktop\code\2022-12-gogopool\contracts\contract\BaseAbstract.sol
32: 	modifier onlySpecificRegisteredContract(string memory contractName, address contractAddress) {
33: 		if (contractAddress != getAddress(keccak256(abi.encodePacked("contract.address", contractName)))) {
34: 			revert InvalidOrOutdatedContract();
35: 		}
36: 		_;
37: 	}


File: c:\Users\pc\Desktop\code\2022-12-gogopool\contracts\contract\BaseAbstract.sol
40: 	modifier guardianOrRegisteredContract() {
41: 		bool isContract = getBool(keccak256(abi.encodePacked("contract.exists", msg.sender)));
42: 		bool isGuardian = msg.sender == gogoStorage.getGuardian();
43: 
44: 		if (!(isGuardian || isContract)) {
45: 			revert MustBeGuardianOrValidContract();
46: 		}
47: 		_;
48: 	}

File: c:\Users\pc\Desktop\code\2022-12-gogopool\contracts\contract\BaseAbstract.sol
51: 	modifier guardianOrSpecificRegisteredContract(string memory contractName, address contractAddress) {
52: 		bool isContract = contractAddress == getAddress(keccak256(abi.encodePacked("contract.address", contractName)));
53: 		bool isGuardian = msg.sender == gogoStorage.getGuardian();
54: 
55: 		if (!(isGuardian || isContract)) {
56: 			revert MustBeGuardianOrValidContract();
57: 		}
58: 		_;
59: 	}
60: 

File: c:\Users\pc\Desktop\code\2022-12-gogopool\contracts\contract\BaseAbstract.sol
62: 	modifier onlyGuardian() {
63: 		if (msg.sender != gogoStorage.getGuardian()) {
64: 			revert MustBeGuardian();
65: 		}
66: 		_;
67: 	}


File: c:\Users\pc\Desktop\code\2022-12-gogopool\contracts\contract\BaseAbstract.sol
70: 	modifier onlyMultisig() {
71: 		int256 multisigIndex = int256(getUint(keccak256(abi.encodePacked("multisig.index", msg.sender)))) - 1;
72: 		address addr = getAddress(keccak256(abi.encodePacked("multisig.item", multisigIndex, ".address")));
73: 		bool enabled = (addr != address(0)) && getBool(keccak256(abi.encodePacked("multisig.item", multisigIndex, ".enabled")));
74: 		if (enabled == false) {
75: 			revert MustBeMultisig();
76: 		}
77: 		_;
78: 	}


File: c:\Users\pc\Desktop\code\2022-12-gogopool\contracts\contract\BaseAbstract.sol
81: 	modifier whenNotPaused() {
82: 		string memory contractName = getContractName(address(this));
83: 		if (getBool(keccak256(abi.encodePacked("contract.paused", contractName)))) {
84: 			revert ContractPaused();
85: 		}
86: 		_;
87: 	}

```
Ocyticus

``` solidity
File: c:\Users\pc\Desktop\code\2022-12-gogopool\contracts\contract\Ocyticus.sol
15: 	modifier onlyDefender() {
16: 		if (!defenders[msg.sender]) {
17: 			revert NotAllowed();
18: 		}
19: 		_;
20: 	}
```
ProtocolDAO
``` solidity
File: c:\Users\pc\Desktop\code\2022-12-gogopool\contracts\contract\ProtocolDAO.sol
12: 	modifier valueNotGreaterThanOne(uint256 setterValue) {
13: 		if (setterValue > 1 ether) {
14: 			revert ValueNotWithinRange();
15: 		}
16: 		_;
17: 	}
18: 
```
Storage
``` solidity  
File: c:\Users\pc\Desktop\code\2022-12-gogopool\contracts\contract\Storage.sol
28: 	modifier onlyRegisteredNetworkContract() {
29: 		if (booleanStorage[keccak256(abi.encodePacked("contract.exists", msg.sender))] == false && msg.sender != guardian) {
30: 			revert InvalidOrOutdatedContract();
31: 		}
32: 		_;
33: 	}
```
TokenggAVAX
``` solidity
File: c:\Users\pc\Desktop\code\2022-12-gogopool\contracts\contract\tokens\TokenggAVAX.sol
58: 	modifier whenTokenNotPaused(uint256 amt) {
59: 		if (amt > 0 && getBool(keccak256(abi.encodePacked("contract.paused", "TokenggAVAX")))) {
60: 			revert ContractPaused();
61: 		}
62: 		_;
63: 	}
```

### [G-2] Consider Unused named returns
While not consuming more gas with the Optimizer enabled: using both named returns and a return statement isn't necessary. Removing one of those can improve code clarity:


Using both named returns and a return statement isn't necessary.
Removing unused named return variables can reduce gas usage and improve code clarity. To save gas and improve code quality: consider using only one of those.

``` solidity
File: c:\Users\pc\Desktop\code\2022-12-gogopool\contracts\contract\Oracle.sol
36: function getGGPPriceInAVAXFromOneInch() external view returns (uint256 price, uint256 timestamp) {
46: function getGGPPriceInAVAX() external view returns (uint256 price, uint256 timestamp) {
```

``` solidity
File: c:\Users\pc\Desktop\code\2022-12-gogopool\contracts\contract\tokens\TokenggAVAX.sol
167: 	function depositAVAX() public payable returns (uint256 shares) {
181: 	function withdrawAVAX(uint256 assets) public returns (uint256 shares) {
192: 	function redeemAVAX(uint256 shares) public returns (uint256 assets) {
```

``` solidity
File: c:\Users\pc\Desktop\code\2022-12-gogopool\contracts\contract\tokens\upgradeable\ERC4626Upgradeable.sol
42: 	function deposit(uint256 assets, address receiver) public virtual returns (uint256 shares) {
56:   function mint(uint256 shares, address receiver) public virtual returns (uint256 assets) {
73: 	) public virtual returns (uint256 shares) {
95: 	) public virtual returns (uint256 assets) {

```