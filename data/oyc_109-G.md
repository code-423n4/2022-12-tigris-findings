## [G-01] Don't Initialize Variables with Default Value

Uninitialized variables are assigned with the types default value. Explicitly initializing a variable with it's default value costs unnecesary gas.

```
2022-12-tigris/contracts/BondNFT.sol::284 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/BondNFT.sol::292 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/BondNFT.sol::300 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/BondNFT.sol::342 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/GovNFT.sol::53 => for (uint i=0; i<assetsLength(); i++) {
2022-12-tigris/contracts/GovNFT.sol::67 => for (uint i=0; i<assetsLength(); i++) {
2022-12-tigris/contracts/GovNFT.sol::78 => for (uint i=0; i<assetsLength(); i++) {
2022-12-tigris/contracts/GovNFT.sol::95 => for (uint i=0; i<assetsLength(); i++) {
2022-12-tigris/contracts/GovNFT.sol::105 => for (uint i=0; i<_amount; i++) {
2022-12-tigris/contracts/GovNFT.sol::131 => for (uint i=0; i<tokenId.length; i++) {
2022-12-tigris/contracts/GovNFT.sol::200 => for (uint i=0; i<tokenId.length; i++) {
2022-12-tigris/contracts/GovNFT.sol::246 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/GovNFT.sol::252 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/GovNFT.sol::258 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/GovNFT.sol::325 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::51 => for (uint i=0; i<assetsLength(); i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::59 => for (uint i=0; i<assetsLength(); i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::73 => for (uint i=0; i<assetsLength(); i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::93 => for (uint i=0; i<tokenId.length; i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::160 => for (uint i=0; i<tokenId.length; i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::206 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::212 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::218 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::276 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/Lock.sol::113 => for (uint i=0; i < assets.length; i++) {
2022-12-tigris/contracts/NFTSale.sol::45 => for (uint i=0; i<_amount; i++) {
2022-12-tigris/contracts/NFTSale.sol::48 => for (uint i=0; i<_amount; i++) {
2022-12-tigris/contracts/Position.sol::296 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/Position.sol::304 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/Referrals.sol::70 => for (uint i=0; i<_codeOwnersL; i++) {
2022-12-tigris/contracts/Referrals.sol::73 => for (uint i=0; i<_referredAL; i++) {
```

## [G-02] Cache Array Length Outside of Loop

Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.

```
2022-12-tigris/contracts/BondNFT.sol::284 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/BondNFT.sol::292 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/BondNFT.sol::300 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/BondNFT.sol::342 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/GovNFT.sol::131 => for (uint i=0; i<tokenId.length; i++) {
2022-12-tigris/contracts/GovNFT.sol::200 => for (uint i=0; i<tokenId.length; i++) {
2022-12-tigris/contracts/GovNFT.sol::246 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/GovNFT.sol::252 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/GovNFT.sol::258 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/GovNFT.sol::325 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::93 => for (uint i=0; i<tokenId.length; i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::160 => for (uint i=0; i<tokenId.length; i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::206 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::212 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::218 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::276 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/Lock.sol::113 => for (uint i=0; i < assets.length; i++) {
2022-12-tigris/contracts/Position.sol::296 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/Position.sol::304 => for (uint i=0; i<_ids.length; i++) {
```

## [G-03] Using > 0 costs more gas than != 0 when used on a uint in a require() statement

When dealing with unsigned integer types, comparisons with != 0 are cheaper then with > 0. This change saves 6 gas per instance

```
2022-12-tigris/contracts/GovNFT.sol::130 => require(tokenId.length > 0, "Not bridging");
2022-12-tigris/contracts/GovNFTBridged.sol::92 => require(tokenId.length > 0, "Not bridging");
2022-12-tigris/contracts/PairsContract.sol::35 => require(_name.length > 0, "!Asset");
2022-12-tigris/contracts/PairsContract.sol::51 => require(bytes(_name).length > 0, "No name");
2022-12-tigris/contracts/PairsContract.sol::52 => require(_maxLeverage >= _minLeverage && _minLeverage > 0, "Wrong leverage values");
2022-12-tigris/contracts/PairsContract.sol::75 => require(_name.length > 0, "!Asset");
2022-12-tigris/contracts/PairsContract.sol::94 => require(_name.length > 0, "!Asset");
2022-12-tigris/contracts/PairsContract.sol::106 => require(_name.length > 0, "!Asset");
2022-12-tigris/contracts/PairsContract.sol::117 => require(_name.length > 0, "!Asset");
2022-12-tigris/contracts/PairsContract.sol::141 => require(_name.length > 0, "!Asset");
```

## [G-04] Long Revert Strings

Shortening revert strings to fit in 32 bytes will decrease gas costs for deployment and gas costs when the revert condition has been met.

If the contract(s) in scope allow using Solidity >=0.8.4, consider using Custom Errors as they are more gas efficient while allowing developers to describe the error in detail using NatSpec.

```
2022-12-tigris/contracts/GovNFT.sol::185 => require(msg.sender == address(this), "NonblockingLzApp: caller must be app");
2022-12-tigris/contracts/GovNFT.sol::209 => require(payloadHash != bytes32(0), "NonblockingLzApp: no stored message");
2022-12-tigris/contracts/GovNFT.sol::210 => require(keccak256(_payload) == payloadHash, "NonblockingLzApp: invalid payload");
2022-12-tigris/contracts/GovNFTBridged.sol::145 => require(msg.sender == address(this), "NonblockingLzApp: caller must be app");
2022-12-tigris/contracts/GovNFTBridged.sol::169 => require(payloadHash != bytes32(0), "NonblockingLzApp: no stored message");
2022-12-tigris/contracts/GovNFTBridged.sol::170 => require(keccak256(_payload) == payloadHash, "NonblockingLzApp: invalid payload");
```

## [G-05] Use Shift Right/Left instead of Division/Multiplication if possible

A division/multiplication by any number x being a power of 2 can be calculated by shifting log2(x) to the right/left.

While the DIV opcode uses 5 gas, the SHR opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

