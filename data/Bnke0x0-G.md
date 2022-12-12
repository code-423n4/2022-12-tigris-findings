
### [G01] State variables only set in the constructor should be declared `immutable`

#### Impact
Avoids a Gusset (20000 gas)
#### Findings:
```
2022-12-tigris-main/contracts/BondNFT.sol::27 => uint private totalBonds;
2022-12-tigris-main/contracts/BondNFT.sol::28 => string public baseURI;
2022-12-tigris-main/contracts/BondNFT.sol::29 => address public manager;
2022-12-tigris-main/contracts/BondNFT.sol::30 => address[] public assets;
2022-12-tigris-main/contracts/GovNFT.sol::18 => string public baseURI;
2022-12-tigris-main/contracts/GovNFT.sol::19 => uint256 public maxBridge = 20;
2022-12-tigris-main/contracts/GovNFT.sol::20 => ILayerZeroEndpoint public endpoint;
2022-12-tigris-main/contracts/GovNFT.sol::264 => address[] public assets;
2022-12-tigris-main/contracts/PairsContract.sol::10 => address public protocol;
2022-12-tigris-main/contracts/Position.sol::23 => uint256[] private _openPositions;
2022-12-tigris-main/contracts/Position.sol::73 => Counters.Counter private _tokenIds;
2022-12-tigris-main/contracts/Position.sol::74 => string public baseURI;
2022-12-tigris-main/contracts/Referrals.sol::9 => bool private isInit;
2022-12-tigris-main/contracts/Referrals.sol::11 => address public protocol;
2022-12-tigris-main/contracts/StableVault.sol::31 => address[] public tokens;
2022-12-tigris-main/contracts/Trading.sol::118 => uint public maxWinPercent;
2022-12-tigris-main/contracts/Trading.sol::119 => uint public vaultFundingPercent;
2022-12-tigris-main/contracts/Trading.sol::121 => IPairsContract private pairsContract;
2022-12-tigris-main/contracts/Trading.sol::122 => IPosition private position;
2022-12-tigris-main/contracts/Trading.sol::123 => IGovNFT private gov;
2022-12-tigris-main/contracts/Trading.sol::124 => ITradingExtension private tradingExtension;
2022-12-tigris-main/contracts/Trading.sol::131 => uint public blockDelay;
2022-12-tigris-main/contracts/TradingExtension.sol::13 => address public trading;
2022-12-tigris-main/contracts/TradingExtension.sol::14 => uint256 public validSignatureTimer;
2022-12-tigris-main/contracts/TradingExtension.sol::15 => bool public chainlinkEnabled;
2022-12-tigris-main/contracts/TradingExtension.sol::20 => bool public paused;
2022-12-tigris-main/contracts/TradingExtension.sol::22 => IPairsContract private pairsContract;
2022-12-tigris-main/contracts/TradingExtension.sol::23 => IReferrals private referrals;
2022-12-tigris-main/contracts/TradingExtension.sol::24 => IPosition private position;
```



### [G02] State variables can be packed into fewer storage slots

#### Impact
If variables occupying the same slot are both written the same 
function or by the constructor avoids a separate Gsset (20000 gas). 
Reads of the variables are also cheaperIf variables occupying the same slot are both written the same 
function or by the constructor avoids a separate Gsset (20000 gas). 
Reads of the variables are also cheaper
#### Findings:
```
2022-12-tigris-main/contracts/BondNFT.sol::29 => address public manager;
2022-12-tigris-main/contracts/PairsContract.sol::10 => address public protocol;
2022-12-tigris-main/contracts/Referrals.sol::11 => address public protocol;
2022-12-tigris-main/contracts/StableVault.sol::33 => address public immutable stable;
2022-12-tigris-main/contracts/TradingExtension.sol::13 => address public trading;
```





### [G03] `<array>.length` should not be looked up in every loop of a `for` loop

#### Impact
Even memory arrays incur the overhead of bit tests and bit shifts to 
calculate the array length. Storage array length checks incur an extra 
Gwarmaccess (100 gas) PER-LOOP.
#### Findings:
```
2022-12-tigris-main/contracts/BondNFT.sol::284 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris-main/contracts/BondNFT.sol::292 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris-main/contracts/BondNFT.sol::300 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris-main/contracts/BondNFT.sol::342 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris-main/contracts/GovNFT.sol::131 => for (uint i=0; i<tokenId.length; i++) {
2022-12-tigris-main/contracts/GovNFT.sol::200 => for (uint i=0; i<tokenId.length; i++) {
2022-12-tigris-main/contracts/GovNFT.sol::246 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris-main/contracts/GovNFT.sol::252 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris-main/contracts/GovNFT.sol::258 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris-main/contracts/GovNFT.sol::325 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris-main/contracts/Lock.sol::113 => for (uint i=0; i < assets.length; i++) {
2022-12-tigris-main/contracts/Position.sol::296 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris-main/contracts/Position.sol::304 => for (uint i=0; i<_ids.length; i++) {
```



### [G04] `++i/i++` should be `unchecked{++i}`/`unchecked{++i}` when it is not possible for them to overflow, as is the case when used in `for` and `while` loops


