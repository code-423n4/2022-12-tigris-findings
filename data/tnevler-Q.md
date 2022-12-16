# Report
## Non-Critical Issues ##
### [N-1]: Function defines a named return variable but then it uses return statements
**Context:**

1. ```return payout_;``` [L809](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L809) 
1. ```return referrals.getReferral(referrals.getReferred(_trader));``` [L151](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L151) 
1. ```return MetaContext._msgSender();``` [L321](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L321) 
1. ```return``` [L226](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L226) 
1. ```return MetaContext._msgSender();``` [L333](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L333) 
1. ```return MetaContext._msgSender();``` [L59](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableToken.sol#L59) 
1. ```return super._msgSender();``` [L25](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/MetaContext.sol#L25) 

**Recommendation:**

Choose named return variable or return statement. It is unnecessary to use both.


### [N-2]: Scientific notation
**Context:**

1. ```uint public maxGasPrice = 1000000000000; // 1000 gwei``` [L26](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L26) (10e12)
1. ```uint256 private constant MAX = 10000;``` [L16](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L16) (10e4)

**Description:**

Scientific notation should be used for better code readability.

### [N-3]: Wrong order of functions
**Context:**

1. ```function verifyPrice(``` [L90](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L90) (external function can not go after public function)
1. ```using Counters for Counters.Counter;``` [L15](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L15) (using for declaration can no go after public function)
1. ```mapping(uint256 => mapping(address => OpenInterest)) private _idToOi;``` [L21](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L21) (state variable can not go after public function)
1. ```function setBaseURI(string calldata _newBaseURI) external onlyOwner {``` [L46](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L46) (external function can not go after internal function)
1. ```modifier onlyMinter() {``` [L51](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableToken.sol#L51) (modifier definition can not go after public function
1. ```function depositWithPermit(address _token, uint256 _amount, uint256 _deadline, bool _permitMax, uint8 v, bytes32 r, bytes32 s) external {``` [L53](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L53) (external function can not go after public function)
1. ```function claimDebt(``` [L47](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L47) (external function can not go after public function)
1. ```pragma solidity ^0.8.0;``` [L5](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ITrading.sol#L5) (pragma directive can not go after import directive)

**Description:**

According to [official solidity documentation](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions) functions should be grouped according to their visibility and ordered:

+ constructor

+ receive function (if exists)

+ fallback function (if exists)

+ external

+ public

+ internal

+ private

Within a grouping, place the view and pure functions last.

**Recommendation:**

Put the functions in the correct order according to the documentation.

### [N-4]: NatSpec is missing
**Context:**

1. [TradingExtension.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol) (10 functions)
1. [Position.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol) (19 functions)
1. [PairsContract.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol) (2 functions) 
1. [Referrals.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol) (3 functions)
1. [GovNFT.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol) (25 functions)
1. [StableToken.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableToken.sol) (4 functions)
1. ```function depositWithPermit(address _token, uint256 _amount, uint256 _deadline, bool _permitMax, uint8 v, bytes32 r, bytes32 s) external {``` [L53](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L53) 
1. [BondNFT.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol) (13 functions)
1. [MetaContext.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/MetaContext.sol) 
1. [IBondNFT.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IBondNFT.sol)
1. [IGovNFT.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IGovNFT.sol)
1. [IPairsContract.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPairsContract.sol)
1. [IPosition.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPosition.sol)
1. [IReferrals.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IReferrals.sol)
1. [IStableVault.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IStableVault.sol)
1. [ITrading.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ITrading.sol)

### [N-5]: Typos
**Context:**

1. ```* @param _maxLeverage maximimum allowed leverage``` [L44](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L44) (Change ***maximimum*** to ***maximum***)
1. ```* @param _maxLeverage minimum allowed leverage``` [L45](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L45) (Change ***_maxLeverage*** to ***minLeverage***)
1. ```* @param _onOpen true if adding to open interesr``` [L151](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L151) (Change ***interesr*** to ***interest***)
1. ```* @param _onOpen true if adding to open interesr``` [L171](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L171) (Change ***interesr*** to ***interest***)
1. ```* @notice Owner can retreive Gov NFTs``` [L135](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L135) (Change ***retreive*** to ***retrieve***)

### [N-6]: Line is too long
**Context:**

1. ```uint256 _marginAfterFees = _tradeInfo.margin - _handleOpenFees(_tradeInfo.asset, _tradeInfo.margin*_tradeInfo.leverage/1e18, _trader, _tigAsset, false);``` [L178](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L178) 
1. ```(uint256 _price,) = tradingExtension.getVerifiedPrice(_trade.asset, _priceData, _signature, _trade.direction ? 1 : 2);``` [L285](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L285) 
1. ```(,int256 _payout) = TradingLibrary.pnl(_trade.direction, _assetPrice, _trade.price, _newMargin, _newLeverage, _trade.accInterest);``` [L434](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L434) 
1. ```if (_price > trade.price+trade.price*limitOrderPriceRange/DIVISION_CONSTANT || _price < trade.price-trade.price*limitOrderPriceRange/DIVISION_CONSTANT) revert("6"); //LimitNotMet``` [L496](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L496) 
1. ```emit LimitOrderExecuted(trade.asset, trade.direction, trade.price, trade.leverage, trade.margin - _fee, _id, trade.trader, _msgSender());``` [L520](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L520) 
1. ```(uint256 _positionSizeAfterPrice, int256 _payout) = TradingLibrary.pnl(_trade.direction, _price, _trade.price, _trade.margin, _trade.leverage, _trade.accInterest);``` [L543](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L543) 
1. ```(IPosition.Trade memory _trade, uint256 _positionSize, int256 _payout) = tradingExtension._closePosition(_id, _price, _percent);``` [L612](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L612) 
1. ```if ((_trade.margin*_trade.leverage*(DIVISION_CONSTANT-_percent)/DIVISION_CONSTANT)/1e18 < tradingExtension.minPos(_trade.tigAsset)) revert("!size");``` [L616](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L616) 
1. ```_toMint = _handleCloseFees(_trade.asset, uint256(_payout)*_percent/DIVISION_CONSTANT, _trade.tigAsset, _positionSize*_percent/DIVISION_CONSTANT, _trade.trader, _isBot);``` [L624](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L624) 
1. ```function _handleDeposit(address _tigAsset, address _marginAsset, uint256 _margin, address _stableVault, ERC20PermitData calldata _permitData, address _trader) internal {``` [L643](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L643) 
1. ```ERC20Permit(_marginAsset).permit(_trader, address(this), _permitData.amount, _permitData.deadline, _permitData.v, _permitData.r, _permitData.s);``` [L647](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L647) 
1. ```if (IERC20(_outputToken).balanceOf(address(this)) != _balBefore + _toMint/(10**(18-ExtendedIERC20(_outputToken).decimals()))) revert BadWithdraw();``` [L675](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L675) 
1. ```require(_token == IStableVault(_stableVault).stable() || IStableVault(_stableVault).allowed(_token), "Token not approved in vault");``` [L877](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L877) 
1. ```function setFees(bool _open, uint _daoFees, uint _burnFees, uint _referralFees, uint _botFees, uint _percent) external onlyOwner {``` [L952](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L952) 
1. ```(_positionSize, _payout) = TradingLibrary.pnl(_trade.direction, _price, _trade.price, _trade.margin, _trade.leverage, _trade.accInterest);``` [L67](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L67) 
1. ```modifyLongOi(_trade.asset, _trade.tigAsset, false, (_trade.margin*_trade.leverage/1e18)*_percent/DIVISION_CONSTANT);``` [L71](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L71) 
1. ```modifyShortOi(_trade.asset, _trade.tigAsset, false, (_trade.margin*_trade.leverage/1e18)*_percent/DIVISION_CONSTANT);``` [L73](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L73) 
1. ```function pnl(bool _direction, uint _currentPrice, uint _price, uint _margin, uint _leverage, int256 accInterest) external pure returns (uint256 _positionSize, int256 _payout) {``` [L36](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L36) 
1. ```_payout = int256(_margin) + int256(_initPositionSize * (1e18 * _currentPrice / _price - 1e18)/1e18) + accInterest;``` [L40](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L40) 
1. ```_payout = int256(_margin) - int256(_initPositionSize * (1e18 - 1e18 * _currentPrice / _price)/1e18) + accInterest;``` [L42](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L42) 
1. ```_payout = int256(_margin) + int256(_initPositionSize * (1e18 - 1e18 * _currentPrice / _price)/1e18) + accInterest;``` [L44](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L44) 
1. ```_payout = int256(_margin) - int256(_initPositionSize * (1e18 * _currentPrice / _price - 1e18)/1e18) + accInterest;``` [L46](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L46) 
1. ```function liqPrice(bool _direction, uint _tradePrice, uint _leverage, uint _margin, int _accInterest, uint _liqPercent) public pure returns (uint256 _liqPrice) {``` [L62](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L62) 
1. ```_liqPrice = _tradePrice - ((_tradePrice*1e18/_leverage) * uint(int(_margin)+_accInterest) / _margin) * _liqPercent / 1e10;``` [L64](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L64) 
1. ```_liqPrice = _tradePrice + ((_tradePrice*1e18/_leverage) * uint(int(_margin)+_accInterest) / _margin) * _liqPercent / 1e10;``` [L66](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L66) 
1. ```_pendingFunding = (int256(block.timestamp-lastUpdate[_trade.asset][_trade.tigAsset])*fundingDeltaPerSec[_trade.asset][_trade.tigAsset])*1e18/int256(longOi[_trade.asset][_trade.tigAsset]);``` [L48](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L48) 
1. ```_pendingFunding = (int256(block.timestamp-lastUpdate[_trade.asset][_trade.tigAsset])*fundingDeltaPerSec[_trade.asset][_trade.tigAsset])*1e18/int256(shortOi[_trade.asset][_trade.tigAsset]);``` [L55](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L55) 
1. ```_trade.accInterest += (int256(_trade.margin*_trade.leverage/1e18)*(accInterestPerOi[_trade.asset][_trade.tigAsset][_trade.direction]+_pendingFunding)/1e18)-initId[_id];``` [L62](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L62) 
1. ```function assetOpenPositionsIndexes(uint _asset, uint _id) public view returns (uint256) { return _assetOpenPositionsIndexes[_asset][_id]; }``` [L69](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L69) 
1. ```function updateFunding(uint256 _asset, address _tigAsset, uint256 _longOi, uint256 _shortOi, uint256 _baseFundingRate, uint _vaultFundingPercent) external onlyMinter {``` [L99](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L99) 
1. ```accInterestPerOi[_asset][_tigAsset][true] += ((int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(longOi[_asset][_tigAsset]))*int256(1e10-vaultFundingPercent[_asset][_tigAsset])/1e10;``` [L102](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L102) 
1. ```accInterestPerOi[_asset][_tigAsset][false] -= (int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(shortOi[_asset][_tigAsset]);``` [L104](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L104) 
1. ```accInterestPerOi[_asset][_tigAsset][true] -= (int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(longOi[_asset][_tigAsset]);``` [L107](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L107) 
1. ```accInterestPerOi[_asset][_tigAsset][false] += ((int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(shortOi[_asset][_tigAsset]))*int256(1e10-vaultFundingPercent[_asset][_tigAsset])/1e10;``` [L109](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L109) 
1. ```initId[newTokenID] = accInterestPerOi[_mintTrade.asset][_mintTrade.tigAsset][_mintTrade.direction]*int256(_mintTrade.margin*_mintTrade.leverage/1e18)/1e18;``` [L153](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L153) 
1. ```initId[_id] = accInterestPerOi[_trade.asset][_trade.tigAsset][_trade.direction]*int256(_trade.margin*_trade.leverage/1e18)/1e18;``` [L187](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L187) 
1. ```initId[_id] = accInterestPerOi[_trades[_id].asset][_trades[_id].tigAsset][_trades[_id].direction]*int256(_newMargin*_trades[_id].leverage/1e18)/1e18;``` [L212](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L212) 
1. ```initId[_id] = accInterestPerOi[_trades[_id].asset][_trades[_id].tigAsset][_trades[_id].direction]*int256(_trades[_id].margin*_trades[_id].leverage/1e18)/1e18;``` [L233](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L233) 
1. ```_limitOrderIndexes[_asset][_limitOrders[_asset][_limitOrders[_asset].length-1]] = _limitOrderIndexes[_asset][_id];``` [L264](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L264) 
1. ```_assetOpenPositionsIndexes[_asset][_assetOpenPositions[_asset][_assetOpenPositions[_asset].length-1]] = _assetOpenPositionsIndexes[_asset][_id];``` [L269](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L269) 
1. ```_assetOpenPositions[_asset][_assetOpenPositionsIndexes[_asset][_id]] = _assetOpenPositions[_asset][_assetOpenPositions[_asset].length-1];``` [L270](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L270) 
1. ```function addAsset(uint256 _asset, string memory _name, address _chainlinkFeed, uint256 _minLeverage, uint256 _maxLeverage, uint256 _feeMultiplier, uint256 _baseFundingRate) external onlyOwner {``` [L48](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L48) 
1. ```require(_idToOi[_asset][_tigAsset].longOi <= _idToOi[_asset][_tigAsset].maxOi || _idToOi[_asset][_tigAsset].maxOi == 0, "MaxLongOi");``` [L157](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L157) 
1. ```require(_idToOi[_asset][_tigAsset].shortOi <= _idToOi[_asset][_tigAsset].maxOi || _idToOi[_asset][_tigAsset].maxOi == 0, "MaxShortOi");``` [L177](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L177) 
1. ```(bool success, bytes memory reason) = address(this).excessivelySafeCall(gasleft()*4/5, 150, abi.encodeWithSelector(this.nonblockingLzReceive.selector, _srcChainId, _srcAddress, _nonce, _payload));``` [L175](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L175) 
1. ```function nonblockingLzReceive(uint16 _srcChainId, bytes calldata _srcAddress, uint64 _nonce, bytes calldata _payload) public {``` [L183](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L183) 
1. ```if (assets.length == 0 || assets[assetsIndex[_tigAsset]] == address(0) || totalSupply() == 0 || !_allowedAsset[_tigAsset]) return;``` [L288](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L288) 
1. ```function depositWithPermit(address _token, uint256 _amount, uint256 _deadline, bool _permitMax, uint8 v, bytes32 r, bytes32 s) external {``` [L53](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L53) 
1. ```uint _pendingDelta = (bond.shares * accRewardsPerShare[bond.asset][epoch[bond.asset]] / 1e18 - bondPaid[_id][bond.asset]) - (bond.shares * accRewardsPerShare[bond.asset][bond.expireEpoch-1] / 1e18 - bondPaid[_id][bond.asset]);``` [L178](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L178) 
1. ```uint _accRewardsPerShare = accRewardsPerShare[bond.asset][bond.expired ? bond.expireEpoch-1 : epoch[bond.asset]];``` [L240](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L240) 
1. ```// @param _refundAddress - if the source transaction is cheaper than the amount of value passed, refund the additional amount to this address``` [L12](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ILayerZeroEndpoint.sol#L12) 
1. ```// @param _adapterParams - parameters for custom functionality. e.g. receive airdropped native gas from the relayer on destination``` [L14](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ILayerZeroEndpoint.sol#L14) 
1. ```function send(uint16 _dstChainId, bytes calldata _destination, bytes calldata _payload, address payable _refundAddress, address _zroPaymentAddress, bytes calldata _adapterParams) external payable;``` [L15](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ILayerZeroEndpoint.sol#L15) 
1. ```function receivePayload(uint16 _srcChainId, bytes calldata _srcAddress, address _dstAddress, uint64 _nonce, uint _gasLimit, bytes calldata _payload) external;``` [L24](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ILayerZeroEndpoint.sol#L24) 
1. ```function estimateFees(uint16 _dstChainId, address _userApplication, bytes calldata _payload, bool _payInZRO, bytes calldata _adapterParam) external view returns (uint nativeFee, uint zroFee);``` [L41](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ILayerZeroEndpoint.sol#L41) 
1. ```function getConfig(uint16 _version, uint16 _chainId, address _userApplication, uint _configType) external view returns (bytes memory);``` [L78](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ILayerZeroEndpoint.sol#L78) 
1. ```function updateFunding(uint256 _asset, address _tigAsset, uint256 _longOi, uint256 _shortOi, uint256 _baseFundingRate, uint256 _vaultFundingPercent) external;``` [L52](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPosition.sol#L52) 

**Description:**

Maximum suggested line length is [120 characters](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-length).
