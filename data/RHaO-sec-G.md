# [G-1] Bytes constants are more efficient than string constants.
If data can fit into 32 bytes, then you should use bytes32 datatype rather than bytes or strings as it is cheaper in solidity.

[contracts/BondNFT.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L41-L43)

41:         string memory _setBaseURI,

42:         string memory _name,

43:         string memory _symbol
[contracts/BondNFT.sol#L28](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L28)
[contracts/BondNFT.sol#L362](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L362)


[contracts/GovNFT.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L34-L36)

34:         string memory _setBaseURI,

35:         string memory _name,

36:         string memory _symbol


[contracts/Position.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L74-L85)



 
    string public baseURI;

    constructor(string memory _setBaseURI, string memory _name, string memory _symbol) ERC721(_name, _symbol) {
        baseURI = _setBaseURI;
        _tokenIds.increment();
    }

    function _baseURI() internal override view returns (string memory) {
        return baseURI;
    }

    function setBaseURI(string memory _newBaseURI) external onlyOwner {


[contracts/PairsContract.sol#L48](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L48)
[contracts/PairsContract.sol#L198](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L198)


[contracts/StableToken.sol#L11](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableToken.sol#L11)

[contracts/interfaces/IPairsContract.sol#L8](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPairsContract.sol#L8)


# [G-2]  BondNFT.extendLock(): Bond should be cached

[contracts/BondNFT.sol#L106-L111](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L106-L111)
 
```
  function extendLock(
        uint _id,
        address _asset,
        uint _amount,
        uint _period,
        address _sender
    ) external onlyManager() {
        Bond memory bond = idToBond(_id);
        Bond storage _bond = _idToBond[_id];
        require(bond.owner == _sender, "!owner");//@audit gas: should cache "bond" (SLOAD 1)
        require(!bond.expired, "Expired");
        require(bond.asset == _asset, "!BondAsset");//@audit gas: should cache "bond" (SLOAD 2)
        require(bond.pending == 0);//@audit gas: should cache "bond" (SLOAD 3)
        require(epoch[bond.asset] == block.timestamp/DAY, "Bad epoch");
        require(bond.period+_period <= 365, "MAX PERIOD");//@audit gas: should cache "bond" (SLOAD 4)

```

# [G-3] Lock.lock(): _period should be cached

[contracts/Lock.sol#L66-67](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L66-67)

    function lock(
        address _asset,
        uint _amount,
        uint _period
    ) public {
        require(_period <= maxPeriod, "MAX PERIOD");//@audit gas: should cache "_period" (SLOAD 1)
        require(_period >= minPeriod, "MIN PERIOD");//@audit gas: should cache "_period" (SLOAD 2)


# [G-4] TradingLibrary.verifyPrice(): _priceData and block.timestamp should be cached

[contracts/utils/TradingLibrary.sol#L109-L110](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L109-L110)

        require(block.timestamp >= _priceData.timestamp, "FutSig");
        require(block.timestamp <= _priceData.timestamp + _validSignatureTimer, "ExpSig");

[TradingLibrary.sol#L111](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol)

111.    require(_priceData.price > 0, "NoPrice");
        if (_chainlinkEnabled && _chainlinkFeed != address(0)) {
            int256 assetChainlinkPriceInt = IPrice(_chainlinkFeed).latestAnswer();
            if (assetChainlinkPriceInt != 0) {
                uint256 assetChainlinkPrice = uint256(assetChainlinkPriceInt) * 10**(18 - IPrice(_chainlinkFeed).decimals());
117.                 require(
                    _priceData.price < assetChainlinkPrice+assetChainlinkPrice*2/100 &&
                    _priceData.price > assetChainlinkPrice-assetChainlinkPrice*2/100, "!chainlinkPrice"


# [G-5]  Structs can be packed into fewer storage slots [Ref](https://dev.to/javier123454321/solidity-gas-optimizations-pt-3-packing-structs-23f4)


[contracts/interfaces/ITrading.sol#L9-L19]( https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ITrading.sol#L9-L19)

[contracts/interfaces/IPosition.sol#L22-L32](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPosition.sol#L22-L32)

[contracts/utils/TradingLibrary.sol#L12-L18](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L12-L18)

# [G-6] x = x + y is more efficient, than x += y

[contracts/GovNFT.sol#L76-L99](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L76-L99)

[contracts/Position.sol#L230-L232](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L230-L232)

[contracts/PairsContract.sol#L156](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L156)
[contracts/PairsContract.sol#L160](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L160)


[contracts/Lock.sol#L103](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L73)
[contracts/Lock.sol#L103](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L103)



# [G-7]  Use a more recent version of solidity[ In all sol files except  [IBondNFT.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IBondNFT.sol) ]

Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

# [G-8] Splitting require() statements that use && saves gas

instead of using operator && on single require check (PairsContract.sol#L52). using double require check can save more gas:

[contracts/PairsContract.sol#L52](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L52)



