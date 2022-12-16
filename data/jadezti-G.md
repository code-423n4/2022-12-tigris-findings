## Gas Optimizations

|         | Issue                                                        | Instances |
| ------- |:------------------------------------------------------------ |:---------:|
| [GAS-1] | Using `unchecked` math operations when no overflow/underflow |    21     | 


### [GAS-1] Using `unchecked` math operations when no overflow/underflow

*Instances (21)*

#### Using `unchecked` math operations in for-loops: *15 instances*

C4 report has optimized for-loops. There is a further optimization `unchecked index increment` can be applied since the looping times is limited which means its safe to use unchecked operation.
The optimized for-loop:
```solidity
for(uint i; i<length;){
  // ...operations...
  unchecked {++i;}
}
```

Instances in scope:

```solidity
File: contracts/GovNFT.sol

53:         for (uint i=0; i<assetsLength(); i++) {

67:         for (uint i=0; i<assetsLength(); i++) {

78:         for (uint i=0; i<assetsLength(); i++) {

95:         for (uint i=0; i<assetsLength(); i++) {

105:         for (uint i=0; i<_amount; i++) {

131:         for (uint i=0; i<tokenId.length; i++) {

200:         for (uint i=0; i<tokenId.length; i++) {

246:         for (uint i=0; i<_ids.length; i++) {

252:         for (uint i=0; i<_ids.length; i++) {

258:         for (uint i=0; i<_ids.length; i++) {

325:         for (uint i=0; i<_ids.length; i++) {
```

```solidity
File: contracts/Position.sol

296:         for (uint i=0; i<_ids.length; i++) {

304:         for (uint i=0; i<_ids.length; i++) {
```

```solidity
File: contracts/Referrals.sol

70:         for (uint i=0; i<_codeOwnersL; i++) {

73:         for (uint i=0; i<_referredAL; i++) {
```

#### Using `unchecked` math operations when no overflow/underflow: *6 Instances*

```solidity
File: contracts/Position.sol

//@audit - refer to L114
115:   _oiDelta = int256(_longOi)-int256(_shortOi);

//@audit - refer to L114
117:    _oiDelta = int256(_shortOi)-int256(_longOi);

//@audit - refer to L150
151:    _limitOrderIndexes[_mintTrade.asset][newTokenID] = _limitOrders[_mintTrade.asset].length-1;

//@audit - refer to L154
155:    _openPositionsIndexes[newTokenID] = _openPositions.length-1;

//@audit - refer to L157
158:    _assetOpenPositionsIndexes[_mintTrade.asset][newTokenID] = _assetOpenPositions[_mintTrade.asset].length-1;

//@audit - refer to L182
183:    _openPositionsIndexes[_id] = _openPositions.length-1;
```
