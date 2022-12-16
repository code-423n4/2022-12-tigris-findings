# QA Report
## Finding Summary
||Issue|Instances|
|-|-|-|
|[NC-01]|Lack Of Whitespace In Operation|113|
|[NC-02]|Long Lines (> 120 Characters)|78|
|[NC-03]|NatSpec Comments Use `//` Instead of `///`|67|
|[NC-04]|`uint256` And Alias `uint` Used|14|
|[NC-05]|Order of Functions Not Compliant With Solidity Docs|9|
|[NC-06]|Underscore Notation Not Used / Not Used Consistently|5|
|[NC-07]|Power of Ten Literal > `10e3` Not In Scientific Notation|3|
|[NC-08]|Style Guide Voiding If Structure Braces / Inconsistent If Style|1|
|[NC-09]|`EVENTS` Comment Violates Style|1|

### [NC-01] Lack Of Whitespace In Operation

The [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#other-recommendations) recommends each operation (+-/...) have a single whitespace before and after. Consider adding a whitespace before and after expressions.

#### Findings:

*/contracts/Trading.sol*
Links: [178](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L178), [208](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L208), [274](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L274), [295](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L295), [436](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L436), [493](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L493), [496](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L496), [514](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L514), [516](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L516), [544](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L544), [545](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L545), [616](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L616), [624](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L624), [625](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L625), [626](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L626), [650](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L650), [651](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L651), [653](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L653), [675](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L675), [717](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L717), [779](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L779), [780](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L780), [786](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L786), [792](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L792), [796](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L796), [954](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L954).
```solidity
178:	uint256 _marginAfterFees = _tradeInfo.margin - _handleOpenFees(_tradeInfo.asset, _tradeInfo.margin*_tradeInfo.leverage/1e18, _trader, _tigAsset, false);
208:	emit PositionOpened(_tradeInfo, 0, _price, position.getCount()-1, _trader, _marginAfterFees);
274:	uint _fee = _handleOpenFees(_trade.asset, _addMargin*_trade.leverage/1e18, _trader, _trade.tigAsset, false);
295:	uint _newPrice = _trade.price*_trade.margin/_newMargin + _price*_addMargin/_newMargin;
436:	if (_payout <= int256(_newMargin*(DIVISION_CONSTANT-liqPercent)/DIVISION_CONSTANT)) revert LiqThreshold();
493:	uint _fee = _handleOpenFees(trade.asset, trade.margin*trade.leverage/1e18, trade.trader, trade.tigAsset, true);
496:	if (_price > trade.price+trade.price*limitOrderPriceRange/DIVISION_CONSTANT || _price < trade.price-trade.price*limitOrderPriceRange/DIVISION_CONSTANT) revert("6");
514:	tradingExtension.modifyLongOi(trade.asset, trade.tigAsset, true, trade.margin*trade.leverage/1e18);
516:	tradingExtension.modifyShortOi(trade.asset, trade.tigAsset, true, trade.margin*trade.leverage/1e18);
544:	uint256 _positionSize = _trade.margin*_trade.leverage/1e18;
545:	if (_payout > int256(_trade.margin*(DIVISION_CONSTANT-liqPercent)/DIVISION_CONSTANT)) revert NotLiquidatable();
616:	if ((_trade.margin*_trade.leverage*(DIVISION_CONSTANT-_percent)/DIVISION_CONSTANT)/1e18 < tradingExtension.minPos(_trade.tigAsset)) revert("!size");
624:	_toMint = _handleCloseFees(_trade.asset, uint256(_payout)*_percent/DIVISION_CONSTANT, _trade.tigAsset, _positionSize*_percent/DIVISION_CONSTANT, _trade.trader, _isBot);
625:	if (maxWinPercent > 0 && _toMint > _trade.margin*maxWinPercent/DIVISION_CONSTANT) {
626:	_toMint = _trade.margin*maxWinPercent/DIVISION_CONSTANT;
650:	uint _marginDecMultiplier = 10**(18-ExtendedIERC20(_marginAsset).decimals());
651:	IERC20(_marginAsset).transferFrom(_trader, address(this), _margin/_marginDecMultiplier);
653:	IStableVault(_stableVault).deposit(_marginAsset, _margin/_marginDecMultiplier);
675:	if (IERC20(_outputToken).balanceOf(address(this)) != _balBefore + _toMint/(10**(18-ExtendedIERC20(_outputToken).decimals()))) revert BadWithdraw();
717:	_fees.daoFees = _fees.daoFees - _fees.referralFees*2;
779:	_daoFeesPaid = (_positionSize*_fees.daoFees/DIVISION_CONSTANT)*asset.feeMultiplier/DIVISION_CONSTANT;
780:	_burnFeesPaid = (_positionSize*_fees.burnFees/DIVISION_CONSTANT)*asset.feeMultiplier/DIVISION_CONSTANT;
786:	_referralFeesPaid = (_positionSize*_fees.referralFees/DIVISION_CONSTANT)*asset.feeMultiplier/DIVISION_CONSTANT;
792:	_daoFeesPaid = _daoFeesPaid-_referralFeesPaid*2;
796:	_botFeesPaid = (_positionSize*_fees.botFees/DIVISION_CONSTANT)*asset.feeMultiplier/DIVISION_CONSTANT;
954:	require(_daoFees >= _botFees+_referralFees*2);
```

*/contracts/TradingExtension.sol*
Links: [71](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L71), [73](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L73), [218](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L218).
```solidity
71:	modifyLongOi(_trade.asset, _trade.tigAsset, false, (_trade.margin*_trade.leverage/1e18)*_percent/DIVISION_CONSTANT);
73:	modifyShortOi(_trade.asset, _trade.tigAsset, false, (_trade.margin*_trade.leverage/1e18)*_percent/DIVISION_CONSTANT);
218:	if (_margin*_leverage/1e18 < minPositionSize[_tigAsset]) revert("!size");
```

*/contracts/utils/TradingLibrary.sol*
Links: [40](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L40), [42](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L42), [44](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L44), [46](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L46), [64](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L64), [66](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L66), [117](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L117), [118](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L118).
```solidity
40:	_payout = int256(_margin) + int256(_initPositionSize * (1e18 * _currentPrice / _price - 1e18)/1e18) + accInterest;
42:	_payout = int256(_margin) - int256(_initPositionSize * (1e18 - 1e18 * _currentPrice / _price)/1e18) + accInterest;
44:	_payout = int256(_margin) + int256(_initPositionSize * (1e18 - 1e18 * _currentPrice / _price)/1e18) + accInterest;
46:	_payout = int256(_margin) - int256(_initPositionSize * (1e18 * _currentPrice / _price - 1e18)/1e18) + accInterest;
64:	_liqPrice = _tradePrice - ((_tradePrice*1e18/_leverage) * uint(int(_margin)+_accInterest) / _margin) * _liqPercent / 1e10;
66:	_liqPrice = _tradePrice + ((_tradePrice*1e18/_leverage) * uint(int(_margin)+_accInterest) / _margin) * _liqPercent / 1e10;
117:	_priceData.price < assetChainlinkPrice+assetChainlinkPrice*2/100 &&
118:	_priceData.price > assetChainlinkPrice-assetChainlinkPrice*2/100, "!chainlinkPrice"
```

*/contracts/Position.sol*
Links: [48](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L48), [52](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L52), [55](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L55), [59](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L59), [62](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L62), [102](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L102), [104](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L104), [107](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L107), [109](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L109), [115](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L115), [117](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L117), [120](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L120), [151](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L151), [153](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L153), [155](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L155), [158](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L158), [177](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L177), [178](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L178), [183](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L183), [185](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L185), [187](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L187), [212](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L212), [231](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L231), [232](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L232), [233](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L233), [264](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L264), [265](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L265), [269](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L269), [270](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L270), [274](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L274), [275](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L275), [296](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L296), [303](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L303), [304](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L304), [305](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L305).
```solidity
48:	_pendingFunding = (int256(block.timestamp-lastUpdate[_trade.asset][_trade.tigAsset])*fundingDeltaPerSec[_trade.asset][_trade.tigAsset])*1e18/int256(longOi[_trade.asset][_trade.tigAsset]);
52:	_pendingFunding = _pendingFunding*int256(1e10-vaultFundingPercent[_trade.asset][_trade.tigAsset])/1e10;
55:	_pendingFunding = (int256(block.timestamp-lastUpdate[_trade.asset][_trade.tigAsset])*fundingDeltaPerSec[_trade.asset][_trade.tigAsset])*1e18/int256(shortOi[_trade.asset][_trade.tigAsset]);
59:	_pendingFunding = _pendingFunding*int256(1e10-vaultFundingPercent[_trade.asset][_trade.tigAsset])/1e10;
62:	_trade.accInterest += (int256(_trade.margin*_trade.leverage/1e18)*(accInterestPerOi[_trade.asset][_trade.tigAsset][_trade.direction]+_pendingFunding)/1e18)-initId[_id];
102:	accInterestPerOi[_asset][_tigAsset][true] += ((int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(longOi[_asset][_tigAsset]))*int256(1e10-vaultFundingPercent[_asset][_tigAsset])/1e10;
104:	accInterestPerOi[_asset][_tigAsset][false] -= (int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(shortOi[_asset][_tigAsset]);
107:	accInterestPerOi[_asset][_tigAsset][true] -= (int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(longOi[_asset][_tigAsset]);
109:	accInterestPerOi[_asset][_tigAsset][false] += ((int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(shortOi[_asset][_tigAsset]))*int256(1e10-vaultFundingPercent[_asset][_tigAsset])/1e10;
115:	_oiDelta = int256(_longOi)-int256(_shortOi);
117:	_oiDelta = int256(_shortOi)-int256(_longOi);
120:	fundingDeltaPerSec[_asset][_tigAsset] = (_oiDelta*int256(_baseFundingRate)/int256(DIVISION_CONSTANT))/31536000;
151:	_limitOrderIndexes[_mintTrade.asset][newTokenID] = _limitOrders[_mintTrade.asset].length-1;
153:	initId[newTokenID] = accInterestPerOi[_mintTrade.asset][_mintTrade.tigAsset][_mintTrade.direction]*int256(_mintTrade.margin*_mintTrade.leverage/1e18)/1e18;
155:	_openPositionsIndexes[newTokenID] = _openPositions.length-1;
158:	_assetOpenPositionsIndexes[_mintTrade.asset][newTokenID] = _assetOpenPositions[_mintTrade.asset].length-1;
177:	_limitOrderIndexes[_asset][_limitOrders[_asset][_limitOrders[_asset].length-1]] = _limitOrderIndexes[_asset][_id];
178:	_limitOrders[_asset][_limitOrderIndexes[_asset][_id]] = _limitOrders[_asset][_limitOrders[_asset].length-1];
183:	_openPositionsIndexes[_id] = _openPositions.length-1;
185:	_assetOpenPositionsIndexes[_asset][_id] = _assetOpenPositions[_asset].length-1;
187:	initId[_id] = accInterestPerOi[_trade.asset][_trade.tigAsset][_trade.direction]*int256(_trade.margin*_trade.leverage/1e18)/1e18;
212:	initId[_id] = accInterestPerOi[_trades[_id].asset][_trades[_id].tigAsset][_trades[_id].direction]*int256(_newMargin*_trades[_id].leverage/1e18)/1e18;
231:	_trades[_id].accInterest -= _trades[_id].accInterest*int256(_percent)/int256(DIVISION_CONSTANT);
232:	_trades[_id].margin -= _trades[_id].margin*_percent/DIVISION_CONSTANT;
233:	initId[_id] = accInterestPerOi[_trades[_id].asset][_trades[_id].tigAsset][_trades[_id].direction]*int256(_trades[_id].margin*_trades[_id].leverage/1e18)/1e18;
264:	_limitOrderIndexes[_asset][_limitOrders[_asset][_limitOrders[_asset].length-1]] = _limitOrderIndexes[_asset][_id];
265:	_limitOrders[_asset][_limitOrderIndexes[_asset][_id]] = _limitOrders[_asset][_limitOrders[_asset].length-1];
269:	_assetOpenPositionsIndexes[_asset][_assetOpenPositions[_asset][_assetOpenPositions[_asset].length-1]] = _assetOpenPositionsIndexes[_asset][_id];
270:	_assetOpenPositions[_asset][_assetOpenPositionsIndexes[_asset][_id]] = _assetOpenPositions[_asset][_assetOpenPositions[_asset].length-1];
274:	_openPositionsIndexes[_openPositions[_openPositions.length-1]] = _openPositionsIndexes[_id];
275:	_openPositions[_openPositionsIndexes[_id]] = _openPositions[_openPositions.length-1];
296:	for (uint i=0; i<_ids.length; i++) {
303:	uint[] memory _ids = new uint[](_to-_from);
304:	for (uint i=0; i<_ids.length; i++) {
305:	_ids[i] = _openPositions[i+_from];
```

*/contracts/Referrals.sol*
Links: [70](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol#L70), [73](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol#L73).
```solidity
70:	for (uint i=0; i<_codeOwnersL; i++) {
73:	for (uint i=0; i<_referredAL; i++) {
```

*/contracts/GovNFT.sol*
Links: [53](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L53), [67](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L67), [78](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L78), [80](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L80), [81](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L81), [95](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L95), [97](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L97), [98](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L98), [105](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L105), [131](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L131), [144](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L144), [175](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L175), [200](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L200), [246](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L246), [252](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L252), [258](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L258), [290](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L290), [297](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L297), [325](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L325).
```solidity
53:	for (uint i=0; i<assetsLength(); i++) {
67:	for (uint i=0; i<assetsLength(); i++) {
78:	for (uint i=0; i<assetsLength(); i++) {
80:	userDebt[owner][assets[i]] -= userPaid[owner][assets[i]]/balanceOf(owner);
81:	userPaid[owner][assets[i]] -= userPaid[owner][assets[i]]/balanceOf(owner);
95:	for (uint i=0; i<assetsLength(); i++) {
97:	userDebt[from][assets[i]] -= userPaid[from][assets[i]]/balanceOf(from);
98:	userPaid[from][assets[i]] -= userPaid[from][assets[i]]/balanceOf(from);
105:	for (uint i=0; i<_amount; i++) {
131:	for (uint i=0; i<tokenId.length; i++) {
144:	uint256 _gas = 500_000 + gas*tokenId.length;
175:	(bool success, bytes memory reason) = address(this).excessivelySafeCall(gasleft()*4/5, 150, abi.encodeWithSelector(this.nonblockingLzReceive.selector, _srcChainId, _srcAddress, _nonce, _payload));
200:	for (uint i=0; i<tokenId.length; i++) {
246:	for (uint i=0; i<_ids.length; i++) {
252:	for (uint i=0; i<_ids.length; i++) {
258:	for (uint i=0; i<_ids.length; i++) {
290:	accRewardsPerNFT[_tigAsset] += _amount/totalSupply();
297:	return userDebt[user][_tigAsset] + balanceOf(user)*accRewardsPerNFT[_tigAsset] - userPaid[user][_tigAsset];
325:	for (uint i=0; i<_ids.length; i++) {
```

*/contracts/StableVault.sol*
Links: [49](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L49), [67](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L67), [91](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L91), [92](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L92).
```solidity
49:	_amount*(10**(18-IERC20Mintable(_token).decimals()))
67:	_output = _amount/10**(18-IERC20Mintable(_token).decimals());
91:	tokenIndex[tokens[tokens.length-1]] = tokenIndex[_token];
92:	tokens[tokenIndex[_token]] = tokens[tokens.length-1];
```

*/contracts/Lock.sol*
Links: [113](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L113).
```solidity
113:	for (uint i=0; i < assets.length; i++) {
```

*/contracts/BondNFT.sol*
Links: [110](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L110), [111](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L111), [114](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L114), [115](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L115), [178](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L178), [180](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L180), [220](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L220), [222](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L222), [240](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L240), [284](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L284), [292](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L292), [300](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L300), [329](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L329), [342](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L342), [354](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L354).
```solidity
110:	require(epoch[bond.asset] == block.timestamp/DAY, "Bad epoch");
111:	require(bond.period+_period <= 365, "MAX PERIOD");
114:	uint expireEpoch = block.timestamp/DAY + bond.period + _period;
115:	totalShares[bond.asset] += shares-bond.shares;
178:	uint _pendingDelta = (bond.shares * accRewardsPerShare[bond.asset][epoch[bond.asset]] / 1e18 - bondPaid[_id][bond.asset]) - (bond.shares * accRewardsPerShare[bond.asset][bond.expireEpoch-1] / 1e18 - bondPaid[_id][bond.asset]);
180:	accRewardsPerShare[bond.asset][epoch[bond.asset]] += _pendingDelta*1e18/totalShares[bond.asset];
220:	for (uint i=epoch[_tigAsset]; i<aEpoch; i++) {
222:	accRewardsPerShare[_tigAsset][i+1] = accRewardsPerShare[_tigAsset][i];
240:	uint _accRewardsPerShare = accRewardsPerShare[bond.asset][bond.expired ? bond.expireEpoch-1 : epoch[bond.asset]];
284:	for (uint i=0; i<_ids.length; i++) {
292:	for (uint i=0; i<_ids.length; i++) {
300:	for (uint i=0; i<_ids.length; i++) {
329:	require(epoch[bond.asset] == block.timestamp/DAY, "Bad epoch");
342:	for (uint i=0; i<_ids.length; i++) {
354:	epoch[_asset] = block.timestamp/DAY;
```

### [NC-02] Long Lines (> 120 Characters)

Lines with greater length than 120 characters are used. The [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-lengthhttps://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-length) suggests that all lines should be 120 characters or less in width.

#### Findings:

*/contracts/Trading.sol*
Links: [178](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L178), [180](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L180), [285](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L285), [330](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L330), [434](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L434), [493](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L493), [494](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L494), [496](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L496), [520](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L520), [543](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L543), [545](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L545), [552](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L552), [579](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L579), [612](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L612), [616](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L616), [624](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L624), [643](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L643), [647](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L647), [668](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L668), [675](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L675), [786](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L786), [853](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L853), [877](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L877), [952](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L952).
```solidity
178:	        uint256 _marginAfterFees = _tradeInfo.margin - _handleOpenFees(_tradeInfo.asset, _tradeInfo.margin*_tradeInfo.leverage/1e18, _trader, _tigAsset, false);
180:	        _handleDeposit(_tigAsset, _tradeInfo.marginAsset, _tradeInfo.margin, _tradeInfo.stableVault, _permitData, _trader);
285:	            (uint256 _price,) = tradingExtension.getVerifiedPrice(_trade.asset, _priceData, _signature, _trade.direction ? 1 : 2);
330:	        _handleDeposit(_tigAsset, _tradeInfo.marginAsset, _tradeInfo.margin, _tradeInfo.stableVault, _permitData, _trader);
434:	        (,int256 _payout) = TradingLibrary.pnl(_trade.direction, _assetPrice, _trade.price, _newMargin, _newLeverage, _trade.accInterest);
493:	            uint _fee = _handleOpenFees(trade.asset, trade.margin*trade.leverage/1e18, trade.trader, trade.tigAsset, true);
494:	            (uint256 _price, uint256 _spread) = tradingExtension.getVerifiedPrice(trade.asset, _priceData, _signature, 0);
496:	            if (_price > trade.price+trade.price*limitOrderPriceRange/DIVISION_CONSTANT || _price < trade.price-trade.price*limitOrderPriceRange/DIVISION_CONSTANT) revert("6"); //LimitNotMet
520:	            emit LimitOrderExecuted(trade.asset, trade.direction, trade.price, trade.leverage, trade.margin - _fee, _id, trade.trader, _msgSender());
543:	            (uint256 _positionSizeAfterPrice, int256 _payout) = TradingLibrary.pnl(_trade.direction, _price, _trade.price, _trade.margin, _trade.leverage, _trade.accInterest);
545:	            if (_payout > int256(_trade.margin*(DIVISION_CONSTANT-liqPercent)/DIVISION_CONSTANT)) revert NotLiquidatable();
552:	            _handleCloseFees(_trade.asset, type(uint).max, _trade.tigAsset, _positionSizeAfterPrice, _trade.trader, true);
579:	     * @notice Trader can approve a proxy wallet address for it to trade on its behalf. Can also provide proxy wallet with gas.
612:	        (IPosition.Trade memory _trade, uint256 _positionSize, int256 _payout) = tradingExtension._closePosition(_id, _price, _percent);
616:	            if ((_trade.margin*_trade.leverage*(DIVISION_CONSTANT-_percent)/DIVISION_CONSTANT)/1e18 < tradingExtension.minPos(_trade.tigAsset)) revert("!size");
624:	                _toMint = _handleCloseFees(_trade.asset, uint256(_payout)*_percent/DIVISION_CONSTANT, _trade.tigAsset, _positionSize*_percent/DIVISION_CONSTANT, _trade.trader, _isBot);
643:	    function _handleDeposit(address _tigAsset, address _marginAsset, uint256 _margin, address _stableVault, ERC20PermitData calldata _permitData, address _trader) internal {
647:	                ERC20Permit(_marginAsset).permit(_trader, address(this), _permitData.amount, _permitData.deadline, _permitData.v, _permitData.r, _permitData.s);
668:	    function _handleWithdraw(IPosition.Trade memory _trade, address _stableVault, address _outputToken, uint _toMint) internal {
675:	            if (IERC20(_outputToken).balanceOf(address(this)) != _balBefore + _toMint/(10**(18-ExtendedIERC20(_outputToken).decimals()))) revert BadWithdraw();
786:	                _referralFeesPaid = (_positionSize*_fees.referralFees/DIVISION_CONSTANT)*asset.feeMultiplier/DIVISION_CONSTANT;
853:	     * @dev This is to prevent profitable opening and closing in the same tx with two different prices in the "valid signature pool".
877:	        require(_token == IStableVault(_stableVault).stable() || IStableVault(_stableVault).allowed(_token), "Token not approved in vault");
952:	    function setFees(bool _open, uint _daoFees, uint _burnFees, uint _referralFees, uint _botFees, uint _percent) external onlyOwner {
```

*/contracts/TradingExtension.sol*
Links: [67](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L67), [71](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L71), [73](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L73).
```solidity
67:	        (_positionSize, _payout) = TradingLibrary.pnl(_trade.direction, _price, _trade.price, _trade.margin, _trade.leverage, _trade.accInterest);
71:	                modifyLongOi(_trade.asset, _trade.tigAsset, false, (_trade.margin*_trade.leverage/1e18)*_percent/DIVISION_CONSTANT);
73:	                modifyShortOi(_trade.asset, _trade.tigAsset, false, (_trade.margin*_trade.leverage/1e18)*_percent/DIVISION_CONSTANT);
```

*/contracts/utils/TradingLibrary.sol*
Links: [36](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/utils/TradingLibrary.sol#L36), [40](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/utils/TradingLibrary.sol#L40), [42](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/utils/TradingLibrary.sol#L42), [44](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/utils/TradingLibrary.sol#L44), [46](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/utils/TradingLibrary.sol#L46), [62](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/utils/TradingLibrary.sol#L62), [64](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/utils/TradingLibrary.sol#L64), [66](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/utils/TradingLibrary.sol#L66), [78](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/utils/TradingLibrary.sol#L78), [115](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/utils/TradingLibrary.sol#L115).
```solidity
36:	    function pnl(bool _direction, uint _currentPrice, uint _price, uint _margin, uint _leverage, int256 accInterest) external pure returns (uint256 _positionSize, int256 _payout) {
40:	                _payout = int256(_margin) + int256(_initPositionSize * (1e18 * _currentPrice / _price - 1e18)/1e18) + accInterest;
42:	                _payout = int256(_margin) - int256(_initPositionSize * (1e18 - 1e18 * _currentPrice / _price)/1e18) + accInterest;
44:	                _payout = int256(_margin) + int256(_initPositionSize * (1e18 - 1e18 * _currentPrice / _price)/1e18) + accInterest;
46:	                _payout = int256(_margin) - int256(_initPositionSize * (1e18 * _currentPrice / _price - 1e18)/1e18) + accInterest;
62:	    function liqPrice(bool _direction, uint _tradePrice, uint _leverage, uint _margin, int _accInterest, uint _liqPercent) public pure returns (uint256 _liqPrice) {
64:	            _liqPrice = _tradePrice - ((_tradePrice*1e18/_leverage) * uint(int(_margin)+_accInterest) / _margin) * _liqPercent / 1e10;
66:	            _liqPrice = _tradePrice + ((_tradePrice*1e18/_leverage) * uint(int(_margin)+_accInterest) / _margin) * _liqPercent / 1e10;
78:	        return liqPrice(_trade.direction, _trade.price, _trade.leverage, _trade.margin, _trade.accInterest, _liqPercent);
115:	                uint256 assetChainlinkPrice = uint256(assetChainlinkPriceInt) * 10**(18 - IPrice(_chainlinkFeed).decimals());
```

*/contracts/Position.sol*
Links: [30](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L30), [48](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L48), [55](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L55), [62](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L62), [68](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L68), [69](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L69), [71](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L71), [99](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L99), [102](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L102), [104](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L104), [107](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L107), [109](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L109), [153](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L153), [177](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L177), [187](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L187), [212](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L212), [233](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L233), [264](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L264), [265](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L265), [269](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L269), [270](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L270).
```solidity
30:	    mapping(uint256 => mapping(uint256 => uint256)) private _limitOrderIndexes; // Keeps track of asset -> id -> array index
48:	            _pendingFunding = (int256(block.timestamp-lastUpdate[_trade.asset][_trade.tigAsset])*fundingDeltaPerSec[_trade.asset][_trade.tigAsset])*1e18/int256(longOi[_trade.asset][_trade.tigAsset]);
55:	            _pendingFunding = (int256(block.timestamp-lastUpdate[_trade.asset][_trade.tigAsset])*fundingDeltaPerSec[_trade.asset][_trade.tigAsset])*1e18/int256(shortOi[_trade.asset][_trade.tigAsset]);
62:	        _trade.accInterest += (int256(_trade.margin*_trade.leverage/1e18)*(accInterestPerOi[_trade.asset][_trade.tigAsset][_trade.direction]+_pendingFunding)/1e18)-initId[_id];
68:	    function assetOpenPositions(uint _asset) public view returns (uint256[] memory) { return _assetOpenPositions[_asset]; }
69:	    function assetOpenPositionsIndexes(uint _asset, uint _id) public view returns (uint256) { return _assetOpenPositionsIndexes[_asset][_id]; }
71:	    function limitOrderIndexes(uint _asset, uint _id) public view returns (uint256) { return _limitOrderIndexes[_asset][_id]; }
99:	    function updateFunding(uint256 _asset, address _tigAsset, uint256 _longOi, uint256 _shortOi, uint256 _baseFundingRate, uint _vaultFundingPercent) external onlyMinter {
102:	                accInterestPerOi[_asset][_tigAsset][true] += ((int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(longOi[_asset][_tigAsset]))*int256(1e10-vaultFundingPercent[_asset][_tigAsset])/1e10;
104:	            accInterestPerOi[_asset][_tigAsset][false] -= (int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(shortOi[_asset][_tigAsset]);
107:	            accInterestPerOi[_asset][_tigAsset][true] -= (int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(longOi[_asset][_tigAsset]);
109:	                accInterestPerOi[_asset][_tigAsset][false] += ((int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(shortOi[_asset][_tigAsset]))*int256(1e10-vaultFundingPercent[_asset][_tigAsset])/1e10;
153:	            initId[newTokenID] = accInterestPerOi[_mintTrade.asset][_mintTrade.tigAsset][_mintTrade.direction]*int256(_mintTrade.margin*_mintTrade.leverage/1e18)/1e18;
177:	        _limitOrderIndexes[_asset][_limitOrders[_asset][_limitOrders[_asset].length-1]] = _limitOrderIndexes[_asset][_id];
187:	        initId[_id] = accInterestPerOi[_trade.asset][_trade.tigAsset][_trade.direction]*int256(_trade.margin*_trade.leverage/1e18)/1e18;
212:	        initId[_id] = accInterestPerOi[_trades[_id].asset][_trades[_id].tigAsset][_trades[_id].direction]*int256(_newMargin*_trades[_id].leverage/1e18)/1e18;
233:	        initId[_id] = accInterestPerOi[_trades[_id].asset][_trades[_id].tigAsset][_trades[_id].direction]*int256(_trades[_id].margin*_trades[_id].leverage/1e18)/1e18;
264:	            _limitOrderIndexes[_asset][_limitOrders[_asset][_limitOrders[_asset].length-1]] = _limitOrderIndexes[_asset][_id];
265:	            _limitOrders[_asset][_limitOrderIndexes[_asset][_id]] = _limitOrders[_asset][_limitOrders[_asset].length-1];
269:	            _assetOpenPositionsIndexes[_asset][_assetOpenPositions[_asset][_assetOpenPositions[_asset].length-1]] = _assetOpenPositionsIndexes[_asset][_id];
270:	            _assetOpenPositions[_asset][_assetOpenPositionsIndexes[_asset][_id]] = _assetOpenPositions[_asset][_assetOpenPositions[_asset].length-1];
```

*/contracts/PairsContract.sol*
Links: [48](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/PairsContract.sol#L48), [157](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/PairsContract.sol#L157), [177](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/PairsContract.sol#L177).
```solidity
48:	    function addAsset(uint256 _asset, string memory _name, address _chainlinkFeed, uint256 _minLeverage, uint256 _maxLeverage, uint256 _feeMultiplier, uint256 _baseFundingRate) external onlyOwner {
157:	            require(_idToOi[_asset][_tigAsset].longOi <= _idToOi[_asset][_tigAsset].maxOi || _idToOi[_asset][_tigAsset].maxOi == 0, "MaxLongOi");
177:	            require(_idToOi[_asset][_tigAsset].shortOi <= _idToOi[_asset][_tigAsset].maxOi || _idToOi[_asset][_tigAsset].maxOi == 0, "MaxShortOi");
```

*/contracts/GovNFT.sol*
Links: [175](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L175), [183](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L183), [189](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L189), [206](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L206), [288](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L288).
```solidity
175:	        (bool success, bytes memory reason) = address(this).excessivelySafeCall(gasleft()*4/5, 150, abi.encodeWithSelector(this.nonblockingLzReceive.selector, _srcChainId, _srcAddress, _nonce, _payload));
183:	    function nonblockingLzReceive(uint16 _srcChainId, bytes calldata _srcAddress, uint64 _nonce, bytes calldata _payload) public {
189:	    function _nonblockingLzReceive(uint16 _srcChainId, bytes memory _srcAddress, uint64, bytes memory _payload) internal {
206:	    function retryMessage(uint16 _srcChainId, bytes calldata _srcAddress, uint64 _nonce, bytes calldata _payload) public {
288:	        if (assets.length == 0 || assets[assetsIndex[_tigAsset]] == address(0) || totalSupply() == 0 || !_allowedAsset[_tigAsset]) return;
```

*/contracts/StableVault.sol*
Links: [53](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/StableVault.sol#L53).
```solidity
53:	    function depositWithPermit(address _token, uint256 _amount, uint256 _deadline, bool _permitMax, uint8 v, bytes32 r, bytes32 s) external {
```

*/contracts/BondNFT.sol*
Links: [35](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L35), [178](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L178), [240](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L240).
```solidity
35:	    mapping(address => mapping(uint256 => uint256)) private accRewardsPerShare; // tigAsset => epoch => accRewardsPerShare
178:	                uint _pendingDelta = (bond.shares * accRewardsPerShare[bond.asset][epoch[bond.asset]] / 1e18 - bondPaid[_id][bond.asset]) - (bond.shares * accRewardsPerShare[bond.asset][bond.expireEpoch-1] / 1e18 - bondPaid[_id][bond.asset]);
240:	            uint _accRewardsPerShare = accRewardsPerShare[bond.asset][bond.expired ? bond.expireEpoch-1 : epoch[bond.asset]];
```

*/contracts/interfaces/ILayerZeroEndpoint.sol*
Links: [12](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/interfaces/ILayerZeroEndpoint.sol#L12), [14](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/interfaces/ILayerZeroEndpoint.sol#L14), [15](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/interfaces/ILayerZeroEndpoint.sol#L15), [24](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/interfaces/ILayerZeroEndpoint.sol#L24), [41](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/interfaces/ILayerZeroEndpoint.sol#L41), [78](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/interfaces/ILayerZeroEndpoint.sol#L78).
```solidity
12:	    // @param _refundAddress - if the source transaction is cheaper than the amount of value passed, refund the additional amount to this address
14:	    // @param _adapterParams - parameters for custom functionality. e.g. receive airdropped native gas from the relayer on destination
15:	    function send(uint16 _dstChainId, bytes calldata _destination, bytes calldata _payload, address payable _refundAddress, address _zroPaymentAddress, bytes calldata _adapterParams) external payable;
24:	    function receivePayload(uint16 _srcChainId, bytes calldata _srcAddress, address _dstAddress, uint64 _nonce, uint _gasLimit, bytes calldata _payload) external;
41:	    function estimateFees(uint16 _dstChainId, address _userApplication, bytes calldata _payload, bool _payInZRO, bytes calldata _adapterParam) external view returns (uint nativeFee, uint zroFee);
78:	    function getConfig(uint16 _version, uint16 _chainId, address _userApplication, uint _configType) external view returns (bytes memory);
```

*/contracts/interfaces/ILayerZeroReceiver.sol*
Links: [11](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/interfaces/ILayerZeroReceiver.sol#L11).
```solidity
11:	    function lzReceive(uint16 _srcChainId, bytes calldata _srcAddress, uint64 _nonce, bytes calldata _payload) external;
```

*/contracts/interfaces/IPosition.sol*
Links: [52](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/interfaces/IPosition.sol#L52).
```solidity
52:	    function updateFunding(uint256 _asset, address _tigAsset, uint256 _longOi, uint256 _shortOi, uint256 _baseFundingRate, uint256 _vaultFundingPercent) external;
```

### [NC-03] NatSpec Comments Use `//` Instead of `///`

The [NatSpec notation](https://docs.soliditylang.org/en/v0.8.17/natspec-format.html#documentation-example) requires the use of `///` or `/**` to differentiate it from regular comments. There are instances in the codebase where a `//` is used instead of `///`.

#### Findings:

*/contracts/interfaces/ILayerZeroEndpoint.sol*

All NatSpec comments use  `//` instead of `///` (50 occurrences).

*/contracts/interfaces/ILayerZeroReceiver.sol*

All NatSpec comments use  `//` instead of `///` (5 occurrences).

*/contracts/interfaces/ILayerZeroUserApplicationConfig.sol*

All NatSpec comments use  `//` instead of `///` (12 occurrences).

### [NC-04] `uint256` And Alias `uint` Used

According to the [Solidity Documentation](https://docs.soliditylang.org/en/v0.8.10/types.html#integers) `int` and `uint` are aliases for `uint256` and `int256` respectively. There are times in the codebase where both formats are used. Consider using `uint256` and `int256` for better code specificity and style consistency. 

#### Findings:

*/contracts/interfaces/IPosition.sol*
Links: [8](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPosition.sol#L8), [9](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPosition.sol#L9), [10](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPosition.sol#L10), [12](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPosition.sol#L12), [13](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPosition.sol#L13), [14](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPosition.sol#L14), [15](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPosition.sol#L15), [17](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPosition.sol#L17), [19](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPosition.sol#L19), [46](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPosition.sol#L46), [48](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPosition.sol#L48), [49](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPosition.sol#L49), [50](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPosition.sol#L50), [51](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPosition.sol#L51).
```solidity
8:	uint margin;
9:	uint leverage;
10:	uint asset;
12:	uint price;
13:	uint tpPrice;
14:	uint slPrice;
15:	uint orderType;
17:	uint id;
19:	int accInterest;
46:	function ownerOf(uint _id) external view returns (address);
48:	function burn(uint _id) external;
49:	function modifyTp(uint _id, uint _tpPrice) external;
50:	function modifySl(uint _id, uint _slPrice) external;
51:	function getCount() external view returns (uint);
```

### [NC-05] Order of Functions Not Compliant With Solidity Docs

The [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions) suggests the following function ordering:  constructor, receive function (if exists), fallback function (if exists), external, public, internal, private.

#### Findings:

The following contracts are not compliant (examples are only to prove the functions are out of order NOT a full description): 

[Trading.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol): external functions are positioned after internal functions.
[TradingExtension.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol): external functions are positioned after public functions.
[TradingLibrary.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol): external functions are positioned after public functions.
[Position.sol](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol): the constructor is located after public functions.
[PairsContract.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol): external functions are positioned after private functions.
[GovNFT.sol](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol): external functions are positioned after internal functions.
[StableVault.sol](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/StableVault.sol): the constructor is positioned after external functions.
[Lock.sol](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Lock.sol): external functions are positioned after public functions.
[BondNFT.sol](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol): external functions are positioned after public functions.

### [NC-06] Underscore Notation Not Used / Not Used Consistently

Consider using underscore notation to help with contract readability (Ex. `23453` -> `23_453`).

#### Findings:

*/contracts/Position.sol*
Links: [120](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L120).
```solidity
120:	fundingDeltaPerSec[_asset][_tigAsset] = (_oiDelta*int256(_baseFundingRate)/int256(DIVISION_CONSTANT))/31536000;
```

*/contracts/GovNFT.sol*
Links: [17](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L17).
```solidity
17:	uint256 public gas = 150000;
```


### [NC-07] Power of Ten Literal > `10e3` Not In Scientific Notation

Power of ten literals > `10e3` are easier to read when expressed in scientific notation. Consider expressing large powers of ten in scientific notation (Ex. 10e5).

#### Findings:

*/contracts/TradingExtension.sol*
Links: [26](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L26).
```solidity
26:	uint public maxGasPrice = 1000000000000;
```

*/contracts/GovNFT.sol*
Links: [16](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L16), [66](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L66).
```solidity
16:	uint256 private constant MAX = 10000;
66:	require(tokenId <= 10000, "BadID");
```

### [NC-08] Style Guide Voiding If Structure Braces / Inconsistent If Style

  The [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#control-structures) states: *[t]he braces denoting the body of a contract, library, functions and structs should ... close on their own line at the same indentation level as the beginning of the declaration*. Consider sticking to a single style (preferably the [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#control-structures)).
  
#### Findings:

*/contracts/PairsContract.sol*
Links: [178-179](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L178-L179).
```solidity
178:	    }
179:	else {
```

### [NC-09] `EVENTS` Comment Violates Style

There is a conflicting code style. One format is all capitalized, the other not. Consider maintaining a single code style.

*/contracts/PairsContract.sol*
Links: [187](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L187), [194](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L194).
```solidity
187:	// Modifiers
194:	// EVENTS
```