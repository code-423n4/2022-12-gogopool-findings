Currently for boolean storage they are using a mapping from uint to bool -> https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L16

Should create a function that takes in an array of booleans, and use a bitmap instead: https://soliditydeveloper.com/bitmaps