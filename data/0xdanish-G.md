## Trading.sol
In line 622 there is  `if (_payout > 0)`
it should be `if (_payout != 0)`

In line 509 there is  `trade.price += trade.price * _spread / DIVISION_CONSTANT;`
it should be `trade.price = trade.price + (trade.price * _spread) / DIVISION_CONSTANT;`

In line 511 there is  `trade.price -= trade.price * _spread / DIVISION_CONSTANT;`
it should be `trade.price = trade.price - (trade.price * _spread) / DIVISION_CONSTANT;`

In line 293 there is  `_addMargin -= _fee;`
it should be `_addMargin = _addMargin - _fee;`


## TradingExtension.sol
In line 185 there is  `_price += _price * _spread / DIVISION_CONSTANT`
it should be `_price = _price + (_price * _spread) / DIVISION_CONSTANT`

In line 187 there is  `_price -= _price * _spread / DIVISION_CONSTANT`
it should be `_price = _price - (_price * _spread) / DIVISION_CONSTANT`


Line 13,14,15 -> Address is 20 bytes, bool is 1 byte and uint256 32 bytes
`address public trading;
    uint256 public validSignatureTimer;
    bool public chainlinkEnabled;`
Should be -> Now address and bool will store in 1 storage, that will help in optimize gas during deployment
`address public trading;
 bool public chainlinkEnabled;
 uint256 public validSignatureTimer;`


## Position.sol
In line 44 `if (_trade.orderType > 0) return _trade;`
should be ` if (_trade.orderType != 0) return _trade;`

In line 47 `if (_trade.direction && longOi[_trade.asset][_trade.tigAsset] > 0) {`
should be `if (_trade.direction && longOi[_trade.asset][_trade.tigAsset] != 0) {`

In line 54 `else if (shortOi[_trade.asset][_trade.tigAsset] > 0) {`
should be `else if (shortOi[_trade.asset][_trade.tigAsset] != 0) {`

In line 101 `if (longOi[_asset][_tigAsset] > 0) {
{ accInterestPerOi[_asset][_tigAsset][true] += ((int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(longOi[_asset][_tigAsset]))*int256(1e10-vaultFundingPercent[_asset][_tigAsset])/1e10;`
should be `if (longOi[_asset][_tigAsset] != 0)  { accInterestPerOi[_asset][_tigAsset][true] =accInterestPerOi[_asset][_tigAsset][true] + ((int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(longOi[_asset][_tigAsset]))*int256(1e10-vaultFundingPercent[_asset][_tigAsset])/1e10;`

In line 108 `if (shortOi[_asset][_tigAsset] > 0) {
                accInterestPerOi[_asset][_tigAsset][false] += ((int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(shortOi[_asset][_tigAsset]))*int256(1e10-vaultFundingPercent[_asset][_tigAsset])/1e10;
            }`
should be `if (shortOi[_asset][_tigAsset] != 0) {
                accInterestPerOi[_asset][_tigAsset][false] = accInterestPerOi[_asset][_tigAsset][false] + ((int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(shortOi[_asset][_tigAsset]))*int256(1e10-vaultFundingPercent[_asset][_tigAsset])/1e10;
            }`

In line 62 `_trade.accInterest += (int256(_trade.margin*_trade.leverage/1e18)*(accInterestPerOi[_trade.asset][_trade.tigAsset][_trade.direction]+_pendingFunding)/1e18)-initId[_id];`
should be `_trade.accInterest = _trade.accInterest + (int256(_trade.margin*_trade.leverage/1e18)*(accInterestPerOi[_trade.asset][_trade.tigAsset][_trade.direction]+_pendingFunding)/1e18)-initId[_id];`

In line 104 ` accInterestPerOi[_asset][_tigAsset][false] -= (int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(shortOi[_asset][_tigAsset]);`
should be ` accInterestPerOi[_asset][_tigAsset][false] = accInterestPerOi[_asset][_tigAsset][false] - (int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(shortOi[_asset][_tigAsset]);`

