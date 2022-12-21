**BaseAbstract.sol**
- L25/74 - It is less expensive to validate if(!(bool)) than to do if(bool == false).

- L41/42/44/52/53/55/72/73/74/82/83 - If a variable is created and only used once, it is less expensive to directly execute the operation where it is used.


**ClaimNodeOp.sol**
- L47/48/49/50/51/69/70/72/81/82 - If a variable is created and only used once, it is less expensive to directly execute the operation where it is used.


**MinipoolManager.sol**
- L130/131/153/154/229/230/235/236/260/274/275/279/294/295/297/298/300/301/317/318/338/339/341/342/365/366/371/374/393/394/398/399/340/429/430/467/468/469/503/504/548/549/558/559/560/652/656/662/663 - Yes a variable is created and only used once, it is less expensive to directly execute the operation where it is used.


**MultisigManager.sol**
- L36/37/110/111 - If a variable is created and only used once, it is less expensive to directly execute the operation where it is used.


**Oracle.sol**
- L37/38/39/58/59 - If a variable is created and only used once, it is less expensive to directly execute the operation where it is used.


**RewardsPool.sol**
- L55/60/68/75/83/84/87/88/96/98/126/127/128/135/136/193/194 - If a variable is created and only used once, it is less costly to directly execute the operation where it is used.


**Staking.sol**
- L67/68/81/82/88/89/95/96/104/105/111/112/118/119/127/128/134/135/141/142/150/151/157/158/165/166/174/175/181/182/188/189/197/198/205/210/218/219/225/226/232/233/241/242/249/250/275/276/277/278/295/296/297/298/300/301/309/310/312/313/314/372/373/380/382 - If a variable is created and only used once, it is less expensive to directly execute the operation where it is used.


**Storage.sol**
- L29 - It is less expensive to validate if(!(bool)) than to do if(bool == false).


**Vault.sol**
- L71/75/95/99/151/155/183/187 - When it is previously validated that an operation cannot occur underflow, it can be wrapped with unchecked to spend less gas on unnecessary validations, as in lines 75, 99, 155, 187.


**TokenggAVAX.sol
- L76 - The rewardsCycleLength variable is only set in the constructor, therefore it could be an immutable variable and this generates less gas costs.

- L97/99/133/134/138/162/163 - If a variable is created and only used once, it is less expensive to directly execute the operation where it is used.
