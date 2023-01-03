
# Summary

[NC-01] Revert an explicit error for better understanding
[NC-02] Return keyword is not necessary
[NC-03] Return assets value directly when is equal to zero


# [NC-01] Revert an explicit error for better understanding

 ### RewardsPool.sol [L36](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L36) 

     In the initialize function I recommend to revert an explicit Error instead of returning nothing. It's a better understanding for the gardian 
     that would like to initialize the rewards pool, and for the developper that want to read an explicit code. 

     Even if when you're using "return ;" you optimize gas consumption
     
### Recommandation : create an error, for example RewardsPoolAlreadyInitialised(), and revert it in L36.


![Capture d’écran du 2023-01-03 12-33-52](https://user-images.githubusercontent.com/121401405/210349493-3252a314-f8ca-4bf8-a70a-7bdd6463fd42.png)


# [NC-02] Return keyword is not necessary

 ### RewardsPool.sol [L77](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/RewardsPool.sol#L77) 

    currentTotalSupply & newTotalSupply already returned in the declaration function 

![Capture d’écran du 2023-01-03 12-27-08](https://user-images.githubusercontent.com/121401405/210348551-1a67fe8a-4881-4f17-80d4-cf5042eb02b7.png)


### Recommendation 

![Capture d’écran du 2023-01-03 12-29-25](https://user-images.githubusercontent.com/121401405/210348920-051b8296-746d-47be-b638-e1bc3a5d4d9a.png)


# [NC-03] Return assets value directly when is equal to zero

### ERC4626Upgradeable.sol [L123](https://github.com/code-423n4/2022-12-gogopool/blob/main/contracts/contract/tokens/upgradeable/ERC4626Upgradeable.sol#L123) 

    When your are performing a convertion of assests value into shares, I recommand to return assets value directly when asssets is equal to 0 
    (instead of doing math calculation that going to return 0)

### Recommendation 


![Capture d’écran du 2023-01-03 16-31-33](https://user-images.githubusercontent.com/121401405/210388972-cb4c1664-8f13-4a72-89aa-ad9c59dd278b.png)