```
2022-12-tigris/contracts/GovNFT.sol::175 => (bool success, bytes memory reason) = address(this).excessivelySafeCall(gasleft()*4/5, 150, abi.encodeWithSelector(this.nonblockingLzReceive.selector, _srcChainId, _srcAddress, _nonce, _payload));
2022-12-tigris/contracts/GovNFTBridged.sol::135 => (bool success, bytes memory reason) = address(this).excessivelySafeCall(gasleft()*4/5, 150, abi.encodeWithSelector(this.nonblockingLzReceive.selector, _srcChainId, _srcAddress, _nonce, _payload));
2022-12-tigris/contracts/Trading.sol::717 => _fees.daoFees = _fees.daoFees - _fees.referralFees*2;
2022-12-tigris/contracts/Trading.sol::792 => _daoFeesPaid = _daoFeesPaid-_referralFeesPaid*2;
2022-12-tigris/contracts/Trading.sol::954 => require(_daoFees >= _botFees+_referralFees*2);
```

## [G-06] Using private rather than public for constants, saves gas

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

```
2022-12-tigris/contracts/Faucet.sol::10 => IERC20 public immutable usd;
2022-12-tigris/contracts/Lock.sol::15 => IBondNFT public immutable bondNFT;
2022-12-tigris/contracts/Lock.sol::16 => IGovNFT public immutable govNFT;
2022-12-tigris/contracts/StableVault.sol::33 => address public immutable stable;
```

## [G-07] Functions guaranteed to revert when called by normal users can be marked payable

If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

```
2022-12-tigris/contracts/BondNFT.sol::349 => function addAsset(address _asset) external onlyOwner {
2022-12-tigris/contracts/BondNFT.sol::357 => function setAllowedAsset(address _asset, bool _bool) external onlyOwner {
2022-12-tigris/contracts/BondNFT.sol::362 => function setBaseURI(string calldata _newBaseURI) external onlyOwner {
2022-12-tigris/contracts/GovNFT.sol::46 => function setBaseURI(string calldata _newBaseURI) external onlyOwner {
2022-12-tigris/contracts/GovNFT.sol::104 => function mintMany(uint _amount) external onlyOwner {
2022-12-tigris/contracts/GovNFT.sol::110 => function mint() external onlyOwner {
2022-12-tigris/contracts/GovNFT.sol::114 => function setTrustedAddress(uint16 _chainId, address _contract, bool _bool) external onlyOwner {
2022-12-tigris/contracts/GovNFT.sol::236 => function setGas(uint _gas) external onlyOwner {
2022-12-tigris/contracts/GovNFT.sol::240 => function setEndpoint(ILayerZeroEndpoint _endpoint) external onlyOwner {
2022-12-tigris/contracts/GovNFT.sol::300 => function addAsset(address _asset) external onlyOwner {
2022-12-tigris/contracts/GovNFT.sol::307 => function setAllowedAsset(address _asset, bool _bool) external onlyOwner {
2022-12-tigris/contracts/GovNFT.sol::311 => function setMaxBridge(uint256 _max) external onlyOwner {
2022-12-tigris/contracts/GovNFTBridged.sol::44 => function setBaseURI(string calldata _newBaseURI) external onlyOwner {
2022-12-tigris/contracts/GovNFTBridged.sol::82 => function setTrustedAddress(uint16 _chainId, address _contract, bool _bool) external onlyOwner {
2022-12-tigris/contracts/GovNFTBridged.sol::196 => function setGas(uint _gas) external onlyOwner {
2022-12-tigris/contracts/GovNFTBridged.sol::200 => function setEndpoint(ILayerZeroEndpoint _endpoint) external onlyOwner {
2022-12-tigris/contracts/GovNFTBridged.sol::251 => function addAsset(address _asset) external onlyOwner {
2022-12-tigris/contracts/GovNFTBridged.sol::258 => function setAllowedAsset(address _asset, bool _bool) external onlyOwner {
2022-12-tigris/contracts/GovNFTBridged.sol::262 => function setMaxBridge(uint256 _max) external onlyOwner {
2022-12-tigris/contracts/NFTSale.sol::32 => function setPrice(uint _price) external onlyOwner {
2022-12-tigris/contracts/NFTSale.sol::58 => function recoverNft() external onlyOwner {
2022-12-tigris/contracts/NFTSale.sol::63 => function setIds(uint[] calldata _ids) external onlyOwner {
2022-12-tigris/contracts/PairsContract.sol::33 => function setAssetChainlinkFeed(uint256 _asset, address _feed) external onlyOwner {
2022-12-tigris/contracts/PairsContract.sol::48 => function addAsset(uint256 _asset, string memory _name, address _chainlinkFeed, uint256 _minLeverage, uint256 _maxLeverage, uint256 _feeMultiplier, uint256 _baseFundingRate) external onlyOwner {
2022-12-tigris/contracts/PairsContract.sol::73 => function updateAssetLeverage(uint256 _asset, uint256 _minLeverage, uint256 _maxLeverage) external onlyOwner {
2022-12-tigris/contracts/PairsContract.sol::92 => function setAssetBaseFundingRate(uint256 _asset, uint256 _baseFundingRate) external onlyOwner {
2022-12-tigris/contracts/PairsContract.sol::104 => function updateAssetFeeMultiplier(uint256 _asset, uint256 _feeMultiplier) external onlyOwner {
2022-12-tigris/contracts/PairsContract.sol::115 => function pauseAsset(uint256 _asset, bool _isPaused) external onlyOwner {
2022-12-tigris/contracts/PairsContract.sol::125 => function setMaxBaseFundingRate(uint256 _maxBaseFundingRate) external onlyOwner {
2022-12-tigris/contracts/PairsContract.sol::129 => function setProtocol(address _protocol) external onlyOwner {
2022-12-tigris/contracts/PairsContract.sol::139 => function setMaxOi(uint256 _asset, address _tigAsset, uint256 _maxOi) external onlyOwner {
2022-12-tigris/contracts/PairsContract.sol::154 => function modifyLongOi(uint256 _asset, address _tigAsset, bool _onOpen, uint256 _amount) external onlyProtocol {
2022-12-tigris/contracts/PairsContract.sol::174 => function modifyShortOi(uint256 _asset, address _tigAsset, bool _onOpen, uint256 _amount) external onlyProtocol {
2022-12-tigris/contracts/Position.sol::85 => function setBaseURI(string memory _newBaseURI) external onlyOwner {
2022-12-tigris/contracts/Position.sol::99 => function updateFunding(uint256 _asset, address _tigAsset, uint256 _longOi, uint256 _shortOi, uint256 _baseFundingRate, uint _vaultFundingPercent) external onlyMinter {
2022-12-tigris/contracts/Position.sol::168 => function executeLimitOrder(uint256 _id, uint256 _price, uint256 _newMargin) external onlyMinter {
2022-12-tigris/contracts/Position.sol::197 => function modifyMargin(uint256 _id, uint256 _newMargin, uint256 _newLeverage) external onlyMinter {
2022-12-tigris/contracts/Position.sol::209 => function addToPosition(uint256 _id, uint256 _newMargin, uint256 _newPrice) external onlyMinter {
2022-12-tigris/contracts/Position.sol::220 => function setAccInterest(uint256 _id) external onlyMinter {
2022-12-tigris/contracts/Position.sol::230 => function reducePosition(uint256 _id, uint256 _percent) external onlyMinter {
2022-12-tigris/contracts/Position.sol::242 => function modifyTp(uint _id, uint _tpPrice) external onlyMinter {
2022-12-tigris/contracts/Position.sol::252 => function modifySl(uint _id, uint _slPrice) external onlyMinter {
2022-12-tigris/contracts/Position.sol::260 => function burn(uint _id) external onlyMinter {
2022-12-tigris/contracts/Position.sol::310 => function setMinter(address _minter, bool _bool) external onlyOwner {
2022-12-tigris/contracts/Referrals.sol::32 => function setReferred(address _referredTrader, bytes32 _hash) external onlyProtocol {
2022-12-tigris/contracts/Referrals.sol::53 => function setProtocol(address _protocol) external onlyOwner {
2022-12-tigris/contracts/StableVault.sol::78 => function listToken(address _token) external onlyOwner {
2022-12-tigris/contracts/StableVault.sol::89 => function delistToken(address _token) external onlyOwner {
2022-12-tigris/contracts/Trading.sol::939 => function setLimitOrderPriceRange(uint _range) external onlyOwner {
2022-12-tigris/contracts/Trading.sol::952 => function setFees(bool _open, uint _daoFees, uint _burnFees, uint _referralFees, uint _botFees, uint _percent) external onlyOwner {
2022-12-tigris/contracts/TradingExtension.sol::144 => function setMaxGasPrice(uint _maxGasPrice) external onlyOwner {
2022-12-tigris/contracts/TradingExtension.sol::231 => function setChainlinkEnabled(bool _bool) external onlyOwner {
2022-12-tigris/contracts/TradingExtension.sol::240 => function setNode(address _node, bool _bool) external onlyOwner {
2022-12-tigris/contracts/TradingExtension.sol::274 => function setPaused(bool _paused) external onlyOwner {
```