#### Findings:
```
2022-12-tigris-main/contracts/BondNFT.sol::220 => for (uint i=epoch[_tigAsset]; i<aEpoch; i++) {
2022-12-tigris-main/contracts/BondNFT.sol::284 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris-main/contracts/BondNFT.sol::292 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris-main/contracts/BondNFT.sol::300 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris-main/contracts/BondNFT.sol::342 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris-main/contracts/GovNFT.sol::53 => for (uint i=0; i<assetsLength(); i++) {
2022-12-tigris-main/contracts/GovNFT.sol::67 => for (uint i=0; i<assetsLength(); i++) {
2022-12-tigris-main/contracts/GovNFT.sol::78 => for (uint i=0; i<assetsLength(); i++) {
2022-12-tigris-main/contracts/GovNFT.sol::95 => for (uint i=0; i<assetsLength(); i++) {
2022-12-tigris-main/contracts/GovNFT.sol::105 => for (uint i=0; i<_amount; i++) {
2022-12-tigris-main/contracts/GovNFT.sol::131 => for (uint i=0; i<tokenId.length; i++) {
2022-12-tigris-main/contracts/GovNFT.sol::200 => for (uint i=0; i<tokenId.length; i++) {
2022-12-tigris-main/contracts/GovNFT.sol::246 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris-main/contracts/GovNFT.sol::252 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris-main/contracts/GovNFT.sol::258 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris-main/contracts/GovNFT.sol::325 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris-main/contracts/Lock.sol::113 => for (uint i=0; i < assets.length; i++) {
2022-12-tigris-main/contracts/Position.sol::296 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris-main/contracts/Position.sol::304 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris-main/contracts/Referrals.sol::70 => for (uint i=0; i<_codeOwnersL; i++) {
2022-12-tigris-main/contracts/Referrals.sol::73 => for (uint i=0; i<_referredAL; i++) {
```



### [G05] `require()`/`revert()` strings longer than 32 bytes cost extra gas


#### Findings:
```
2022-12-tigris-main/contracts/GovNFT.sol::185 => require(msg.sender == address(this), "NonblockingLzApp: caller must be app");
2022-12-tigris-main/contracts/GovNFT.sol::209 => require(payloadHash != bytes32(0), "NonblockingLzApp: no stored message");
2022-12-tigris-main/contracts/GovNFT.sol::210 => require(keccak256(_payload) == payloadHash, "NonblockingLzApp: invalid payload");
```



### [G06] Not using the named return variables when a function returns, wastes deployment gas


#### Findings:
```
2022-12-tigris-main/contracts/BondNFT.sol::346 => return _ids;
2022-12-tigris-main/contracts/GovNFT.sol::328 => return _ids;
2022-12-tigris-main/contracts/Lock.sol::40 => return _tigAsset;
2022-12-tigris-main/contracts/Position.sol::64 => return _trade;
2022-12-tigris-main/contracts/Position.sol::299 => return _ids;
2022-12-tigris-main/contracts/Position.sol::307 => return _ids;
2022-12-tigris-main/contracts/Referrals.sol::44 => return _referred[_trader];
2022-12-tigris-main/contracts/Referrals.sol::48 => return _referral[_hash];
```



### [G07] Using `> 0` costs more gas than `!= 0` when used on a `uint` in a `require()` statement


#### Findings:
```
2022-12-tigris-main/contracts/GovNFT.sol::130 => require(tokenId.length > 0, "Not bridging");
2022-12-tigris-main/contracts/PairsContract.sol::35 => require(_name.length > 0, "!Asset");
2022-12-tigris-main/contracts/PairsContract.sol::51 => require(bytes(_name).length > 0, "No name");
2022-12-tigris-main/contracts/PairsContract.sol::52 => require(_maxLeverage >= _minLeverage && _minLeverage > 0, "Wrong leverage values");
2022-12-tigris-main/contracts/PairsContract.sol::75 => require(_name.length > 0, "!Asset");
2022-12-tigris-main/contracts/PairsContract.sol::94 => require(_name.length > 0, "!Asset");
2022-12-tigris-main/contracts/PairsContract.sol::106 => require(_name.length > 0, "!Asset");
2022-12-tigris-main/contracts/PairsContract.sol::117 => require(_name.length > 0, "!Asset");
2022-12-tigris-main/contracts/PairsContract.sol::141 => require(_name.length > 0, "!Asset");
2022-12-tigris-main/contracts/utils/TradingLibrary.sol::111 => require(_priceData.price > 0, "NoPrice");
```



### [G08] It costs more gas to initialize variables to zero than to let the default of zero be applied


#### Findings:
```
2022-12-tigris-main/contracts/BondNFT.sol::201 => userDebt[_user][_tigAsset] = 0;
2022-12-tigris-main/contracts/PairsContract.sol::162 => _idToOi[_asset][_tigAsset].longOi = 0;
2022-12-tigris-main/contracts/PairsContract.sol::182 => _idToOi[_asset][_tigAsset].shortOi = 0;
2022-12-tigris-main/contracts/Position.sol::173 => _trade.orderType = 0;
2022-12-tigris-main/contracts/Trading.sol::730 => _fees.botFees = 0;
```



### [G09] `++i` costs less gas than `i++`, especially when it’s used in forloops (`--i`/`i--` too)


