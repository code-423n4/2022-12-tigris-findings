
## `block.timestamp` used as time proxy 
### Summary
Risk of using `block.timestamp` for time should be considered. 
### Details
`block.timestamp` is not an ideal proxy for time because of issues with synchronization, miner manipulation and changing block times. 

This kind of issue may affect the code allowing or reverting the code before the expected deadline, modifying the normal functioning or reverting sometimes.
### References
SWC ID: 116

### Github Permalinks 
- Dangerous comparisons with `block.timestamp`
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L491
            require(block.timestamp >= limitDelay[_id]);

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/utils/TradingLibrary.sol#L109
        require(block.timestamp >= _priceData.timestamp, "FutSig");

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L110
        require(epoch[bond.asset] == block.timestamp/DAY, "Bad epoch");

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L329
        require(epoch[bond.asset] == block.timestamp/DAY, "Bad epoch");

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L332
            require(block.timestamp > bond.mintTime + 300, "Recent update");


https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/utils/TradingLibrary.sol#L110
        require(block.timestamp <= _priceData.timestamp + _validSignatureTimer, "ExpSig");

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L887
            require(_proxy.proxy == _msgSender() && _proxy.time >= block.timestamp, "Proxy not approved");

- `block.timestamp` used as proxy of time should be avoided
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L347
        limitDelay[_id] = block.timestamp + 4;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L48
            _pendingFunding = (int256(block.timestamp-lastUpdate[_trade.asset][_trade.tigAsset])*fundingDeltaPerSec[_trade.asset][_trade.tigAsset])*1e18/int256(longOi[_trade.asset][_trade.tigAsset]);

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L55
            _pendingFunding = (int256(block.timestamp-lastUpdate[_trade.asset][_trade.tigAsset])*fundingDeltaPerSec[_trade.asset][_trade.tigAsset])*1e18/int256(shortOi[_trade.asset][_trade.tigAsset]);

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L102
                accInterestPerOi[_asset][_tigAsset][true] += ((int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(longOi[_asset][_tigAsset]))*int256(1e10-vaultFundingPercent[_asset][_tigAsset])/1e10;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L104
            accInterestPerOi[_asset][_tigAsset][false] -= (int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(shortOi[_asset][_tigAsset]);

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L107
            accInterestPerOi[_asset][_tigAsset][true] -= (int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(longOi[_asset][_tigAsset]);

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L109
                accInterestPerOi[_asset][_tigAsset][false] += ((int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(shortOi[_asset][_tigAsset]))*int256(1e10-vaultFundingPercent[_asset][_tigAsset])/1e10;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L112
        lastUpdate[_asset][_tigAsset] = block.timestamp;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L75
                block.timestamp,// mint timestamp

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L114
            uint expireEpoch = block.timestamp/DAY + bond.period + _period;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L120
            _bond.mintTime = block.timestamp;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L218
            uint aEpoch = block.timestamp / DAY;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L354
        epoch[_asset] = block.timestamp/DAY;

### Mitigation
- Consider the risk of using `block.timestamp` as time proxy and evaluate if block numbers can be used as an approximation for the application logic. Both have risks that need to be factored in. 
- Consider using an oracle for precision


## Use of hardcoded amount of days
### Summary 
Formula uses a hardcoded value of `365` (days) which would be wrong applied in a leap year (`366` days)
### Github Permalinks
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Lock.sol#L13
    uint public constant maxPeriod = 365;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L65
            uint shares = _amount * _period / 365;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L111
        require(bond.period+_period <= 365, "MAX PERIOD");

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L113
            uint shares = (bond.amount + _amount) * (bond.period + _period) / 365;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Lock.sol#L13
    uint public constant maxPeriod = 365;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L65
            uint shares = _amount * _period / 365;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L111
        require(bond.period+_period <= 365, "MAX PERIOD");

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L113
            uint shares = (bond.amount + _amount) * (bond.period + _period) / 365;


### Mitigation
Consider using an oracle for this
Consider using a method that change the value between `365` and `366` for the operations in leap years and regular years



## Return value not being checked 
### Details
Return values not being checked may lead into unexpected behaviors with functions. Not events/reverts are being emitted if that fails.

### Github Permalinks
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L674
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Lock.sol#L75

### Mitigation
Check values and `revert`/`emit` events if needed


## Variable shadows another variable
###  Summary 
Name shadowing where two or more variables/functions share the same name could be confusing to developers and/or reviewers
###  Details 
Use of `owner` as new value variable shadows Ownable `owner`
###  Github Permalinks 
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L76-L84
```
    function _burn(uint256 tokenId) internal override {
        address owner = ownerOf(tokenId);
        for (uint i=0; i<assetsLength(); i++) {
            userDebt[owner][assets[i]] += accRewardsPerNFT[assets[i]];
            userDebt[owner][assets[i]] -= userPaid[owner][assets[i]]/balanceOf(owner);//@audit shadows
            userPaid[owner][assets[i]] -= userPaid[owner][assets[i]]/balanceOf(owner);            
        }
        super._burn(tokenId);
    }
```
###  Mitigation
Replace `owner` variable in the function parameter to `_owner` or a similar substitution











# Informational
## Deprecated code should be removed from the code
### Summary
Having deprecated or not used code in the code can lead to unexpected behaviors.

### Impact
First of all, this affects gas cost, if something is not used, better remove it.

Second, as notice multiple time in the blockchain, unused code after some time can be used for exploits (sometimes), this happens when code is not used and therefore not tested enough. This is not likely to happen here because of the `onlyOwner` modifier, however, who knows if owner address can be compromised or malicious in some moment.

### Github Permalinks
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Referrals.sol#L57-L76
`* @notice deprecated`
### Mitigation
Remove unused and deprecated code


##  Use of magic numbers is confusing and risky
### Note
Not found by Picodes C4 4naly3er
### Summary
Magic numbers are hardcoded numbers used in the code which are ambiguous to their intended purpose. These should be replaced with constants to make code more readable and maintainable.
### Details
Values are hardcoded and would be more readable and maintainable if declared as a constant

### Github Permalinks
- Many 1e18, 1e10, etc

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L95
    uint constant private DIVISION_CONSTANT = 1e10; // 100%

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L116
    uint public limitOrderPriceRange = 1e8; // 1%

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L178
        uint256 _marginAfterFees = _tradeInfo.margin - _handleOpenFees(_tradeInfo.asset, _tradeInfo.margin*_tradeInfo.leverage/1e18, _trader, _tigAsset, false);

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L179
        uint256 _positionSize = _marginAfterFees * _tradeInfo.leverage / 1e18;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L274
        uint _fee = _handleOpenFees(_trade.asset, _addMargin*_trade.leverage/1e18, _trader, _trade.tigAsset, false);

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L286
            uint _positionSize = (_addMargin - _fee) * _trade.leverage / 1e18;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L493
            uint _fee = _handleOpenFees(trade.asset, trade.margin*trade.leverage/1e18, trade.trader, trade.tigAsset, true);

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L514
                tradingExtension.modifyLongOi(trade.asset, trade.tigAsset, true, trade.margin*trade.leverage/1e18);

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L516
                tradingExtension.modifyShortOi(trade.asset, trade.tigAsset, true, trade.margin*trade.leverage/1e18);

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L544
            uint256 _positionSize = _trade.margin*_trade.leverage/1e18;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L616
            if ((_trade.margin*_trade.leverage*(DIVISION_CONSTANT-_percent)/DIVISION_CONSTANT)/1e18 < tradingExtension.minPos(_trade.tigAsset)) revert("!size");

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/TradingExtension.sol#L11
    uint constant private DIVISION_CONSTANT = 1e10; // 100%

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/TradingExtension.sol#L71
                modifyLongOi(_trade.asset, _trade.tigAsset, false, (_trade.margin*_trade.leverage/1e18)*_percent/DIVISION_CONSTANT);

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/TradingExtension.sol#L73
                modifyShortOi(_trade.asset, _trade.tigAsset, false, (_trade.margin*_trade.leverage/1e18)*_percent/DIVISION_CONSTANT);     

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/TradingExtension.sol#L218
            if (_margin*_leverage/1e18 < minPositionSize[_tigAsset]) revert("!size");

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/utils/TradingLibrary.sol#L38
            uint _initPositionSize = _margin * _leverage / 1e18;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/utils/TradingLibrary.sol#L40
                _payout = int256(_margin) + int256(_initPositionSize * (1e18 * _currentPrice / _price - 1e18)/1e18) + accInterest;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/utils/TradingLibrary.sol#L42
                _payout = int256(_margin) - int256(_initPositionSize * (1e18 - 1e18 * _currentPrice / _price)/1e18) + accInterest;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/utils/TradingLibrary.sol#L44
                _payout = int256(_margin) + int256(_initPositionSize * (1e18 - 1e18 * _currentPrice / _price)/1e18) + accInterest;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/utils/TradingLibrary.sol#L46
                _payout = int256(_margin) - int256(_initPositionSize * (1e18 * _currentPrice / _price - 1e18)/1e18) + accInterest;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/utils/TradingLibrary.sol#L64
            _liqPrice = _tradePrice - ((_tradePrice*1e18/_leverage) * uint(int(_margin)+_accInterest) / _margin) * _liqPercent / 1e10;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/utils/TradingLibrary.sol#L66
            _liqPrice = _tradePrice + ((_tradePrice*1e18/_leverage) * uint(int(_margin)+_accInterest) / _margin) * _liqPercent / 1e10;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L16
    uint constant public DIVISION_CONSTANT = 1e10; // 100%

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L48
            _pendingFunding = (int256(block.timestamp-lastUpdate[_trade.asset][_trade.tigAsset])*fundingDeltaPerSec[_trade.asset][_trade.tigAsset])*1e18/int256(longOi[_trade.asset][_trade.tigAsset]);

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L52
                _pendingFunding = _pendingFunding*int256(1e10-vaultFundingPercent[_trade.asset][_trade.tigAsset])/1e10;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L55
            _pendingFunding = (int256(block.timestamp-lastUpdate[_trade.asset][_trade.tigAsset])*fundingDeltaPerSec[_trade.asset][_trade.tigAsset])*1e18/int256(shortOi[_trade.asset][_trade.tigAsset]);

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L59
                _pendingFunding = _pendingFunding*int256(1e10-vaultFundingPercent[_trade.asset][_trade.tigAsset])/1e10;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L62
        _trade.accInterest += (int256(_trade.margin*_trade.leverage/1e18)*(accInterestPerOi[_trade.asset][_trade.tigAsset][_trade.direction]+_pendingFunding)/1e18)-initId[_id];

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L102
                accInterestPerOi[_asset][_tigAsset][true] += ((int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(longOi[_asset][_tigAsset]))*int256(1e10-vaultFundingPercent[_asset][_tigAsset])/1e10;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L104
            accInterestPerOi[_asset][_tigAsset][false] -= (int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(shortOi[_asset][_tigAsset]);

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L107
            accInterestPerOi[_asset][_tigAsset][true] -= (int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(longOi[_asset][_tigAsset]);

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L109
                accInterestPerOi[_asset][_tigAsset][false] += ((int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(shortOi[_asset][_tigAsset]))*int256(1e10-vaultFundingPercent[_asset][_tigAsset])/1e10;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L153
            initId[newTokenID] = accInterestPerOi[_mintTrade.asset][_mintTrade.tigAsset][_mintTrade.direction]*int256(_mintTrade.margin*_mintTrade.leverage/1e18)/1e18;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L187
        initId[_id] = accInterestPerOi[_trade.asset][_trade.tigAsset][_trade.direction]*int256(_trade.margin*_trade.leverage/1e18)/1e18;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L212
        initId[_id] = accInterestPerOi[_trades[_id].asset][_trades[_id].tigAsset][_trades[_id].direction]*int256(_newMargin*_trades[_id].leverage/1e18)/1e18;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L233
        initId[_id] = accInterestPerOi[_trades[_id].asset][_trades[_id].tigAsset][_trades[_id].direction]*int256(_trades[_id].margin*_trades[_id].leverage/1e18)/1e18;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/PairsContract.sol#L14
    uint256 private maxBaseFundingRate = 1e10;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/PairsContract.sol#L161
            if (_idToOi[_asset][_tigAsset].longOi < 1e9) {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/PairsContract.sol#L181
            if (_idToOi[_asset][_tigAsset].shortOi < 1e9) {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L122
            bondPaid[_id][bond.asset] = accRewardsPerShare[bond.asset][epoch[bond.asset]] * _bond.shares / 1e18;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L178
                uint _pendingDelta = (bond.shares * accRewardsPerShare[bond.asset][epoch[bond.asset]] / 1e18 - bondPaid[_id][bond.asset]) - (bond.shares * accRewardsPerShare[bond.asset][bond.expireEpoch-1] / 1e18 - bondPaid[_id][bond.asset]);

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L180
                    accRewardsPerShare[bond.asset][epoch[bond.asset]] += _pendingDelta*1e18/totalShares[bond.asset];

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L225
            accRewardsPerShare[_tigAsset][aEpoch] += _amount * 1e18 / totalShares[_tigAsset];

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L241
            bond.pending = bond.shares * _accRewardsPerShare / 1e18 - bondPaid[_id][bond.asset];

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L311
            bondPaid[bond.id][bond.asset] = accRewardsPerShare[bond.asset][epoch[bond.asset]] * bond.shares / 1e18;

- some other numbers
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/utils/TradingLibrary.sol#L117
                    _priceData.price < assetChainlinkPrice+assetChainlinkPrice*2/100 &&

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/utils/TradingLibrary.sol#L118
                    _priceData.price > assetChainlinkPrice-assetChainlinkPrice*2/100, "!chainlinkPrice"

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L120
        fundingDeltaPerSec[_asset][_tigAsset] = (_oiDelta*int256(_baseFundingRate)/int256(DIVISION_CONSTANT))/31536000;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L332
            require(block.timestamp > bond.mintTime + 300, "Recent update");



### Mitigation
Replace magic hardcoded numbers with declared constants.



## Naming convention of constants
### Summary
Constant naming convention is all upper case. 
### Details
Some constants are not using proper style.
Constant should be in `UPPER_CASE_WITH_UNDERSCORES` as per Solidity Style Guide. 
### Github Permalinks
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L96
    uint private constant liqPercent = 9e9; // 90%

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Lock.sol#L12
    uint public constant minPeriod = 7;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Lock.sol#L13
    uint public constant maxPeriod = 365;

### Mitigation
Rename the constant to uppercase style: `CONSTANTS_WITH_UNDERSCORES`. 


## Different versions of pragma
### Summary
Some of the contracts include an unlocked pragma, e.g., pragma solidity >=0.8.9. 

Locking the pragma helps ensure that contracts are not accidentally deployed using an old compiler version with unfixed bugs.

### Github Permalinks
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/TradingExtension.sol
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/utils/TradingLibrary.sol
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/PairsContract.sol
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Referrals.sol
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/StableToken.sol
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/StableVault.sol
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Lock.sol
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/utils/MetaContext.sol
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/interfaces/IBondNFT.sol
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/interfaces/IGovNFT.sol
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/interfaces/ILayerZeroEndpoint.sol
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/interfaces/ILayerZeroReceiver.sol
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/interfaces/ILayerZeroUserApplicationConfig.sol
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/interfaces/IPairsContract.sol
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/interfaces/IPosition.sol
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/interfaces/IReferrals.sol
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/interfaces/IStableVault.sol
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/interfaces/ITrading.sol

### Mitigation
Lock pragmas to a specific Solidity version. 
Consider converting ^0.8.0 into 0.8.17
Consider converting ^0.8.9 into 0.8.17







## Inconsistent spacing in comments
Some lines use `// x` and some use `//x`. The instances below point out the usages that don't follow the majority, within each file
### Details

`uint256 public genericDeclaration; //generic comment without space`
But following the style of the other comments would be:
`uint256 public genericDeclaration; // generic comment with space`]]
### Github Permalinks


https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L1
//SPDX-License-Identifier: Unlicense

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L81
    error LimitNotSet(); //7

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L238
        if (_trade.orderType != 0) revert("4"); //IsLimit        

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L273
        if (_trade.orderType != 0) revert("4"); //IsLimit

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L392
        if (_trade.orderType != 0) revert(); //IsLimit

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L428
        if (_trade.orderType != 0) revert(); //IsLimit

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L464
        if (_trade.orderType != 0) revert("4"); //IsLimit

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L496
            if (_price > trade.price+trade.price*limitOrderPriceRange/DIVISION_CONSTANT || _price < trade.price-trade.price*limitOrderPriceRange/DIVISION_CONSTANT) revert("6"); //LimitNotMet

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L498
                if (trade.price < _price) revert("6"); //LimitNotMet

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L500
                if (trade.price > _price) revert("6"); //LimitNotMet

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L502
                if (trade.price < _price) revert("6"); //LimitNotMet

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L505
                if (trade.price > _price) revert("6"); //LimitNotMet

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L540
            if (_trade.orderType != 0) revert("4"); //IsLimit

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L707
        address _referrer = tradingExtension.getRef(_trader); //referrals.getReferral(referrals.getReferred(_trader));

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L783
        address _referrer = tradingExtension.getRef(_trader);//referrals.getReferral(referrals.getReferred(_trader));

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L836
            if (_sl > _price) revert("3"); //BadStopLoss

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L838
            if (_sl < _price && _sl != 0) revert("3"); //BadStopLoss

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L848
        if (position.ownerOf(_id) != _trader) revert("2"); //NotPositionOwner   

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L863
                if (block.number < _delay.delay) revert("0"); //Wait

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/TradingExtension.sol#L1
//SPDX-License-Identifier: Unlicense

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/TradingExtension.sol#L101
        if (_trade.orderType != 0) revert("4"); //IsLimit

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/TradingExtension.sol#L104
            if (_trade.tpPrice == 0) revert("7"); //LimitNotSet

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/TradingExtension.sol#L106
                if (_trade.tpPrice > _price) revert("6"); //LimitNotMet

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/TradingExtension.sol#L108
                if (_trade.tpPrice < _price) revert("6"); //LimitNotMet

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/TradingExtension.sol#L112
            if (_trade.slPrice == 0) revert("7"); //LimitNotSet

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/TradingExtension.sol#L114
                if (_trade.slPrice < _price) revert("6"); //LimitNotMet

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/TradingExtension.sol#L116
                if (_trade.slPrice > _price) revert("6"); //LimitNotMet

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/TradingExtension.sol#L123
        if (tx.gasprice > maxGasPrice) revert("1"); //GasTooHigh

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/utils/TradingLibrary.sol#L1
//SPDX-License-Identifier: MIT

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/PairsContract.sol#L1
//SPDX-License-Identifier: Unlicense

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Referrals.sol#L1
//SPDX-License-Identifier: Unlicense

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/utils/MetaContext.sol#L1
//SPDX-License-Identifier: MIT

### Mitigation
Use a consistent style of `// comment`


## Maximum line length exceeded
### Summary
Long lines should be wrapped to conform with Solidity Style guidelines. 
### Details 
Lines that exceed the 120 character length suggested by the Solidity Style guidelines. Reference: https://docs.soliditylang.org/en/v0.8.16/style-guide.html#maximum-line-length
### Github Permalinks 


https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L178

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L180

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L285

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L330

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L434

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L493

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L494

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L496

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L520

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L543

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L545

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L552

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L579

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L612

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L616

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L624

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L643

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L647

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L668

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L675

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L786

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L853

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L877

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L952

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/TradingExtension.sol#L67

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/TradingExtension.sol#L71

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/TradingExtension.sol#L73

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/utils/TradingLibrary.sol#L36

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/utils/TradingLibrary.sol#L40

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/utils/TradingLibrary.sol#L42

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/utils/TradingLibrary.sol#L44

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/utils/TradingLibrary.sol#L46

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/utils/TradingLibrary.sol#L62

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/utils/TradingLibrary.sol#L64

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/utils/TradingLibrary.sol#L66

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/utils/TradingLibrary.sol#L78

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/utils/TradingLibrary.sol#L115

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L30

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L48

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L55

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L62

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L68

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L69

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L71

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L99

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L102

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L104

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L107

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L109

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L153

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L177

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L187

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L212

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L233

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L264

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L265

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L269

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L270

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/PairsContract.sol#L48

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/PairsContract.sol#L157

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/PairsContract.sol#L177

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L175

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L183

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L189

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L206

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L288

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/StableVault.sol#L53

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L35

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L178

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L240

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/interfaces/ILayerZeroEndpoint.sol#L12

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/interfaces/ILayerZeroEndpoint.sol#L14

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/interfaces/ILayerZeroEndpoint.sol#L15

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/interfaces/ILayerZeroEndpoint.sol#L24

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/interfaces/ILayerZeroEndpoint.sol#L41

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/interfaces/ILayerZeroEndpoint.sol#L78

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/interfaces/ILayerZeroReceiver.sol#L11

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/interfaces/IPosition.sol#L52


### Mitigation
Reduce line length to less than 120 at least to improve maintainability and readability of the code 


## Large multiples of ten should use scientific notation (e.g. 1e6) rather than decimal literals (e.g. 1000000), for readability
### Summary
Multiples of 10 can be declared as constants with scientific notation so it's easier to read them and less prone to miss/exceed a 0 of the expected value.

### Github Permalinks
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/TradingExtension.sol#L26
    uint public maxGasPrice = 1000000000000; // 1000 gwei

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L17
    uint256 public gas = 150000;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L66
        require(tokenId <= 10000, "BadID");

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L144
        uint256 _gas = 500_000 + gas*tokenId.length;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L16
    uint256 private constant MAX = 10000;

### Mitigation
Replace hardcoded numbers with constants that represent the scientific corresponding notation

## Missing inheritance
### Summary
Some contracts are missing expected inheritances:
- GovNFT should inherit from IERC721
- StableToken should inherit from IERC20Mintable
- BondNFT should inherit from IBondNFT

### Github permalinks
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L12-L339
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/StableToken.sol#L7-L66
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L8-L384sol#L13-L131
### Mitigation
Consider adding missing inheritance




## Bad order of code
### Summary
Clearness of the code is important for the readability and maintainability.
As Solidity guidelines says about declaration order:
1.Type declarations
2.State variables
3.Events
4.Modifiers
5.Functions
Also, state variables order affects to gas in the same way as ordering structs for saving storage slots

### Github Permalink
- events at the end
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L376
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Referrals.sol#L84-L88

- modifier at the end
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/TradingExtension.sol#L281
### Mitigation
Follow solidity style guidelines https://docs.soliditylang.org/en/v0.8.15/style-guide.html




## Missing Natspec 
### Summary 
Missing Natspec and regular comments affect readability and maintainability of a codebase. 

### Details 
Contracts has partial or full lack of comments

### Github Permalinks 
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/interfaces/ITrading.sol#L1
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/interfaces/IStableVault.sol#L5
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/interfaces/IReferrals.sol#L5
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/interfaces/IPosition.sol#L5
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/interfaces/IReferrals.sol#L5
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/interfaces/IPosition.sol#L5
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/interfaces/IPairsContract.sol#L5
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/interfaces/IGovNFT.sol#L5
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/interfaces/IBondNFT.sol#L4
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/utils/MetaContext.sol#L6
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/StableToken.sol#L7

- partial lack
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L1
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L44
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Referrals.sol#L42

- `@return`
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/interfaces/ILayerZeroEndpoint.sol#L73-L86
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/interfaces/ILayerZeroEndpoint.sol#L43-L63

 ### Mitigation
 - Add `@param` descriptors
 - Add `@return` descriptors
 - Add Natspec comments. 
 - Add inline comments. 
 - Add comments for what the contract does

## Naming convention of variables
### Summary
Regular variables should be camelcase
### Github Permalinks
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L276
### Mitigation
Rename to `camelCase`