## [G-08] Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

```
2022-12-tigris/contracts/GovNFT.sol::143 => uint16 version = 1;
2022-12-tigris/contracts/GovNFTBridged.sol::105 => uint16 version = 1;
```

## [G-09] Using bools for storage incurs overhead

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.
Use uint256(1) and uint256(2) for true/false instead

```
2022-12-tigris/contracts/BondNFT.sol::32 => mapping(address => bool) public allowedAsset;
2022-12-tigris/contracts/Faucet.sol::11 => mapping(address => bool) public used;
2022-12-tigris/contracts/GovNFT.sol::22 => mapping(uint16 => mapping(address => bool)) public isTrustedAddress;
2022-12-tigris/contracts/GovNFT.sol::265 => mapping(address => bool) private _allowedAsset;
2022-12-tigris/contracts/GovNFTBridged.sol::20 => mapping(uint16 => mapping(address => bool)) public isTrustedAddress;
2022-12-tigris/contracts/GovNFTBridged.sol::225 => mapping(address => bool) private _allowedAsset;
2022-12-tigris/contracts/Lock.sol::18 => mapping(address => bool) public allowedAssets;
2022-12-tigris/contracts/PairsContract.sol::12 => mapping(uint256 => bool) public allowedAsset;
2022-12-tigris/contracts/Position.sol::20 => mapping(address => bool) private _isMinter; // Trading contract should be minter
2022-12-tigris/contracts/Position.sol::34 => mapping(uint256 => mapping(address => mapping(bool => int256))) private accInterestPerOi;
2022-12-tigris/contracts/Referrals.sol::9 => bool private isInit;
2022-12-tigris/contracts/StableToken.sol::9 => mapping(address => bool) public isMinter;
2022-12-tigris/contracts/StableVault.sol::29 => mapping(address => bool) public allowed;
2022-12-tigris/contracts/Trading.sol::134 => mapping(address => bool) public allowedVault;
2022-12-tigris/contracts/TradingExtension.sol::15 => bool public chainlinkEnabled;
2022-12-tigris/contracts/TradingExtension.sol::17 => mapping(address => bool) private isNode;
2022-12-tigris/contracts/TradingExtension.sol::19 => mapping(address => bool) public allowedMargin;
2022-12-tigris/contracts/TradingExtension.sol::20 => bool public paused;
```

## [G-10] ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, for example when used in for- and while-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop

```
2022-12-tigris/contracts/BondNFT.sol::67 => id = ++totalBonds;
2022-12-tigris/contracts/BondNFT.sol::220 => for (uint i=epoch[_tigAsset]; i<aEpoch; i++) {
2022-12-tigris/contracts/BondNFT.sol::284 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/BondNFT.sol::292 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/BondNFT.sol::300 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/BondNFT.sol::342 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/GovNFT.sol::53 => for (uint i=0; i<assetsLength(); i++) {
2022-12-tigris/contracts/GovNFT.sol::67 => for (uint i=0; i<assetsLength(); i++) {
2022-12-tigris/contracts/GovNFT.sol::78 => for (uint i=0; i<assetsLength(); i++) {
2022-12-tigris/contracts/GovNFT.sol::95 => for (uint i=0; i<assetsLength(); i++) {
2022-12-tigris/contracts/GovNFT.sol::105 => for (uint i=0; i<_amount; i++) {
2022-12-tigris/contracts/GovNFT.sol::131 => for (uint i=0; i<tokenId.length; i++) {
2022-12-tigris/contracts/GovNFT.sol::200 => for (uint i=0; i<tokenId.length; i++) {
2022-12-tigris/contracts/GovNFT.sol::246 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/GovNFT.sol::252 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/GovNFT.sol::258 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/GovNFT.sol::325 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::51 => for (uint i=0; i<assetsLength(); i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::59 => for (uint i=0; i<assetsLength(); i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::73 => for (uint i=0; i<assetsLength(); i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::93 => for (uint i=0; i<tokenId.length; i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::160 => for (uint i=0; i<tokenId.length; i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::206 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::212 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::218 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::276 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/Lock.sol::113 => for (uint i=0; i < assets.length; i++) {
2022-12-tigris/contracts/NFTSale.sol::45 => for (uint i=0; i<_amount; i++) {
2022-12-tigris/contracts/NFTSale.sol::48 => for (uint i=0; i<_amount; i++) {
2022-12-tigris/contracts/Position.sol::296 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/Position.sol::304 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/Referrals.sol::70 => for (uint i=0; i<_codeOwnersL; i++) {
2022-12-tigris/contracts/Referrals.sol::73 => for (uint i=0; i<_referredAL; i++) {
```

## [G-11] <x> += <y> costs more gas than <x> = <x> + <y> for state variables

use <x> = <x> + <y> or <x> = <x> - <y> instead to save gas

```
2022-12-tigris/contracts/BondNFT.sol::68 => totalShares[_asset] += shares;
2022-12-tigris/contracts/BondNFT.sol::115 => totalShares[bond.asset] += shares-bond.shares;
2022-12-tigris/contracts/BondNFT.sol::117 => _bond.amount += _amount;
2022-12-tigris/contracts/BondNFT.sol::119 => _bond.period += _period;
2022-12-tigris/contracts/BondNFT.sol::150 => totalShares[bond.asset] -= bond.shares;
2022-12-tigris/contracts/BondNFT.sol::152 => amount += _claimAmount;
2022-12-tigris/contracts/BondNFT.sol::180 => accRewardsPerShare[bond.asset][epoch[bond.asset]] += _pendingDelta*1e18/totalShares[bond.asset];
2022-12-tigris/contracts/BondNFT.sol::183 => bondPaid[_id][bond.asset] += amount;
2022-12-tigris/contracts/BondNFT.sol::221 => epoch[_tigAsset] += 1;
2022-12-tigris/contracts/BondNFT.sol::225 => accRewardsPerShare[_tigAsset][aEpoch] += _amount * 1e18 / totalShares[_tigAsset];
2022-12-tigris/contracts/BondNFT.sol::333 => userDebt[from][bond.asset] += bond.pending;
2022-12-tigris/contracts/BondNFT.sol::334 => bondPaid[_id][bond.asset] += bond.pending;
2022-12-tigris/contracts/GovNFT.sol::52 => counter += 1;
2022-12-tigris/contracts/GovNFT.sol::54 => userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];
2022-12-tigris/contracts/GovNFT.sol::68 => userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];
2022-12-tigris/contracts/GovNFT.sol::79 => userDebt[owner][assets[i]] += accRewardsPerNFT[assets[i]];
2022-12-tigris/contracts/GovNFT.sol::80 => userDebt[owner][assets[i]] -= userPaid[owner][assets[i]]/balanceOf(owner);
2022-12-tigris/contracts/GovNFT.sol::81 => userPaid[owner][assets[i]] -= userPaid[owner][assets[i]]/balanceOf(owner);
2022-12-tigris/contracts/GovNFT.sol::96 => userDebt[from][assets[i]] += accRewardsPerNFT[assets[i]];
2022-12-tigris/contracts/GovNFT.sol::97 => userDebt[from][assets[i]] -= userPaid[from][assets[i]]/balanceOf(from);
2022-12-tigris/contracts/GovNFT.sol::98 => userPaid[from][assets[i]] -= userPaid[from][assets[i]]/balanceOf(from);
2022-12-tigris/contracts/GovNFT.sol::99 => userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];
2022-12-tigris/contracts/GovNFT.sol::278 => userPaid[_msgsender][_tigAsset] += amount;
2022-12-tigris/contracts/GovNFT.sol::290 => accRewardsPerNFT[_tigAsset] += _amount/totalSupply();
2022-12-tigris/contracts/GovNFTBridged.sol::52 => userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];
2022-12-tigris/contracts/GovNFTBridged.sol::60 => userDebt[owner][assets[i]] += accRewardsPerNFT[assets[i]];
2022-12-tigris/contracts/GovNFTBridged.sol::61 => userDebt[owner][assets[i]] -= userPaid[owner][assets[i]]/balanceOf(owner);
2022-12-tigris/contracts/GovNFTBridged.sol::62 => userPaid[owner][assets[i]] -= userPaid[owner][assets[i]]/balanceOf(owner);
2022-12-tigris/contracts/GovNFTBridged.sol::74 => userDebt[from][assets[i]] += accRewardsPerNFT[assets[i]];
2022-12-tigris/contracts/GovNFTBridged.sol::75 => userDebt[from][assets[i]] -= userPaid[from][assets[i]]/balanceOf(from);
2022-12-tigris/contracts/GovNFTBridged.sol::76 => userPaid[from][assets[i]] -= userPaid[from][assets[i]]/balanceOf(from);
2022-12-tigris/contracts/GovNFTBridged.sol::77 => userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];
2022-12-tigris/contracts/GovNFTBridged.sol::234 => userPaid[_msgsender][_tigAsset] += amount;
2022-12-tigris/contracts/GovNFTBridged.sol::241 => accRewardsPerNFT[_tigAsset] += _amount/totalSupply();
2022-12-tigris/contracts/Lock.sol::73 => totalLocked[_asset] += _amount;
2022-12-tigris/contracts/Lock.sol::103 => totalLocked[asset] -= lockAmount;
2022-12-tigris/contracts/PairsContract.sol::156 => _idToOi[_asset][_tigAsset].longOi += _amount;
2022-12-tigris/contracts/PairsContract.sol::160 => _idToOi[_asset][_tigAsset].longOi -= _amount;
2022-12-tigris/contracts/PairsContract.sol::176 => _idToOi[_asset][_tigAsset].shortOi += _amount;
2022-12-tigris/contracts/PairsContract.sol::180 => _idToOi[_asset][_tigAsset].shortOi -= _amount;
2022-12-tigris/contracts/Position.sol::62 => _trade.accInterest += (int256(_trade.margin*_trade.leverage/1e18)*(accInterestPerOi[_trade.asset][_trade.tigAsset][_trade.direction]+_pendingFunding)/1e18)-initId[_id];
2022-12-tigris/contracts/Position.sol::102 => accInterestPerOi[_asset][_tigAsset][true] += ((int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(longOi[_asset][_tigAsset]))*int256(1e10-vaultFundingPercent[_asset][_tigAsset])/1e10;
2022-12-tigris/contracts/Position.sol::104 => accInterestPerOi[_asset][_tigAsset][false] -= (int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(shortOi[_asset][_tigAsset]);
2022-12-tigris/contracts/Position.sol::107 => accInterestPerOi[_asset][_tigAsset][true] -= (int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(longOi[_asset][_tigAsset]);
2022-12-tigris/contracts/Position.sol::109 => accInterestPerOi[_asset][_tigAsset][false] += ((int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(shortOi[_asset][_tigAsset]))*int256(1e10-vaultFundingPercent[_asset][_tigAsset])/1e10;
2022-12-tigris/contracts/Position.sol::231 => _trades[_id].accInterest -= _trades[_id].accInterest*int256(_percent)/int256(DIVISION_CONSTANT);
2022-12-tigris/contracts/Position.sol::232 => _trades[_id].margin -= _trades[_id].margin*_percent/DIVISION_CONSTANT;
2022-12-tigris/contracts/Trading.sol::293 => _addMargin -= _fee;
2022-12-tigris/contracts/Trading.sol::509 => trade.price += trade.price * _spread / DIVISION_CONSTANT;
2022-12-tigris/contracts/Trading.sol::511 => trade.price -= trade.price * _spread / DIVISION_CONSTANT;
2022-12-tigris/contracts/TradingExtension.sol::185 => _price += _price * _spread / DIVISION_CONSTANT;
2022-12-tigris/contracts/TradingExtension.sol::187 => _price -= _price * _spread / DIVISION_CONSTANT;
```