#### Findings:
```
2022-12-tigris-main/contracts/BondNFT.sol::220 => for (uint i=epoch[_tigAsset]; i<aEpoch; i++) {
2022-12-tigris-main/contracts/BondNFT.sol::284 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris-main/contracts/BondNFT.sol::292 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris-main/contracts/BondNFT.sol::300 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris-main/contracts/BondNFT.sol::342 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris-main/contracts/GovNFT.sol::53 => for (uint i=0; i<assetsLength(); i++) {
2022-12-tigris-main/contracts/GovNFT.sol::67 => for (uint i=0; i<assetsLength(); i++) {
2022-12-tigris-main/contracts/GovNFT.sol::78 => for (uint i=0; i<assetsLength(); i++) {
2022-12-tigris-main/contracts/GovNFT.sol::95 => for (uint i=0; i<assetsLength(); i++) {
2022-12-tigris-main/contracts/GovNFT.sol::105 => for (uint i=0; i<_amount; i++) {
2022-12-tigris-main/contracts/GovNFT.sol::131 => for (uint i=0; i<tokenId.length; i++) {
2022-12-tigris-main/contracts/GovNFT.sol::200 => for (uint i=0; i<tokenId.length; i++) {
2022-12-tigris-main/contracts/GovNFT.sol::246 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris-main/contracts/GovNFT.sol::252 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris-main/contracts/GovNFT.sol::258 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris-main/contracts/GovNFT.sol::325 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris-main/contracts/Lock.sol::113 => for (uint i=0; i < assets.length; i++) {
2022-12-tigris-main/contracts/Position.sol::296 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris-main/contracts/Position.sol::304 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris-main/contracts/Referrals.sol::70 => for (uint i=0; i<_codeOwnersL; i++) {
2022-12-tigris-main/contracts/Referrals.sol::73 => for (uint i=0; i<_referredAL; i++) {
```



### [G10] Splitting `require()` statements that use `&&` Cost gas


#### Findings:
```
2022-12-tigris-main/contracts/PairsContract.sol::52 => require(_maxLeverage >= _minLeverage && _minLeverage > 0, "Wrong leverage values");
2022-12-tigris-main/contracts/Trading.sol::887 => require(_proxy.proxy == _msgSender() && _proxy.time >= block.timestamp, "Proxy not approved");
```



### [G11] Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead

#### Impact
> When using elements that are smaller than 32 bytes, your 
contract’s gas usage may be higher. This is because the EVM operates on 
32 bytes at a time. Therefore, if the element is smaller than that, the 
EVM must use more operations in order to reduce the size of the element 
from 32 bytes to the desired size.
> 

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html)
#### Findings:
```
2022-12-tigris-main/contracts/GovNFT.sol::22 => mapping(uint16 => mapping(address => bool)) public isTrustedAddress;
2022-12-tigris-main/contracts/GovNFT.sol::23 => mapping(uint16 => mapping(bytes => mapping(uint64 => bytes32))) public failedMessages;
2022-12-tigris-main/contracts/GovNFT.sol::24 => event MessageFailed(uint16 _srcChainId, bytes _srcAddress, uint64 _nonce, bytes _payload, bytes _reason);
2022-12-tigris-main/contracts/GovNFT.sol::25 => event RetryMessageSuccess(uint16 _srcChainId, bytes _srcAddress, uint64 _nonce, bytes32 _payloadHash);
2022-12-tigris-main/contracts/GovNFT.sol::27 => uint16 _srcChainId,
2022-12-tigris-main/contracts/GovNFT.sol::114 => function setTrustedAddress(uint16 _chainId, address _contract, bool _bool) external onlyOwner {
2022-12-tigris-main/contracts/GovNFT.sol::125 => uint16 _dstChainId,
2022-12-tigris-main/contracts/GovNFT.sol::143 => uint16 version = 1;
2022-12-tigris-main/contracts/GovNFT.sol::169 => uint16 _srcChainId,
2022-12-tigris-main/contracts/GovNFT.sol::171 => uint64 _nonce,
2022-12-tigris-main/contracts/GovNFT.sol::183 => function nonblockingLzReceive(uint16 _srcChainId, bytes calldata _srcAddress, uint64 _nonce, bytes calldata _payload) public {
2022-12-tigris-main/contracts/GovNFT.sol::189 => function _nonblockingLzReceive(uint16 _srcChainId, bytes memory _srcAddress, uint64, bytes memory _payload) internal {
2022-12-tigris-main/contracts/GovNFT.sol::220 => uint16 _dstChainId,
2022-12-tigris-main/contracts/StableVault.sol::21 => uint8 v,
2022-12-tigris-main/contracts/Trading.sol::73 => uint8 v,
2022-12-tigris-main/contracts/interfaces/ILayerZeroEndpoint.sol::15 => function send(uint16 _dstChainId, bytes calldata _destination, bytes calldata _payload, address payable _refundAddress, address _zroPaymentAddress, bytes calldata _adapterParams) external payable;
2022-12-tigris-main/contracts/interfaces/ILayerZeroEndpoint.sol::24 => function receivePayload(uint16 _srcChainId, bytes calldata _srcAddress, address _dstAddress, uint64 _nonce, uint _gasLimit, bytes calldata _payload) external;
2022-12-tigris-main/contracts/interfaces/ILayerZeroEndpoint.sol::29 => function getInboundNonce(uint16 _srcChainId, bytes calldata _srcAddress) external view returns (uint64);
2022-12-tigris-main/contracts/interfaces/ILayerZeroEndpoint.sol::33 => function getOutboundNonce(uint16 _dstChainId, address _srcAddress) external view returns (uint64);
2022-12-tigris-main/contracts/interfaces/ILayerZeroEndpoint.sol::41 => function estimateFees(uint16 _dstChainId, address _userApplication, bytes calldata _payload, bool _payInZRO, bytes calldata _adapterParam) external view returns (uint nativeFee, uint zroFee);
2022-12-tigris-main/contracts/interfaces/ILayerZeroEndpoint.sol::44 => function getChainId() external view returns (uint16);
2022-12-tigris-main/contracts/interfaces/ILayerZeroEndpoint.sol::50 => function retryPayload(uint16 _srcChainId, bytes calldata _srcAddress, bytes calldata _payload) external;
2022-12-tigris-main/contracts/interfaces/ILayerZeroEndpoint.sol::55 => function hasStoredPayload(uint16 _srcChainId, bytes calldata _srcAddress) external view returns (bool);
2022-12-tigris-main/contracts/interfaces/ILayerZeroEndpoint.sol::78 => function getConfig(uint16 _version, uint16 _chainId, address _userApplication, uint _configType) external view returns (bytes memory);
2022-12-tigris-main/contracts/interfaces/ILayerZeroEndpoint.sol::82 => function getSendVersion(address _userApplication) external view returns (uint16);
2022-12-tigris-main/contracts/interfaces/ILayerZeroEndpoint.sol::86 => function getReceiveVersion(address _userApplication) external view returns (uint16);
2022-12-tigris-main/contracts/interfaces/ILayerZeroReceiver.sol::11 => function lzReceive(uint16 _srcChainId, bytes calldata _srcAddress, uint64 _nonce, bytes calldata _payload) external;
2022-12-tigris-main/contracts/interfaces/ILayerZeroUserApplicationConfig.sol::11 => function setConfig(uint16 _version, uint16 _chainId, uint _configType, bytes calldata _config) external;
2022-12-tigris-main/contracts/interfaces/ILayerZeroUserApplicationConfig.sol::15 => function setSendVersion(uint16 _version) external;
2022-12-tigris-main/contracts/interfaces/ILayerZeroUserApplicationConfig.sol::19 => function setReceiveVersion(uint16 _version) external;
2022-12-tigris-main/contracts/interfaces/ILayerZeroUserApplicationConfig.sol::24 => function forceResumeReceive(uint16 _srcChainId, bytes calldata _srcAddress) external;
2022-12-tigris-main/contracts/interfaces/ITrading.sol::24 => uint8 v;
```


