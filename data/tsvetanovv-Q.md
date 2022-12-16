## [QA-01] Use latest Solidity version

Solidity pragma versioning should be upgraded to latest available version. Currently the solidity version in contracts is ^0.8.0 which was found to possess some bugs.

## [QA-02] Use stable pragma statement

Using a floating pragma `^` statement is discouraged as code can compile to different bytecodes with different compiler versions. Use a stable pragma statement to get a deterministic bytecode.

## [QA-03] LINES ARE TOO LONG

Usually lines in source code are limited to 120 characters. Today’s screens are much larger so it’s reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length.

[Contract Link](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol)

```contracts/Position.sol

```

```
if (_trade.direction && longOi[_trade.asset][_trade.tigAsset] > 0) {
            _pendingFunding = (int256(block.timestamp-lastUpdate[_trade.asset][_trade.tigAsset])*fundingDeltaPerSec[_trade.asset][_trade.tigAsset])*1e18/int256(longOi[_trade.asset][_trade.tigAsset]);
            if (longOi[_trade.asset][_trade.tigAsset] > shortOi[_trade.asset][_trade.tigAsset]) {
                _pendingFunding = -_pendingFunding;
            } else {
                _pendingFunding = _pendingFunding*int256(1e10-vaultFundingPercent[_trade.asset][_trade.tigAsset])/1e10;
            }
        } else if (shortOi[_trade.asset][_trade.tigAsset] > 0) {
            _pendingFunding = (int256(block.timestamp-lastUpdate[_trade.asset][_trade.tigAsset])*fundingDeltaPerSec[_trade.asset][_trade.tigAsset])*1e18/int256(shortOi[_trade.asset][_trade.tigAsset]);
            if (shortOi[_trade.asset][_trade.tigAsset] > longOi[_trade.asset][_trade.tigAsset]) {
                _pendingFunding = -_pendingFunding;
            } else {
                _pendingFunding = _pendingFunding*int256(1e10-vaultFundingPercent[_trade.asset][_trade.tigAsset])/1e10;
            }
        }
        _trade.accInterest += (int256(_trade.margin*_trade.leverage/1e18)*(accInterestPerOi[_trade.asset][_trade.tigAsset][_trade.direction]+_pendingFunding)/1e18)-initId[_id];
```

```
unction updateFunding(uint256 _asset, address _tigAsset, uint256 _longOi, uint256 _shortOi, uint256 _baseFundingRate, uint _vaultFundingPercent) external onlyMinter {
        if(longOi[_asset][_tigAsset] < shortOi[_asset][_tigAsset]) {
            if (longOi[_asset][_tigAsset] > 0) {
                accInterestPerOi[_asset][_tigAsset][true] += ((int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(longOi[_asset][_tigAsset]))*int256(1e10-vaultFundingPercent[_asset][_tigAsset])/1e10;
            }
            accInterestPerOi[_asset][_tigAsset][false] -= (int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(shortOi[_asset][_tigAsset]);

        } else if(longOi[_asset][_tigAsset] > shortOi[_asset][_tigAsset]) {
            accInterestPerOi[_asset][_tigAsset][true] -= (int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(longOi[_asset][_tigAsset]);
            if (shortOi[_asset][_tigAsset] > 0) {
                accInterestPerOi[_asset][_tigAsset][false] += ((int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(shortOi[_asset][_tigAsset]))*int256(1e10-vaultFundingPercent[_asset][_tigAsset])/1e10;
            }
        }
```

Reference: [Link](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-length)
