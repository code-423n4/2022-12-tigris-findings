

### [L01] New min/max values should be checked against the current stored value

#### Impact
If `_idToAsset[_asset].minLeverage` is above the new max or below the new min, the next update will likely have a similar value and immediately cause problems. The code should require that the current

#### Findings:
```
2022-12-tigris-main/contracts/PairsContract.sol::59 => _idToAsset[_asset].minLeverage = _minLeverage;
2022-12-tigris-main/contracts/PairsContract.sol::60 => _idToAsset[_asset].maxLeverage = _maxLeverage;
```





### [L02] Missing checks for `address(0x0)` when assigning values to `address` state variables


#### Findings:
```
2022-12-tigris-main/contracts/BondNFT.sol::45 => baseURI = _setBaseURI;
2022-12-tigris-main/contracts/BondNFT.sol::363 => baseURI = _newBaseURI;
2022-12-tigris-main/contracts/BondNFT.sol::369 => manager = _manager;
2022-12-tigris-main/contracts/GovNFT.sol::39 => baseURI = _setBaseURI;
2022-12-tigris-main/contracts/GovNFT.sol::47 => baseURI = _newBaseURI;
2022-12-tigris-main/contracts/GovNFT.sol::237 => gas = _gas;
2022-12-tigris-main/contracts/GovNFT.sol::242 => endpoint = _endpoint;
2022-12-tigris-main/contracts/GovNFT.sol::312 => maxBridge = _max;
2022-12-tigris-main/contracts/PairsContract.sol::126 => maxBaseFundingRate = _maxBaseFundingRate;
2022-12-tigris-main/contracts/PairsContract.sol::130 => protocol = _protocol;
2022-12-tigris-main/contracts/Position.sol::77 => baseURI = _setBaseURI;
2022-12-tigris-main/contracts/Position.sol::86 => baseURI = _newBaseURI;
2022-12-tigris-main/contracts/Referrals.sol::54 => protocol = _protocol;
2022-12-tigris-main/contracts/StableVault.sol::36 => stable = _stable;
2022-12-tigris-main/contracts/Trading.sol::904 => blockDelay = _blockDelay;
2022-12-tigris-main/contracts/Trading.sol::932 => maxWinPercent = _maxWinPercent;
2022-12-tigris-main/contracts/TradingExtension.sol::35 => trading = _trading;
2022-12-tigris-main/contracts/TradingExtension.sol::118 => _limitPrice = _trade.slPrice;
2022-12-tigris-main/contracts/TradingExtension.sol::145 => maxGasPrice = _maxGasPrice;
2022-12-tigris-main/contracts/TradingExtension.sol::275 => paused = _paused;
```




###  [L03] `address.call{value:x}()` should be used instead of `payable.transfer()`

#### Impact
The use of `payable.transfer()` is heavily frowned upon because it can lead to the locking of funds. The `transfer()` call requires that the recipient has a `payable` callback, only provides 2300 gas for its operation. This means the following cases can cause the transfer to fail:

- The contract does not have a `payable` callback
- The contract’s `payable` callback spends more than 2300 gas (which is only enough to emit something)
- The contract is called through a proxy which itself uses up the 2300 gas
#### Findings:
```
2022-12-tigris-main/contracts/Trading.sol::588 => payable(_proxy).transfer(msg.value);
```



### [L04] approve should be replaced with safeApprove or safeIncreaseAllowance() / safeDecreaseAllowance()

#### Impact
approve is subject to a known front-running attack. Consider using safeApprove instead:
#### Findings:
```
2022-12-tigris-main/contracts/Lock.sol::117 => IERC20(assets[i]).approve(address(bondNFT), type(uint256).max);
2022-12-tigris-main/contracts/Trading.sol::652 => IERC20(_marginAsset).approve(_stableVault, type(uint).max);
2022-12-tigris-main/contracts/Trading.sol::807 => IStable(_tigAsset).approve(address(gov), type(uint).max);
```







### [L05] Return values of `transfer()`/`transferFrom()` not checked

#### Impact
Not all IERC20 implementations revert() when there’s a failure in transfer()/transferFrom(). The function signature has a boolean
 return value and they indicate errors that way instead. By not checking
 the return value, operations that should have been marked as failed may 
