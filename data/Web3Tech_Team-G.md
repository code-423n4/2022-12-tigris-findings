  ## GAS  OPTIMIZATION RELATED

1) G-1:

          Description : Use short reason strings in require statements to save gas 

          Total No. of Instances Found: 10

           For Example: 
	    ----------------
		FileName: contracts/GovNFT.sol
		Line No : 210
               ```require(keccak256(_payload) == payloadHash, "NonblockingLzApp: invalid payload");
               ```
		We can use reasons in the form of short codes , like 'IP' for above example , and in documentation
		or in comment section ,its detail description can be provided to refer the emitted error
		
		
	   	FileName: contracts/GovNFT.sol
		Line No : 185,209,210
		
		FileName: contracts/PairsContract.sol
		Line No : 95,52,84
		
		FileName: contracts/Referrals.sol
		Line No : 21
		
		FileName: contracts/Trading.sol
		Line No : 876,877 ,887
		
		
2) G-2:
       
	   Description : If possible , make your state varibles 'private' as it consumes less gas. 
		
	   Total No. of Instances Found: 3
		
	   FileName: contracts/GovNFT.sol
     
	   Line No : 17,19
	   
	   FileName: contracts/GovNFTBridged.sol
	
	   Line No : 15
	   
	   
		
		
3) G-3:
	
       Description : No need to initialize by default intialized variables.In Solidity ,all unit vairables are by default initialized to zero.
	   
	   For Example:
	   
	   uint a; //optimized 
       uint a =0 ; // unoptimized 
       
	   Total No. of Instances Found: 17
	   
	   FileName: contracts/BondNFT.sol
	   
	   Line No : 284,292,300,342
	   
	   FileName: contracts/GovNFT.sol
	   
	   Line No :    53 ,67 ,78 ,95 ,105 ,131 ,200 ,246 ,252 ,258 ,325
	 
	   FileName: contracts/Referrals.sol
	
       Line No : 70,73
   
  	
   4)  G-4:
       
	   Description : ++i costs less gas than i++ (same in case of '--')
		
	   Total No. of Instances Found: 16
		
	   FileName: contracts/GovNFT.sol
     
	   Line No : 53,67,78,95,105 ,131 ,200 ,246 ,252 ,258,325
		
	   FileName: contracts/BondNFT.sol
       
	   Line No : 220 ,284 ,292 ,300 ,342
	   
	   
