## Low

### Large unchecked block throughout the codebase

It is very risky to have large unchecked block throughout the codebase. Invariant need to be checked to make sure none of them can overflow and cause unexpected behavior.

## Non Critical

### Constant should be named all cap

Rename liqPercent to LIQ_PERCENT

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L96

```solidity
    uint private constant liqPercent = 9e9; // 90%
```

### Lack events in many setter

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L939-L942

```solidity
    function setLimitOrderPriceRange(uint _range) external onlyOwner {
        limitOrderPriceRange = _range;
    }

```

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L349-L355

```solidity
    function addAsset(address _asset) external onlyOwner {
        require(assets.length == 0 || assets[assetsIndex[_asset]] != _asset, "Already added");
        assetsIndex[_asset] = assets.length;
        assets.push(_asset);
        allowedAsset[_asset] = true;
        epoch[_asset] = block.timestamp/DAY;
    }
```

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L357-L370

```solidity
    function setAllowedAsset(address _asset, bool _bool) external onlyOwner {
        require(assets[assetsIndex[_asset]] == _asset, "Not added");
        allowedAsset[_asset] = _bool;
    }

    function setBaseURI(string calldata _newBaseURI) external onlyOwner {
        baseURI = _newBaseURI;
    }

    function setManager(
        address _manager
    ) public onlyOwner() {
        manager = _manager;
    }
```

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L975-L979

```solidity
    function setTradingExtension(
        address _ext
    ) external onlyOwner() {
        tradingExtension = ITradingExtension(_ext);
    }
```

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L952-L969

```solidity
    function setFees(bool _open, uint _daoFees, uint _burnFees, uint _referralFees, uint _botFees, uint _percent) external onlyOwner {
        unchecked {
            require(_daoFees >= _botFees+_referralFees*2);
            if (_open) {
                openFees.daoFees = _daoFees;
                openFees.burnFees = _burnFees;
                openFees.referralFees = _referralFees;
                openFees.botFees = _botFees;
            } else {
                closeFees.daoFees = _daoFees;
                closeFees.burnFees = _burnFees;
                closeFees.referralFees = _referralFees;
                closeFees.botFees = _botFees;                
            }
            require(_percent <= DIVISION_CONSTANT);
            vaultFundingPercent = _percent;
        }
    }
```