potentially go through without actually making a payment
#### Findings:
```
2022-12-tigris-main/contracts/BondNFT.sol::185 => IERC20(tigAsset).transfer(manager, amount);
2022-12-tigris-main/contracts/BondNFT.sol::202 => IERC20(_tigAsset).transfer(manager, amount);
2022-12-tigris-main/contracts/BondNFT.sol::216 => IERC20(_tigAsset).transferFrom(_msgSender(), address(this), _amount);
2022-12-tigris-main/contracts/GovNFT.sol::279 => IERC20(_tigAsset).transfer(_msgsender, amount);
2022-12-tigris-main/contracts/Lock.sol::39 => IERC20(_tigAsset).transfer(msg.sender, _amount);
2022-12-tigris-main/contracts/Lock.sol::52 => IERC20(_tigAsset).transfer(msg.sender, amount);
2022-12-tigris-main/contracts/Lock.sol::104 => IERC20(asset).transfer(_owner, amount);
2022-12-tigris-main/contracts/StableVault.sol::68 => IERC20(_token).transfer(
2022-12-tigris-main/contracts/Trading.sol::671 => IERC20(_outputToken).transfer(_trade.trader, _toMint);
2022-12-tigris-main/contracts/Trading.sol::676 => IERC20(_outputToken).transfer(_trade.trader, IERC20(_outputToken).balanceOf(address(this)) - _balBefore);
```




### [L06] Unspecific Compiler Version Pragma

#### Impact
Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.
#### Findings:
```
2022-12-tigris-main/contracts/BondNFT.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/GovNFT.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/Lock.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/PairsContract.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/Position.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/Referrals.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/StableToken.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/StableVault.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/Trading.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/TradingExtension.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/interfaces/IBondNFT.sol::2 => pragma solidity ^0.8.9;
2022-12-tigris-main/contracts/interfaces/IGovNFT.sol::3 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/interfaces/ILayerZeroEndpoint.sol::3 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/interfaces/ILayerZeroReceiver.sol::3 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/interfaces/ILayerZeroUserApplicationConfig.sol::3 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/interfaces/IPairsContract.sol::3 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/interfaces/IPosition.sol::3 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/interfaces/IReferrals.sol::3 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/interfaces/IStableVault.sol::3 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/interfaces/ITrading.sol::5 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/utils/MetaContext.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/utils/TradingLibrary.sol::2 => pragma solidity ^0.8.0;
```



### [L07] Return values of transfer()/transferFrom() not checked

#### Impact
Not all IERC20 implementations revert() when there’s a failure in transfer()/transferFrom(). The function signature has a boolean
 return value and they indicate errors that way instead. By not checking
 the return value, operations that should have been marked as failed may 
potentially go through without actually making a payment
#### Findings:
```
2022-12-tigris-main/contracts/BondNFT.sol::185 => IERC20(tigAsset).transfer(manager, amount);
2022-12-tigris-main/contracts/BondNFT.sol::202 => IERC20(_tigAsset).transfer(manager, amount);
2022-12-tigris-main/contracts/BondNFT.sol::216 => IERC20(_tigAsset).transferFrom(_msgSender(), address(this), _amount);
2022-12-tigris-main/contracts/GovNFT.sol::279 => IERC20(_tigAsset).transfer(_msgsender, amount);
2022-12-tigris-main/contracts/GovNFT.sol::289 => try IERC20(_tigAsset).transferFrom(_msgSender(), address(this), _amount) {
2022-12-tigris-main/contracts/Lock.sol::39 => IERC20(_tigAsset).transfer(msg.sender, _amount);
2022-12-tigris-main/contracts/Lock.sol::52 => IERC20(_tigAsset).transfer(msg.sender, amount);
2022-12-tigris-main/contracts/Lock.sol::72 => IERC20(_asset).transferFrom(msg.sender, address(this), _amount);
2022-12-tigris-main/contracts/Lock.sol::90 => IERC20(_asset).transferFrom(msg.sender, address(this), _amount);
2022-12-tigris-main/contracts/Lock.sol::104 => IERC20(asset).transfer(_owner, amount);
2022-12-tigris-main/contracts/StableVault.sol::46 => IERC20(_token).transferFrom(_msgSender(), address(this), _amount);
2022-12-tigris-main/contracts/StableVault.sol::68 => IERC20(_token).transfer(
2022-12-tigris-main/contracts/Trading.sol::651 => IERC20(_marginAsset).transferFrom(_trader, address(this), _margin/_marginDecMultiplier);
2022-12-tigris-main/contracts/Trading.sol::671 => IERC20(_outputToken).transfer(_trade.trader, _toMint);
2022-12-tigris-main/contracts/Trading.sol::676 => IERC20(_outputToken).transfer(_trade.trader, IERC20(_outputToken).balanceOf(address(this)) - _balBefore);
```








