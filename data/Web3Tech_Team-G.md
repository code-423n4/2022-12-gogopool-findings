  GAS  OPTIMIZATION RELATED
 1) G-1:

   Description :  Use fixed size bytes array rather than string or bytes[] for smaller naming convention such as contract name(#27)
		  
                 Total No. of Instances Found: 7

                      For Example: 
	               ----------------
		      FileName: cotract/BaseAbstract.sol
		      Line No : 32,51,82,90
               
	   	      FileName:  contract/ClaimProtocolDAO.sol
		      Line No : 21
		
		      FileName: contract/RewardsPool.sol
		      Line No : 134
		
		      FileName: contract/Vault.sol
		      Line No : 52
		
		
		
  2) G-2:
     
 Description :  Use unchecked for arithmetic where you are sure that 'max' parameter won't over or underflow (#28)
	 

          Total No. of Instances Found: 1

           For Example: 
		```   
		   function loop(uint256 length) public {
      	   for (uint256 i = 0; i < length; ) {
        	// do something
       	   unchecked {
        	i++;
        	}
      	  }
              ```
	    ----------------
		FileName: contract/MinipoolManager.sol
		Line No : 619
		
		
		

3) G-3:
 Description :  Refactor a modifier to call a local function instead of directly having the code in the modifier , saving bytecode size and thereby deployment cost(#29)
	                 Modifiers code is copied in all instances where it's used, increasing bytecode size. 
	                 By doing a refractor to the internal function, one can reduce bytecode size significantly at the cost of one JUMP. 
					 Consider doing this only if you are constrained by bytecode size.


    For Example: 					 
					  
     Unoptimized:
    
     modifier onlyOwner() {
       require(owner() == msg.sender, "Ownable: caller is not the owner");
   	 _;
 	}
    
    Optimized:
    
    modifier onlyOwner() {
        _checkOwner();
   	         _;
 	    }
    function _checkOwner() internal view virtual {
    require(owner() == msg.sender, "Ownable: caller is not the owner");  }

    Total No. of Instances Found: 2
    
    FileName: contract/BaseAbstract.sol
    Line No : 7
		 
    FileName: contract/ProtocolDao.sol
    Line No : 12
               
   
4) G-4:
   Description :   Booleans use 8 bits while you only need 1 bit  (#60)
	                  Under the hood of solidity, Booleans (bool) are uint8 which means they use 8 bits of storage. A Boolean can only have two values: True or False. 
	                  This means that you can store a boolean in only a single bit.
	                  You can pack 256 booleans in a single word. The easiest way is to take a uint256 variable and use all 256 bits of it to represent individual booleans.
	 

          Total No. of Instances Found: 2
	    ----------------
		FileName: contract/MinipoolManager.sol
		Line No : 155
		
		FileName: contract/MultisigManager.sol
		Line No : 83
		
		
