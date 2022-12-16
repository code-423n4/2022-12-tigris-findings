## Missing `address(0)` checks 

There are variables in the following contracts that could be set as `address(0)` by mistake. Despite they could be fixed later by owner, it's a good practice to check for `address(0)`.

[`StableToken.sol#L39`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableToken.sol#L39)

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


[`BondNFT.sol#L367`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L367)

```solidity
    function setManager(
        address _manager
    ) public onlyOwner() {
        manager = _manager;
    }
```


[`BondNFT.sol#L350`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L350)
```solidity
    function addAsset(address _asset) external onlyOwner {
        require(assets.length == 0 || assets[assetsIndex[_asset]] != _asset, "Already added");
        assetsIndex[_asset] = assets.length;
        assets.push(_asset);
        allowedAsset[_asset] = true;
        epoch[_asset] = block.timestamp/DAY;
    }
```

[`GovNFT.sol#L115`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L115)

```solidity
    function setTrustedAddress(uint16 _chainId, address _contract, bool _bool) external onlyOwner {
        isTrustedAddress[_chainId][_contract] = _bool;
    }
```

*Recommendation:* Consider inserting `address(0)` check:

```solidity
require(address(_VARIABLE_TO_BE_CHECKED) != address(0), "Address cannot be zero");
```