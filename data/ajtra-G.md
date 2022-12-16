# Index
1. Require instead of &&
2. I = I + (-) X is cheaper in gas cost than I += (-=) X
3. Require / Revert strings longer than 32 bytes cost extra gas
4. Use unchecked in for/while loops when it's not possible to overflow
5. Using storage instead of memory for structs/arrays
6. Unchecked operations in getPrice() when it's not possible to overflow/underflow
7. Declare variables when are going to be use in BondNFT.extendLock
8. Optimize TradingLibrary.pnl
9. Optimize Trading.executeLimitOrder
# Details

## 1. Require instead of &&
### Description
Split of conditions of an require sentence in different requires sentences can save gas

### Lines in the code

[Trading.sol#L887](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L887)
[PairsContract.sol#L52](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/PairsContract.sol#L52)
[TradingLibrary.sol#L116-L119](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/utils/TradingLibrary.sol#L116-L119)

## 2. I = I + (-) X is cheaper in gas cost than I += (-=) X
### Description
When we are working with state variables is cheaper to use I = I + X than I += X.

### Lines in the code

[Position.sol#L102](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L102)
[Position.sol#L104](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L104)
[Position.sol#L107](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L107)
[Position.sol#L109](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L109)
[Position.sol#L231](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L231)
[Position.sol#L232](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L232)
[PairsContract.sol#L156](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/PairsContract.sol#L156)
[PairsContract.sol#L160](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/PairsContract.sol#L160)
[PairsContract.sol#L176](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/PairsContract.sol#L176)
[PairsContract.sol#L180](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/PairsContract.sol#L180)
[GovNFT.sol#L52](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L52)
[GovNFT.sol#L54](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L54)
[GovNFT.sol#L68](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L68)
[GovNFT.sol#L79](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L79)
[GovNFT.sol#L80](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L80)
[GovNFT.sol#L81](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L81)
[GovNFT.sol#L96](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L96)
[GovNFT.sol#L97](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L97)
[GovNFT.sol#L98](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L98)
[GovNFT.sol#L99](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L99)
[GovNFT.sol#L278](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L278)
[GovNFT.sol#L290](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L290)
[Lock.sol#L73](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Lock.sol#L73)
[Lock.sol#L103](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Lock.sol#L103)
[BondNFT.sol#L68](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L68)
[BondNFT.sol#L115](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L115)
[BondNFT.sol#L117](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L117)
[BondNFT.sol#L119](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L119)
[BondNFT.sol#L150](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L150)
[BondNFT.sol#L152](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L152)
[BondNFT.sol#L180](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L180)
[BondNFT.sol#L183](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L183)
[BondNFT.sol#L221](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L221)
[BondNFT.sol#L225](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L225)
[BondNFT.sol#L333](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L333)
[BondNFT.sol#L334](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L334)


## 3. Require / Revert strings longer than 32 bytes cost extra gas
### Description
Each extra memory word of bytes past the original 32 incurs an MSTORE which costs 3 gas

### Lines in the code

[GovNFT.sol#L153](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L153)
[GovNFT.sol#L185](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L185)
[GovNFT.sol#L209](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L209)
[GovNFT.sol#L210](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L210)


## 4. Use unchecked in for/while loops when it's not possible to overflow
### Description
Use unchecked { i++; } or unchecked{ ++i; } when it's not possible to overflow to save gas.

### Lines in the code

[Position.sol#L296](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L296)
[Position.sol#L304](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L304)
[Referrals.sol#L70](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Referrals.sol#L70)
[Referrals.sol#L73](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Referrals.sol#L73)
[GovNFT.sol#L53](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L53)
[GovNFT.sol#L67](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L67)
[GovNFT.sol#L78](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L78)
[GovNFT.sol#L95](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L95)
[GovNFT.sol#L105](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L105)
[GovNFT.sol#L131](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L131)
[GovNFT.sol#L200](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L200)
[GovNFT.sol#L246](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L246)
[GovNFT.sol#L252](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L252)
[GovNFT.sol#L258](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L258)
[GovNFT.sol#L325](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L325)
[Lock.sol#L113](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Lock.sol#L113)
[BondNFT.sol#L220](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L220)
[BondNFT.sol#L284](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L284)
[BondNFT.sol#L292](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L292)
[BondNFT.sol#L300](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L300)
[BondNFT.sol#L342](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L342)

## 5. Using storage instead of memory for structs/arrays
### Description
When retrieving data from a memory location, assigning the data to a memory variable causes all fields of the struct/array to be read from memory, 
resulting in a Gcoldsload (2100 gas) for each field of the struct/array. 
When reading fields from new memory variables, they cause an extra MLOAD instead of a cheap stack read. Rather than declaring a variable with the memory keyword, 
it is much cheaper to declare a variable with the storage keyword and cache all fields that need to be read again in a stack variable, 
because the fields actually read will only result in a Gcoldsload. 
The only case where the entire struct/array is read into a memory variable is when the entire struct/array is returned by a function, passed to a function that needs memory, 
or when the array/struct is read from another store array/struc

### Lines in the code

[Trading.sol#L859](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L859)
[Trading.sol#L886](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L886)
[Position.sol#L42](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L42)
[BondNFT.sol#L251](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L251)

## 6. Unchecked operations in getPrice() when it's not possible to overflow/underflow
### Description
Use unchecked when in a arithmetic operation is not possible to overflow due to an if or require statement.

```solidity
        if (_longOi > _shortOi) {
            _oiDelta = int256(_longOi)-int256(_shortOi);
        } else {
            _oiDelta = int256(_shortOi)-int256(_longOi);
        }
```
[Position.sol#L115-L117](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L115-L117)

## 7. Declare variables when are going to be use in BondNFT.extendLock

```diff
    function extendLock(
        uint _id,
        address _asset,
        uint _amount,
        uint _period,
        address _sender
    ) external onlyManager() {
        Bond memory bond = idToBond(_id);
-       Bond storage _bond = _idToBond[_id];
        require(bond.owner == _sender, "!owner");
        require(!bond.expired, "Expired");
        require(bond.asset == _asset, "!BondAsset");
        require(bond.pending == 0);
        require(epoch[bond.asset] == block.timestamp/DAY, "Bad epoch");
        require(bond.period+_period <= 365, "MAX PERIOD");
        unchecked {
            uint shares = (bond.amount + _amount) * (bond.period + _period) / 365;
            uint expireEpoch = block.timestamp/DAY + bond.period + _period;
            totalShares[bond.asset] += shares-bond.shares;
+       	Bond storage _bond = _idToBond[_id];
            _bond.shares = shares;
            _bond.amount += _amount;
            _bond.expireEpoch = expireEpoch;
            _bond.period += _period;
            _bond.mintTime = block.timestamp;
            _bond.mintEpoch = epoch[bond.asset];
            bondPaid[_id][bond.asset] = accRewardsPerShare[bond.asset][epoch[bond.asset]] * _bond.shares / 1e18;
        }
        emit ExtendLock(_period, _amount, _sender,  _id);
    }
```
[BondNFT.sol#L97-L125](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L97-L125)

# 8. Optimize TradingLibrary.pnl

```diff
    function pnl(bool _direction, uint _currentPrice, uint _price, uint _margin, uint _leverage, int256 accInterest) external pure returns (uint256 _positionSize, int256 _payout) {
        unchecked {
            uint _initPositionSize = _margin * _leverage / 1e18;
-           if (_direction && _currentPrice >= _price) {
-               _payout = int256(_margin) + int256(_initPositionSize * (1e18 * _currentPrice / _price - 1e18)/1e18) + accInterest;
-           } else if (_direction && _currentPrice < _price) {
-               _payout = int256(_margin) - int256(_initPositionSize * (1e18 - 1e18 * _currentPrice / _price)/1e18) + accInterest;
+           if (_direction) {
+				if (_currentPrice >= _price){
+					_payout = int256(_margin) + int256(_initPositionSize * (1e18 * _currentPrice / _price - 1e18)/1e18) + accInterest;
+				} else {
+					_payout = int256(_margin) - int256(_initPositionSize * (1e18 - 1e18 * _currentPrice / _price)/1e18) + accInterest;
+				}
            } else if (!_direction && _currentPrice <= _price) {
                _payout = int256(_margin) + int256(_initPositionSize * (1e18 - 1e18 * _currentPrice / _price)/1e18) + accInterest;
            } else {
                _payout = int256(_margin) - int256(_initPositionSize * (1e18 * _currentPrice / _price - 1e18)/1e18) + accInterest;
            }
            _positionSize = _initPositionSize * _currentPrice / _price;
        }
    }
```
[TradingLibrary.sol#L36-L50](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/utils/TradingLibrary.sol#L36-L50)

# 9. Optimize Trading.executeLimitOrder

```diff
            if(trade.direction) {
                trade.price += trade.price * _spread / DIVISION_CONSTANT;
+				tradingExtension.modifyLongOi(trade.asset, trade.tigAsset, true, trade.margin*trade.leverage/1e18);
            } else {
                trade.price -= trade.price * _spread / DIVISION_CONSTANT;
+				tradingExtension.modifyShortOi(trade.asset, trade.tigAsset, true, trade.margin*trade.leverage/1e18);
            }
-            if (trade.direction) {
-                tradingExtension.modifyLongOi(trade.asset, trade.tigAsset, true, trade.margin*trade.leverage/1e18);
-            } else {
-                tradingExtension.modifyShortOi(trade.asset, trade.tigAsset, true, trade.margin*trade.leverage/1e18);
-            }
```
[Trading.sol#L508-L517](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L508-L517)