##### `Non-Critical-Issues`




### [N01] Adding a return statement when the function defines a named return variable, is redundant


#### Findings:
```
2022-12-tigris-main/contracts/BondNFT.sol::275 => return assets;
2022-12-tigris-main/contracts/BondNFT.sol::279 => return baseURI;
2022-12-tigris-main/contracts/BondNFT.sol::346 => return _ids;
2022-12-tigris-main/contracts/GovNFT.sol::43 => return baseURI;
2022-12-tigris-main/contracts/GovNFT.sol::328 => return _ids;
2022-12-tigris-main/contracts/Lock.sol::40 => return _tigAsset;
2022-12-tigris-main/contracts/Position.sol::64 => return _trade;
2022-12-tigris-main/contracts/Position.sol::82 => return baseURI;
2022-12-tigris-main/contracts/Position.sol::299 => return _ids;
2022-12-tigris-main/contracts/Position.sol::307 => return _ids;
2022-12-tigris-main/contracts/Trading.sol::809 => return payout_;
```



### [N02] `require()`/`revert()` statements should have descriptive reason strings



#### Findings:
```
2022-12-tigris-main/contracts/BondNFT.sol::109 => require(bond.pending == 0);
2022-12-tigris-main/contracts/Referrals.sol::66 => require(!isInit);
2022-12-tigris-main/contracts/Trading.sol::364 => if (_trade.orderType == 0) revert();
2022-12-tigris-main/contracts/Trading.sol::392 => if (_trade.orderType != 0) revert(); //IsLimit
2022-12-tigris-main/contracts/Trading.sol::428 => if (_trade.orderType != 0) revert(); //IsLimit
2022-12-tigris-main/contracts/Trading.sol::954 => require(_daoFees >= _botFees+_referralFees*2);
2022-12-tigris-main/contracts/Trading.sol::966 => require(_percent <= DIVISION_CONSTANT);
```



### [N03] constants should be defined rather than using magic numbers