## [G-12] abi.encode() is less efficient than abi.encodePacked()

use abi.encodePacked() where possible to save gas

```
2022-12-tigris/contracts/GovNFT.sol::141 => bytes memory payload = abi.encode(_to, tokenId);
2022-12-tigris/contracts/GovNFTBridged.sol::103 => bytes memory payload = abi.encode(_to, tokenId);
```

## [G-13] Use custom errors rather than revert()/require() strings to save gas

Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they're hitby avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas

```
2022-12-tigris/contracts/BondNFT.sol::63 => require(allowedAsset[_asset], "!Asset");
2022-12-tigris/contracts/BondNFT.sol::106 => require(bond.owner == _sender, "!owner");
2022-12-tigris/contracts/BondNFT.sol::107 => require(!bond.expired, "Expired");
2022-12-tigris/contracts/BondNFT.sol::108 => require(bond.asset == _asset, "!BondAsset");
2022-12-tigris/contracts/BondNFT.sol::110 => require(epoch[bond.asset] == block.timestamp/DAY, "Bad epoch");
2022-12-tigris/contracts/BondNFT.sol::111 => require(bond.period+_period <= 365, "MAX PERIOD");
2022-12-tigris/contracts/BondNFT.sol::142 => require(bond.expired, "!expire");
2022-12-tigris/contracts/BondNFT.sol::145 => require(bond.expireEpoch + 7 < epoch[bond.asset], "Bond owner priority");
2022-12-tigris/contracts/BondNFT.sol::173 => require(_claimer == bond.owner, "!owner");
2022-12-tigris/contracts/BondNFT.sol::329 => require(epoch[bond.asset] == block.timestamp/DAY, "Bad epoch");
2022-12-tigris/contracts/BondNFT.sol::330 => require(!bond.expired, "Expired!");
2022-12-tigris/contracts/BondNFT.sol::332 => require(block.timestamp > bond.mintTime + 300, "Recent update");
2022-12-tigris/contracts/BondNFT.sol::350 => require(assets.length == 0 || assets[assetsIndex[_asset]] != _asset, "Already added");
2022-12-tigris/contracts/BondNFT.sol::358 => require(assets[assetsIndex[_asset]] == _asset, "Not added");
2022-12-tigris/contracts/BondNFT.sol::373 => require(msg.sender == manager, "!manager");
2022-12-tigris/contracts/Faucet.sol::18 => require(!used[msg.sender], "Already used faucet");
2022-12-tigris/contracts/Faucet.sol::19 => require(msg.sender == tx.origin, "Is Contract");
2022-12-tigris/contracts/GovNFT.sol::51 => require(counter <= MAX, "Exceeds supply");
2022-12-tigris/contracts/GovNFT.sol::65 => require(msg.sender == address(this) || _msgSender() == owner(), "NotBridge");
2022-12-tigris/contracts/GovNFT.sol::66 => require(tokenId <= 10000, "BadID");
2022-12-tigris/contracts/GovNFT.sol::94 => require(ownerOf(tokenId) == from, "!Owner");
2022-12-tigris/contracts/GovNFT.sol::130 => require(tokenId.length > 0, "Not bridging");
2022-12-tigris/contracts/GovNFT.sol::132 => require(_msgSender() == ownerOf(tokenId[i]), "Not the owner");
2022-12-tigris/contracts/GovNFT.sol::140 => require(isTrustedAddress[_dstChainId][targetAddress], "!Trusted");
2022-12-tigris/contracts/GovNFT.sol::174 => require(_msgSender() == address(endpoint), "!Endpoint");
2022-12-tigris/contracts/GovNFT.sol::185 => require(msg.sender == address(this), "NonblockingLzApp: caller must be app");
2022-12-tigris/contracts/GovNFT.sol::194 => require(isTrustedAddress[_srcChainId][fromAddress], "!TrustedAddress");
2022-12-tigris/contracts/GovNFT.sol::209 => require(payloadHash != bytes32(0), "NonblockingLzApp: no stored message");
2022-12-tigris/contracts/GovNFT.sol::210 => require(keccak256(_payload) == payloadHash, "NonblockingLzApp: invalid payload");
2022-12-tigris/contracts/GovNFT.sol::241 => require(address(_endpoint) != address(0), "ZeroAddress");
2022-12-tigris/contracts/GovNFT.sol::301 => require(assets.length == 0 || assets[assetsIndex[_asset]] != _asset, "Already added");
2022-12-tigris/contracts/GovNFTBridged.sol::49 => require(msg.sender == address(this) || _msgSender() == owner(), "NotBridge");
2022-12-tigris/contracts/GovNFTBridged.sol::50 => require(tokenId <= 10000 && tokenId != 0, "BadID");
2022-12-tigris/contracts/GovNFTBridged.sol::72 => require(ownerOf(tokenId) == from, "!Owner");
2022-12-tigris/contracts/GovNFTBridged.sol::92 => require(tokenId.length > 0, "Not bridging");
2022-12-tigris/contracts/GovNFTBridged.sol::94 => require(_msgSender() == ownerOf(tokenId[i]), "Not the owner");
2022-12-tigris/contracts/GovNFTBridged.sol::102 => require(isTrustedAddress[_dstChainId][targetAddress], "!Trusted");
2022-12-tigris/contracts/GovNFTBridged.sol::134 => require(_msgSender() == address(endpoint), "!Endpoint");
2022-12-tigris/contracts/GovNFTBridged.sol::145 => require(msg.sender == address(this), "NonblockingLzApp: caller must be app");
2022-12-tigris/contracts/GovNFTBridged.sol::154 => require(isTrustedAddress[_srcChainId][fromAddress], "!TrustedAddress");
2022-12-tigris/contracts/GovNFTBridged.sol::169 => require(payloadHash != bytes32(0), "NonblockingLzApp: no stored message");
2022-12-tigris/contracts/GovNFTBridged.sol::170 => require(keccak256(_payload) == payloadHash, "NonblockingLzApp: invalid payload");
2022-12-tigris/contracts/GovNFTBridged.sol::201 => require(address(_endpoint) != address(0), "ZeroAddress");
2022-12-tigris/contracts/GovNFTBridged.sol::252 => require(assets.length == 0 || assets[assetsIndex[_asset]] != _asset, "Already added");
2022-12-tigris/contracts/Lock.sol::66 => require(_period <= maxPeriod, "MAX PERIOD");
2022-12-tigris/contracts/Lock.sol::67 => require(_period >= minPeriod, "MIN PERIOD");
2022-12-tigris/contracts/Lock.sol::68 => require(allowedAssets[_asset], "!asset");
2022-12-tigris/contracts/NFTSale.sol::41 => require(_amount <= availableIds.length, "Not enough for sale");
2022-12-tigris/contracts/PairsContract.sol::35 => require(_name.length > 0, "!Asset");
2022-12-tigris/contracts/PairsContract.sol::50 => require(_assetName.length == 0, "Already exists");
2022-12-tigris/contracts/PairsContract.sol::51 => require(bytes(_name).length > 0, "No name");
2022-12-tigris/contracts/PairsContract.sol::52 => require(_maxLeverage >= _minLeverage && _minLeverage > 0, "Wrong leverage values");
2022-12-tigris/contracts/PairsContract.sol::75 => require(_name.length > 0, "!Asset");
2022-12-tigris/contracts/PairsContract.sol::84 => require(_idToAsset[_asset].maxLeverage >= _idToAsset[_asset].minLeverage, "Wrong leverage values");
2022-12-tigris/contracts/PairsContract.sol::94 => require(_name.length > 0, "!Asset");
2022-12-tigris/contracts/PairsContract.sol::95 => require(_baseFundingRate <= maxBaseFundingRate, "baseFundingRate too high");
2022-12-tigris/contracts/PairsContract.sol::106 => require(_name.length > 0, "!Asset");
2022-12-tigris/contracts/PairsContract.sol::117 => require(_name.length > 0, "!Asset");
2022-12-tigris/contracts/PairsContract.sol::141 => require(_name.length > 0, "!Asset");
2022-12-tigris/contracts/PairsContract.sol::157 => require(_idToOi[_asset][_tigAsset].longOi <= _idToOi[_asset][_tigAsset].maxOi || _idToOi[_asset][_tigAsset].maxOi == 0, "MaxLongOi");
2022-12-tigris/contracts/PairsContract.sol::177 => require(_idToOi[_asset][_tigAsset].shortOi <= _idToOi[_asset][_tigAsset].maxOi || _idToOi[_asset][_tigAsset].maxOi == 0, "MaxShortOi");
2022-12-tigris/contracts/PairsContract.sol::190 => require(_msgSender() == address(protocol), "!Protocol");
2022-12-tigris/contracts/Position.sol::315 => require(_isMinter[_msgSender()], "!Minter");
2022-12-tigris/contracts/Referrals.sol::21 => require(_referral[_hash] == address(0), "Referral code already exists");
2022-12-tigris/contracts/Referrals.sol::81 => require(_msgSender() == address(protocol), "!Protocol");
2022-12-tigris/contracts/StableToken.sol::52 => require(isMinter[_msgSender()], "!Minter");
2022-12-tigris/contracts/StableVault.sol::45 => require(allowed[_token], "Token not listed");
2022-12-tigris/contracts/StableVault.sol::79 => require(!allowed[_token], "Already added");
2022-12-tigris/contracts/StableVault.sol::90 => require(allowed[_token], "Not added");
2022-12-tigris/contracts/Trading.sol::876 => require(allowedVault[_stableVault], "Unapproved stablevault");
2022-12-tigris/contracts/Trading.sol::877 => require(_token == IStableVault(_stableVault).stable() || IStableVault(_stableVault).allowed(_token), "Token not approved in vault");
2022-12-tigris/contracts/Trading.sol::887 => require(_proxy.proxy == _msgSender() && _proxy.time >= block.timestamp, "Proxy not approved");
2022-12-tigris/contracts/TradingExtension.sol::279 => require(msg.sender == trading, "!protocol");
```

