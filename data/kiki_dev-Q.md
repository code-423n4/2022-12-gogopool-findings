# Non - Critical

#### [1] Version too recent to be trusted. Consider deploying with 0.8.16

It is recomended to use a more battle tested pragma incase security vulnerabilities are discoverd. 0.8.17 is considered to recent.

Files affected:
All files in scope.

#### [2] Some conditionals compare variables to a boolean constant. 

Boolean constants can be used directly and do not need to be compare to true or false.

Recommendation:
Remove the equality to the boolean constant.
https://github.com/crytic/slither/wiki/Detector-Documentation#boolean-equality 

Lines of effected code:
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L25
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/BaseAbstract.sol#L74
https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/Storage.sol#L29