#### Findings:
```
2022-12-tigris-main/contracts/BondNFT.sol::10 => uint constant private DAY = 24 * 60 * 60;
2022-12-tigris-main/contracts/BondNFT.sol::65 => uint shares = _amount * _period / 365;
2022-12-tigris-main/contracts/BondNFT.sol::113 => uint shares = (bond.amount + _amount) * (bond.period + _period) / 365;
2022-12-tigris-main/contracts/BondNFT.sol::122 => bondPaid[_id][bond.asset] = accRewardsPerShare[bond.asset][epoch[bond.asset]] * _bond.shares / 1e18;
2022-12-tigris-main/contracts/BondNFT.sol::178 => uint _pendingDelta = (bond.shares * accRewardsPerShare[bond.asset][epoch[bond.asset]] / 1e18 - bondPaid[_id][bond.asset]) - (bond.shares * accRewardsPerShare[bond.asset][bond.expireEpoch-1] / 1e18 - bondPaid[_id][bond.asset]);
2022-12-tigris-main/contracts/BondNFT.sol::180 => accRewardsPerShare[bond.asset][epoch[bond.asset]] += _pendingDelta*1e18/totalShares[bond.asset];
2022-12-tigris-main/contracts/BondNFT.sol::225 => accRewardsPerShare[_tigAsset][aEpoch] += _amount * 1e18 / totalShares[_tigAsset];
2022-12-tigris-main/contracts/BondNFT.sol::241 => bond.pending = bond.shares * _accRewardsPerShare / 1e18 - bondPaid[_id][bond.asset];
2022-12-tigris-main/contracts/BondNFT.sol::311 => bondPaid[bond.id][bond.asset] = accRewardsPerShare[bond.asset][epoch[bond.asset]] * bond.shares / 1e18;
2022-12-tigris-main/contracts/GovNFT.sol::16 => uint256 private constant MAX = 10000;
2022-12-tigris-main/contracts/GovNFT.sol::17 => uint256 public gas = 150000;
2022-12-tigris-main/contracts/GovNFT.sol::66 => require(tokenId <= 10000, "BadID");
2022-12-tigris-main/contracts/GovNFT.sol::144 => uint256 _gas = 500_000 + gas*tokenId.length;
2022-12-tigris-main/contracts/GovNFT.sol::175 => (bool success, bytes memory reason) = address(this).excessivelySafeCall(gasleft()*4/5, 150, abi.encodeWithSelector(this.nonblockingLzReceive.selector, _srcChainId, _srcAddress, _nonce, _payload));
2022-12-tigris-main/contracts/Position.sol::48 => _pendingFunding = (int256(block.timestamp-lastUpdate[_trade.asset][_trade.tigAsset])*fundingDeltaPerSec[_trade.asset][_trade.tigAsset])*1e18/int256(longOi[_trade.asset][_trade.tigAsset]);
2022-12-tigris-main/contracts/Position.sol::52 => _pendingFunding = _pendingFunding*int256(1e10-vaultFundingPercent[_trade.asset][_trade.tigAsset])/1e10;
2022-12-tigris-main/contracts/Position.sol::55 => _pendingFunding = (int256(block.timestamp-lastUpdate[_trade.asset][_trade.tigAsset])*fundingDeltaPerSec[_trade.asset][_trade.tigAsset])*1e18/int256(shortOi[_trade.asset][_trade.tigAsset]);
2022-12-tigris-main/contracts/Position.sol::59 => _pendingFunding = _pendingFunding*int256(1e10-vaultFundingPercent[_trade.asset][_trade.tigAsset])/1e10;
2022-12-tigris-main/contracts/Position.sol::62 => _trade.accInterest += (int256(_trade.margin*_trade.leverage/1e18)*(accInterestPerOi[_trade.asset][_trade.tigAsset][_trade.direction]+_pendingFunding)/1e18)-initId[_id];
2022-12-tigris-main/contracts/Position.sol::102 => accInterestPerOi[_asset][_tigAsset][true] += ((int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(longOi[_asset][_tigAsset]))*int256(1e10-vaultFundingPercent[_asset][_tigAsset])/1e10;
2022-12-tigris-main/contracts/Position.sol::104 => accInterestPerOi[_asset][_tigAsset][false] -= (int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(shortOi[_asset][_tigAsset]);
2022-12-tigris-main/contracts/Position.sol::107 => accInterestPerOi[_asset][_tigAsset][true] -= (int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(longOi[_asset][_tigAsset]);
2022-12-tigris-main/contracts/Position.sol::109 => accInterestPerOi[_asset][_tigAsset][false] += ((int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(shortOi[_asset][_tigAsset]))*int256(1e10-vaultFundingPercent[_asset][_tigAsset])/1e10;
2022-12-tigris-main/contracts/Position.sol::120 => fundingDeltaPerSec[_asset][_tigAsset] = (_oiDelta*int256(_baseFundingRate)/int256(DIVISION_CONSTANT))/31536000;
2022-12-tigris-main/contracts/Position.sol::153 => initId[newTokenID] = accInterestPerOi[_mintTrade.asset][_mintTrade.tigAsset][_mintTrade.direction]*int256(_mintTrade.margin*_mintTrade.leverage/1e18)/1e18;
2022-12-tigris-main/contracts/Position.sol::187 => initId[_id] = accInterestPerOi[_trade.asset][_trade.tigAsset][_trade.direction]*int256(_trade.margin*_trade.leverage/1e18)/1e18;
2022-12-tigris-main/contracts/Position.sol::212 => initId[_id] = accInterestPerOi[_trades[_id].asset][_trades[_id].tigAsset][_trades[_id].direction]*int256(_newMargin*_trades[_id].leverage/1e18)/1e18;
2022-12-tigris-main/contracts/StableVault.sol::49 => _amount*(10**(18-IERC20Mintable(_token).decimals()))
2022-12-tigris-main/contracts/Trading.sol::650 => uint _marginDecMultiplier = 10**(18-ExtendedIERC20(_marginAsset).decimals());
2022-12-tigris-main/contracts/TradingExtension.sol::26 => uint public maxGasPrice = 1000000000000; // 1000 gwei
2022-12-tigris-main/contracts/utils/TradingLibrary.sol::38 => uint _initPositionSize = _margin * _leverage / 1e18;
2022-12-tigris-main/contracts/utils/TradingLibrary.sol::40 => _payout = int256(_margin) + int256(_initPositionSize * (1e18 * _currentPrice / _price - 1e18)/1e18) + accInterest;
2022-12-tigris-main/contracts/utils/TradingLibrary.sol::42 => _payout = int256(_margin) - int256(_initPositionSize * (1e18 - 1e18 * _currentPrice / _price)/1e18) + accInterest;
2022-12-tigris-main/contracts/utils/TradingLibrary.sol::44 => _payout = int256(_margin) + int256(_initPositionSize * (1e18 - 1e18 * _currentPrice / _price)/1e18) + accInterest;
2022-12-tigris-main/contracts/utils/TradingLibrary.sol::46 => _payout = int256(_margin) - int256(_initPositionSize * (1e18 * _currentPrice / _price - 1e18)/1e18) + accInterest;
2022-12-tigris-main/contracts/utils/TradingLibrary.sol::64 => _liqPrice = _tradePrice - ((_tradePrice*1e18/_leverage) * uint(int(_margin)+_accInterest) / _margin) * _liqPercent / 1e10;
2022-12-tigris-main/contracts/utils/TradingLibrary.sol::66 => _liqPrice = _tradePrice + ((_tradePrice*1e18/_leverage) * uint(int(_margin)+_accInterest) / _margin) * _liqPercent / 1e10;
2022-12-tigris-main/contracts/utils/TradingLibrary.sol::115 => uint256 assetChainlinkPrice = uint256(assetChainlinkPriceInt) * 10**(18 - IPrice(_chainlinkFeed).decimals());
2022-12-tigris-main/contracts/utils/TradingLibrary.sol::117 => _priceData.price < assetChainlinkPrice+assetChainlinkPrice*2/100 &&
2022-12-tigris-main/contracts/utils/TradingLibrary.sol::118 => _priceData.price > assetChainlinkPrice-assetChainlinkPrice*2/100, "!chainlinkPrice"
```