## [G-14] Do not calculate constants

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.
Consequences: each usage of a constant costs more gas on each access. Since these are not real constants, they can't be referenced from a real constant environment (e.g. from assembly, or from another library)

```
2022-12-tigris/contracts/BondNFT.sol::10 => uint constant private DAY = 24 * 60 * 60;
```

## [G-15] Use a more recent version of solidity

Use a solidity version of at least 0.8.0 to get overflow protection without SafeMath
Use a solidity version of at least 0.8.2 to get compiler automatic inlining
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

```
2022-12-tigris/contracts/BondNFT.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/Faucet.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/Forwarder.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/GovNFT.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/GovNFTBridged.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/Lock.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/NFTSale.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/PairsContract.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/Position.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/Referrals.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/StableToken.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/StableVault.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/Timelock.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/Trading.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/TradingExtension.sol::2 => pragma solidity ^0.8.0;
```

## [G-16] Prefix increments cheaper than Postfix increments

++i costs less gas than i++, especially when it's used in for-loops (--i/i-- too)
Saves 5 gas PER LOOP

```
2022-12-tigris/contracts/BondNFT.sol::220 => for (uint i=epoch[_tigAsset]; i<aEpoch; i++) {
2022-12-tigris/contracts/BondNFT.sol::284 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/BondNFT.sol::292 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/BondNFT.sol::300 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/BondNFT.sol::342 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/GovNFT.sol::53 => for (uint i=0; i<assetsLength(); i++) {
2022-12-tigris/contracts/GovNFT.sol::67 => for (uint i=0; i<assetsLength(); i++) {
2022-12-tigris/contracts/GovNFT.sol::78 => for (uint i=0; i<assetsLength(); i++) {
2022-12-tigris/contracts/GovNFT.sol::95 => for (uint i=0; i<assetsLength(); i++) {
2022-12-tigris/contracts/GovNFT.sol::105 => for (uint i=0; i<_amount; i++) {
2022-12-tigris/contracts/GovNFT.sol::131 => for (uint i=0; i<tokenId.length; i++) {
2022-12-tigris/contracts/GovNFT.sol::200 => for (uint i=0; i<tokenId.length; i++) {
2022-12-tigris/contracts/GovNFT.sol::246 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/GovNFT.sol::252 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/GovNFT.sol::258 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/GovNFT.sol::325 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::51 => for (uint i=0; i<assetsLength(); i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::59 => for (uint i=0; i<assetsLength(); i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::73 => for (uint i=0; i<assetsLength(); i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::93 => for (uint i=0; i<tokenId.length; i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::160 => for (uint i=0; i<tokenId.length; i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::206 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::212 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::218 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::276 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/Lock.sol::113 => for (uint i=0; i < assets.length; i++) {
2022-12-tigris/contracts/NFTSale.sol::45 => for (uint i=0; i<_amount; i++) {
2022-12-tigris/contracts/NFTSale.sol::48 => for (uint i=0; i<_amount; i++) {
2022-12-tigris/contracts/Position.sol::296 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/Position.sol::304 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/Referrals.sol::70 => for (uint i=0; i<_codeOwnersL; i++) {
2022-12-tigris/contracts/Referrals.sol::73 => for (uint i=0; i<_referredAL; i++) {
```

