
# Emitting events even when code reverts

# Impact

There are a few places in code where events are emitted. A function  can revert  at any point of time. The external listeners to the event assume the event happened while there are chances the code might have reverted.

Eg: emit AVAXWithdrawn(contractName, amount); in vault.sol
     emit AVAXTransfer(fromContractName, toContractName, amount);
    emit WithdrawnForStaking(msg.sender, assets); in TokenggAVAX.sol

#Remedy

Put emit emitters at the end of the code . I observed there are a few places where this is followed.
 
