
## Summary

### Low Risk Issues
| |Issue|Instances|
|-|:-|:-:|
| [L&#x2011;01] | `latestAnswer()` is deprecated | 1 | 
| [L&#x2011;02] | Don't use `payable.transfer()`/`payable.send()` | 1 | 
| [L&#x2011;03] | Owner can renounce while system is paused | 2 | 
| [L&#x2011;04] | `_safeMint()` should be used rather than `_mint()` wherever possible | 4 | 
| [L&#x2011;05] | Draft imports may break in new minor versions | 1 | 

Total: 9 instances over 5 issues


### Non-critical Issues
| |Issue|Instances|
|-|:-|:-:|
| [N&#x2011;01] | Import declarations should import specific identifiers, rather than the whole file | 49 | 
| [N&#x2011;02] | Remove `include` for hardhat's console | 1 | 
| [N&#x2011;03] | Adding a `return` statement when the function defines a named return variable, is redundant | 1 | 
| [N&#x2011;04] | `constant`s should be defined rather than using magic numbers | 82 | 
| [N&#x2011;05] | Numeric values having to do with time should use time units for readability | 4 | 
| [N&#x2011;06] | Large multiples of ten should use scientific notation (e.g. `1e6`) rather than decimal literals (e.g. `1000000`), for readability | 1 | 
| [N&#x2011;07] | Missing event and or timelock for critical parameter change | 18 | 
| [N&#x2011;08] | Events that mark critical parameter changes should contain both the old and the new value | 2 | 
| [N&#x2011;09] | Use a more recent version of solidity | 2 | 
| [N&#x2011;10] | Use a more recent version of solidity | 1 | 
| [N&#x2011;11] | Inconsistent spacing in comments | 20 | 
| [N&#x2011;12] | Lines are too long | 20 | 
| [N&#x2011;13] | Non-library/interface files should use fixed compiler versions, not floating ones | 11 | 
| [N&#x2011;14] | File is missing NatSpec | 8 | 
| [N&#x2011;15] | NatSpec is incomplete | 16 | 
| [N&#x2011;16] | Not using the named return variables anywhere in the function is confusing | 5 | 
| [N&#x2011;17] | Duplicated `require()`/`revert()` checks should be refactored to a modifier or function | 10 | 
| [N&#x2011;18] | Consider using `delete` rather than assigning zero to clear values | 5 | 
| [N&#x2011;19] | Avoid the use of sensitive terms | 6 | 
| [N&#x2011;20] | Contracts should have full test coverage | 1 | 
| [N&#x2011;21] | Large or complicated code bases should implement fuzzing tests | 1 | 

Total: 264 instances over 21 issues





## Low Risk Issues

### [L&#x2011;01]  `latestAnswer()` is deprecated
Use `latestRoundData()` instead so that you can tell whether the answer is stale or not. The `latestAnswer()` function returns zero if it is unable to fetch data, which may be the case if ChainLink stops supporting this API. The API and its deprecation message no longer even appear on the ChainLink website, so it is dangerous to continue using it.

*There is 1 instance of this issue:*

```solidity
File: contracts/utils/TradingLibrary.sol

113:              int256 assetChainlinkPriceInt = IPrice(_chainlinkFeed).latestAnswer();

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/utils/TradingLibrary.sol#L113

### [L&#x2011;02]  Don't use `payable.transfer()`/`payable.send()`
The use of `payable.transfer()` is [heavily frowned upon](https://consensys.net/diligence/blog/2019/09/stop-using-soliditys-transfer-now/) because it can lead to the locking of funds. The `transfer()` call requires that the recipient is either an EOA account, or is a contract that has a `payable` callback. For the contract case, the `transfer()` call only provides 2300 gas for the contract to complete its operations. This means the following cases can cause the transfer to fail:
* The contract does not have a `payable` callback
* The contract's `payable` callback spends more than 2300 gas (which is only enough to emit something)
* The contract is called through a proxy which itself uses up the 2300 gas

Use OpenZeppelin's `Address.sendValue()` instead

*There is 1 instance of this issue:*

```solidity
File: contracts/Trading.sol

588:          payable(_proxy).transfer(msg.value);

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L588

### [L&#x2011;03]  Owner can renounce while system is paused
The contract owner or single user with a role is not prevented from renouncing the role/ownership while the contract is paused, which would cause any user assets stored in the protocol, to be locked indefinitely

*There are 2 instances of this issue:*

```solidity
File: contracts/PairsContract.sol

115       function pauseAsset(uint256 _asset, bool _isPaused) external onlyOwner {
116           bytes memory _name  = bytes(_idToAsset[_asset].name);
117           require(_name.length > 0, "!Asset");
118           allowedAsset[_asset] = !_isPaused;
119:      }

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L115-L119

```solidity
File: contracts/TradingExtension.sol

274       function setPaused(bool _paused) external onlyOwner {
275           paused = _paused;
276:      }

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/TradingExtension.sol#L274-L276

### [L&#x2011;04]  `_safeMint()` should be used rather than `_mint()` wherever possible
`_mint()` is [discouraged](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271) in favor of `_safeMint()` which ensures that the recipient is either an EOA or implements `IERC721Receiver`. Both [OpenZeppelin](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L238-L250) and [solmate](https://github.com/Rari-Capital/solmate/blob/4eaf6b68202e36f67cab379768ac6be304c8ebde/src/tokens/ERC721.sol#L180) have versions of this function

*There are 4 instances of this issue:*

```solidity
File: contracts/BondNFT.sol

83:               _mint(_owner, _bond);

313:          _mint(to, bond.id);

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L83

```solidity
File: contracts/GovNFT.sol

106:              _mint(_msgSender(), counter);

111:          _mint(_msgSender(), counter);

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L106

### [L&#x2011;05]  Draft imports may break in new minor versions
While OpenZeppelin draft contracts are safe to use and have been audited, their 'draft' status means that the EIPs they're based on are not finalized, and thus there may be breaking changes in even [minor releases](https://docs.openzeppelin.com/contracts/3.x/api/drafts). If a bug is found in this version of OpenZeppelin, and the version that you're forced to upgrade to has breaking changes in the new version, you'll encounter unnecessary delays in porting and testing replacement contracts. Ensure that you have extensive test coverage of this area so that differences can be automatically detected, and have a plan in place for how you would fully test a new version of these contracts if they do indeed change unexpectedly. Consider creating a forked version of the file rather than importing it from the package, and manually patch your fork as changes are made.

*There is 1 instance of this issue:*

```solidity
File: contracts/StableToken.sol

4:    import "@openzeppelin/contracts/token/ERC20/extensions/draft-ERC20Permit.sol";

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/StableToken.sol#L4

## Non-critical Issues

### [N&#x2011;01]  Import declarations should import specific identifiers, rather than the whole file
Using import declarations of the form `import {<identifier_name>} from "some/file.sol"` avoids polluting the symbol namespace making flattened files smaller, and speeds up compilation

*There are 49 instances of this issue:*

```solidity
File: contracts/BondNFT.sol

4:    import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Enumerable.sol";

5:    import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

6:    import "@openzeppelin/contracts/access/Ownable.sol";

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L4

```solidity
File: contracts/GovNFT.sol

4:    import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Enumerable.sol";

5:    import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

6:    import "./interfaces/ILayerZeroEndpoint.sol";

7:    import "./interfaces/ILayerZeroReceiver.sol";

8:    import "./utils/MetaContext.sol";

9:    import "./interfaces/IGovNFT.sol";

10:   import "./utils/ExcessivelySafeCall.sol";

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L4

```solidity
File: contracts/interfaces/ILayerZeroEndpoint.sol

5:    import "./ILayerZeroUserApplicationConfig.sol";

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/ILayerZeroEndpoint.sol#L5

```solidity
File: contracts/interfaces/ITrading.sol

3:    import "../utils/TradingLibrary.sol";

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/ITrading.sol#L3

```solidity
File: contracts/Lock.sol

4:    import "hardhat/console.sol";

5:    import "@openzeppelin/contracts/access/Ownable.sol";

6:    import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

7:    import "./interfaces/IBondNFT.sol";

8:    import "./interfaces/IGovNFT.sol";

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Lock.sol#L4

```solidity
File: contracts/PairsContract.sol

4:    import "@openzeppelin/contracts/access/Ownable.sol";

5:    import "./interfaces/IPairsContract.sol";

6:    import "./interfaces/IPosition.sol";

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L4

```solidity
File: contracts/Position.sol

4:    import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Enumerable.sol";

5:    import "@openzeppelin/contracts/utils/Counters.sol";

6:    import "./utils/MetaContext.sol";

7:    import "./interfaces/IPosition.sol";

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L4

```solidity
File: contracts/Referrals.sol

4:    import "@openzeppelin/contracts/access/Ownable.sol";

5:    import "./interfaces/IReferrals.sol";

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Referrals.sol#L4

```solidity
File: contracts/StableToken.sol

4:    import "@openzeppelin/contracts/token/ERC20/extensions/draft-ERC20Permit.sol";

5:    import "./utils/MetaContext.sol";

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/StableToken.sol#L4

```solidity
File: contracts/StableVault.sol

4:    import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

5:    import "@openzeppelin/contracts/security/ReentrancyGuard.sol";

6:    import "./utils/MetaContext.sol";

7:    import "./interfaces/IStableVault.sol";

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/StableVault.sol#L4

```solidity
File: contracts/TradingExtension.sol

4:    import "@openzeppelin/contracts/access/Ownable.sol";

5:    import "./interfaces/IPairsContract.sol";

6:    import "./utils/TradingLibrary.sol";

7:    import "./interfaces/IReferrals.sol";

8:    import "./interfaces/IPosition.sol";

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/TradingExtension.sol#L4

```solidity
File: contracts/Trading.sol

4:    import "./utils/MetaContext.sol";

5:    import "./interfaces/ITrading.sol";

6:    import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

7:    import "./interfaces/IPairsContract.sol";

8:    import "./interfaces/IReferrals.sol";

9:    import "./interfaces/IPosition.sol";

10:   import "./interfaces/IGovNFT.sol";

11:   import "./interfaces/IStableVault.sol";

12:   import "./utils/TradingLibrary.sol";

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L4

```solidity
File: contracts/utils/MetaContext.sol

4:    import "@openzeppelin/contracts/access/Ownable.sol";

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/utils/MetaContext.sol#L4

```solidity
File: contracts/utils/TradingLibrary.sol

4:    import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";

5:    import "../interfaces/IPosition.sol";

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/utils/TradingLibrary.sol#L4

### [N&#x2011;02]  Remove `include` for hardhat's console

*There is 1 instance of this issue:*

```solidity
File: contracts/Lock.sol

4:    import "hardhat/console.sol";

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Lock.sol#L4

### [N&#x2011;03]  Adding a `return` statement when the function defines a named return variable, is redundant

*There is 1 instance of this issue:*

```solidity
File: contracts/Trading.sol

809:          return payout_;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L809

### [N&#x2011;04]  `constant`s should be defined rather than using magic numbers
Even [assembly](https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39) can benefit from using readable constants instead of hex/numeric literals

*There are 82 instances of this issue:*

```solidity
File: contracts/BondNFT.sol

/// @audit 365
65:               uint shares = _amount * _period / 365;

/// @audit 365
111:          require(bond.period+_period <= 365, "MAX PERIOD");

/// @audit 365
113:              uint shares = (bond.amount + _amount) * (bond.period + _period) / 365;

/// @audit 1e18
122:              bondPaid[_id][bond.asset] = accRewardsPerShare[bond.asset][epoch[bond.asset]] * _bond.shares / 1e18;

/// @audit 7
145:                  require(bond.expireEpoch + 7 < epoch[bond.asset], "Bond owner priority");

/// @audit 1e18
/// @audit 1e18
178:                  uint _pendingDelta = (bond.shares * accRewardsPerShare[bond.asset][epoch[bond.asset]] / 1e18 - bondPaid[_id][bond.asset]) - (bond.shares * accRewardsPerShare[bond.asset][bond.expireEpoch-1] / 1e18 - bondPaid[_id][bond.asset]);

/// @audit 1e18
180:                      accRewardsPerShare[bond.asset][epoch[bond.asset]] += _pendingDelta*1e18/totalShares[bond.asset];

/// @audit 1e18
225:              accRewardsPerShare[_tigAsset][aEpoch] += _amount * 1e18 / totalShares[_tigAsset];

/// @audit 1e18
241:              bond.pending = bond.shares * _accRewardsPerShare / 1e18 - bondPaid[_id][bond.asset];

/// @audit 1e18
311:              bondPaid[bond.id][bond.asset] = accRewardsPerShare[bond.asset][epoch[bond.asset]] * bond.shares / 1e18;

/// @audit 300
332:              require(block.timestamp > bond.mintTime + 300, "Recent update");

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L65

```solidity
File: contracts/GovNFT.sol

/// @audit 10000
66:           require(tokenId <= 10000, "BadID");

/// @audit 20
138:              targetAddress := mload(add(_destination, 20))

/// @audit 500_000
144:          uint256 _gas = 500_000 + gas*tokenId.length;

/// @audit 0x0
162:              address(0x0),

/// @audit 4
/// @audit 5
/// @audit 150
175:          (bool success, bytes memory reason) = address(this).excessivelySafeCall(gasleft()*4/5, 150, abi.encodeWithSelector(this.nonblockingLzReceive.selector, _srcChainId, _srcAddress, _nonce, _payload));

/// @audit 20
192:              fromAddress := mload(add(_srcAddress, 20))

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L66

```solidity
File: contracts/PairsContract.sol

/// @audit 1e9
161:              if (_idToOi[_asset][_tigAsset].longOi < 1e9) {

/// @audit 1e9
181:              if (_idToOi[_asset][_tigAsset].shortOi < 1e9) {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L161

```solidity
File: contracts/Position.sol

/// @audit 1e18
48:               _pendingFunding = (int256(block.timestamp-lastUpdate[_trade.asset][_trade.tigAsset])*fundingDeltaPerSec[_trade.asset][_trade.tigAsset])*1e18/int256(longOi[_trade.asset][_trade.tigAsset]);

/// @audit 1e10
/// @audit 1e10
52:                   _pendingFunding = _pendingFunding*int256(1e10-vaultFundingPercent[_trade.asset][_trade.tigAsset])/1e10;

/// @audit 1e18
55:               _pendingFunding = (int256(block.timestamp-lastUpdate[_trade.asset][_trade.tigAsset])*fundingDeltaPerSec[_trade.asset][_trade.tigAsset])*1e18/int256(shortOi[_trade.asset][_trade.tigAsset]);

/// @audit 1e10
/// @audit 1e10
59:                   _pendingFunding = _pendingFunding*int256(1e10-vaultFundingPercent[_trade.asset][_trade.tigAsset])/1e10;

/// @audit 1e18
/// @audit 1e18
62:           _trade.accInterest += (int256(_trade.margin*_trade.leverage/1e18)*(accInterestPerOi[_trade.asset][_trade.tigAsset][_trade.direction]+_pendingFunding)/1e18)-initId[_id];

/// @audit 1e18
/// @audit 1e10
/// @audit 1e10
102:                  accInterestPerOi[_asset][_tigAsset][true] += ((int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(longOi[_asset][_tigAsset]))*int256(1e10-vaultFundingPercent[_asset][_tigAsset])/1e10;

/// @audit 1e18
104:              accInterestPerOi[_asset][_tigAsset][false] -= (int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(shortOi[_asset][_tigAsset]);

/// @audit 1e18
107:              accInterestPerOi[_asset][_tigAsset][true] -= (int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(longOi[_asset][_tigAsset]);

/// @audit 1e18
/// @audit 1e10
/// @audit 1e10
109:                  accInterestPerOi[_asset][_tigAsset][false] += ((int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(shortOi[_asset][_tigAsset]))*int256(1e10-vaultFundingPercent[_asset][_tigAsset])/1e10;

/// @audit 31536000
120:          fundingDeltaPerSec[_asset][_tigAsset] = (_oiDelta*int256(_baseFundingRate)/int256(DIVISION_CONSTANT))/31536000;

/// @audit 1e18
/// @audit 1e18
153:              initId[newTokenID] = accInterestPerOi[_mintTrade.asset][_mintTrade.tigAsset][_mintTrade.direction]*int256(_mintTrade.margin*_mintTrade.leverage/1e18)/1e18;

/// @audit 1e18
/// @audit 1e18
187:          initId[_id] = accInterestPerOi[_trade.asset][_trade.tigAsset][_trade.direction]*int256(_trade.margin*_trade.leverage/1e18)/1e18;

/// @audit 1e18
/// @audit 1e18
212:          initId[_id] = accInterestPerOi[_trades[_id].asset][_trades[_id].tigAsset][_trades[_id].direction]*int256(_newMargin*_trades[_id].leverage/1e18)/1e18;

/// @audit 1e18
/// @audit 1e18
233:          initId[_id] = accInterestPerOi[_trades[_id].asset][_trades[_id].tigAsset][_trades[_id].direction]*int256(_trades[_id].margin*_trades[_id].leverage/1e18)/1e18;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L48

```solidity
File: contracts/StableVault.sol

/// @audit 18
49:               _amount*(10**(18-IERC20Mintable(_token).decimals()))

/// @audit 18
67:           _output = _amount/10**(18-IERC20Mintable(_token).decimals());

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/StableVault.sol#L49

```solidity
File: contracts/TradingExtension.sol

/// @audit 1e18
71:                   modifyLongOi(_trade.asset, _trade.tigAsset, false, (_trade.margin*_trade.leverage/1e18)*_percent/DIVISION_CONSTANT);

/// @audit 1e18
73:                   modifyShortOi(_trade.asset, _trade.tigAsset, false, (_trade.margin*_trade.leverage/1e18)*_percent/DIVISION_CONSTANT);     

/// @audit 1e18
218:              if (_margin*_leverage/1e18 < minPositionSize[_tigAsset]) revert("!size");

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/TradingExtension.sol#L71

```solidity
File: contracts/Trading.sol

/// @audit 1e18
178:          uint256 _marginAfterFees = _tradeInfo.margin - _handleOpenFees(_tradeInfo.asset, _tradeInfo.margin*_tradeInfo.leverage/1e18, _trader, _tigAsset, false);

/// @audit 1e18
179:          uint256 _positionSize = _marginAfterFees * _tradeInfo.leverage / 1e18;

/// @audit 1e18
274:          uint _fee = _handleOpenFees(_trade.asset, _addMargin*_trade.leverage/1e18, _trader, _trade.tigAsset, false);

/// @audit 1e18
286:              uint _positionSize = (_addMargin - _fee) * _trade.leverage / 1e18;

/// @audit 4
347:          limitDelay[_id] = block.timestamp + 4;

/// @audit 1e18
493:              uint _fee = _handleOpenFees(trade.asset, trade.margin*trade.leverage/1e18, trade.trader, trade.tigAsset, true);

/// @audit 1e18
514:                  tradingExtension.modifyLongOi(trade.asset, trade.tigAsset, true, trade.margin*trade.leverage/1e18);

/// @audit 1e18
516:                  tradingExtension.modifyShortOi(trade.asset, trade.tigAsset, true, trade.margin*trade.leverage/1e18);

/// @audit 1e18
544:              uint256 _positionSize = _trade.margin*_trade.leverage/1e18;

/// @audit 1e18
616:              if ((_trade.margin*_trade.leverage*(DIVISION_CONSTANT-_percent)/DIVISION_CONSTANT)/1e18 < tradingExtension.minPos(_trade.tigAsset)) revert("!size");

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L178

```solidity
File: contracts/utils/MetaContext.sol

/// @audit 20
31:               return msg.data[:msg.data.length - 20];

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/utils/MetaContext.sol#L31

```solidity
File: contracts/utils/TradingLibrary.sol

/// @audit 1e18
38:               uint _initPositionSize = _margin * _leverage / 1e18;

/// @audit 1e18
/// @audit 1e18
/// @audit 1e18
40:                   _payout = int256(_margin) + int256(_initPositionSize * (1e18 * _currentPrice / _price - 1e18)/1e18) + accInterest;

/// @audit 1e18
/// @audit 1e18
/// @audit 1e18
42:                   _payout = int256(_margin) - int256(_initPositionSize * (1e18 - 1e18 * _currentPrice / _price)/1e18) + accInterest;

/// @audit 1e18
/// @audit 1e18
/// @audit 1e18
44:                   _payout = int256(_margin) + int256(_initPositionSize * (1e18 - 1e18 * _currentPrice / _price)/1e18) + accInterest;

/// @audit 1e18
/// @audit 1e18
/// @audit 1e18
46:                   _payout = int256(_margin) - int256(_initPositionSize * (1e18 * _currentPrice / _price - 1e18)/1e18) + accInterest;

/// @audit 1e18
/// @audit 1e10
64:               _liqPrice = _tradePrice - ((_tradePrice*1e18/_leverage) * uint(int(_margin)+_accInterest) / _margin) * _liqPercent / 1e10;

/// @audit 1e18
/// @audit 1e10
66:               _liqPrice = _tradePrice + ((_tradePrice*1e18/_leverage) * uint(int(_margin)+_accInterest) / _margin) * _liqPercent / 1e10;

/// @audit 100
117:                      _priceData.price < assetChainlinkPrice+assetChainlinkPrice*2/100 &&

/// @audit 100
118:                      _priceData.price > assetChainlinkPrice-assetChainlinkPrice*2/100, "!chainlinkPrice"

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/utils/TradingLibrary.sol#L38

### [N&#x2011;05]  Numeric values having to do with time should use time units for readability
There are [units](https://docs.soliditylang.org/en/latest/units-and-global-variables.html#time-units) for seconds, minutes, hours, days, and weeks, and since they're defined, they should be used

*There are 4 instances of this issue:*

```solidity
File: contracts/BondNFT.sol

/// @audit 60
/// @audit 60
10:       uint constant private DAY = 24 * 60 * 60;

/// @audit 300
332:              require(block.timestamp > bond.mintTime + 300, "Recent update");

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L10

```solidity
File: contracts/Position.sol

/// @audit 31536000
120:          fundingDeltaPerSec[_asset][_tigAsset] = (_oiDelta*int256(_baseFundingRate)/int256(DIVISION_CONSTANT))/31536000;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L120

### [N&#x2011;06]  Large multiples of ten should use scientific notation (e.g. `1e6`) rather than decimal literals (e.g. `1000000`), for readability

*There is 1 instance of this issue:*

```solidity
File: contracts/TradingExtension.sol

26:       uint public maxGasPrice = 1000000000000; // 1000 gwei

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/TradingExtension.sol#L26

### [N&#x2011;07]  Missing event and or timelock for critical parameter change
Events help non-contract tools to track changes, and events prevent users from being surprised by changes

*There are 18 instances of this issue:*

```solidity
File: contracts/BondNFT.sol

362       function setBaseURI(string calldata _newBaseURI) external onlyOwner {
363           baseURI = _newBaseURI;
364:      }

366       function setManager(
367           address _manager
368       ) public onlyOwner() {
369           manager = _manager;
370:      }

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L362-L364

```solidity
File: contracts/GovNFT.sol

46        function setBaseURI(string calldata _newBaseURI) external onlyOwner {
47            baseURI = _newBaseURI;
48:       }

236       function setGas(uint _gas) external onlyOwner {
237           gas = _gas;
238:      }

240       function setEndpoint(ILayerZeroEndpoint _endpoint) external onlyOwner {
241           require(address(_endpoint) != address(0), "ZeroAddress");
242           endpoint = _endpoint;
243:      }

311       function setMaxBridge(uint256 _max) external onlyOwner {
312           maxBridge = _max;
313:      }

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L46-L48

```solidity
File: contracts/PairsContract.sol

125       function setMaxBaseFundingRate(uint256 _maxBaseFundingRate) external onlyOwner {
126           maxBaseFundingRate = _maxBaseFundingRate;
127:      }

129       function setProtocol(address _protocol) external onlyOwner {
130           protocol = _protocol;
131:      }

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L125-L127

```solidity
File: contracts/Position.sol

85        function setBaseURI(string memory _newBaseURI) external onlyOwner {
86            baseURI = _newBaseURI;
87:       }

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L85-L87

```solidity
File: contracts/Referrals.sol

53        function setProtocol(address _protocol) external onlyOwner {
54            protocol = _protocol;
55:       }

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Referrals.sol#L53-L55

```solidity
File: contracts/TradingExtension.sol

144       function setMaxGasPrice(uint _maxGasPrice) external onlyOwner {
145           maxGasPrice = _maxGasPrice;
146:      }

222       function setValidSignatureTimer(
223           uint _validSignatureTimer
224       )
225           external
226           onlyOwner
227       {
228           validSignatureTimer = _validSignatureTimer;
229:      }

231       function setChainlinkEnabled(bool _bool) external onlyOwner {
232           chainlinkEnabled = _bool;
233:      }

274       function setPaused(bool _paused) external onlyOwner {
275           paused = _paused;
276:      }

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/TradingExtension.sol#L144-L146

```solidity
File: contracts/Trading.sol

898       function setBlockDelay(
899           uint _blockDelay
900       )
901           external
902           onlyOwner
903       {
904           blockDelay = _blockDelay;
905:      }

926       function setMaxWinPercent(
927           uint _maxWinPercent
928       )
929           external
930           onlyOwner
931       {
932           maxWinPercent = _maxWinPercent;
933:      }

939       function setLimitOrderPriceRange(uint _range) external onlyOwner {
940           limitOrderPriceRange = _range;
941:      }

952       function setFees(bool _open, uint _daoFees, uint _burnFees, uint _referralFees, uint _botFees, uint _percent) external onlyOwner {
953           unchecked {
954               require(_daoFees >= _botFees+_referralFees*2);
955               if (_open) {
956                   openFees.daoFees = _daoFees;
957                   openFees.burnFees = _burnFees;
958                   openFees.referralFees = _referralFees;
959                   openFees.botFees = _botFees;
960               } else {
961                   closeFees.daoFees = _daoFees;
962                   closeFees.burnFees = _burnFees;
963                   closeFees.referralFees = _referralFees;
964                   closeFees.botFees = _botFees;                
965               }
966               require(_percent <= DIVISION_CONSTANT);
967               vaultFundingPercent = _percent;
968           }
969:      }

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L898-L905

### [N&#x2011;08]  Events that mark critical parameter changes should contain both the old and the new value
This should especially be done if the new value is not required to be different from the old value

*There are 2 instances of this issue:*

```solidity
File: contracts/Referrals.sol

/// @audit setReferred()
40:           emit Referred(_referredTrader, _hash);

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Referrals.sol#L40

```solidity
File: contracts/Trading.sol

/// @audit updateTpSl()
472:          emit UpdateTPSL(_id, _type, _limitPrice, _trader);

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L472

### [N&#x2011;09]  Use a more recent version of solidity
Use a solidity version of at least 0.8.13 to get the ability to use `using for` with a list of free functions

*There are 2 instances of this issue:*

```solidity
File: contracts/Position.sol

2:    pragma solidity ^0.8.0;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L2

```solidity
File: contracts/utils/TradingLibrary.sol

2:    pragma solidity ^0.8.0;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/utils/TradingLibrary.sol#L2

### [N&#x2011;10]  Use a more recent version of solidity
Use a solidity version of at least 0.8.4 to get `bytes.concat()` instead of `abi.encodePacked(<bytes>,<bytes>)`
Use a solidity version of at least 0.8.12 to get `string.concat()` instead of `abi.encodePacked(<str>,<str>)`

*There is 1 instance of this issue:*

```solidity
File: contracts/GovNFT.sol

2:    pragma solidity ^0.8.0;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L2

### [N&#x2011;11]  Inconsistent spacing in comments
Some lines use `// x` and some use `//x`. The instances below point out the usages that don't follow the majority, within each file

*There are 20 instances of this issue:*

```solidity
File: contracts/TradingExtension.sol

11:       uint constant private DIVISION_CONSTANT = 1e10; // 100%

26:       uint public maxGasPrice = 1000000000000; // 1000 gwei

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/TradingExtension.sol#L11

```solidity
File: contracts/Trading.sol

81:       error LimitNotSet(); //7

238:          if (_trade.orderType != 0) revert("4"); //IsLimit        

273:          if (_trade.orderType != 0) revert("4"); //IsLimit

392:          if (_trade.orderType != 0) revert(); //IsLimit

428:          if (_trade.orderType != 0) revert(); //IsLimit

464:          if (_trade.orderType != 0) revert("4"); //IsLimit

496:              if (_price > trade.price+trade.price*limitOrderPriceRange/DIVISION_CONSTANT || _price < trade.price-trade.price*limitOrderPriceRange/DIVISION_CONSTANT) revert("6"); //LimitNotMet

498:                  if (trade.price < _price) revert("6"); //LimitNotMet

500:                  if (trade.price > _price) revert("6"); //LimitNotMet

502:                  if (trade.price < _price) revert("6"); //LimitNotMet

505:                  if (trade.price > _price) revert("6"); //LimitNotMet

540:              if (_trade.orderType != 0) revert("4"); //IsLimit

707:          address _referrer = tradingExtension.getRef(_trader); //referrals.getReferral(referrals.getReferred(_trader));

783:          address _referrer = tradingExtension.getRef(_trader);//referrals.getReferral(referrals.getReferred(_trader));

836:              if (_sl > _price) revert("3"); //BadStopLoss

838:              if (_sl < _price && _sl != 0) revert("3"); //BadStopLoss

848:          if (position.ownerOf(_id) != _trader) revert("2"); //NotPositionOwner   

863:                  if (block.number < _delay.delay) revert("0"); //Wait

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L81

### [N&#x2011;12]  Lines are too long
Usually lines in source code are limited to [80](https://softwareengineering.stackexchange.com/questions/148677/why-is-80-characters-the-standard-limit-for-code-width) characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over [164](https://github.com/aizatto/character-length) characters, the lines below should be split when they reach that length

*There are 20 instances of this issue:*

```solidity
File: contracts/BondNFT.sol

178:                  uint _pendingDelta = (bond.shares * accRewardsPerShare[bond.asset][epoch[bond.asset]] / 1e18 - bondPaid[_id][bond.asset]) - (bond.shares * accRewardsPerShare[bond.asset][bond.expireEpoch-1] / 1e18 - bondPaid[_id][bond.asset]);

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L178

```solidity
File: contracts/GovNFT.sol

175:          (bool success, bytes memory reason) = address(this).excessivelySafeCall(gasleft()*4/5, 150, abi.encodeWithSelector(this.nonblockingLzReceive.selector, _srcChainId, _srcAddress, _nonce, _payload));

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L175

```solidity
File: contracts/interfaces/ILayerZeroEndpoint.sol

15:       function send(uint16 _dstChainId, bytes calldata _destination, bytes calldata _payload, address payable _refundAddress, address _zroPaymentAddress, bytes calldata _adapterParams) external payable;

41:       function estimateFees(uint16 _dstChainId, address _userApplication, bytes calldata _payload, bool _payInZRO, bytes calldata _adapterParam) external view returns (uint nativeFee, uint zroFee);

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/ILayerZeroEndpoint.sol#L15

```solidity
File: contracts/PairsContract.sol

48:       function addAsset(uint256 _asset, string memory _name, address _chainlinkFeed, uint256 _minLeverage, uint256 _maxLeverage, uint256 _feeMultiplier, uint256 _baseFundingRate) external onlyOwner {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L48

```solidity
File: contracts/Position.sol

48:               _pendingFunding = (int256(block.timestamp-lastUpdate[_trade.asset][_trade.tigAsset])*fundingDeltaPerSec[_trade.asset][_trade.tigAsset])*1e18/int256(longOi[_trade.asset][_trade.tigAsset]);

55:               _pendingFunding = (int256(block.timestamp-lastUpdate[_trade.asset][_trade.tigAsset])*fundingDeltaPerSec[_trade.asset][_trade.tigAsset])*1e18/int256(shortOi[_trade.asset][_trade.tigAsset]);

62:           _trade.accInterest += (int256(_trade.margin*_trade.leverage/1e18)*(accInterestPerOi[_trade.asset][_trade.tigAsset][_trade.direction]+_pendingFunding)/1e18)-initId[_id];

99:       function updateFunding(uint256 _asset, address _tigAsset, uint256 _longOi, uint256 _shortOi, uint256 _baseFundingRate, uint _vaultFundingPercent) external onlyMinter {

102:                  accInterestPerOi[_asset][_tigAsset][true] += ((int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(longOi[_asset][_tigAsset]))*int256(1e10-vaultFundingPercent[_asset][_tigAsset])/1e10;

104:              accInterestPerOi[_asset][_tigAsset][false] -= (int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(shortOi[_asset][_tigAsset]);

107:              accInterestPerOi[_asset][_tigAsset][true] -= (int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(longOi[_asset][_tigAsset]);

109:                  accInterestPerOi[_asset][_tigAsset][false] += ((int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(shortOi[_asset][_tigAsset]))*int256(1e10-vaultFundingPercent[_asset][_tigAsset])/1e10;

153:              initId[newTokenID] = accInterestPerOi[_mintTrade.asset][_mintTrade.tigAsset][_mintTrade.direction]*int256(_mintTrade.margin*_mintTrade.leverage/1e18)/1e18;

233:          initId[_id] = accInterestPerOi[_trades[_id].asset][_trades[_id].tigAsset][_trades[_id].direction]*int256(_trades[_id].margin*_trades[_id].leverage/1e18)/1e18;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L48

```solidity
File: contracts/Trading.sol

496:              if (_price > trade.price+trade.price*limitOrderPriceRange/DIVISION_CONSTANT || _price < trade.price-trade.price*limitOrderPriceRange/DIVISION_CONSTANT) revert("6"); //LimitNotMet

543:              (uint256 _positionSizeAfterPrice, int256 _payout) = TradingLibrary.pnl(_trade.direction, _price, _trade.price, _trade.margin, _trade.leverage, _trade.accInterest);

624:                  _toMint = _handleCloseFees(_trade.asset, uint256(_payout)*_percent/DIVISION_CONSTANT, _trade.tigAsset, _positionSize*_percent/DIVISION_CONSTANT, _trade.trader, _isBot);

643:      function _handleDeposit(address _tigAsset, address _marginAsset, uint256 _margin, address _stableVault, ERC20PermitData calldata _permitData, address _trader) internal {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L496

```solidity
File: contracts/utils/TradingLibrary.sol

36:       function pnl(bool _direction, uint _currentPrice, uint _price, uint _margin, uint _leverage, int256 accInterest) external pure returns (uint256 _positionSize, int256 _payout) {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/utils/TradingLibrary.sol#L36

### [N&#x2011;13]  Non-library/interface files should use fixed compiler versions, not floating ones

*There are 11 instances of this issue:*

```solidity
File: contracts/BondNFT.sol

2:    pragma solidity ^0.8.0;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L2

```solidity
File: contracts/GovNFT.sol

2:    pragma solidity ^0.8.0;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L2

```solidity
File: contracts/Lock.sol

2:    pragma solidity ^0.8.0;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Lock.sol#L2

```solidity
File: contracts/PairsContract.sol

2:    pragma solidity ^0.8.0;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L2

```solidity
File: contracts/Position.sol

2:    pragma solidity ^0.8.0;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L2

```solidity
File: contracts/Referrals.sol

2:    pragma solidity ^0.8.0;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Referrals.sol#L2

```solidity
File: contracts/StableToken.sol

2:    pragma solidity ^0.8.0;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/StableToken.sol#L2

```solidity
File: contracts/StableVault.sol

2:    pragma solidity ^0.8.0;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/StableVault.sol#L2

```solidity
File: contracts/TradingExtension.sol

2:    pragma solidity ^0.8.0;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/TradingExtension.sol#L2

```solidity
File: contracts/Trading.sol

2:    pragma solidity ^0.8.0;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L2

```solidity
File: contracts/utils/MetaContext.sol

2:    pragma solidity ^0.8.0;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/utils/MetaContext.sol#L2

### [N&#x2011;14]  File is missing NatSpec

*There are 8 instances of this issue:*

```solidity
File: contracts/interfaces/IBondNFT.sol


```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/IBondNFT.sol

```solidity
File: contracts/interfaces/IGovNFT.sol


```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/IGovNFT.sol

```solidity
File: contracts/interfaces/IPairsContract.sol


```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/IPairsContract.sol

```solidity
File: contracts/interfaces/IPosition.sol


```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/IPosition.sol

```solidity
File: contracts/interfaces/IReferrals.sol


```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/IReferrals.sol

```solidity
File: contracts/interfaces/IStableVault.sol


```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/IStableVault.sol

```solidity
File: contracts/interfaces/ITrading.sol


```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/ITrading.sol

```solidity
File: contracts/utils/MetaContext.sol


```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/utils/MetaContext.sol

### [N&#x2011;15]  NatSpec is incomplete

*There are 16 instances of this issue:*

```solidity
File: contracts/GovNFT.sol

/// @audit Missing: '@param _destination'
118       /**
119       * @notice used to bridge NFTs crosschain using layer zero
120       * @param _dstChainId the layer zero id of the dest chain
121       * @param _to receiving address on dest chain
122       * @param tokenId array of the ids of the NFTs to be bridged
123       */
124       function crossChain(
125           uint16 _dstChainId,
126           bytes memory _destination,
127           address _to,
128:          uint256[] memory tokenId

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L118-L128

```solidity
File: contracts/interfaces/ILayerZeroEndpoint.sol

/// @audit Missing: '@return'
27        // @param _srcChainId - the source chain identifier
28        // @param _srcAddress - the source chain contract address
29:       function getInboundNonce(uint16 _srcChainId, bytes calldata _srcAddress) external view returns (uint64);

/// @audit Missing: '@param _dstChainId'
/// @audit Missing: '@return'
31        // @notice get the outboundNonce from this source chain which, consequently, is always an EVM
32        // @param _srcAddress - the source chain contract address
33:       function getOutboundNonce(uint16 _dstChainId, address _srcAddress) external view returns (uint64);

/// @audit Missing: '@return'
39        // @param _payInZRO - if false, user app pays the protocol fee in native token
40        // @param _adapterParam - parameters for the adapter service, e.g. send some dust native token to dstChain
41:       function estimateFees(uint16 _dstChainId, address _userApplication, bytes calldata _payload, bool _payInZRO, bytes calldata _adapterParam) external view returns (uint nativeFee, uint zroFee);

/// @audit Missing: '@return'
53        // @param _srcChainId - the source chain identifier
54        // @param _srcAddress - the source chain contract address
55:       function hasStoredPayload(uint16 _srcChainId, bytes calldata _srcAddress) external view returns (bool);

/// @audit Missing: '@return'
57        // @notice query if the _libraryAddress is valid for sending msgs.
58        // @param _userApplication - the user app address on this EVM chain
59:       function getSendLibraryAddress(address _userApplication) external view returns (address);

/// @audit Missing: '@return'
61        // @notice query if the _libraryAddress is valid for receiving msgs.
62        // @param _userApplication - the user app address on this EVM chain
63:       function getReceiveLibraryAddress(address _userApplication) external view returns (address);

/// @audit Missing: '@return'
76        // @param _userApplication - the contract address of the user application
77        // @param _configType - type of configuration. every messaging library has its own convention.
78:       function getConfig(uint16 _version, uint16 _chainId, address _userApplication, uint _configType) external view returns (bytes memory);

/// @audit Missing: '@return'
80        // @notice get the send() LayerZero messaging library version
81        // @param _userApplication - the contract address of the user application
82:       function getSendVersion(address _userApplication) external view returns (uint16);

/// @audit Missing: '@return'
84        // @notice get the lzReceive() LayerZero messaging library version
85        // @param _userApplication - the contract address of the user application
86:       function getReceiveVersion(address _userApplication) external view returns (uint16);

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/ILayerZeroEndpoint.sol#L27-L29

```solidity
File: contracts/PairsContract.sol

/// @audit Missing: '@param _minLeverage'
/// @audit Missing: '@param _baseFundingRate'
39        /**
40         * @dev Add an allowed asset to fetch prices for
41         * @param _asset index of the requested asset
42         * @param _name name of the asset
43         * @param _chainlinkFeed optional address of the respective Chainlink price feed
44         * @param _maxLeverage maximimum allowed leverage
45         * @param _maxLeverage minimum allowed leverage
46         * @param _feeMultiplier percent value that the opening/closing fee is multiplied by in BP
47         */
48:       function addAsset(uint256 _asset, string memory _name, address _chainlinkFeed, uint256 _minLeverage, uint256 _maxLeverage, uint256 _feeMultiplier, uint256 _baseFundingRate) external onlyOwner {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L39-L48

```solidity
File: contracts/StableVault.sol

/// @audit Missing: '@return'
63        * @param _amount amount of _token
64        */
65:       function withdraw(address _token, uint256 _amount) external returns (uint256 _output) {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/StableVault.sol#L63-L65

```solidity
File: contracts/TradingExtension.sol

/// @audit Missing: '@return'
43        * @param _asset address of the asset
44        */
45        function minPos(
46            address _asset
47:       ) external view returns(uint) {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/TradingExtension.sol#L43-L47

```solidity
File: contracts/utils/TradingLibrary.sol

/// @audit Missing: '@return'
74        * @param _liqPercent liquidation percent
75        */
76:       function getLiqPrice(address _positions, uint _id, uint _liqPercent) external view returns (uint256) {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/utils/TradingLibrary.sol#L74-L76

### [N&#x2011;16]  Not using the named return variables anywhere in the function is confusing
Consider changing the variable to be an unnamed one

*There are 5 instances of this issue:*

```solidity
File: contracts/GovNFT.sol

/// @audit nativeFee
/// @audit zroFee
219       function estimateFees(
220           uint16 _dstChainId,
221           address _userApplication,
222           bytes calldata _payload,
223           bool _payInZRO,
224           bytes calldata _adapterParams
225:      ) external view returns (uint256 nativeFee, uint256 zroFee) {

/// @audit sender
332:      function _msgSender() internal view override(Context, MetaContext) returns (address sender) {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L219-L225

```solidity
File: contracts/Position.sol

/// @audit sender
320:      function _msgSender() internal view override(Context, MetaContext) returns (address sender) {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L320

```solidity
File: contracts/StableToken.sol

/// @audit sender
57:       function _msgSender() internal view override(Context, MetaContext) returns (address sender) {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/StableToken.sol#L57

### [N&#x2011;17]  Duplicated `require()`/`revert()` checks should be refactored to a modifier or function
The compiler will inline the function, which will avoid `JUMP` instructions usually associated with functions

*There are 10 instances of this issue:*

```solidity
File: contracts/BondNFT.sol

329:          require(epoch[bond.asset] == block.timestamp/DAY, "Bad epoch");

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L329

```solidity
File: contracts/PairsContract.sol

75:           require(_name.length > 0, "!Asset");

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L75

```solidity
File: contracts/TradingExtension.sol

112:              if (_trade.slPrice == 0) revert("7"); //LimitNotSet

108:                  if (_trade.tpPrice < _price) revert("6"); //LimitNotMet

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/TradingExtension.sol#L112

```solidity
File: contracts/Trading.sol

273:          if (_trade.orderType != 0) revert("4"); //IsLimit

495:              if (trade.orderType == 0) revert("5");

392:          if (_trade.orderType != 0) revert(); //IsLimit

432:          if (_newLeverage > asset.maxLeverage) revert("!lev");

498:                  if (trade.price < _price) revert("6"); //LimitNotMet

838:              if (_sl < _price && _sl != 0) revert("3"); //BadStopLoss

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L273

### [N&#x2011;18]  Consider using `delete` rather than assigning zero to clear values
The `delete` keyword more closely matches the semantics of what is being done, and draws more attention to the changing of state, which may lead to a more thorough audit of its associated logic

*There are 5 instances of this issue:*

```solidity
File: contracts/BondNFT.sol

201:          userDebt[_user][_tigAsset] = 0;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L201

```solidity
File: contracts/PairsContract.sol

162:                  _idToOi[_asset][_tigAsset].longOi = 0;

182:                  _idToOi[_asset][_tigAsset].shortOi = 0;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L162

```solidity
File: contracts/Position.sol

173:          _trade.orderType = 0;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L173

```solidity
File: contracts/Trading.sol

730:              _fees.botFees = 0;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L730

### [N&#x2011;19]  Avoid the use of sensitive terms
Use [alternative variants](https://www.zdnet.com/article/mysql-drops-master-slave-and-blacklist-whitelist-terminology/), e.g. allowlist/denylist instead of whitelist/blacklist

*There are 6 instances of this issue:*

```solidity
File: contracts/BondNFT.sol

271:       * @notice Gets an array of all whitelisted token addresses

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L271

```solidity
File: contracts/Lock.sol

123:       * @notice Whitelist an asset

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Lock.sol#L123

```solidity
File: contracts/TradingExtension.sol

236:       * @dev whitelists a node

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/TradingExtension.sol#L236

```solidity
File: contracts/Trading.sol

871:       * @dev Check that the stablevault input is whitelisted and the margin asset is whitelisted in the vault

908:       * @dev Whitelists a stablevault contract address

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L871

```solidity
File: contracts/utils/TradingLibrary.sol

82:       * @notice verifies that price is signed by a whitelisted node

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/utils/TradingLibrary.sol#L82

### [N&#x2011;20]  Contracts should have full test coverage
While 100% code coverage does not guarantee that there are no bugs, it often will catch easy-to-find bugs, and will ensure that there are fewer regressions when the code invariably has to be modified. Furthermore, in order to get full coverage, code authors will often have to re-organize their code so that it is more modular, so that each component can be tested separately, which reduces interdependencies between modules and layers, and makes for code that is easier to reason about and audit.

*There is 1 instance of this issue:*

```solidity
File: contracts/BondNFT.sol


```

### [N&#x2011;21]  Large or complicated code bases should implement fuzzing tests
Large code bases, or code with lots of inline-assembly, complicated math, or complicated interactions between multiple contracts, should implement [fuzzing tests](https://medium.com/coinmonks/smart-contract-fuzzing-d9b88e0b0a05). Fuzzers such as Echidna require the test writer to come up with invariants which should not be violated under any circumstances, and the fuzzer tests various inputs and function calls to ensure that the invariants always hold. Even code with 100% code coverage can still have bugs due to the order of the operations a user performs, and fuzzers, with properly and extensively-written invariants, can close this testing gap significantly.

*There is 1 instance of this issue:*

```solidity
File: contracts/BondNFT.sol


```


___

## Excluded findings
These findings are excluded from awards calculations because there are publicly-available automated tools that find them. The valid ones appear here for completeness

## Summary

### Low Risk Issues
| |Issue|Instances|
|-|:-|:-:|
| [L&#x2011;01] | Unsafe use of `transfer()`/`transferFrom()` with `IERC20` | 15 | 
| [L&#x2011;02] | Return values of `transfer()`/`transferFrom()` not checked | 15 | 
| [L&#x2011;03] | Missing checks for `address(0x0)` when assigning values to `address` state variables | 5 | 

Total: 35 instances over 3 issues


### Non-critical Issues
| |Issue|Instances|
|-|:-|:-:|
| [N&#x2011;01] | Return values of `approve()` not checked | 2 | 
| [N&#x2011;02] | `require()`/`revert()` statements should have descriptive reason strings | 8 | 
| [N&#x2011;03] | `public` functions not called by the contract should be declared `external` instead | 23 | 
| [N&#x2011;04] | `constant`s should be defined rather than using magic numbers | 5 | 
| [N&#x2011;05] | Event is missing `indexed` fields | 21 | 

Total: 59 instances over 5 issues





## Low Risk Issues

### [L&#x2011;01]  Unsafe use of `transfer()`/`transferFrom()` with `IERC20`
Some tokens do not implement the ERC20 standard properly but are still accepted by most code that accepts ERC20 tokens.  For example Tether (USDT)'s `transfer()` and `transferFrom()` functions on L1 do not return booleans as the specification requires, and instead have no return value. When these sorts of tokens are cast to `IERC20`, their [function signatures](https://medium.com/coinmonks/missing-return-value-bug-at-least-130-tokens-affected-d67bf08521ca) do not match and therefore the calls made, revert (see [this](https://gist.github.com/IllIllI000/2b00a32e8f0559e8f386ea4f1800abc5) link for a test case). Use OpenZeppelinâ€™s `SafeERC20`'s `safeTransfer()`/`safeTransferFrom()` instead

*There are 15 instances of this issue:*

```solidity
File: contracts/BondNFT.sol

/// @audit (valid but excluded finding)
185:          IERC20(tigAsset).transfer(manager, amount);

/// @audit (valid but excluded finding)
202:          IERC20(_tigAsset).transfer(manager, amount);

/// @audit (valid but excluded finding)
216:          IERC20(_tigAsset).transferFrom(_msgSender(), address(this), _amount);

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L185

```solidity
File: contracts/GovNFT.sol

/// @audit (valid but excluded finding)
279:          IERC20(_tigAsset).transfer(_msgsender, amount);

/// @audit (valid but excluded finding)
289:          try IERC20(_tigAsset).transferFrom(_msgSender(), address(this), _amount) {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L279

```solidity
File: contracts/Lock.sol

/// @audit (valid but excluded finding)
39:           IERC20(_tigAsset).transfer(msg.sender, _amount);

/// @audit (valid but excluded finding)
52:           IERC20(_tigAsset).transfer(msg.sender, amount);

/// @audit (valid but excluded finding)
72:           IERC20(_asset).transferFrom(msg.sender, address(this), _amount);

/// @audit (valid but excluded finding)
90:           IERC20(_asset).transferFrom(msg.sender, address(this), _amount);

/// @audit (valid but excluded finding)
104:          IERC20(asset).transfer(_owner, amount);

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Lock.sol#L39

```solidity
File: contracts/StableVault.sol

/// @audit (valid but excluded finding)
46:           IERC20(_token).transferFrom(_msgSender(), address(this), _amount);

/// @audit (valid but excluded finding)
68            IERC20(_token).transfer(
69                _msgSender(),
70                _output
71:           );

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/StableVault.sol#L46

```solidity
File: contracts/Trading.sol

/// @audit (valid but excluded finding)
651:              IERC20(_marginAsset).transferFrom(_trader, address(this), _margin/_marginDecMultiplier);

/// @audit (valid but excluded finding)
671:              IERC20(_outputToken).transfer(_trade.trader, _toMint);

/// @audit (valid but excluded finding)
676:              IERC20(_outputToken).transfer(_trade.trader, IERC20(_outputToken).balanceOf(address(this)) - _balBefore);

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L651

### [L&#x2011;02]  Return values of `transfer()`/`transferFrom()` not checked
Not all `IERC20` implementations `revert()` when there's a failure in `transfer()`/`transferFrom()`. The function signature has a `boolean` return value and they indicate errors that way instead. By not checking the return value, operations that should have marked as failed, may potentially go through without actually making a payment

*There are 15 instances of this issue:*

```solidity
File: contracts/BondNFT.sol

/// @audit (valid but excluded finding)
185:          IERC20(tigAsset).transfer(manager, amount);

/// @audit (valid but excluded finding)
202:          IERC20(_tigAsset).transfer(manager, amount);

/// @audit (valid but excluded finding)
216:          IERC20(_tigAsset).transferFrom(_msgSender(), address(this), _amount);

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L185

```solidity
File: contracts/GovNFT.sol

/// @audit (valid but excluded finding)
279:          IERC20(_tigAsset).transfer(_msgsender, amount);

/// @audit (valid but excluded finding)
289:          try IERC20(_tigAsset).transferFrom(_msgSender(), address(this), _amount) {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L279

```solidity
File: contracts/Lock.sol

/// @audit (valid but excluded finding)
39:           IERC20(_tigAsset).transfer(msg.sender, _amount);

/// @audit (valid but excluded finding)
52:           IERC20(_tigAsset).transfer(msg.sender, amount);

/// @audit (valid but excluded finding)
72:           IERC20(_asset).transferFrom(msg.sender, address(this), _amount);

/// @audit (valid but excluded finding)
90:           IERC20(_asset).transferFrom(msg.sender, address(this), _amount);

/// @audit (valid but excluded finding)
104:          IERC20(asset).transfer(_owner, amount);

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Lock.sol#L39

```solidity
File: contracts/StableVault.sol

/// @audit (valid but excluded finding)
46:           IERC20(_token).transferFrom(_msgSender(), address(this), _amount);

/// @audit (valid but excluded finding)
68            IERC20(_token).transfer(
69                _msgSender(),
70                _output
71:           );

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/StableVault.sol#L46

```solidity
File: contracts/Trading.sol

/// @audit (valid but excluded finding)
651:              IERC20(_marginAsset).transferFrom(_trader, address(this), _margin/_marginDecMultiplier);

/// @audit (valid but excluded finding)
671:              IERC20(_outputToken).transfer(_trade.trader, _toMint);

/// @audit (valid but excluded finding)
676:              IERC20(_outputToken).transfer(_trade.trader, IERC20(_outputToken).balanceOf(address(this)) - _balBefore);

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L651

### [L&#x2011;03]  Missing checks for `address(0x0)` when assigning values to `address` state variables

*There are 5 instances of this issue:*

```solidity
File: contracts/BondNFT.sol

/// @audit (valid but excluded finding)
369:          manager = _manager;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L369

```solidity
File: contracts/PairsContract.sol

/// @audit (valid but excluded finding)
130:          protocol = _protocol;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L130

```solidity
File: contracts/Referrals.sol

/// @audit (valid but excluded finding)
54:           protocol = _protocol;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Referrals.sol#L54

```solidity
File: contracts/StableVault.sol

/// @audit (valid but excluded finding)
36:           stable = _stable;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/StableVault.sol#L36

```solidity
File: contracts/TradingExtension.sol

/// @audit (valid but excluded finding)
35:           trading = _trading;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/TradingExtension.sol#L35

## Non-critical Issues

### [N&#x2011;01]  Return values of `approve()` not checked
Not all `IERC20` implementations `revert()` when there's a failure in `approve()`. The function signature has a `boolean` return value and they indicate errors that way instead. By not checking the return value, operations that should have marked as failed, may potentially go through without actually approving anything

*There are 2 instances of this issue:*

```solidity
File: contracts/Lock.sol

/// @audit (valid but excluded finding)
117:              IERC20(assets[i]).approve(address(bondNFT), type(uint256).max);

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Lock.sol#L117

```solidity
File: contracts/Trading.sol

/// @audit (valid but excluded finding)
652:              IERC20(_marginAsset).approve(_stableVault, type(uint).max);

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L652

### [N&#x2011;02]  `require()`/`revert()` statements should have descriptive reason strings

*There are 8 instances of this issue:*

```solidity
File: contracts/BondNFT.sol

/// @audit (valid but excluded finding)
109:          require(bond.pending == 0);

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L109

```solidity
File: contracts/Referrals.sol

/// @audit (valid but excluded finding)
66:           require(!isInit);

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Referrals.sol#L66

```solidity
File: contracts/Trading.sol

/// @audit (valid but excluded finding)
364:          if (_trade.orderType == 0) revert();

/// @audit (valid but excluded finding)
392:          if (_trade.orderType != 0) revert(); //IsLimit

/// @audit (valid but excluded finding)
428:          if (_trade.orderType != 0) revert(); //IsLimit

/// @audit (valid but excluded finding)
491:              require(block.timestamp >= limitDelay[_id]);

/// @audit (valid but excluded finding)
954:              require(_daoFees >= _botFees+_referralFees*2);

/// @audit (valid but excluded finding)
966:              require(_percent <= DIVISION_CONSTANT);

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L364

### [N&#x2011;03]  `public` functions not called by the contract should be declared `external` instead
Contracts [are allowed](https://docs.soliditylang.org/en/latest/contracts.html#function-overriding) to override their parents' functions and change the visibility from `external` to `public`.

*There are 23 instances of this issue:*

```solidity
File: contracts/BondNFT.sol

/// @audit (valid but excluded finding)
196       function claimDebt(
197           address _user,
198           address _tigAsset
199:      ) public onlyManager() returns(uint amount) {

/// @audit (valid but excluded finding)
250:      function isExpired(uint256 _id) public view returns (bool) {

/// @audit (valid but excluded finding)
260       function pending(
261           uint256 _id
262:      ) public view returns (uint256) {

/// @audit (valid but excluded finding)
266:      function totalAssets() public view returns (uint256) {

/// @audit (valid but excluded finding)
274:      function getAssets() public view returns (address[] memory) {

/// @audit (valid but excluded finding)
339:      function balanceIds(address _user) public view returns (uint[] memory) {

/// @audit (valid but excluded finding)
366       function setManager(
367           address _manager
368:      ) public onlyOwner() {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L196-L199

```solidity
File: contracts/GovNFT.sol

/// @audit (valid but excluded finding)
124       function crossChain(
125           uint16 _dstChainId,
126           bytes memory _destination,
127           address _to,
128:          uint256[] memory tokenId

/// @audit (valid but excluded finding)
183:      function nonblockingLzReceive(uint16 _srcChainId, bytes calldata _srcAddress, uint64 _nonce, bytes calldata _payload) public {

/// @audit (valid but excluded finding)
206:      function retryMessage(uint16 _srcChainId, bytes calldata _srcAddress, uint64 _nonce, bytes calldata _payload) public {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L124-L128

```solidity
File: contracts/Lock.sol

/// @audit (valid but excluded finding)
61        function lock(
62            address _asset,
63            uint _amount,
64:           uint _period

/// @audit (valid but excluded finding)
84        function extendLock(
85            uint _id,
86            uint _amount,
87:           uint _period

/// @audit (valid but excluded finding)
98        function release(
99:           uint _id

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Lock.sol#L61-L64

```solidity
File: contracts/PairsContract.sol

/// @audit (valid but excluded finding)
17:       function idToAsset(uint256 _asset) public view returns (Asset memory) {

/// @audit (valid but excluded finding)
22:       function idToOi(uint256 _asset, address _tigAsset) public view returns (OpenInterest memory) {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L17

```solidity
File: contracts/Position.sol

/// @audit (valid but excluded finding)
40:       function isMinter(address _address) public view returns (bool) { return _isMinter[_address]; }

/// @audit (valid but excluded finding)
66:       function openPositions() public view returns (uint256[] memory) { return _openPositions; }

/// @audit (valid but excluded finding)
67:       function openPositionsIndexes(uint _id) public view returns (uint256) { return _openPositionsIndexes[_id]; }

/// @audit (valid but excluded finding)
68:       function assetOpenPositions(uint _asset) public view returns (uint256[] memory) { return _assetOpenPositions[_asset]; }

/// @audit (valid but excluded finding)
69:       function assetOpenPositionsIndexes(uint _asset, uint _id) public view returns (uint256) { return _assetOpenPositionsIndexes[_asset][_id]; }

/// @audit (valid but excluded finding)
70:       function limitOrders(uint _asset) public view returns (uint256[] memory) { return _limitOrders[_asset]; }

/// @audit (valid but excluded finding)
71:       function limitOrderIndexes(uint _asset, uint _id) public view returns (uint256) { return _limitOrderIndexes[_asset][_id]; }

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L40

```solidity
File: contracts/StableToken.sol

/// @audit (valid but excluded finding)
38        function setMinter(
39            address _address,
40            bool _status
41        ) 
42            public
43:           onlyOwner()

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/StableToken.sol#L38-L43

### [N&#x2011;04]  `constant`s should be defined rather than using magic numbers
Even [assembly](https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39) can benefit from using readable constants instead of hex/numeric literals

*There are 5 instances of this issue:*

```solidity
File: contracts/Trading.sol

/// @audit 18 - (valid but excluded finding)
650:              uint _marginDecMultiplier = 10**(18-ExtendedIERC20(_marginAsset).decimals());

/// @audit 18 - (valid but excluded finding)
675:              if (IERC20(_outputToken).balanceOf(address(this)) != _balBefore + _toMint/(10**(18-ExtendedIERC20(_outputToken).decimals()))) revert BadWithdraw();

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L650

```solidity
File: contracts/utils/MetaContext.sol

/// @audit 96 - (valid but excluded finding)
/// @audit 20 - (valid but excluded finding)
22:                   sender := shr(96, calldataload(sub(calldatasize(), 20)))

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/utils/MetaContext.sol#L22

```solidity
File: contracts/utils/TradingLibrary.sol

/// @audit 18 - (valid but excluded finding)
115:                  uint256 assetChainlinkPrice = uint256(assetChainlinkPriceInt) * 10**(18 - IPrice(_chainlinkFeed).decimals());

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/utils/TradingLibrary.sol#L115

### [N&#x2011;05]  Event is missing `indexed` fields
Index event fields make the field more quickly accessible [to off-chain tools](https://ethereum.stackexchange.com/questions/40396/can-somebody-please-explain-the-concept-of-event-indexing) that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each `event` should use three `indexed` fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

*There are 21 instances of this issue:*

```solidity
File: contracts/BondNFT.sol

/// @audit (valid but excluded finding)
377:      event Distribution(address _tigAsset, uint256 _amount);

/// @audit (valid but excluded finding)
378:      event Lock(address _tigAsset, uint256 _amount, uint256 _period, address _owner, uint256 _id);

/// @audit (valid but excluded finding)
379:      event ExtendLock(uint256 _period, uint256 _amount, address _owner, uint256 _id);

/// @audit (valid but excluded finding)
380:      event Release(address _tigAsset, uint256 _amount, address _owner, uint256 _id);

/// @audit (valid but excluded finding)
381:      event ClaimFees(address _tigAsset, uint256 _amount, address _claimer, uint256 _id);

/// @audit (valid but excluded finding)
382:      event ClaimDebt(address _tigAsset, uint256 _amount, address _owner);

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L377

```solidity
File: contracts/GovNFT.sol

/// @audit (valid but excluded finding)
24:       event MessageFailed(uint16 _srcChainId, bytes _srcAddress, uint64 _nonce, bytes _payload, bytes _reason);

/// @audit (valid but excluded finding)
25:       event RetryMessageSuccess(uint16 _srcChainId, bytes _srcAddress, uint64 _nonce, bytes32 _payloadHash);

/// @audit (valid but excluded finding)
26        event ReceiveNFT(
27            uint16 _srcChainId,
28            address _from,
29            uint256[] _tokenId
30:       );

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L24

```solidity
File: contracts/PairsContract.sol

/// @audit (valid but excluded finding)
196       event AssetAdded(
197           uint _asset,
198           string _name
199:      );

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L196-L199

```solidity
File: contracts/Referrals.sol

/// @audit (valid but excluded finding)
85:       event ReferralCreated(address _referrer, bytes32 _hash);

/// @audit (valid but excluded finding)
86:       event Referred(address _referredTrader, bytes32 _hash);

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Referrals.sol#L85

```solidity
File: contracts/Trading.sol

/// @audit (valid but excluded finding)
983       event PositionOpened(
984           TradeInfo _tradeInfo,
985           uint _orderType,
986           uint _price,
987           uint _id,
988           address _trader,
989           uint _marginAfterFees
990:      );

/// @audit (valid but excluded finding)
992       event PositionClosed(
993           uint _id,
994           uint _closePrice,
995           uint _percent,
996           uint _payout,
997           address _trader,
998           address _executor
999:      );

/// @audit (valid but excluded finding)
1001      event PositionLiquidated(
1002          uint _id,
1003          address _trader,
1004          address _executor
1005:     );

/// @audit (valid but excluded finding)
1007      event LimitOrderExecuted(
1008          uint _asset,
1009          bool _direction,
1010          uint _openPrice,
1011          uint _lev,
1012          uint _margin,
1013          uint _id,
1014          address _trader,
1015          address _executor
1016:     );

/// @audit (valid but excluded finding)
1018      event UpdateTPSL(
1019          uint _id,
1020          bool _isTp,
1021          uint _price,
1022          address _trader
1023:     );

/// @audit (valid but excluded finding)
1025      event LimitCancelled(
1026          uint _id,
1027          address _trader
1028:     );

/// @audit (valid but excluded finding)
1030      event MarginModified(
1031          uint _id,
1032          uint _newMargin,
1033          uint _newLeverage,
1034          bool _isMarginAdded,
1035          address _trader
1036:     );

/// @audit (valid but excluded finding)
1038      event AddToPosition(
1039          uint _id,
1040          uint _newMargin,
1041          uint _newPrice,
1042          address _trader
1043:     );

/// @audit (valid but excluded finding)
1045      event FeesDistributed(
1046          address _tigAsset,
1047          uint _daoFees,
1048          uint _burnFees,
1049          uint _refFees,
1050          uint _botFees,
1051          address _referrer
1052:     );

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L983-L990
