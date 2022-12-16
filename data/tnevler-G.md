   # Report
## Gas Optimizations ##
### [G-1]: The increment in for loop post condition can be made unchecked
**Context:**

1. ```for (uint i=0; i<_ids.length; i++) {``` [L296](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L296) 
1. ```for (uint i=0; i<_ids.length; i++) {``` [L304](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L304) 
1. ```for (uint i=0; i<_codeOwnersL; i++) {``` [70](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol#L70) 
1. ```for (uint i=0; i<_referredAL; i++) {``` [L73](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol#L73) 
1. ```for (uint i=0; i<assetsLength(); i++) {``` [L53](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L53) 
1. ```for (uint i=0; i<assetsLength(); i++) {``` [L67](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L67) 
1. ```for (uint i=0; i<assetsLength(); i++) {``` [L78](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L78) 
1. ```for (uint i=0; i<assetsLength(); i++) {``` [95](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L95) 
1. ```for (uint i=0; i<_amount; i++) {``` [L105](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L105) 
1. ```for (uint i=0; i<tokenId.length; i++) {``` [L131](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L131) 
1. ```for (uint i=0; i<tokenId.length; i++) {``` [L200](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L200) 
1. ```for (uint i=0; i<_ids.length; i++) {``` [L246](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L246) 
1. ```for (uint i=0; i<_ids.length; i++) {``` [252](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L252) 
1. ```for (uint i=0; i<_ids.length; i++) {``` [258](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L258) 
1. ```for (uint i=0; i<_ids.length; i++) {``` [325](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L325) 
1. ```for (uint i=0; i < assets.length; i++) {``` [113](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L113)

**Description:**  
[This](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked) can save 30-40 gas per loop iteration.

**Recommendation:**  
Example how to fix. Change:
```
for (uint256 i = 0; i < orders.length; ++i) {
   // Do the thing
}
```

To:
```
for (uint256 i = 0; i < orders.length;) {
   // Do the thing
   unchecked { ++i; }
}
```

### [G-1]: Storage pointer to a structure is cheaper than copyug each value of the structure in memory, same for array and mapping
**Context:**

1. ```Fees memory _fees = openFees;``` [L700](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L700) 
1. ```Fees memory _fees = closeFees;``` [L774](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L774) 
1. ```Delay memory _delay = blockDelayPassed[_id];``` [L859](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L859) 
1. ```Proxy memory _proxy = proxyApprovals[_trader];``` [L886](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L886) 
1. ```Bond memory bond = _idToBond[_id];``` [L257](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L251)

**Description:**  
Every time you copy a storage struct/array/mapping to a memory variable, you are literally copying each member by reading it from storage, which is expensive. And when you use the storage keyword, you are just storing a pointer to the storage, which is much cheaper.

