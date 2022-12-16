# [L-01] BaseFundingRate can be over MaxFundingRate

When adding addAsset, no check if baseFundingRate is below or equal MaxFundingRate
```solidity
File: PairsContract.sol
48:     function addAsset(uint256 _asset, string memory _name, address _chainlinkFeed, uint256 _minLeverage, uint256 _maxLeverage, uint256 _feeMultiplier, uint256 _baseFundingRate) external onlyOwner {
49:         bytes memory _assetName  = bytes(_idToAsset[_asset].name);
50:         require(_assetName.length == 0, "Already exists");
51:         require(bytes(_name).length > 0, "No name");
52:         require(_maxLeverage >= _minLeverage && _minLeverage > 0, "Wrong leverage values");
53: 
54:         allowedAsset[_asset] = true;
55:         _idToAsset[_asset].name = _name;
56: 
57:         _idToAsset[_asset].chainlinkFeed = _chainlinkFeed;
58: 
59:         _idToAsset[_asset].minLeverage = _minLeverage;
60:         _idToAsset[_asset].maxLeverage = _maxLeverage;
61:         _idToAsset[_asset].feeMultiplier = _feeMultiplier;
62:         _idToAsset[_asset].baseFundingRate = _baseFundingRate;
63: 
64:         emit AssetAdded(_asset, _name);
65:     }
```
# [L-02] Unbounded values

There is no boundary on values for feeMultiplier (may become a ruggable vector)

```solidity
File: PairsContract.sol
104:     function updateAssetFeeMultiplier(uint256 _asset, uint256 _feeMultiplier) external onlyOwner {
105:         bytes memory _name  = bytes(_idToAsset[_asset].name);
106:         require(_name.length > 0, "!Asset");
107:         _idToAsset[_asset].feeMultiplier = _feeMultiplier;
108:     }
```

# [L-03] NO TRANSFER OWNERSHIP PATTERN

The protocol use openzeppelin ownable contract `import "@openzeppelin/contracts/access/Ownable.sol";`. This contract doesn't have a two step transfer pattern.

Recommend considering implementing a two step process where the owner or admin nominates an account and the nominated account needs to call an acceptOwnership() function for the transfer of ownership to fully succeed. This ensures the nominated EOA account is a valid and active account.

# [L-04] UNSPECIFIC COMPILER VERSION PRAGMA

```solidity
pragma solidity ^0.8.0;
```

Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

# [L-05] Potential issue on synchronised data

```solidity
mapping(address => bool) public allowedAsset;
```

For example the `allowedAsset` mapping is being used in many contracts, like `BondNFT.sol`, `GovNFT.sol`, `GovNFTBridged.sol`, `Lock.sol`, `PairsContract.sol`. If one contract failed / forgot to set an asset is allowed or not, may cause a sync data issue.

It's best to have a parent storage contract to store this data.


# [N-01] Not using named import

All import using plain `import 'file.sol`' instead use named import.

```solidity
Plain import
File: Trading.sol
04: import "./utils/MetaContext.sol";
05: import "./interfaces/ITrading.sol";
06: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
07: import "./interfaces/IPairsContract.sol";
08: import "./interfaces/IReferrals.sol";
09: import "./interfaces/IPosition.sol";
10: import "./interfaces/IGovNFT.sol";
11: import "./interfaces/IStableVault.sol";
12: import "./utils/TradingLibrary.sol";

Named import (example)
2: import {Ownable} from "@openzeppelin/contracts/access/Ownable.sol";
3: import {EnumerableSet} from "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";
```

# [N-02] LINES ARE TOO LONG

Usually lines in source code are limited to 80 characters. Today’s screens are much larger so it’s reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length.

Reference:

https://docs.soliditylang.org/en/v0.8.10/style-guide.html#maximum-line-length

```solidity
File: Trading.sol

434: (,int256 _payout) = TradingLibrary.pnl(_trade.direction, _assetPrice, _trade.price, _newMargin, _newLeverage, _trade.accInterest);

520: emit LimitOrderExecuted(trade.asset, trade.direction, trade.price, trade.leverage, trade.margin - _fee, _id, trade.trader, _msgSender());

623: unchecked {
624: _toMint = _handleCloseFees(_trade.asset, uint256(_payout)*_percent/DIVISION_CONSTANT, _trade.tigAsset, _positionSize*_percent/DIVISION_CONSTANT, _trade.trader, _isBot);
625: if (maxWinPercent > 0 && _toMint > _trade.margin*maxWinPercent/DIVISION_CONSTANT) {
626: _toMint = _trade.margin*maxWinPercent/DIVISION_CONSTANT;
627: }
628: }
```