### [G12] Duplicated `require()`/`revert()` checks should be refactored to a modifier or function


#### Findings:
```
2022-12-tigris-main/contracts/BondNFT.sol::107 => require(!bond.expired, "Expired");
2022-12-tigris-main/contracts/PairsContract.sol::35 => require(_name.length > 0, "!Asset");
2022-12-tigris-main/contracts/PairsContract.sol::157 => require(_idToOi[_asset][_tigAsset].longOi <= _idToOi[_asset][_tigAsset].maxOi || _idToOi[_asset][_tigAsset].maxOi == 0, "MaxLongOi");
```



### [G13] `require()` or `revert()` statements that check input arguments should be at the top of the function

#### Impact
Checks that involve constants should come before checks that involve state variables
#### Findings:
```
2022-12-tigris-main/contracts/GovNFT.sol::241 => require(address(_endpoint) != address(0), "ZeroAddress");
```



### [G14] Use custom errors rather than `revert()`/`require()` strings to save deployment gas


#### Findings:
```
2022-12-tigris-main/contracts/BondNFT.sol::63 => require(allowedAsset[_asset], "!Asset");
2022-12-tigris-main/contracts/BondNFT.sol::106 => require(bond.owner == _sender, "!owner");
2022-12-tigris-main/contracts/BondNFT.sol::107 => require(!bond.expired, "Expired");
2022-12-tigris-main/contracts/BondNFT.sol::108 => require(bond.asset == _asset, "!BondAsset");
2022-12-tigris-main/contracts/BondNFT.sol::110 => require(epoch[bond.asset] == block.timestamp/DAY, "Bad epoch");
2022-12-tigris-main/contracts/BondNFT.sol::111 => require(bond.period+_period <= 365, "MAX PERIOD");
2022-12-tigris-main/contracts/BondNFT.sol::142 => require(bond.expired, "!expire");
2022-12-tigris-main/contracts/BondNFT.sol::145 => require(bond.expireEpoch + 7 < epoch[bond.asset], "Bond owner priority");
2022-12-tigris-main/contracts/BondNFT.sol::173 => require(_claimer == bond.owner, "!owner");
2022-12-tigris-main/contracts/BondNFT.sol::329 => require(epoch[bond.asset] == block.timestamp/DAY, "Bad epoch");
2022-12-tigris-main/contracts/BondNFT.sol::330 => require(!bond.expired, "Expired!");
2022-12-tigris-main/contracts/BondNFT.sol::332 => require(block.timestamp > bond.mintTime + 300, "Recent update");
2022-12-tigris-main/contracts/BondNFT.sol::350 => require(assets.length == 0 || assets[assetsIndex[_asset]] != _asset, "Already added");
2022-12-tigris-main/contracts/BondNFT.sol::358 => require(assets[assetsIndex[_asset]] == _asset, "Not added");
2022-12-tigris-main/contracts/BondNFT.sol::373 => require(msg.sender == manager, "!manager");
2022-12-tigris-main/contracts/GovNFT.sol::51 => require(counter <= MAX, "Exceeds supply");
2022-12-tigris-main/contracts/GovNFT.sol::65 => require(msg.sender == address(this) || _msgSender() == owner(), "NotBridge");
2022-12-tigris-main/contracts/GovNFT.sol::66 => require(tokenId <= 10000, "BadID");
2022-12-tigris-main/contracts/GovNFT.sol::94 => require(ownerOf(tokenId) == from, "!Owner");
2022-12-tigris-main/contracts/GovNFT.sol::130 => require(tokenId.length > 0, "Not bridging");
2022-12-tigris-main/contracts/GovNFT.sol::132 => require(_msgSender() == ownerOf(tokenId[i]), "Not the owner");
2022-12-tigris-main/contracts/GovNFT.sol::140 => require(isTrustedAddress[_dstChainId][targetAddress], "!Trusted");
2022-12-tigris-main/contracts/GovNFT.sol::174 => require(_msgSender() == address(endpoint), "!Endpoint");
2022-12-tigris-main/contracts/GovNFT.sol::185 => require(msg.sender == address(this), "NonblockingLzApp: caller must be app");
2022-12-tigris-main/contracts/GovNFT.sol::194 => require(isTrustedAddress[_srcChainId][fromAddress], "!TrustedAddress");
2022-12-tigris-main/contracts/GovNFT.sol::209 => require(payloadHash != bytes32(0), "NonblockingLzApp: no stored message");
2022-12-tigris-main/contracts/GovNFT.sol::210 => require(keccak256(_payload) == payloadHash, "NonblockingLzApp: invalid payload");
2022-12-tigris-main/contracts/GovNFT.sol::241 => require(address(_endpoint) != address(0), "ZeroAddress");
2022-12-tigris-main/contracts/GovNFT.sol::301 => require(assets.length == 0 || assets[assetsIndex[_asset]] != _asset, "Already added");
2022-12-tigris-main/contracts/Lock.sol::66 => require(_period <= maxPeriod, "MAX PERIOD");
2022-12-tigris-main/contracts/Lock.sol::67 => require(_period >= minPeriod, "MIN PERIOD");
2022-12-tigris-main/contracts/Lock.sol::68 => require(allowedAssets[_asset], "!asset");
2022-12-tigris-main/contracts/PairsContract.sol::35 => require(_name.length > 0, "!Asset");
2022-12-tigris-main/contracts/PairsContract.sol::50 => require(_assetName.length == 0, "Already exists");
2022-12-tigris-main/contracts/PairsContract.sol::51 => require(bytes(_name).length > 0, "No name");
2022-12-tigris-main/contracts/PairsContract.sol::52 => require(_maxLeverage >= _minLeverage && _minLeverage > 0, "Wrong leverage values");
2022-12-tigris-main/contracts/PairsContract.sol::75 => require(_name.length > 0, "!Asset");
2022-12-tigris-main/contracts/PairsContract.sol::84 => require(_idToAsset[_asset].maxLeverage >= _idToAsset[_asset].minLeverage, "Wrong leverage values");
2022-12-tigris-main/contracts/PairsContract.sol::94 => require(_name.length > 0, "!Asset");
2022-12-tigris-main/contracts/PairsContract.sol::95 => require(_baseFundingRate <= maxBaseFundingRate, "baseFundingRate too high");
2022-12-tigris-main/contracts/PairsContract.sol::106 => require(_name.length > 0, "!Asset");
2022-12-tigris-main/contracts/PairsContract.sol::117 => require(_name.length > 0, "!Asset");
2022-12-tigris-main/contracts/PairsContract.sol::141 => require(_name.length > 0, "!Asset");
2022-12-tigris-main/contracts/PairsContract.sol::157 => require(_idToOi[_asset][_tigAsset].longOi <= _idToOi[_asset][_tigAsset].maxOi || _idToOi[_asset][_tigAsset].maxOi == 0, "MaxLongOi");
2022-12-tigris-main/contracts/PairsContract.sol::177 => require(_idToOi[_asset][_tigAsset].shortOi <= _idToOi[_asset][_tigAsset].maxOi || _idToOi[_asset][_tigAsset].maxOi == 0, "MaxShortOi");
2022-12-tigris-main/contracts/PairsContract.sol::190 => require(_msgSender() == address(protocol), "!Protocol");
2022-12-tigris-main/contracts/Position.sol::315 => require(_isMinter[_msgSender()], "!Minter");
2022-12-tigris-main/contracts/Referrals.sol::21 => require(_referral[_hash] == address(0), "Referral code already exists");
2022-12-tigris-main/contracts/Referrals.sol::81 => require(_msgSender() == address(protocol), "!Protocol");
2022-12-tigris-main/contracts/StableToken.sol::52 => require(isMinter[_msgSender()], "!Minter");
2022-12-tigris-main/contracts/StableVault.sol::45 => require(allowed[_token], "Token not listed");
2022-12-tigris-main/contracts/StableVault.sol::79 => require(!allowed[_token], "Already added");
2022-12-tigris-main/contracts/StableVault.sol::90 => require(allowed[_token], "Not added");
2022-12-tigris-main/contracts/Trading.sol::876 => require(allowedVault[_stableVault], "Unapproved stablevault");
2022-12-tigris-main/contracts/Trading.sol::877 => require(_token == IStableVault(_stableVault).stable() || IStableVault(_stableVault).allowed(_token), "Token not approved in vault");
2022-12-tigris-main/contracts/Trading.sol::887 => require(_proxy.proxy == _msgSender() && _proxy.time >= block.timestamp, "Proxy not approved");
2022-12-tigris-main/contracts/TradingExtension.sol::279 => require(msg.sender == trading, "!protocol");
2022-12-tigris-main/contracts/utils/TradingLibrary.sol::105 => require(_provider == _priceData.provider, "BadSig");
2022-12-tigris-main/contracts/utils/TradingLibrary.sol::106 => require(_isNode[_provider], "!Node");
2022-12-tigris-main/contracts/utils/TradingLibrary.sol::107 => require(_asset == _priceData.asset, "!Asset");
2022-12-tigris-main/contracts/utils/TradingLibrary.sol::108 => require(!_priceData.isClosed, "Closed");
2022-12-tigris-main/contracts/utils/TradingLibrary.sol::109 => require(block.timestamp >= _priceData.timestamp, "FutSig");
2022-12-tigris-main/contracts/utils/TradingLibrary.sol::110 => require(block.timestamp <= _priceData.timestamp + _validSignatureTimer, "ExpSig");
2022-12-tigris-main/contracts/utils/TradingLibrary.sol::111 => require(_priceData.price > 0, "NoPrice");
```



