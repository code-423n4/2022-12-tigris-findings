## QA Report - low risk

### Function `latestAnswer` is deprecated
Function `latestAnswer`, used in `TradingLibrary.sol`, is deprecated (see [API Reference](https://web.archive.org/web/20210304160150/https://docs.chain.link/docs/deprecated-aggregatorinterface-api-reference#config))

[TradingLibrary.sol: L8](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/utils/TradingLibrary.sol#L8)
```solidity
    function latestAnswer() external view returns (int256);
```
___
___

### `pragma solidity` version should be upgraded to latest available version before finalization

The current solidity version in most of the contracts is `^0.8.0` (it is `^0.8.9` in IBondNFT.sol), compared to the latest version of `0.8.17`. Only the latest version receives security fixes. Since the latest version often has bugs, it's safer to use releases a few versions older at first. 

However, the version used here is too old: a version of at least 0.8.12 is needed for `string.concat` to be used instead of `abi.encodePacked`, 0.8.13 is required to get the ability to use `using for` with a list of free functions, 0.8.2 is needed to get simple compiler automatic inlining, 0.8.3 is needed to get better struct packing and cheaper multiple storage reads (gas savings), 0.8.4 is required to use custom errors (gas savings), and 0.8.10 is needed to have external calls skip contract existence checks if the external call has a return value
___
___


## QA Report: non-critical

### Adding a return statement when a function defines a named return variable is redundant
Below, function `_handleCloseFees` defines named return variable `payout_` —

[Trading.sol: L762-771](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L762-L771)
```solidity
    function _handleCloseFees(
        uint _asset,
        uint _payout,
        address _tigAsset,
        uint _positionSize,
        address _trader,
        bool _isBot
    )
        internal
        returns (uint payout_)
```
and an unnecesssary return statement for `payout_` is also given at the end of the function:

[Trading.sol: L809](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L809)
```solidity
        return payout_;
```
___
___


### Inconsistent `returns` syntax
While most functions incorporating `returns` have a space after `returns` (as recommended), some are missing the space. Below, both syntax types occur within a few lines of code (in function `release` and function `ownerOf`). The syntax should be made consistent throughout

[IBondNFT.sol: L30-40](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/IBondNFT.sol#L30-L40)
```solidity
    function release(
        uint _id,
        address _releaser
    ) external returns(uint amount, uint lockAmount, address asset, address _owner);

    function distribute(
        address _tigAsset,
        uint _amount
    ) external;

    function ownerOf(uint _id) external view returns (uint256);
```
The spacing should be corrected for the following `returns`:

**Trading.sol**

[L20](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L20), [L23](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L23), [L29](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L29), [L30](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L30), [L43](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L43), [L49](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L49)

**TradingExtension.sol**

[L47](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/TradingExtension.sol#L47), [L93](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/TradingExtension.sol#L93), [L150](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/TradingExtension.sol#L150), [L170](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/TradingExtension.sol#L170)

**BondNFT.sol**

[L62](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L62), [L140](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L140), [L171](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L171), [L199](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L199)

**IBondNFT.sol**

[L10](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/IBondNFT.sol#L10), [L23](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/IBondNFT.sol#L23), [L28](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/IBondNFT.sol#L28), [L33](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/IBondNFT.sol#L33)
___
___


### Long single-line comments 
In theory, comments over 80 characters should wrap using multi-line comment syntax. Even if longer comments (up to 120 characters) are becoming acceptable and a scroll bar is provided, very long comments can interfere with readability. Below are five instances of extra-long comments whose readability could be improved by wrapping, as shown. Note that a small indentation is used in each continuation line (which flags for the reader that the comment is ongoing), along with a period at the close (to signal the end of the comment).
___
[Trading.sol: L579](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L579)
```solidity
     * @notice Trader can approve a proxy wallet address for it to trade on its behalf. Can also provide proxy wallet with gas.
```
Suggestion:
```solidity
     * @notice Trader can approve a proxy wallet address for it to trade on its behalf — 
     *   can also provide proxy wallet with gas.
```
___
[Trading.sol: L853](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L853)
```solidity
     * @dev This is to prevent profitable opening and closing in the same tx with two different prices in the "valid signature pool".
```
Suggestion:
```solidity
     * @dev This is to prevent profitable opening and closing in the same tx 
     *   with two different prices in the "valid signature pool".
```
___
[ILayerZeroEndpoint.sol: L12](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/ILayerZeroEndpoint.sol#L12)
```solidity
    // @param _refundAddress - if the source transaction is cheaper than the amount of value passed, refund the additional amount to this address
```
Suggestion:
```solidity
    // @param _refundAddress - if the source transaction is cheaper than the amount
    //   of value passed, refund the additional amount to this address.
```
___
[ILayerZeroEndpoint.sol: L14](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/ILayerZeroEndpoint.sol#L14)
```solidity
    // @param _adapterParams - parameters for custom functionality. e.g. receive airdropped native gas from the relayer on destination
```
Suggestion:
```solidity
    // @param _adapterParams - parameters for custom functionality — e.g. receive  
    //   airdropped native gas from the relayer on destination.
```
___
[BondNFT.sol: L35](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L35)
```solidity
    mapping(address => mapping(uint256 => uint256)) private accRewardsPerShare; // tigAsset => epoch => accRewardsPerShare
```
Suggestion:
```solidity
    // tigAsset => epoch => accRewardsPerShare
    mapping(address => mapping(uint256 => uint256)) private accRewardsPerShare; 
```
Above, the suggestion is put the comment line before the code, instead of appending it
___
___


### Update sensitive terms in both comments and code
Terms incorporating "black," "white," "slave" or "master" are potentially problematic. Substituting more neutral terminology is becoming [common practice](https://www.zdnet.com/article/mysql-drops-master-slave-and-blacklist-whitelist-terminology/).
___

[Trading.sol: L871](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L871)
```solidity
     * @dev Check that the stablevault input is whitelisted and the margin asset is whitelisted in the vault
```
Suggestion: Change `whitelisted` to `allowlisted` 
___
Similarly for the following instances of `whitelist` and its variants:

[Trading.sol: L908](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L908)

[TradingExtension.sol: L236](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/TradingExtension.sol#L236)

[TradingLibrary.sol: L82](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/utils/TradingLibrary.sol#L82)

[Lock.sol: L123](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Lock.sol#L123)

[BondNFT.sol: L271](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L271)
___
___


### Typos
___
[PairsContract.sol: L45](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L45)
```solidity
     * @param _maxLeverage minimum allowed leverage
```
Change `_maxLeverage` to `_minLeverage`
___
The same typo (`interesr`) occurs in both lines below:

[PairsContract.sol: L151](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L151)

[PairsContract.sol: L171](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L171)
```solidity
     * @param _onOpen true if adding to open interesr
```
Change `interesr` to `interest` in both cases
___
[Lock.sol: L135](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Lock.sol#L135)
```solidity
     * @notice Owner can retreive Gov NFTs
```
Change `retreive` to `retrieve`
___
___


### Use scientific notation for large multiples of 10 rather than decimal literals
For readability, used scientific notation (e.g. 1e6) rather than decimal literals (e.g. 1000000) for large multiples of ten

[TradingExtension.sol: L26](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/TradingExtension.sol#L26)
```solidity
    uint public maxGasPrice = 1000000000000; // 1000 gwei
```
Suggestion: Change `1000000000000` to `1e12`
___
___


### NatSpec partially missing
NatSpec is partially missing for the following `functions`, as shown:
___
[PairsContract.sol: L39-48](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/PairsContract.sol#L39-L48)
```solidity
    /**
     * @dev Add an allowed asset to fetch prices for
     * @param _asset index of the requested asset
     * @param _name name of the asset
     * @param _chainlinkFeed optional address of the respective Chainlink price feed
     * @param _maxLeverage maximimum allowed leverage
     * @param _maxLeverage minimum allowed leverage
     * @param _feeMultiplier percent value that the opening/closing fee is multiplied by in BP
     */
    function addAsset(uint256 _asset, string memory _name, address _chainlinkFeed, uint256 _minLeverage, uint256 _maxLeverage, uint256 _feeMultiplier, uint256 _baseFundingRate) external onlyOwner {
```
Missing: `@param _baseFundingRate`. Also, the second `@param _maxLeverage` should be `@param _minLeverage` (included in the **Typo** section of this submission)
___
[Referrals.sol: L57-65](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Referrals.sol#L57-L65)
```solidity
    /**
    * @notice deprecated
    */
    function initRefs(
        address[] memory _codeOwners,
        bytes32[] memory _ownedCodes,
        address[] memory _referredA,
        bytes32[] memory _referredTo
    ) external onlyOwner {
```
Missing: `@param _codeOwners`, `@param _ownedCodes`, `@param _referredA`, `@param _referredTo`   
___
[GovNFT.sol: L118-129](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L118-L129)
```solidity
    /**
    * @notice used to bridge NFTs crosschain using layer zero
    * @param _dstChainId the layer zero id of the dest chain
    * @param _to receiving address on dest chain
    * @param tokenId array of the ids of the NFTs to be bridged
    */
    function crossChain(
        uint16 _dstChainId,
        bytes memory _destination,
        address _to,
        uint256[] memory tokenId
    ) public payable {
```
Missing: `@param _destination`
___
[GovNFT.sol: L218-225](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L218-L225)
```solidity
    // Endpoint.sol estimateFees() returns the fees for the message
    function estimateFees(
        uint16 _dstChainId,
        address _userApplication,
        bytes calldata _payload,
        bool _payInZRO,
        bytes calldata _adapterParams
    ) external view returns (uint256 nativeFee, uint256 zroFee) {
```
Missing: Multiple `@param' and `@return` statements
___
[StableToken.sol: L35-42](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/StableToken.sol#L35-L42)
```solidity
    /**
     * @dev Sets the status of minter.
     */
    function setMinter(
        address _address,
        bool _status
    ) 
        public
```
Missing: `@param _address` and `@param _status`
___
[ILayerZeroEndpoint.sol: L31-33](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/ILayerZeroEndpoint.sol#L31-L33)
```solidity
    // @notice get the outboundNonce from this source chain which, consequently, is always an EVM
    // @param _srcAddress - the source chain contract address
    function getOutboundNonce(uint16 _dstChainId, address _srcAddress) external view returns (uint64);
```
Missing: `@param _dstChainId` and `@return` 
___
___

NatSpec is missing only one or more `@returns` for the following `functions`, as shown:
___
[StableVault.sol: L60-65](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/StableVault.sol#L60-L65)
```solidity
    /**
    * @notice swap tigAsset to _token
    * @param _token address of the token to receive
    * @param _amount amount of _token
    */
    function withdraw(address _token, uint256 _amount) external returns (uint256 _output) {
```
___

[ILayerZeroEndpoint.sol: L35-41](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/ILayerZeroEndpoint.sol#L35-L41)
```solidity
    // @notice gets a quote in source native gas, for the amount that send() requires to pay for message delivery
    // @param _dstChainId - the destination chain identifier
    // @param _userApplication - the user app address on this EVM chain
    // @param _payload - the custom message to send over LayerZero
    // @param _payInZRO - if false, user app pays the protocol fee in native token
    // @param _adapterParam - parameters for the adapter service, e.g. send some dust native token to dstChain
    function estimateFees(uint16 _dstChainId, address _userApplication, bytes calldata _payload, bool _payInZRO, bytes calldata _adapterParam) external view returns (uint nativeFee, uint zroFee);
```
___

[ILayerZeroEndpoint.sol: L73-78](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/ILayerZeroEndpoint.sol#L73-L78)
```solidity
    // @notice get the configuration of the LayerZero messaging library of the specified version
    // @param _version - messaging library version
    // @param _chainId - the chainId for the pending config change
    // @param _userApplication - the contract address of the user application
    // @param _configType - type of configuration. every messaging library has its own convention.
    function getConfig(uint16 _version, uint16 _chainId, address _userApplication, uint _configType) external view returns (bytes memory);
```
___
[TradingExtension.sol: L41-47](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/TradingExtension.sol#L41-L47)
```solidity
    /**
    * @notice returns the minimum position size per collateral asset
    * @param _asset address of the asset
    */
    function minPos(
        address _asset
    ) external view returns(uint) {
```
___
[TradingLibrary.sol: L70-76](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/utils/TradingLibrary.sol#L70-L76)
```solidity
    /**
    * @notice uses liqPrice() and returns position liquidation price
    * @param _positions positions contract address
    * @param _id position id
    * @param _liqPercent liquidation percent
    */
    function getLiqPrice(address _positions, uint _id, uint _liqPercent) external view returns (uint256) {
```
___
[ILayerZeroEndpoint.sol: L26-29](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/ILayerZeroEndpoint.sol#L26-L29)
```solidity
    // @notice get the inboundNonce of a receiver from a source chain which could be EVM or non-EVM chain
    // @param _srcChainId - the source chain identifier
    // @param _srcAddress - the source chain contract address
    function getInboundNonce(uint16 _srcChainId, bytes calldata _srcAddress) external view returns (uint64);
```
___

[ILayerZeroEndpoint.sol: L43-44](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/ILayerZeroEndpoint.sol#L43-L44)
```solidity
    // @notice get this Endpoint's immutable source identifier
    function getChainId() external view returns (uint16);
```
___
[ILayerZeroEndpoint.sol: L52-55](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/ILayerZeroEndpoint.sol#L52-L55)
```solidity
    // @notice query if any STORED payload (message blocking) at the endpoint.
    // @param _srcChainId - the source chain identifier
    // @param _srcAddress - the source chain contract address
    function hasStoredPayload(uint16 _srcChainId, bytes calldata _srcAddress) external view returns (bool);
```
___
[ILayerZeroEndpoint.sol: L57-59](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/ILayerZeroEndpoint.sol#L57-L59)
```solidity
    // @notice query if the _libraryAddress is valid for sending msgs.
    // @param _userApplication - the user app address on this EVM chain
    function getSendLibraryAddress(address _userApplication) external view returns (address);
```
___

[ILayerZeroEndpoint.sol: L61-63](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/ILayerZeroEndpoint.sol#L61-L63)
```solidity
    // @notice query if the _libraryAddress is valid for receiving msgs.
    // @param _userApplication - the user app address on this EVM chain
    function getReceiveLibraryAddress(address _userApplication) external view returns (address);
```
___
[ILayerZeroEndpoint.sol: L80-82](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/ILayerZeroEndpoint.sol#L80-L82)
```solidity
    // @notice get the send() LayerZero messaging library version
    // @param _userApplication - the contract address of the user application
    function getSendVersion(address _userApplication) external view returns (uint16);
```
___
[ILayerZeroEndpoint.sol: L84-86](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/ILayerZeroEndpoint.sol#L84-L86)
```solidity
    // @notice get the lzReceive() LayerZero messaging library version
    // @param _userApplication - the contract address of the user application
    function getReceiveVersion(address _userApplication) external view returns (uint16);
```
___
___
Note that NatSpec is recommended for `public` and `external` functions, but it is wholly missing for dozens of such functions 
___
___