## [G-17] Splitting require() statements that use && saves gas

Saves 16 gas per instance.
If you're using the Optimizer at 200, instead of using the && operator in a single require statement to check multiple conditions, multiple require statements with 1 condition per require statement should be used to save gas:

```
2022-12-tigris/contracts/GovNFTBridged.sol::50 => require(tokenId <= 10000 && tokenId != 0, "BadID");
2022-12-tigris/contracts/PairsContract.sol::52 => require(_maxLeverage >= _minLeverage && _minLeverage > 0, "Wrong leverage values");
2022-12-tigris/contracts/Trading.sol::887 => require(_proxy.proxy == _msgSender() && _proxy.time >= block.timestamp, "Proxy not approved");
```

## [G-18] Public functions not called by the contract should be declared external instead

Contracts are allowed to override their parents' functions and change the visibility from external to public and can save gas by doing so.

```
2022-12-tigris/contracts/BondNFT.sol::250 => function isExpired(uint256 _id) public view returns (bool) {
2022-12-tigris/contracts/BondNFT.sol::266 => function totalAssets() public view returns (uint256) {
2022-12-tigris/contracts/BondNFT.sol::274 => function getAssets() public view returns (address[] memory) {
2022-12-tigris/contracts/BondNFT.sol::339 => function balanceIds(address _user) public view returns (uint[] memory) {
2022-12-tigris/contracts/GovNFT.sol::206 => function retryMessage(uint16 _srcChainId, bytes calldata _srcAddress, uint64 _nonce, bytes calldata _payload) public {
2022-12-tigris/contracts/GovNFTBridged.sol::166 => function retryMessage(uint16 _srcChainId, bytes calldata _srcAddress, uint64 _nonce, bytes calldata _payload) public {
2022-12-tigris/contracts/PairsContract.sol::17 => function idToAsset(uint256 _asset) public view returns (Asset memory) {
2022-12-tigris/contracts/PairsContract.sol::22 => function idToOi(uint256 _asset, address _tigAsset) public view returns (OpenInterest memory) {
2022-12-tigris/contracts/Position.sol::40 => function isMinter(address _address) public view returns (bool) { return _isMinter[_address]; }
2022-12-tigris/contracts/Position.sol::66 => function openPositions() public view returns (uint256[] memory) { return _openPositions; }
2022-12-tigris/contracts/Position.sol::67 => function openPositionsIndexes(uint _id) public view returns (uint256) { return _openPositionsIndexes[_id]; }
2022-12-tigris/contracts/Position.sol::68 => function assetOpenPositions(uint _asset) public view returns (uint256[] memory) { return _assetOpenPositions[_asset]; }
2022-12-tigris/contracts/Position.sol::69 => function assetOpenPositionsIndexes(uint _asset, uint _id) public view returns (uint256) { return _assetOpenPositionsIndexes[_asset][_id]; }
2022-12-tigris/contracts/Position.sol::70 => function limitOrders(uint _asset) public view returns (uint256[] memory) { return _limitOrders[_asset]; }
2022-12-tigris/contracts/Position.sol::71 => function limitOrderIndexes(uint _asset, uint _id) public view returns (uint256) { return _limitOrderIndexes[_asset][_id]; }
```

