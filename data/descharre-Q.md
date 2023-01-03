Second variable (timestamp)of getGGPPriceInAVAX is never accessed so it might to remove the timestamp return.
-[Oracle.sol#L46-L51](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Oracle.sol#L46-L51)

Event is missing indexed keyword:
-[Storage.sol#L12](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L12)

Add an extra check if statement to check if the newAddress is not the 0 address
-[Storage.sol#L41-47](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L41-47)

Version is set everywhere to 1 so it's better to make it constant or immutable and only set it one time
-[BaseAbstract.sol#L19](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L19)

