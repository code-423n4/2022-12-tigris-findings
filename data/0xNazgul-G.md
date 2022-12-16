## [NAZ-G1] Rearranging A Function To Fail Sooner
**Context**: [`PairsContract.sol#L73-L85`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L73-L85)

**Description**:
Currently this function stores the new `_maxLeverage && _minLeverage` values costing a possible two `sstores` then has a check if `_idToAsset[_asset].maxLeverage >= _idToAsset[_asset].minLeverage` costing two `sloads`. Rearranging this function to check the new `_maxLeverage && _minLeverage` values before setting them would cause it to fail sooner and save gas.

**Recommendation**: 
Consider rearranging the function like so:
```js
    function updateAssetLeverage(uint256 _asset, uint256 _minLeverage, uint256 _maxLeverage) external onlyOwner {
        bytes memory _name  = bytes(_idToAsset[_asset].name);
        require(_name.length > 0, "!Asset");
  	require(_maxLeverage >= _minLeverage, "Wrong leverage values"); //@audit add this

        if (_maxLeverage > 0) {
            _idToAsset[_asset].maxLeverage = _maxLeverage;
        }
        if (_minLeverage > 0) {
            _idToAsset[_asset].minLeverage = _minLeverage;
        }
        
        //require(_idToAsset[_asset].maxLeverage >= _idToAsset[_asset].minLeverage, "Wrong leverage values"); @audit remove this
    }

```
