## [G001] >= costs less gas than >

he compiler uses opcodes `GT` and `ISZERO` for solidity code that uses `>`, but only requires `LT` for `>=`, which saves 3 gas

#### Instances:

File: BondNFT.sol
```solidity
179 if (totalShares[bond.asset] > 0)
219 if (aEpoch > epoch[_tigAsset])
```

File: PairsContract.sol
```solidity
77 if (_maxLeverage > 0) 
80 if (_minLeverage > 0) 
```

File: Position.sol
```solidity
44 if (_trade.orderType > 0) return _trade;
47 if (_trade.direction && longOi[_trade.asset][_trade.tigAsset] > 0) {
49 if (longOi[_trade.asset][_trade.tigAsset] > shortOi[_trade.asset][_trade.tigAsset]) {
54 } else if (shortOi[_trade.asset][_trade.tigAsset] > 0) {
56 if (shortOi[_trade.asset][_trade.tigAsset] > longOi[_trade.asset][_trade.tigAsset]) {
101 if (longOi[_asset][_tigAsset] > 0) {
106 } else if(longOi[_asset][_tigAsset] > shortOi[_asset][_tigAsset]) {
108 if (shortOi[_asset][_tigAsset] > 0) {
114 if (_longOi > _shortOi) {
149 if (_mintTrade.orderType > 0) {
263 if (_trades[_id].orderType > 0) {
```

File: Trading.sol
```solidity
241 if (_percent > DIVISION_CONSTANT || _percent == 0) revert BadClosePercent();
432 if (_newLeverage > asset.maxLeverage) revert("!lev");
496 if (_price > trade.price+trade.price*limitOrderPriceRange/DIVISION_CONSTANT || _price < trade.price-trade.price*limitOrderPriceRange/DIVISION_CONSTANT) revert("6");
500 if (trade.price > _price) revert("6"); //LimitNotMet
505 if (trade.price > _price) revert("6"); //LimitNotMet
545 if (_payout > int256(_trade.margin*(DIVISION_CONSTANT-liqPercent)/DIVISION_CONSTANT)) revert NotLiquidatable();
622 if (_payout > 0) {
625 if (maxWinPercent > 0 && _toMint > _trade.margin*maxWinPercent/DIVISION_CONSTANT) {
836 if (_sl > _price) revert("3"); //BadStopLoss
```

File: TradingExtension.sol
```solidity
106 if (_trade.tpPrice > _price) revert("6"); //LimitNotMet
116 if (_trade.slPrice > _price) revert("6"); //LimitNotMet
123 if (tx.gasprice > maxGasPrice) revert("1"); //GasTooHigh
217 if (_leverage < asset.minLeverage || _leverage > asset.maxLeverage) revert("!lev");
```

File: TradingLibrary.sol
```solidity
39  if (_direction && _currentPrice >= _price) {
```

## [G002] `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables

#### Instances:

File: BondNFT.sol
```solidity
68  totalShares[_asset] += shares;
115  totalShares[bond.asset] += shares-bond.shares;
117  _bond.amount += _amount;
119  _bond.period += _period;
150  totalShares[bond.asset] -= bond.shares;
152  amount += _claimAmount;
180  accRewardsPerShare[bond.asset][epoch[bond.asset]] += _pendingDelta*1e18/totalShares[bond.asset];
183  bondPaid[_id][bond.asset] += amount;
221  epoch[_tigAsset] += 1;
225  accRewardsPerShare[_tigAsset][aEpoch] += _amount * 1e18 / totalShares[_tigAsset];
333  userDebt[from][bond.asset] += bond.pending;
334  bondPaid[_id][bond.asset] += bond.pending;
```

File: GovNFT.sol
```solidity
52  counter += 1;
54  userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];
68  userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];
79  userDebt[owner][assets[i]] += accRewardsPerNFT[assets[i]];
80  userDebt[owner][assets[i]] -= userPaid[owner][assets[i]]/balanceOf(owner);
81  userPaid[owner][assets[i]] -= userPaid[owner][assets[i]]/balanceOf(owner);
96  userDebt[from][assets[i]] += accRewardsPerNFT[assets[i]];
97  userDebt[from][assets[i]] -= userPaid[from][assets[i]]/balanceOf(from);
98  userPaid[from][assets[i]] -= userPaid[from][assets[i]]/balanceOf(from);
99  userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];
278  userPaid[_msgsender][_tigAsset] += amount;
290  accRewardsPerNFT[_tigAsset] += _amount/totalSupply();
```

File: Lock.sol
```solidity
73  totalLocked[_asset] += _amount;
103  totalLocked[asset] -= lockAmount;
```