### [G15] Functions guaranteed to revert when called by normal users can be marked `payable`

#### Impact
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.
#### Findings:
```
2022-12-tigris-main/contracts/BondNFT.sol::349 => function addAsset(address _asset) external onlyOwner {
2022-12-tigris-main/contracts/BondNFT.sol::357 => function setAllowedAsset(address _asset, bool _bool) external onlyOwner {
2022-12-tigris-main/contracts/BondNFT.sol::362 => function setBaseURI(string calldata _newBaseURI) external onlyOwner {
2022-12-tigris-main/contracts/GovNFT.sol::46 => function setBaseURI(string calldata _newBaseURI) external onlyOwner {
2022-12-tigris-main/contracts/GovNFT.sol::104 => function mintMany(uint _amount) external onlyOwner {
2022-12-tigris-main/contracts/GovNFT.sol::110 => function mint() external onlyOwner {
2022-12-tigris-main/contracts/GovNFT.sol::114 => function setTrustedAddress(uint16 _chainId, address _contract, bool _bool) external onlyOwner {
2022-12-tigris-main/contracts/GovNFT.sol::236 => function setGas(uint _gas) external onlyOwner {
2022-12-tigris-main/contracts/GovNFT.sol::240 => function setEndpoint(ILayerZeroEndpoint _endpoint) external onlyOwner {
2022-12-tigris-main/contracts/GovNFT.sol::300 => function addAsset(address _asset) external onlyOwner {
2022-12-tigris-main/contracts/GovNFT.sol::307 => function setAllowedAsset(address _asset, bool _bool) external onlyOwner {
2022-12-tigris-main/contracts/GovNFT.sol::311 => function setMaxBridge(uint256 _max) external onlyOwner {
2022-12-tigris-main/contracts/PairsContract.sol::33 => function setAssetChainlinkFeed(uint256 _asset, address _feed) external onlyOwner {
2022-12-tigris-main/contracts/PairsContract.sol::48 => function addAsset(uint256 _asset, string memory _name, address _chainlinkFeed, uint256 _minLeverage, uint256 _maxLeverage, uint256 _feeMultiplier, uint256 _baseFundingRate) external onlyOwner {
2022-12-tigris-main/contracts/PairsContract.sol::73 => function updateAssetLeverage(uint256 _asset, uint256 _minLeverage, uint256 _maxLeverage) external onlyOwner {
2022-12-tigris-main/contracts/PairsContract.sol::92 => function setAssetBaseFundingRate(uint256 _asset, uint256 _baseFundingRate) external onlyOwner {
2022-12-tigris-main/contracts/PairsContract.sol::104 => function updateAssetFeeMultiplier(uint256 _asset, uint256 _feeMultiplier) external onlyOwner {
2022-12-tigris-main/contracts/PairsContract.sol::115 => function pauseAsset(uint256 _asset, bool _isPaused) external onlyOwner {
2022-12-tigris-main/contracts/PairsContract.sol::125 => function setMaxBaseFundingRate(uint256 _maxBaseFundingRate) external onlyOwner {
2022-12-tigris-main/contracts/PairsContract.sol::129 => function setProtocol(address _protocol) external onlyOwner {
2022-12-tigris-main/contracts/PairsContract.sol::139 => function setMaxOi(uint256 _asset, address _tigAsset, uint256 _maxOi) external onlyOwner {
2022-12-tigris-main/contracts/PairsContract.sol::154 => function modifyLongOi(uint256 _asset, address _tigAsset, bool _onOpen, uint256 _amount) external onlyProtocol {
2022-12-tigris-main/contracts/PairsContract.sol::174 => function modifyShortOi(uint256 _asset, address _tigAsset, bool _onOpen, uint256 _amount) external onlyProtocol {
2022-12-tigris-main/contracts/Position.sol::85 => function setBaseURI(string memory _newBaseURI) external onlyOwner {
2022-12-tigris-main/contracts/Position.sol::99 => function updateFunding(uint256 _asset, address _tigAsset, uint256 _longOi, uint256 _shortOi, uint256 _baseFundingRate, uint _vaultFundingPercent) external onlyMinter {
2022-12-tigris-main/contracts/Position.sol::168 => function executeLimitOrder(uint256 _id, uint256 _price, uint256 _newMargin) external onlyMinter {
2022-12-tigris-main/contracts/Position.sol::197 => function modifyMargin(uint256 _id, uint256 _newMargin, uint256 _newLeverage) external onlyMinter {
2022-12-tigris-main/contracts/Position.sol::209 => function addToPosition(uint256 _id, uint256 _newMargin, uint256 _newPrice) external onlyMinter {
2022-12-tigris-main/contracts/Position.sol::220 => function setAccInterest(uint256 _id) external onlyMinter {
2022-12-tigris-main/contracts/Position.sol::230 => function reducePosition(uint256 _id, uint256 _percent) external onlyMinter {
2022-12-tigris-main/contracts/Position.sol::242 => function modifyTp(uint _id, uint _tpPrice) external onlyMinter {
2022-12-tigris-main/contracts/Position.sol::252 => function modifySl(uint _id, uint _slPrice) external onlyMinter {
2022-12-tigris-main/contracts/Position.sol::260 => function burn(uint _id) external onlyMinter {
2022-12-tigris-main/contracts/Position.sol::310 => function setMinter(address _minter, bool _bool) external onlyOwner {
2022-12-tigris-main/contracts/Referrals.sol::32 => function setReferred(address _referredTrader, bytes32 _hash) external onlyProtocol {
2022-12-tigris-main/contracts/Referrals.sol::53 => function setProtocol(address _protocol) external onlyOwner {
2022-12-tigris-main/contracts/StableVault.sol::78 => function listToken(address _token) external onlyOwner {
2022-12-tigris-main/contracts/StableVault.sol::89 => function delistToken(address _token) external onlyOwner {
2022-12-tigris-main/contracts/Trading.sol::939 => function setLimitOrderPriceRange(uint _range) external onlyOwner {
2022-12-tigris-main/contracts/Trading.sol::952 => function setFees(bool _open, uint _daoFees, uint _burnFees, uint _referralFees, uint _botFees, uint _percent) external onlyOwner {
2022-12-tigris-main/contracts/TradingExtension.sol::144 => function setMaxGasPrice(uint _maxGasPrice) external onlyOwner {
2022-12-tigris-main/contracts/TradingExtension.sol::231 => function setChainlinkEnabled(bool _bool) external onlyOwner {
2022-12-tigris-main/contracts/TradingExtension.sol::240 => function setNode(address _node, bool _bool) external onlyOwner {
2022-12-tigris-main/contracts/TradingExtension.sol::274 => function setPaused(bool _paused) external onlyOwner {
2022-12-tigris-main/contracts/utils/MetaContext.sol::9 => function setTrustedForwarder(address _forwarder, bool _bool) external onlyOwner {
```



