# Gas optimization :

# Summary

 [G-01]  Preferer use unchecked statement for values cannot over/underflow  (7)
 [G-02]  Do not compare a boolean to true or false

# [G-01] Prefer using unchecked statement for values cannot over/underflow 

###  MultisigManager.sol [L84](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MultisigManager.sol#L84) : 

	As specified max of "multisig.count" never going over 10 then i<= total <= 10
	
	we can simply add an unchecked statement that increment i value like this : unchecked {i++;}, or unchecked {++i;} if you prefer 
	
	By adding this statement we saved 71 gas fee (11777 - 11641) per call  (on average)


	This gas optimization is  also available in :


###  Ocyticus.sol : [L61](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Ocyticus.sol#L61)

###  RewardsPool.sol : [L74](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L74) [L215](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L215) [L219](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L219)
		L74  (i <= inflationIntervalsElapsed) 
		L215 (for incrementtion of i)
		L219 (for incrementtion enbledCount); 

###  MiniPoolManager.sol : [L619](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/MiniPoolManager.sol#L619)
		L619 (for both i and total increntation variable, because total <= i <= max)


### Staking.sol :  [L428](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Staking.sol#L428)
		L428 (variable i will never overflow because i <= max <= totalStackers)


### Details :

								Before		After		Saved
 								  G1		  G1

	contracts/contract/MinipoolManager.sol:MinipoolManager contract

	Deployment Cost 					3918819	3910211 	8608
	Deployment Size 					19732		19689		43
	getMinipools()   					344064		342949		1115 

	contracts/contract/MultisigManager.sol:MultisigManager contract

	Deployment Cost  					696692		689686		7006
	Deployment Size  					3562		3527 		35
	requireNextActiveMultisig()   			7242		7239		3 

	contracts/contract/Ocyticus.sol:Ocyticus contract

	Deployment Cost					569114		559307		9807
	Deployment Size					2831		2782		49
	disableAllMultisigs()			       13343		13284		59 
	pauseEverything()					84853		84808 		45 

	contracts/contract/RewardsPool.sol:RewardsPool contract

	Deployment Cost					1143951	1138344	5607
	Deployment Size					5796 		5768		28
	getInflationAmt()					124631		96054		28577 
	startRewardsCycle()				       256692 	250051		6641 

	contracts/contract/Staking.sol:Staking contract

	Deployment Cost					2368350 	2359135	9215
	Deployment Size					12048 		12002		46
	getStakers()						25065		24867		198 


	Total saved Deployment Cost : 40243

	Total saved Deployment Size : 201

	Total saved methods call : 36638 

	The protocol for getting these results was to compare all forge test --gas-report, between non-optimized and optimized case for each function that was affected by any optimisation. 


# [G-02] Do not compare a boolean to true or false


### BaseAbstract.sol :  [L25](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L25) -  [L74](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L74)

### Storage.sol  [L29](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L29)

## Details
	Directly use the boolean

	this optimization saves 18 gas fee per statement (tested on remix with simple example)

## PoC


![Capture d’écran du 2023-01-03 11-19-58](https://user-images.githubusercontent.com/121401405/210338711-7b6eb2c8-c0c8-43d0-ad3b-b0129ec95afd.png)