## [G-19] Not using the named return variables when a function returns, wastes deployment gas

It is not necessary to have both a named return and a return statement.

```
2022-12-tigris/contracts/GovNFT.sol::332 => function _msgSender() internal view override(Context, MetaContext) returns (address sender) {
2022-12-tigris/contracts/GovNFT.sol::335 => function _msgData() internal view override(Context, MetaContext) returns (bytes calldata) {
2022-12-tigris/contracts/GovNFTBridged.sol::283 => function _msgSender() internal view override(Context, MetaContext) returns (address sender) {
2022-12-tigris/contracts/GovNFTBridged.sol::286 => function _msgData() internal view override(Context, MetaContext) returns (bytes calldata) {
2022-12-tigris/contracts/Position.sol::320 => function _msgSender() internal view override(Context, MetaContext) returns (address sender) {
2022-12-tigris/contracts/Position.sol::323 => function _msgData() internal view override(Context, MetaContext) returns (bytes calldata) {
2022-12-tigris/contracts/StableToken.sol::57 => function _msgSender() internal view override(Context, MetaContext) returns (address sender) {
2022-12-tigris/contracts/StableToken.sol::62 => function _msgData() internal view override(Context, MetaContext) returns (bytes calldata) {
2022-12-tigris/contracts/Trading.sol::771 => returns (uint payout_)
```

## [G-20] Multiple address mappings can be combined into a single mapping of an address to a struct, where appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

```
2022-12-tigris/contracts/BondNFT.sol::26 => mapping(address => uint256) public epoch;
2022-12-tigris/contracts/BondNFT.sol::32 => mapping(address => bool) public allowedAsset;
2022-12-tigris/contracts/BondNFT.sol::33 => mapping(address => uint) private assetsIndex;
2022-12-tigris/contracts/BondNFT.sol::34 => mapping(uint256 => mapping(address => uint256)) private bondPaid;
2022-12-tigris/contracts/BondNFT.sol::35 => mapping(address => mapping(uint256 => uint256)) private accRewardsPerShare; // tigAsset => epoch => accRewardsPerShare
2022-12-tigris/contracts/BondNFT.sol::37 => mapping(address => uint) public totalShares;
2022-12-tigris/contracts/BondNFT.sol::38 => mapping(address => mapping(address => uint)) public userDebt; // user => tigAsset => amount
2022-12-tigris/contracts/GovNFT.sol::22 => mapping(uint16 => mapping(address => bool)) public isTrustedAddress;
2022-12-tigris/contracts/GovNFT.sol::265 => mapping(address => bool) private _allowedAsset;
2022-12-tigris/contracts/GovNFT.sol::266 => mapping(address => uint) private assetsIndex;
2022-12-tigris/contracts/GovNFT.sol::267 => mapping(address => mapping(address => uint256)) private userPaid;
2022-12-tigris/contracts/GovNFT.sol::268 => mapping(address => mapping(address => uint256)) private userDebt;
2022-12-tigris/contracts/GovNFT.sol::269 => mapping(address => uint256) private accRewardsPerNFT;
2022-12-tigris/contracts/GovNFTBridged.sol::20 => mapping(uint16 => mapping(address => bool)) public isTrustedAddress;
2022-12-tigris/contracts/GovNFTBridged.sol::225 => mapping(address => bool) private _allowedAsset;
2022-12-tigris/contracts/GovNFTBridged.sol::226 => mapping(address => uint) private assetsIndex;
2022-12-tigris/contracts/GovNFTBridged.sol::227 => mapping(address => mapping(address => uint256)) private userPaid;
2022-12-tigris/contracts/GovNFTBridged.sol::228 => mapping(address => mapping(address => uint256)) private userDebt;
2022-12-tigris/contracts/GovNFTBridged.sol::229 => mapping(address => uint256) private accRewardsPerNFT;
2022-12-tigris/contracts/Lock.sol::18 => mapping(address => bool) public allowedAssets;
2022-12-tigris/contracts/Lock.sol::19 => mapping(address => uint) public totalLocked;
2022-12-tigris/contracts/Position.sol::18 => mapping(uint => mapping(address => uint)) public vaultFundingPercent;
2022-12-tigris/contracts/Position.sol::20 => mapping(address => bool) private _isMinter; // Trading contract should be minter
2022-12-tigris/contracts/Position.sol::33 => mapping(uint256 => mapping(address => int256)) public fundingDeltaPerSec;
2022-12-tigris/contracts/Position.sol::34 => mapping(uint256 => mapping(address => mapping(bool => int256))) private accInterestPerOi;
2022-12-tigris/contracts/Position.sol::35 => mapping(uint256 => mapping(address => uint256)) private lastUpdate;
2022-12-tigris/contracts/Position.sol::37 => mapping(uint256 => mapping(address => uint256)) private longOi;
2022-12-tigris/contracts/Position.sol::38 => mapping(uint256 => mapping(address => uint256)) private shortOi;
2022-12-tigris/contracts/StableVault.sol::29 => mapping(address => bool) public allowed;
2022-12-tigris/contracts/StableVault.sol::30 => mapping(address => uint) private tokenIndex;
2022-12-tigris/contracts/Trading.sol::134 => mapping(address => bool) public allowedVault;
2022-12-tigris/contracts/Trading.sol::141 => mapping(address => Proxy) public proxyApprovals;
2022-12-tigris/contracts/TradingExtension.sol::17 => mapping(address => bool) private isNode;
2022-12-tigris/contracts/TradingExtension.sol::18 => mapping(address => uint) public minPositionSize;
2022-12-tigris/contracts/TradingExtension.sol::19 => mapping(address => bool) public allowedMargin;
```