# [L-01] Deprecated @rari-capital/solmate

The GoGoPool is still using @rari-capital/solmate library, meanwhile the package has been deprecated. https://www.npmjs.com/package/@rari-capital/solmate.

Using outdated package, can lead to another open security vulnerabilities which being patched on the latest release of a library/package. Thus it's necessary to keep the latest updated package.

Consider to use the updated one, https://github.com/transmissions11/solmate

# [L-02] MultisigManager doesn't have `unregister` function

MultisigManager.sol contains a hard limit of `MULTISIG_LIMIT` to 10.
Meanwhile in the contract, there are functions like `registerMultisig`, `enableMultisig`, and `disableMultisig`. So, logically, there should be a `unregister` or `remove` for the multisig.

If the limit is reached and some address was disabled or maybe compromised, it will locked the protocol from registering new multisig.

If multisig is secure enough so that it wont get compromised, then the hard limit of it seems unnecessary.

# [L-03] Missing TokenggAVAX.sol storage gaps

Storage gaps are a convention for reserving storage slots in a base contract, allowing future versions of that contract to use up those slots without affecting the storage layout of child contracts. The TokenggAVAX, which inherits openzeppelin/contracts-upgradeable library is missing this storage gaps.

https://docs.openzeppelin.com/upgrades-plugins/1.x/writing-upgradeable#storage-gaps