### [G16] Use a more recent version of solidity

#### Impact
Use a solidity version of at least 0.8.10 to have external calls skip
 contract existence checks if the external call has a return value
#### Findings:
```
2022-12-tigris-main/contracts/BondNFT.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/GovNFT.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/Lock.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/PairsContract.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/Position.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/Referrals.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/StableToken.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/StableVault.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/Trading.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/TradingExtension.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/interfaces/IBondNFT.sol::2 => pragma solidity ^0.8.9;
2022-12-tigris-main/contracts/interfaces/IGovNFT.sol::3 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/interfaces/ILayerZeroEndpoint.sol::3 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/interfaces/ILayerZeroReceiver.sol::3 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/interfaces/ILayerZeroUserApplicationConfig.sol::3 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/interfaces/IPairsContract.sol::3 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/interfaces/IPosition.sol::3 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/interfaces/IReferrals.sol::3 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/interfaces/IStableVault.sol::3 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/interfaces/ITrading.sol::5 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/utils/MetaContext.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris-main/contracts/utils/TradingLibrary.sol::2 => pragma solidity ^0.8.0;
```


### [G17] Use `calldata` instead of `memory` for function parameters

#### Impact
Use calldata instead of memory for function parameters. Having function arguments use calldata instead of memory can save gas.
#### Findings:
```
2022-12-tigris-main/contracts/GovNFT.sol::189 => function _nonblockingLzReceive(uint16 _srcChainId, bytes memory _srcAddress, uint64, bytes memory _payload) internal {
2022-12-tigris-main/contracts/PairsContract.sol::48 => function addAsset(uint256 _asset, string memory _name, address _chainlinkFeed, uint256 _minLeverage, 
2022-12-tigris-main/contracts/Position.sol::85 => function setBaseURI(string memory _newBaseURI) external onlyOwner {
2022-12-tigris-main/contracts/Trading.sol::668 => function _handleWithdraw(IPosition.Trade memory _trade, address _stableVault, address _outputToken, uint _toMint) internal {
```




