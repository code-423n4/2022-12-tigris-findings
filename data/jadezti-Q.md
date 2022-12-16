
## Non Critical Issues

|      | Issue                                                         | Instances |
| ---- |:------------------------------------------------------------- |:---------:|
| NC-1 | Do not use magic numbers or values, define a constant or enum |     8     |
| NC-2 | Use `MAX` instead of `10000`                                  | 1          |
 
### [NC-1] Do not use magic numbers or values, define a constant or enum

*Instances (8)*:

Using constants helps to understand and maintain code a lot.

In the below code, the lines of code with `magic numbers` where is used for the 1st time are listed.

`365, 1e18, 300` can be defined using meaningful constants.
```solidity
File: contracts/BondNFT.sol

//@audit - define a constant: DAYS_A_YEAR = 365
113:    uint shares = (bond.amount + _amount) * (bond.period + _period) / 365;

//@audit - define a constant, e.g. BASE_UNIT = 1e18
122:    bondPaid[_id][bond.asset] = accRewardsPerShare[bond.asset][epoch[bond.asset]] * _bond.shares / 1e18;

322:    require(block.timestamp > bond.mintTime + 300, "Recent update");
```
[Link to code](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol)

`1e9` can be defined using meaningful constants.
```solidity
File: contracts/PairsContract.sol

//@audit - define a meaningful private constant for `1e9`
161:    if (_idToOi[_asset][_tigAsset].longOi < 1e9) {

181:    if (_idToOi[_asset][_tigAsset].shortOi < 1e9) {
```
[Link to code](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L161)

`1e18, 1e10` can be defined using meaningful constants.
```solidity
File: contracts/Position.sol

//@audit - define a meaningful private constant for `1e18`
48:    _pendingFunding = (int256(block.timestamp-lastUpdate[_trade.asset][_trade.tigAsset])*fundingDeltaPerSec[_trade.asset][_trade.tigAsset])*1e18/int256(longOi[_trade.asset][_trade.tigAsset]);

//@audit - define a meaningful private constant for `1e10`
52:    _pendingFunding = _pendingFunding*int256(1e10-vaultFundingPercent[_trade.asset][_trade.tigAsset])/1e10;

```
[Link to file](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol)

`1e18, 1,2` can be defined using meaningful constants.
```soidity
FIle:  contracts/Trading.sol

178:   uint256 _marginAfterFees = _tradeInfo.margin - _handleOpenFees(_tradeInfo.asset, _tradeInfo.margin*_tradeInfo.leverage/1e18, _trader, _tigAsset, false);

181:    uint256 _isLong = _tradeInfo.direction ? 1 : 2;

```
[Link to file](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol)

### [NC-2] Use `MAX` instead of `10000`

In file [GovNFT.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol), the max number of token has been defined as a constant `MAX=10000`. On [Line 166](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L66) it still uses `10000`.

```solidity
File: contracts/GovNFT.sol

//@audit - should use `MAX` instead of `10000`
66:    require(tokenId <= 10000, "BadID");
```
[Link to code](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L66)
