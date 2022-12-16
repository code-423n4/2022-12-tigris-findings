# Gas Optimizations Report for Tigris Trade contest
## Overview
During the audit, 2 gas issues were found.  

№ | Title | Instance Count
--- | --- | --- 
G-1 | Use unchecked blocks for incrementing i | 16 
G-2 | Using ```storage``` pointer to ```struct```/```array```/```mapping``` is cheaper than using ```memory``` | 5 

## Gas Optimizations Findings(2)
### G-1. Use unchecked blocks for incrementing i
##### Description
In Solidity 0.8+, there’s a default overflow and underflow check on unsigned integers. In the loops, "i" will not overflow because the loop will run out of gas before that.
##### Instances
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L296
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L304
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol#L70
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol#L73
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L53
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L67 
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L78
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L95 
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L105
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L131
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L200
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L246
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L252
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L258
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L325
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L113

##### Recommendation
Change:
```
for (uint256 i; i < n; ++i) {
 // ...
}
```
to:
```
for (uint256 i; i < n;) { 
 // ...
 unchecked { ++i; }
}
```

##### Saved
This saves ~30-40 gas per iteration.  
So, ~35*16 = 560
#
### G-2. Using ```storage``` pointer to ```struct```/```array```/```mapping``` is cheaper than using ```memory``` 
##### Description
When you copy a storage ```struct```/```array```/```mapping``` to a memory variable, you are copying each member by reading it from the storage, which is expensive, but when you use the ```storage``` keyword, you are just storing a pointer to the storage, which is much cheaper.

##### Instances
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L700
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L774
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L859
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L886 
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L251

##### Recommendation
For example, change: 
```
Fees memory _fees = openFees;
```  
to:  
```
Fees storage _fees = openFees;
```