PairsContract.sol
```solidity
156  _idToOi[_asset][_tigAsset].longOi += _amount;
160  _idToOi[_asset][_tigAsset].longOi -= _amount;
176  _idToOi[_asset][_tigAsset].shortOi += _amount;
180  _idToOi[_asset][_tigAsset].shortOi -= _amount;
```

File: Position.sol
```solidity
62  _trade.accInterest += (int256(_trade.margin*_trade.leverage/1e18)*(accInterestPerOi[_trade.asset][_trade.tigAsset][_trade.direction]+_pendingFunding)/1e18)-initId[_id];
102  accInterestPerOi[_asset][_tigAsset][true] += ((int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(longOi[_asset][_tigAsset]))*int256(1e10-vaultFundingPercent[_asset][_tigAsset])/1e10;
104  accInterestPerOi[_asset][_tigAsset][false] -= (int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(shortOi[_asset][_tigAsset]);
107  accInterestPerOi[_asset][_tigAsset][true] -= (int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(longOi[_asset][_tigAsset]);
109  accInterestPerOi[_asset][_tigAsset][false] += ((int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(shortOi[_asset][_tigAsset]))*int256(1e10-vaultFundingPercent[_asset][_tigAsset])/1e10;
231  _trades[_id].accInterest -= _trades[_id].accInterest*int256(_percent)/int256(DIVISION_CONSTANT);
232  _trades[_id].margin -= _trades[_id].margin*_percent/DIVISION_CONSTANT;
```

File: Trading.sol
```solidity
293  _addMargin -= _fee;
511  trade.price -= trade.price * _spread / DIVISION_CONSTANT;
```

File: TradingExtension.sol
```solidity
185  _price += _price * _spread / DIVISION_CONSTANT;
187  _price -= _price * _spread / DIVISION_CONSTANT;
```

## [G003] `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops

The `unchecked` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas PER LOOP

#### Instances:

File: BondNFT.sol
```solidity
220  for (uint i=epoch[_tigAsset]; i<aEpoch; i++) {
284  for (uint i=0; i<_ids.length; i++) {
292  for (uint i=0; i<_ids.length; i++) {
300  for (uint i=0; i<_ids.length; i++) {
342  for (uint i=0; i<_ids.length; i++) {
```

File: GovNFT.sol
```solidity
53  for (uint i=0; i<assetsLength(); i++) {
67  for (uint i=0; i<assetsLength(); i++) {
78  for (uint i=0; i<assetsLength(); i++) {
95  for (uint i=0; i<assetsLength(); i++) {
105 for (uint i=0; i<_amount; i++) {
131 for (uint i=0; i<tokenId.length; i++) {
200 for (uint i=0; i<tokenId.length; i++) {
246 for (uint i=0; i<_ids.length; i++) {
252 for (uint i=0; i<_ids.length; i++) {
258 for (uint i=0; i<_ids.length; i++) {
325 for (uint i=0; i<_ids.length; i++) {
```

File: Lock.sol
```solidity
113  for (uint i=0; i < assets.length; i++) {
```

File: Position.sol
```solidity
296  for (uint i=0; i<_ids.length; i++) {
304  for (uint i=0; i<_ids.length; i++) {
```

File: Referrals.sol
```solidity
70  for (uint i=0; i<_codeOwnersL; i++) {
73  for (uint i=0; i<_referredAL; i++) {
```

## [G004] Splitting `require()` statements that use `&&` saves gas

See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper

#### Instances:

File: PairsContract.sol
```solidity
52  require(_maxLeverage >= _minLeverage && _minLeverage > 0, "Wrong leverage values");
```

File: Trading.sol
```solidity
887  require(_proxy.proxy == _msgSender() && _proxy.time >= block.timestamp, "Proxy not approved");
```

## [G005] Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.\[layout_in_storage.html]{https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html}\Use a larger size then downcast where needed

#### Instances:

File: GovNFT.sol
```solidity
22  mapping(uint16  mapping(address  bool)) public isTrustedAddress;
23  mapping(uint16  mapping(bytes  mapping(uint64  bytes32))) public failedMessages;
143  uint16 version = 1;
```
## [G006] abi.encode() is less efficient than abi.encodePacked()


#### Instances:

File: GovNFT.sol
```solidity
141  bytes memory payload = abi.encode(_to, tokenId);
```

File: TradingLibrary.sol
```solidity
103  keccak256(abi.encode(_priceData))
```

## [G007] Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (`if(x){}else if(y){...}else{...}`  `if(!x){if(y){...}else{...}}`)

#### Instances:

File: StableToken.sol
```solidity
11  constructor(string memory name_, string memory symbol_) ERC20Permit(name_) ERC20(name_, symbol_) {}
```