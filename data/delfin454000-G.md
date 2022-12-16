### Avoid storage of uints or ints smaller than 32 bytes, if possible
Storage of uints or ints smaller than 32 bytes incurs overhead. Instead, use size of at least 32, then downcast where needed
___
[GovNFT.sol: L114](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L114)
```solidity
    function setTrustedAddress(uint16 _chainId, address _contract, bool _bool) external onlyOwner {
```
Similarly for the following additional instances of `uint16 _chainId':

[ILayerZeroEndpoint.sol: L78](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/ILayerZeroEndpoint.sol#L78)

[ILayerZeroUserApplicationConfig.sol: L11](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/ILayerZeroUserApplicationConfig.sol#L11)
___
[GovNFT.sol: L124-124](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L124-L125)
```solidity
    function crossChain(
        uint16 _dstChainId,
```
Similarly for the following additional instances of `uint16 _dstChainId':

[GovNFT.sol: L219-220](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L219-L220)

[ILayerZeroEndpoint.sol: L15](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/ILayerZeroEndpoint.sol#L15)

[ILayerZeroEndpoint.sol: L33](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/ILayerZeroEndpoint.sol#L33)

[ILayerZeroEndpoint.sol: L41](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/ILayerZeroEndpoint.sol#L41)
___
[GovNFT.sol: L143](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L143)
```solidity
        uint16 version = 1;
```
___
[GovNFT.sol: L168-169](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L168-L169)
```solidity
    function lzReceive(
        uint16 _srcChainId,
```
Similarly for the following additional instances of `uint16 _srcChainId':

[GovNFT.sol: L183](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L183)

[GovNFT.sol: L189](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L189)

[GovNFT.sol: L206](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L206)

[ILayerZeroEndpoint.sol: L24](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/ILayerZeroEndpoint.sol#L24)

[ILayerZeroEndpoint.sol: L29](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/ILayerZeroEndpoint.sol#L29)

[ILayerZeroEndpoint.sol: L50](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/ILayerZeroEndpoint.sol#L50)

[ILayerZeroEndpoint.sol: L55](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/ILayerZeroEndpoint.sol#L55)

[ILayerZeroReceiver.sol: L11](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/ILayerZeroReceiver.sol#L11)

[ILayerZeroUserApplicationConfig.sol: L24](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/ILayerZeroUserApplicationConfig.sol#L24)
___
[ILayerZeroEndpoint.sol: L78](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/ILayerZeroEndpoint.sol#L78)
```solidity
    function getConfig(uint16 _version, uint16 _chainId, address _userApplication, uint _configType) external view returns (bytes memory);
```
Similarly for the following additional instances of `uint16 _version':

[ILayerZeroUserApplicationConfig.sol: L11](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/ILayerZeroUserApplicationConfig.sol#L11)

[ILayerZeroUserApplicationConfig.sol: L15](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/ILayerZeroUserApplicationConfig.sol#L15)

[ILayerZeroUserApplicationConfig.sol: L19](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/ILayerZeroUserApplicationConfig.sol#L19)
___
___

### Avoid use of '&&' within a `require` function
Splitting into separate `require()` statements saves gas
___
[Trading.sol: L887](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L887)
```solidity
            require(_proxy.proxy == _msgSender() && _proxy.time >= block.timestamp, "Proxy not approved");
```
Recommendation:
```solidity
            require(_proxy.proxy == _msgSender(), "Proxy not approved");
            require(_proxy.time >= block.timestamp, "Proxy not approved");
```
___
[TradingLibrary.sol: L116-119](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/utils/TradingLibrary.sol#L116-L119)
```solidity
                require(
                    _priceData.price < assetChainlinkPrice+assetChainlinkPrice*2/100 &&
                    _priceData.price > assetChainlinkPrice-assetChainlinkPrice*2/100, "!chainlinkPrice"
                );
```
Recommendation:
```solidity
                require(_priceData.price < assetChainlinkPrice+assetChainlinkPrice*2/100, "!chainlinkPrice");
                require(_priceData.price > assetChainlinkPrice-assetChainlinkPrice*2/100, "!chainlinkPrice");
```
___
[PairsContract.sol: L52](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/PairsContract.sol#L52)
```solidity
        require(_maxLeverage >= _minLeverage && _minLeverage > 0, "Wrong leverage values");
```
Recommendation:
```solidity
        require(_maxLeverage >= _minLeverage, "Wrong leverage values");
        require(_minLeverage > 0, "Wrong leverage values");
```
___
___

### Avoid use of default "checked" behavior in a `for` loop
Underflow/overflow checks are made every time `++i` (or `i++` or equivalent counter) is called. Such checks are unnecessary since `i` is already limited. Therefore, use `unchecked {++i}`/`unchecked {i++}` instead, as shown below:

___
[Position.sol: L296-298](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L296-L298)
```solidity
        for (uint i=0; i<_ids.length; i++) {
            _ids[i] = tokenOfOwnerByIndex(_user, i);
        }
```
Suggestion:
```solidity
        for (uint i=0; i< _ids.length;) {
            _ids[i] = tokenOfOwnerByIndex(_user, i);

            unchecked {
              i++;
          }
        }
```
___
Similarly, use `unchecked` for the following `for` loops:

**Position.sol**

[L304-306](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L304-L306)

**Referrals.sol**

[L70-72](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Referrals.sol#L70-L72), [L73-75](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Referrals.sol#L73-L75)

**GovNFT.sol**

[L53-55](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L53-L55), [L67-69](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L67-L69), [L78-82](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L78-L82), [L95-100](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L95-L100)

[L105-107](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L105-L107), [L131-135](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L131-L135), [L200-202](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L200-L202), [L246-248](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L246-L248)

[L252-254](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L252-L254), [L258-260](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L258-L260), [L325-327](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L325-L327)

**Lock.sol**

[L113-119](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Lock.sol#L113-L119)

**Position.sol**

[L296-298](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L296-L298), [L304-306](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L304-L306)

**Referrals.sol**

[L70-72](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Referrals.sol#L70-L72), [L73-75](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Referrals.sol#L73-L75)

**GovNFT.sol**

[L53-55](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L53-L55), [L67-69](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L67-L69), [L78-82](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L78-L82), [L95-100](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L95-L100)

[L105-107](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L105-L107), [L131-135](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L131-L135), [L200-202](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L200-L202), [L246-248](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L246-L248)

[L252-254](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L252-L254), [L258-260](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L258-L260), [L325-327](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L325-L327)

**Lock.sol**

[L113-119](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Lock.sol#L113-L119)
___
___