### [G18] Multiple `address` mappings can be combined into a single `mapping` of an `address` to a `struct`, where appropriate

#### Impact
Saves a storage slot for the mapping. Depending on the circumstances 
and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. 
Reads and subsequent writes can also be cheaper when a function requires
 both values and they both fit in the same storage slot
#### Findings:
```
2022-12-tigris-main/contracts/BondNFT.sol::33 => mapping(address => uint) private assetsIndex;
2022-12-tigris-main/contracts/BondNFT.sol::37 => mapping(address => uint) public totalShares;
2022-12-tigris-main/contracts/GovNFT.sol::267 => mapping(address => mapping(address => uint256)) private userPaid;
2022-12-tigris-main/contracts/GovNFT.sol::268 => mapping(address => mapping(address => uint256)) private userDebt;
2022-12-tigris-main/contracts/Position.sol::26 => mapping(uint256 => uint256[]) private _assetOpenPositions;
2022-12-tigris-main/contracts/Position.sol::29 => mapping(uint256 => uint256[]) private _limitOrders; // List of limit order nft ids per asset
2022-12-tigris-main/contracts/Position.sol::33 => mapping(uint256 => mapping(address => int256)) public fundingDeltaPerSec;
2022-12-tigris-main/contracts/Position.sol::35 => mapping(uint256 => mapping(address => uint256)) private lastUpdate;
2022-12-tigris-main/contracts/Position.sol::37 => mapping(uint256 => mapping(address => uint256)) private longOi;
2022-12-tigris-main/contracts/Position.sol::38 => mapping(uint256 => mapping(address => uint256)) private shortOi;
2022-12-tigris-main/contracts/StableToken.sol::9 => mapping(address => bool) public isMinter;
2022-12-tigris-main/contracts/StableVault.sol::29 => mapping(address => bool) public allowed;
2022-12-tigris-main/contracts/StableVault.sol::30 => mapping(address => uint) private tokenIndex;
2022-12-tigris-main/contracts/Trading.sol::132 => mapping(uint => uint) public limitDelay; // id => block.timestamp
2022-12-tigris-main/contracts/Trading.sol::134 => mapping(address => bool) public allowedVault;
2022-12-tigris-main/contracts/TradingExtension.sol::17 => mapping(address => bool) private isNode;
2022-12-tigris-main/contracts/TradingExtension.sol::18 => mapping(address => uint) public minPositionSize;
2022-12-tigris-main/contracts/TradingExtension.sol::19 => mapping(address => bool) public allowedMargin;
2022-12-tigris-main/contracts/utils/MetaContext.sol::7 => mapping(address => bool) private _isTrustedForwarder;
```


