On lines 156 and 157, there are uint256 variable declarations.
```
Line 156 and 157
uint256 rewardsAmount = 100 ether;
uint256 liquidStakerRewards = 50 ether - ((50 ether * 15) / 100);
```

Since both these variables hold only upto 100 ether, we can make them both into uint128 which can still hold the same amount of ether and we’ll be able to save gas by using only one slot of uint128 + uint128 instead of 2 uint256 slots hence saving half the gas. 

The next finding is,
on line 188 of TokenggAVAX.t.sol, we see ```uint256 endTime = block.timestamp + mp.duration;```

This variable endTime is again used in line 191 ```minipoolMgr.recordStakingEnd{value: totalStakedAmount + rewardsAmount}(nodeID, endTime, rewardsAmount);```

Since the variable is being called only once, we can save gas by removing the specific uint256 declaration of endTime and directly mentioning  ```block.timestamp + mp.duration;```

This saves a whole memory slot that would otherwise be used to make the uint256 slot.

So, the end result would look something like this on line 191:

```minipoolMgr.recordStakingEnd{value: totalStakedAmount + rewardsAmount}(nodeID, block.timestamp + mp.duration, rewardsAmount);```

The next finding is on Line 152:

```
Line 152: 
uint256 depositAmount = 2000 ether;
uint256 stakingWithdrawAmount = 1000 ether;
uint256 totalStakedAmount = 2000 ether;
```

We have three variables here declaring the amount of ether they store. Note that depositAmount is used multiple times throughout the remainder of the code by stakingWithdrawAmount and totalStakedAmount are used 1 and 2 times, respectively.

So, for stakingWithdrawAmount, which is declared as 1000 ether, it can be directly addressed in the lines that used the variable. Hence on line 178, we can replace
```
assertEq(ggAVAX.stakingTotalAssets(), stakingWithdrawAmount); 
with just assertEq(ggAVAX.stakingTotalAssets(), 1000 ether);
```

Similarly, for the variable totalStakedAmount, on lines 176 and 191, we can replace the existing lines with:
```assertEq(address(rialto).balance, rialtoInitBal + 2000 ether)
minipoolMgr.recordStakingEnd{value: 2000 ether+ rewardsAmount}(nodeID, endTime, rewardsAmount);;```