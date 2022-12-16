### Description

I'd like to update the automated extraction of `GAS-8` description and its way to have best practice of gas saving in loops.


### [GAS-8] `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too)

`unchecked` is useful with `++i(--i)` and it will save around 22 wei more gas.

```solidity
# Actual Code
for (uint i=0; i<_ids.length; i++) {}
```

```solidity
# Optimized Code
uint length = _ids.length;
for (uint i=0; i<length;) {
  ...
  unchecked{ ++i; }
}
```

---
*Instances (21)*:

```solidity
File: contracts/BondNFT.sol

220:             for (uint i=epoch[_tigAsset]; i<aEpoch; i++) {

284:             for (uint i=0; i<_ids.length; i++) {

292:             for (uint i=0; i<_ids.length; i++) {

300:             for (uint i=0; i<_ids.length; i++) {

342:             for (uint i=0; i<_ids.length; i++) {

```

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
File: contracts/Lock.sol

113:         for (uint i=0; i < assets.length; i++) {

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