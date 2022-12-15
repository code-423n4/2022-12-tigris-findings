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

G2. https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/PairsContract.sol#L73-L85
Checking arguments rather than the state variables `` _idToAsset[_asset].maxLeverage >= _idToAsset[_asset].minLeverage`` can save gas:
``
function updateAssetLeverage(uint256 _asset, uint256 _minLeverage, uint256 _maxLeverage) external onlyOwner {
        bytes memory _name  = bytes(_idToAsset[_asset].name);
        require(_name.length > 0, "!Asset");
        require(_minLeverage > 0 && _maxLeverage >= _minLeverage, "Wrong leverage values");


       _idToAsset[_asset].maxLeverage = _maxLeverage;
       _idToAsset[_asset].minLeverage = _minLeverage;
}

``