### [G19] Using `bools` for storage incurs overhead


#### Findings:
```
2022-12-tigris-main/contracts/BondNFT.sol::32 => mapping(address => bool) public allowedAsset;
2022-12-tigris-main/contracts/GovNFT.sol::22 => mapping(uint16 => mapping(address => bool)) public isTrustedAddress;
2022-12-tigris-main/contracts/GovNFT.sol::265 => mapping(address => bool) private _allowedAsset;
2022-12-tigris-main/contracts/Lock.sol::18 => mapping(address => bool) public allowedAssets;
2022-12-tigris-main/contracts/PairsContract.sol::12 => mapping(uint256 => bool) public allowedAsset;
2022-12-tigris-main/contracts/Position.sol::20 => mapping(address => bool) private _isMinter; // Trading contract should be minter
2022-12-tigris-main/contracts/Position.sol::34 => mapping(uint256 => mapping(address => mapping(bool => int256))) private accInterestPerOi;
2022-12-tigris-main/contracts/Referrals.sol::9 => bool private isInit;
2022-12-tigris-main/contracts/StableToken.sol::9 => mapping(address => bool) public isMinter;
2022-12-tigris-main/contracts/StableVault.sol::29 => mapping(address => bool) public allowed;
2022-12-tigris-main/contracts/Trading.sol::134 => mapping(address => bool) public allowedVault;
2022-12-tigris-main/contracts/TradingExtension.sol::15 => bool public chainlinkEnabled;
2022-12-tigris-main/contracts/TradingExtension.sol::17 => mapping(address => bool) private isNode;
2022-12-tigris-main/contracts/TradingExtension.sol::19 => mapping(address => bool) public allowedMargin;
2022-12-tigris-main/contracts/TradingExtension.sol::20 => bool public paused;
2022-12-tigris-main/contracts/utils/MetaContext.sol::7 => mapping(address => bool) private _isTrustedForwarder;
2022-12-tigris-main/contracts/utils/TradingLibrary.sol::98 => mapping(address => bool) storage _isNode
```


### [G20] Using `private` rather than `public` for constants, saves gas

#### Impact
If needed, the value can be read from the verified contract source 
code.
#### Findings:
```
2022-12-tigris-main/contracts/Lock.sol::12 => uint public constant minPeriod = 7;
2022-12-tigris-main/contracts/Lock.sol::13 => uint public constant maxPeriod = 365;
2022-12-tigris-main/contracts/Position.sol::16 => uint constant public DIVISION_CONSTANT = 1e10; // 100%
```


### [G21] Empty blocks should be removed or emit something

#### Impact
The code should be refactored such that they no longer exist, or the 
block should do something useful, such as emitting an event or 
reverting. If the contract is meant to be extended, the contract should 
be abstract and the function signatures be added without 
any default implementation. If the block is an empty if-statement block 
to avoid doing subsequent checks in the else-if/else conditions, the 
else-if/else conditions should be nested under the negation of the 
if-statement, because they involve different classes of checks, which 
may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})
#### Findings:
```
2022-12-tigris-main/contracts/StableToken.sol::11 => constructor(string memory name_, string memory symbol_) ERC20Permit(name_) ERC20(name_, symbol_) {}
```


### [G22] `abi.encode()` is less efficient than abi.encodePacked()


#### Findings:
```
2022-12-tigris-main/contracts/GovNFT.sol::141 => bytes memory payload = abi.encode(_to, tokenId);
2022-12-tigris-main/contracts/utils/TradingLibrary.sol::103 => keccak256(abi.encode(_priceData))
```