# [N-03] Use latest solidity version

# [N-04] Inconsistent Private constant

```solidity
File: Trading.sol

95: uint constant private DIVISION_CONSTANT = 1e10; // 100%
96: uint private constant liqPercent = 9e9; // 90%
```

# [N-05] Make readable code, split lines for readability

Most of code inside function implementation are packed (no new line gap) between code instruction make it harder to read

```solidity
File: Trading.sol

172: _validateProxy(_trader);
173: _checkDelay(position.getCount(), true);
174: _checkVault(_tradeInfo.stableVault, _tradeInfo.marginAsset);
175: address _tigAsset = IStableVault(_tradeInfo.stableVault).stable();
176: tradingExtension.validateTrade(_tradeInfo.asset, _tigAsset, _tradeInfo.margin, _tradeInfo.leverage);
177: tradingExtension._setReferral(_tradeInfo.referral, _trader);
178: uint256 _marginAfterFees = _tradeInfo.margin - _handleOpenFees(_tradeInfo.asset, _tradeInfo.margin*_tradeInfo.leverage/1e18, _trader, _tigAsset, false);
179: uint256 _positionSize = _marginAfterFees * _tradeInfo.leverage / 1e18;
180: _handleDeposit(_tigAsset, _tradeInfo.marginAsset, _tradeInfo.margin, _tradeInfo.stableVault, _permitData, _trader);
181: uint256 _isLong = _tradeInfo.direction ? 1 : 2;
182: (uint256 _price,) = tradingExtension.getVerifiedPrice(_tradeInfo.asset, _priceData, _signature, _isLong);
```

# [N-06] No emit for storage variable change set

When changing storage variable commonly a setter function it's recommended to emit an event, meanwhile the protocol most of the setter function didn't emit an event. Emitting events allows monitoring activities with off-chain monitoring tools.

For example:

```solidity
File: Trading.sol
898:     function setBlockDelay(
899:         uint _blockDelay
900:     )
901:         external
902:         onlyOwner
903:     {
904:         blockDelay = _blockDelay;
905:     }
```

There are other functions such as `setAllowedVault`, `setMaxWinPercent`, `setLimitOrderPriceRange`, `setFees`, `setTradingExtension`, `setProtocol`, `setMinter`, `setAllowedAsset`, `setBaseURI`, `setManager` and others. You may check the rest with similar setter function with no emit

# [N-08] Make space between aritmetic operator for code visibility

For example this function (and many of similar implementation)

```solidity
File: Position.sol
230:     function reducePosition(uint256 _id, uint256 _percent) external onlyMinter {
231:         _trades[_id].accInterest -= _trades[_id].accInterest*int256(_percent)/int256(DIVISION_CONSTANT);
232:         _trades[_id].margin -= _trades[_id].margin*_percent/DIVISION_CONSTANT;
233:         initId[_id] = accInterestPerOi[_trades[_id].asset][_trades[_id].tigAsset][_trades[_id].direction]*int256(_trades[_id].margin*_trades[_id].leverage/1e18)/1e18;
234:     }
```

The multiplication (*), division (/), between variable are close to each other, no space, which is hard to read, thus prone to error and debug.

# [N-09] Use uint256 consistently even though uint is same type

uint data type is similar to uint256, but it may cause an issue if using this data type as function selector, thus it's recomended to just use only one type of it, no interchangable.

for example

```solidity
File: BondNFT.sol
32:     mapping(address => bool) public allowedAsset;
33:     mapping(address => uint) private assetsIndex;
34:     mapping(uint256 => mapping(address => uint256)) private bondPaid;
35:     mapping(address => mapping(uint256 => uint256)) private accRewardsPerShare; // tigAsset => epoch => accRewardsPerShare
36:     mapping(uint => Bond) private _idToBond;
```

this shows uint256 and uint are being used even though it's same. it's best to just use one type of data. Readability and consistency in your code, and it allows you to adhere to best practices in smart contracts.

# [N-10] LARGE MULTIPLES OF TEN SHOULD USE SCIENTIFIC NOTATION

Use (e.g. 1e6) rather than decimal literals (e.g. 100000), for better code readability.

# [N-11] USE `BYTES.CONCAT()`

Solidity version 0.8.4 introduces `bytes.concat()` (vs `abi.encodePacked(<bytes>,<bytes>)`)

### Recommended Mitigation Steps

Use `bytes.concat()` and upgrade to at least Solidity version 0.8.4 if required.