### [N04] Use a more recent version of solidity

#### Impact
Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions
#### Findings:
```
2022-12-tigris-main/contracts/GovNFT.sol::13 => using ExcessivelySafeCall for address;
2022-12-tigris-main/contracts/Position.sol::15 => using Counters for Counters.Counter;
2022-12-tigris-main/contracts/utils/TradingLibrary.sol::23 => using ECDSA for bytes32;
```



### [N05] Event is missing `indexed` fields

#### Impact
Each event should use three indexed fields if there are three or more fields
#### Findings:
```
2022-12-tigris-main/contracts/BondNFT.sol::377 => event Distribution(address _tigAsset, uint256 _amount);
2022-12-tigris-main/contracts/BondNFT.sol::378 => event Lock(address _tigAsset, uint256 _amount, uint256 _period, address _owner, uint256 _id);
2022-12-tigris-main/contracts/BondNFT.sol::379 => event ExtendLock(uint256 _period, uint256 _amount, address _owner, uint256 _id);
2022-12-tigris-main/contracts/BondNFT.sol::380 => event Release(address _tigAsset, uint256 _amount, address _owner, uint256 _id);
2022-12-tigris-main/contracts/BondNFT.sol::381 => event ClaimFees(address _tigAsset, uint256 _amount, address _claimer, uint256 _id);
2022-12-tigris-main/contracts/BondNFT.sol::382 => event ClaimDebt(address _tigAsset, uint256 _amount, address _owner);
2022-12-tigris-main/contracts/GovNFT.sol::24 => event MessageFailed(uint16 _srcChainId, bytes _srcAddress, uint64 _nonce, bytes _payload, bytes _reason);
2022-12-tigris-main/contracts/GovNFT.sol::25 => event RetryMessageSuccess(uint16 _srcChainId, bytes _srcAddress, uint64 _nonce, bytes32 _payloadHash);
2022-12-tigris-main/contracts/Referrals.sol::85 => event ReferralCreated(address _referrer, bytes32 _hash);
2022-12-tigris-main/contracts/Referrals.sol::86 => event Referred(address _referredTrader, bytes32 _hash);
```



