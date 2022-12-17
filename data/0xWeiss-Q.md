import {ERC20} from "@rari-capital/solmate/src/mixins/ERC4626.sol";
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Staking.sol#L11

Why import from erc4626 erc20? 


THEY ARE TAKING THE PRICE FROM A DEX, ISN'T THAT A FLASHLOAN OPPORTUNITY?
https://github.com/code-423n4/2022-12-gogopool/blob/aec9928d8bdce8a5a4efe45f54c39d4fc7313731/contracts/contract/Oracle.sol#L36-L40