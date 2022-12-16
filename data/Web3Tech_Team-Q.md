
        QA-1:
       
	    Description : Revert on Transfer to the Zero Address.Validation checks should be provided for "Null" address.
	   
	    Total No. of Instances Found: 1
	   
	   For example :
	   ---------------
	   require(address(_owneraddress) != address(0));   
	   
	   FileName: contracts/BondNFT.sol
       
	   Line No : 369
	   