### [N06] Unused file


#### Findings:
```
2022-12-tigris-main/contracts/BondNFT.sol::1 => // SPDX-License-Identifier: MIT
2022-12-tigris-main/contracts/GovNFT.sol::1 => // SPDX-License-Identifier: MIT
2022-12-tigris-main/contracts/Position.sol::1 => // SPDX-License-Identifier: MIT
2022-12-tigris-main/contracts/StableToken.sol::1 => // SPDX-License-Identifier: MIT
2022-12-tigris-main/contracts/StableVault.sol::1 => // SPDX-License-Identifier: MIT
2022-12-tigris-main/contracts/interfaces/IGovNFT.sol::1 => // SPDX-License-Identifier: MIT
2022-12-tigris-main/contracts/interfaces/IPairsContract.sol::1 => // SPDX-License-Identifier: MIT
2022-12-tigris-main/contracts/interfaces/IPosition.sol::1 => // SPDX-License-Identifier: MIT
2022-12-tigris-main/contracts/interfaces/IReferrals.sol::1 => // SPDX-License-Identifier: MIT
2022-12-tigris-main/contracts/interfaces/IStableVault.sol::1 => // SPDX-License-Identifier: MIT
2022-12-tigris-main/contracts/interfaces/ITrading.sol::1 => // SPDX-License-Identifier: MIT
```


### [N07] `public` functions not called by the contract should be declared `external` instead

#### Impact
Contracts are allowed to override their parents’ functions and change the visibility from public to external .
#### Findings:
```
2022-12-tigris-main/contracts/BondNFT.sol::171 => ) public onlyManager() returns(uint amount, address tigAsset) {
2022-12-tigris-main/contracts/BondNFT.sol::199 => ) public onlyManager() returns(uint amount) {
2022-12-tigris-main/contracts/BondNFT.sol::235 => function idToBond(uint256 _id) public view returns (Bond memory bond) {
2022-12-tigris-main/contracts/BondNFT.sol::250 => function isExpired(uint256 _id) public view returns (bool) {
2022-12-tigris-main/contracts/BondNFT.sol::262 => ) public view returns (uint256) {
2022-12-tigris-main/contracts/BondNFT.sol::266 => function totalAssets() public view returns (uint256) {
2022-12-tigris-main/contracts/BondNFT.sol::274 => function getAssets() public view returns (address[] memory) {
2022-12-tigris-main/contracts/BondNFT.sol::339 => function balanceIds(address _user) public view returns (uint[] memory) {
2022-12-tigris-main/contracts/BondNFT.sol::368 => ) public onlyOwner() {
2022-12-tigris-main/contracts/GovNFT.sol::64 => function _bridgeMint(address to, uint256 tokenId) public {
2022-12-tigris-main/contracts/GovNFT.sol::129 => ) public payable {
2022-12-tigris-main/contracts/GovNFT.sol::183 => function nonblockingLzReceive(uint16 _srcChainId, bytes calldata _srcAddress, uint64 _nonce, bytes calldata _payload) public {
2022-12-tigris-main/contracts/GovNFT.sol::206 => function retryMessage(uint16 _srcChainId, bytes calldata _srcAddress, uint64 _nonce, bytes calldata _payload) public {
2022-12-tigris-main/contracts/GovNFT.sol::296 => function pending(address user, address _tigAsset) public view returns (uint256) {
2022-12-tigris-main/contracts/GovNFT.sol::315 => function assetsLength() public view returns (uint256) {
2022-12-tigris-main/contracts/Lock.sol::36 => ) public returns (address) {
2022-12-tigris-main/contracts/Lock.sol::65 => ) public {
2022-12-tigris-main/contracts/Lock.sol::88 => ) public {
2022-12-tigris-main/contracts/Lock.sol::100 => ) public {
2022-12-tigris-main/contracts/Lock.sol::110 => function claimGovFees() public {
2022-12-tigris-main/contracts/PairsContract.sol::17 => function idToAsset(uint256 _asset) public view returns (Asset memory) {
2022-12-tigris-main/contracts/PairsContract.sol::22 => function idToOi(uint256 _asset, address _tigAsset) public view returns (OpenInterest memory) {
2022-12-tigris-main/contracts/Position.sol::11 => function ownerOf(uint _id) public view override(ERC721, IERC721, IPosition) returns (address) {
2022-12-tigris-main/contracts/Position.sol::40 => function isMinter(address _address) public view returns (bool) { return _isMinter[_address]; }
2022-12-tigris-main/contracts/Position.sol::41 => function trades(uint _id) public view returns (Trade memory) {
2022-12-tigris-main/contracts/Position.sol::66 => function openPositions() public view returns (uint256[] memory) { return _openPositions; }
2022-12-tigris-main/contracts/Position.sol::67 => function openPositionsIndexes(uint _id) public view returns (uint256) { return _openPositionsIndexes[_id]; }
2022-12-tigris-main/contracts/Position.sol::68 => function assetOpenPositions(uint _asset) public view returns (uint256[] memory) { return _assetOpenPositions[_asset]; }
2022-12-tigris-main/contracts/Position.sol::69 => function assetOpenPositionsIndexes(uint _asset, uint _id) public view returns (uint256) { return _assetOpenPositionsIndexes[_asset][_id]; }
2022-12-tigris-main/contracts/Position.sol::70 => function limitOrders(uint _asset) public view returns (uint256[] memory) { return _limitOrders[_asset]; }
2022-12-tigris-main/contracts/Position.sol::71 => function limitOrderIndexes(uint _asset, uint _id) public view returns (uint256) { return _limitOrderIndexes[_asset][_id]; }
2022-12-tigris-main/contracts/StableVault.sol::44 => function deposit(address _token, uint256 _amount) public {
2022-12-tigris-main/contracts/TradingExtension.sol::122 => function _checkGas() public view {
2022-12-tigris-main/contracts/TradingExtension.sol::131 => ) public onlyProtocol {
2022-12-tigris-main/contracts/TradingExtension.sol::140 => ) public onlyProtocol {
2022-12-tigris-main/contracts/utils/TradingLibrary.sol::62 => function liqPrice(bool _direction, uint _tradePrice, uint _leverage, uint _margin, int _accInterest, uint _liqPercent) public pure returns (uint256 _liqPrice) {
```

