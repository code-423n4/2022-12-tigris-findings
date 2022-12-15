## Not checking address

StableToken 

```solidity
    function setMinter(
        address _address,
        bool _status
    ) 
        public
        onlyOwner()
    {
        isMinter[_address] = _status;
    }
```

BondNFT

```solidity
    function setManager(
        address _manager
    ) public onlyOwner() {
        manager = _manager;
    }

    function addAsset(address _asset) external onlyOwner {
        require(assets.length == 0 || assets[assetsIndex[_asset]] != _asset, "Already added");
        assetsIndex[_asset] = assets.length;
        assets.push(_asset);
        allowedAsset[_asset] = true;
        epoch[_asset] = block.timestamp/DAY;
    }
```