In line 107 `accInterestPerOi[_asset][_tigAsset][true] -= (int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(longOi[_asset][_tigAsset]);`
should be `accInterestPerOi[_asset][_tigAsset][true] =accInterestPerOi[_asset][_tigAsset][true] - (int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(longOi[_asset][_tigAsset]);`

In line 108 `if (shortOi[_asset][_tigAsset] > 0) {
                accInterestPerOi[_asset][_tigAsset][false] += ((int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(shortOi[_asset][_tigAsset]))*int256(1e10-vaultFundingPercent[_asset][_tigAsset])/1e10;
            }`
should be `if (shortOi[_asset][_tigAsset] != 0) {
                accInterestPerOi[_asset][_tigAsset][false] = accInterestPerOi[_asset][_tigAsset][false] + ((int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(shortOi[_asset][_tigAsset]))*int256(1e10-vaultFundingPercent[_asset][_tigAsset])/1e10;`

In line 231 `_trades[_id].accInterest -= _trades[_id].accInterest*int256(_percent)/int256(DIVISION_CONSTANT);`
should be `_trades[_id].accInterest =_trades[_id].accInterest - _trades[_id].accInterest*int256(_percent)/int256(DIVISION_CONSTANT);`
 
In line 232 `_trades[_id].margin -= _trades[_id].margin*_percent/DIVISION_CONSTANT;`
should be `_trades[_id].margin = _trades[_id].margin - _trades[_id].margin*_percent/DIVISION_CONSTANT;`

## PairsContract.sol
In line 35,75,94,106,117,141 ` require(_name.length > 0, "!Asset");`
should be `require(_name.length != 0, "!Asset");`

In line 51 `require(bytes(_name).length > 0, "No name");`
should be `require(bytes(_name).length != 0, "No name");`

In line 52 `require(_maxLeverage >= _minLeverage && _minLeverage > 0, "Wrong leverage values");`
should be `require(_maxLeverage >= _minLeverage && _minLeverage != 0, "Wrong leverage values");`

In line 77 ` if (_maxLeverage != 0) {`
should be ` if (_maxLeverage != 0) {`

In line 80 ` if (_minLeverage > 0) {`
` if (_minLeverage != 0) {`

In line 156 `_idToOi[_asset][_tigAsset].longOi += _amount;`
should be `_idToOi[_asset][_tigAsset].longOi = _idToOi[_asset][_tigAsset].longOi + _amount;`

In line 176 `_idToOi[_asset][_tigAsset].shortOi += _amount;`
should be `_idToOi[_asset][_tigAsset].shortOi = _idToOi[_asset][_tigAsset].shortOi +_amount;`

In line 160 `_idToOi[_asset][_tigAsset].longOi -= _amount;`
should be `_idToOi[_asset][_tigAsset].longOi = _idToOi[_asset][_tigAsset].longOi - _amount;`

In line 180 `_idToOi[_asset][_tigAsset].shortOi -= _amount;`
should be `_idToOi[_asset][_tigAsset].shortOi = _idToOi[_asset][_tigAsset].shortOi -_amount;`

## GovNFT.sol 
In line 52 `counter += 1;`
should be `counter += counter + 1;`

In line 54, 68, 99  `userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];`
should be `userPaid[to][assets[i]] =userPaid[to][assets[i]] + accRewardsPerNFT[assets[i]];`

In line 79 `userDebt[owner][assets[i]] += accRewardsPerNFT[assets[i]];`
should be `userDebt[owner][assets[i]] = userDebt[owner][assets[i]] + accRewardsPerNFT[assets[i]];`

In line 96 `userDebt[from][assets[i]] += accRewardsPerNFT[assets[i]];`
should be `userDebt[from][assets[i]] = userDebt[from][assets[i]] + accRewardsPerNFT[assets[i]];`

In line 278 `userPaid[_msgsender][_tigAsset] += amount;`
should be `userPaid[_msgsender][_tigAsset] = userPaid[_msgsender][_tigAsset] + amount;`