### [N08] NC-library/interface files should use fixed compiler versions, not floating ones


#### Findings:
```
2022-12-tigris-main/contracts/BondNFT.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/GovNFT.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/Lock.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/PairsContract.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/Position.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/Referrals.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/StableToken.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/StableVault.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/Trading.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/TradingExtension.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/interfaces/IBondNFT.sol::2 => pragma solidity ^0.8.9;
2022-12-tigris-main/contracts/interfaces/IGovNFT.sol::3 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/interfaces/ILayerZeroEndpoint.sol::3 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/interfaces/ILayerZeroReceiver.sol::3 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/interfaces/ILayerZeroUserApplicationConfig.sol::3 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/interfaces/IPairsContract.sol::3 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/interfaces/IPosition.sol::3 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/interfaces/IReferrals.sol::3 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/interfaces/IStableVault.sol::3 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/interfaces/ITrading.sol::5 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/utils/MetaContext.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/utils/TradingLibrary.sol::2 => pragma solidity ^0.8.0;
```

### [N09] Interfaces should be moved to separate files

#### Impact
Most of the other interfaces in this project are in their own file in
 the interfaces directory. The interfaces below do not follow this 
pattern
#### Findings:
```
2022-12-tigris-main/contracts/StableVault.sol::9 => interface IERC20Mintable is IERC20 {
2022-12-tigris-main/contracts/StableVault.sol::15 => interface ERC20Permit is IERC20 {
2022-12-tigris-main/contracts/Trading.sol::14 => interface ITradingExtension {
2022-12-tigris-main/contracts/Trading.sol::58 => interface IStable is IERC20 {
2022-12-tigris-main/contracts/Trading.sol::63 => interface ExtendedIERC20 is IERC20 {
2022-12-tigris-main/contracts/Trading.sol::67 => interface ERC20Permit is IERC20 {
2022-12-tigris-main/contracts/utils/TradingLibrary.sol::7 => interface IPrice {
```




