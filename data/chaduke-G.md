G1. https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L84
Instead of checking the state variables, it is more gas saving to check the arguments instead:
```
function updateAssetLeverage(uint256 _asset, uint256 _minLeverage, uint256 _maxLeverage) external onlyOwner {
        bytes memory _name  = bytes(_idToAsset[_asset].name);
        require(_name.length > 0, "!Asset");
        require(_inLeverage > 0 && _minLeverage < _maxLeverage, "Wrong leverage values");
        _idToAsset[_asset].maxLeverage = _maxLeverage;
        _idToAsset[_asset].minLeverage = _minLeverage;
  }
```