In line 290 `accRewardsPerNFT[_tigAsset] += _amount/totalSupply();`
should be `accRewardsPerNFT[_tigAsset] =accRewardsPerNFT[_tigAsset] + (_amount/totalSupply());`

In line 80 `userDebt[owner][assets[i]] -= userPaid[owner][assets[i]]/balanceOf(owner);`
should be `userDebt[owner][assets[i]] = userDebt[owner][assets[i]] - (userPaid[owner][assets[i]]/balanceOf(owner));`

In line 81 `userPaid[owner][assets[i]] -= userPaid[owner][assets[i]]/balanceOf(owner);` 
should be `userPaid[owner][assets[i]] = userPaid[owner][assets[i]] - (userPaid[owner][assets[i]]/balanceOf(owner));` 

In line 97 `userDebt[from][assets[i]] -= userPaid[from][assets[i]]/balanceOf(from);`
should be `userDebt[from][assets[i]] = userDebt[from][assets[i]] - (userPaid[from][assets[i]]/balanceOf(from));`

In line 98 `userPaid[from][assets[i]] -= userPaid[from][assets[i]]/balanceOf(from)`;
should be `userPaid[from][assets[i]] =userPaid[from][assets[i]] - (userPaid[from][assets[i]]/balanceOf(from))`;


## Lock.sol
In line 73 `totalLocked[_asset] += _amount;`
should be `totalLocked[_asset] +=totalLocked[_asset] + _amount;`

In line 103 `totalLocked[asset] -= lockAmount;`
should be `totalLocked[asset] = totalLocked[asset] - lockAmount;`


## BondNFT.sol
In line 179 `if (totalShares[bond.asset] > 0) {
                    accRewardsPerShare[bond.asset][epoch[bond.asset]] += _pendingDelta*1e18/totalShares[bond.asset];
                }`
should be `if (totalShares[bond.asset] != 0) {
                    accRewardsPerShare[bond.asset][epoch[bond.asset]] = accRewardsPerShare[bond.asset][epoch[bond.asset]] + _pendingDelta*1e18/totalShares[bond.asset];
                }`

In line 68 `totalShares[_asset] += shares;`
should be `totalShares[_asset] = totalShares[_asset] + shares;`

In line 115 `totalShares[bond.asset] += shares-bond.shares;`
should be `totalShares[bond.asset] =totalShares[bond.asset] + shares-bond.shares;`

In line 117 `_bond.amount += _amount;`
should be `_bond.amount =_bond.amount +  _amount;`

In line 119 `_bond.period += _period;`
should be `_bond.period = _bond.period + _period;`

In line 152 `amount += _claimAmount;`
should be  `amount = amount + _claimAmount;`

In line 183` bondPaid[_id][bond.asset] += amount;`
should be `bondPaid[_id][bond.asset] =bondPaid[_id][bond.asset] +  amount;`

In line 221 `epoch[_tigAsset] += 1;`
should be `epoch[_tigAsset] =epoch[_tigAsset] + 1;`

In line 225 `accRewardsPerShare[_tigAsset][aEpoch] += _amount * 1e18 / totalShares[_tigAsset];`
should be `accRewardsPerShare[_tigAsset][aEpoch] = accRewardsPerShare[_tigAsset][aEpoch] + (_amount * 1e18 / totalShares[_tigAsset]);`

In line 333 ` userDebt[from][bond.asset] += bond.pending;`
should be  ` userDebt[from][bond.asset] =userDebt[from][bond.asset] +  bond.pending;`

In line 334 `bondPaid[_id][bond.asset] += bond.pending;`
should be `bondPaid[_id][bond.asset] =bondPaid[_id][bond.asset] +  bond.pending;`

In line 150 ` totalShares[bond.asset] -= bond.shares;`
should be ` totalShares[bond.asset] = totalShares[bond.asset] - bond.shares;`

## contracts/utils/TradingLibrary.sol	
In line 111 ` require(_priceData.price > 0, "NoPrice");`
should be `require(_priceData.price != 0, "NoPrice");`