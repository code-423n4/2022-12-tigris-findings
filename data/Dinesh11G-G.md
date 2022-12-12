### 1st Bug
Don't Initialize Variables with Default Value

#### Impact
Issue Information: 
Uninitialized variables are assigned with the types default value.

Explicitly initializing a variable with it's default value costs unnecessary gas.

Example
🤦 Bad:

uint256 x = 0;
bool y = false;
🚀 Good:

uint256 x;
bool y;

#### Findings:
```
2022-12-tigris/contracts/GovNFTBridged.sol::51 => for (uint i=0; i<assetsLength(); i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::59 => for (uint i=0; i<assetsLength(); i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::73 => for (uint i=0; i<assetsLength(); i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::93 => for (uint i=0; i<tokenId.length; i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::160 => for (uint i=0; i<tokenId.length; i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::206 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::212 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::218 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::276 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/NFTSale.sol::45 => for (uint i=0; i<_amount; i++) {
2022-12-tigris/contracts/NFTSale.sol::48 => for (uint i=0; i<_amount; i++) {
2022-12-tigris/contracts/mock/MockEndpoint.sol::141 => for (uint i = 0; i < msgs.length - 1; i++) {
```
#### Tools used
Manual Code review

### 2nd Bug
Cache Array Length Outside of Loop

#### Impact
Issue Information: 
Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.

Example
🤦 Bad:

for (uint256 i = 0; i < array.length; i++) {
    // invariant: array's length is not changed
}
🚀 Good:

uint256 len = array.length
for (uint256 i = 0; i < len; i++) {
    // invariant: array's length is not changed
}

#### Findings:
```
2022-12-tigris/contracts/BondNFT.sol::267 => return assets.length;
2022-12-tigris/contracts/BondNFT.sol::350 => require(assets.length == 0 || assets[assetsIndex[_asset]] != _asset, "Already added");
2022-12-tigris/contracts/BondNFT.sol::351 => assetsIndex[_asset] = assets.length;
2022-12-tigris/contracts/GovNFT.sol::130 => require(tokenId.length > 0, "Not bridging");
2022-12-tigris/contracts/GovNFT.sol::144 => uint256 _gas = 500_000 + gas*tokenId.length;
2022-12-tigris/contracts/GovNFT.sol::288 => if (assets.length == 0 || assets[assetsIndex[_tigAsset]] == address(0) || totalSupply() == 0 || !_allowedAsset[_tigAsset]) return;
2022-12-tigris/contracts/GovNFT.sol::301 => require(assets.length == 0 || assets[assetsIndex[_asset]] != _asset, "Already added");
2022-12-tigris/contracts/GovNFT.sol::302 => assetsIndex[_asset] = assets.length;
2022-12-tigris/contracts/GovNFT.sol::316 => return assets.length;
2022-12-tigris/contracts/GovNFT.sol::325 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::92 => require(tokenId.length > 0, "Not bridging");
2022-12-tigris/contracts/GovNFTBridged.sol::93 => for (uint i=0; i<tokenId.length; i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::106 => uint256 _gas = 500_000 + gas*tokenId.length;
2022-12-tigris/contracts/GovNFTBridged.sol::160 => for (uint i=0; i<tokenId.length; i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::206 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::212 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::218 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/GovNFTBridged.sol::239 => if (assets.length == 0 || assets[assetsIndex[_tigAsset]] == address(0) || totalSupply() == 0 || !_allowedAsset[_tigAsset]) return;
2022-12-tigris/contracts/GovNFTBridged.sol::252 => require(assets.length == 0 || assets[assetsIndex[_asset]] != _asset, "Already added");
2022-12-tigris/contracts/GovNFTBridged.sol::253 => assetsIndex[_asset] = assets.length;
2022-12-tigris/contracts/GovNFTBridged.sol::267 => return assets.length;
2022-12-tigris/contracts/GovNFTBridged.sol::276 => for (uint i=0; i<_ids.length; i++) {
2022-12-tigris/contracts/NFTSale.sol::41 => require(_amount <= availableIds.length, "Not enough for sale");
2022-12-tigris/contracts/NFTSale.sol::46 => _sold[i] = availableIds[(availableIds.length-i) - 1];
2022-12-tigris/contracts/PairsContract.sol::35 => require(_name.length > 0, "!Asset");
2022-12-tigris/contracts/PairsContract.sol::50 => require(_assetName.length == 0, "Already exists");
2022-12-tigris/contracts/PairsContract.sol::51 => require(bytes(_name).length > 0, "No name");
2022-12-tigris/contracts/PairsContract.sol::75 => require(_name.length > 0, "!Asset");
2022-12-tigris/contracts/PairsContract.sol::94 => require(_name.length > 0, "!Asset");
2022-12-tigris/contracts/PairsContract.sol::106 => require(_name.length > 0, "!Asset");
2022-12-tigris/contracts/PairsContract.sol::117 => require(_name.length > 0, "!Asset");
2022-12-tigris/contracts/PairsContract.sol::141 => require(_name.length > 0, "!Asset");
2022-12-tigris/contracts/Position.sol::151 => _limitOrderIndexes[_mintTrade.asset][newTokenID] = _limitOrders[_mintTrade.asset].length-1;
2022-12-tigris/contracts/Position.sol::155 => _openPositionsIndexes[newTokenID] = _openPositions.length-1;
2022-12-tigris/contracts/Position.sol::158 => _assetOpenPositionsIndexes[_mintTrade.asset][newTokenID] = _assetOpenPositions[_mintTrade.asset].length-1;
2022-12-tigris/contracts/Position.sol::177 => _limitOrderIndexes[_asset][_limitOrders[_asset][_limitOrders[_asset].length-1]] = _limitOrderIndexes[_asset][_id];
2022-12-tigris/contracts/Position.sol::178 => _limitOrders[_asset][_limitOrderIndexes[_asset][_id]] = _limitOrders[_asset][_limitOrders[_asset].length-1];
2022-12-tigris/contracts/Position.sol::183 => _openPositionsIndexes[_id] = _openPositions.length-1;
2022-12-tigris/contracts/Position.sol::185 => _assetOpenPositionsIndexes[_asset][_id] = _assetOpenPositions[_asset].length-1;
2022-12-tigris/contracts/Position.sol::264 => _limitOrderIndexes[_asset][_limitOrders[_asset][_limitOrders[_asset].length-1]] = _limitOrderIndexes[_asset][_id];
2022-12-tigris/contracts/Position.sol::265 => _limitOrders[_asset][_limitOrderIndexes[_asset][_id]] = _limitOrders[_asset][_limitOrders[_asset].length-1];
2022-12-tigris/contracts/Position.sol::269 => _assetOpenPositionsIndexes[_asset][_assetOpenPositions[_asset][_assetOpenPositions[_asset].length-1]] = _assetOpenPositionsIndexes[_asset][_id];
2022-12-tigris/contracts/Position.sol::270 => _assetOpenPositions[_asset][_assetOpenPositionsIndexes[_asset][_id]] = _assetOpenPositions[_asset][_assetOpenPositions[_asset].length-1];
2022-12-tigris/contracts/Position.sol::274 => _openPositionsIndexes[_openPositions[_openPositions.length-1]] = _openPositionsIndexes[_id];
2022-12-tigris/contracts/Position.sol::275 => _openPositions[_openPositionsIndexes[_id]] = _openPositions[_openPositions.length-1];
2022-12-tigris/contracts/Position.sol::283 => return _assetOpenPositions[_asset].length;
2022-12-tigris/contracts/Position.sol::287 => return _limitOrders[_asset].length;
2022-12-tigris/contracts/Referrals.sol::68 => uint _codeOwnersL = _codeOwners.length;
2022-12-tigris/contracts/Referrals.sol::69 => uint _referredAL = _referredA.length;
2022-12-tigris/contracts/StableVault.sol::80 => tokenIndex[_token] = tokens.length;
2022-12-tigris/contracts/StableVault.sol::91 => tokenIndex[tokens[tokens.length-1]] = tokenIndex[_token];
2022-12-tigris/contracts/StableVault.sol::92 => tokens[tokenIndex[_token]] = tokens[tokens.length-1];
2022-12-tigris/contracts/interfaces/ILayerZeroEndpoint.sol::10 => // @param _destination - the address on destination chain (in bytes). address length/format may vary by chains
2022-12-tigris/contracts/mock/MockEndpoint.sol::88 => require(msg.value >= nativeFee * _payload.length, "LayerZeroMock: not enough native for fees");
2022-12-tigris/contracts/mock/MockEndpoint.sol::136 => if (msgs.length > 0) {
2022-12-tigris/contracts/mock/MockEndpoint.sol::141 => for (uint i = 0; i < msgs.length - 1; i++) {
2022-12-tigris/contracts/mock/MockEndpoint.sol::151 => storedPayload[_srcChainId][_srcAddress] = StoredPayload(uint64(_payload.length), _dstAddress, keccak256(_payload));
2022-12-tigris/contracts/mock/MockEndpoint.sol::168 => return msgsToDeliver[_srcChainId][_srcAddress].length;
2022-12-tigris/contracts/mock/MockEndpoint.sol::178 => _nativeFee = nativeFee * _payload.length;
2022-12-tigris/contracts/mock/MockEndpoint.sol::187 => calldatacopy(ptr, sub(_b.offset, 2), add(_b.length, 2))
2022-12-tigris/contracts/mock/MockEndpoint.sol::248 => while (msgs.length > 0) {
2022-12-tigris/contracts/mock/MockEndpoint.sol::249 => QueuedPayload memory payload = msgs[msgs.length - 1];
2022-12-tigris/contracts/mock/MockEndpoint.sol::275 => require(_payload.length == sp.payloadLength && keccak256(_payload) == sp.payloadHash, "LayerZero: invalid payload");
2022-12-tigris/contracts/utils/ExcessivelySafeCall.sol::52 => // Store the length of the copied bytes
2022-12-tigris/contracts/utils/ExcessivelySafeCall.sol::103 => // Store the length of the copied bytes
2022-12-tigris/contracts/utils/ExcessivelySafeCall.sol::124 => require(_buf.length >= 4);
2022-12-tigris/contracts/utils/MetaContext.sol::31 => return msg.data[:msg.data.length - 20];
```
#### Tools used
Manual Code review

### 3rd Bug
Use != 0 instead of > 0 for Unsigned Integer Comparison

#### Impact
Issue Information: 
When dealing with unsigned integer types, comparisons with != 0 are cheaper than with > 0.

Example
🤦 Bad:

// `a` being of type unsigned integer
require(a > 0, "!a > 0");
🚀 Good:

// `a` being of type unsigned integer
require(a != 0, "!a > 0");
#### Findings:
```
2022-12-tigris/contracts/GovNFTBridged.sol::92 => require(tokenId.length > 0, "Not bridging");
2022-12-tigris/contracts/mock/MockEndpoint.sol::136 => if (msgs.length > 0) {
2022-12-tigris/contracts/mock/MockEndpoint.sol::248 => while (msgs.length > 0) {
```
#### Tools used
Manual Code review

### 4th Bug
Use immutable for OpenZeppelin AccessControl's Roles Declarations

#### Impact
Issue Information: 
⚡️ Only valid for solidity versions <0.6.12 ⚡️

Access roles marked as constant results in computing the keccak256 operation each time the variable is used because assigned operations for constant variables are re-evaluated every time.

Changing the variables to immutable results in computing the hash only once on deployment, leading to gas savings.

Example
🤦 Bad:

bytes32 public constant GOVERNOR_ROLE = keccak256("GOVERNOR_ROLE");
🚀 Good:

bytes32 public immutable GOVERNOR_ROLE = keccak256("GOVERNOR_ROLE");

#### Findings:
```
2022-12-tigris/contracts/GovNFT.sol::178 => failedMessages[_srcChainId][_srcAddress][_nonce] = keccak256(_payload);
2022-12-tigris/contracts/GovNFT.sol::210 => require(keccak256(_payload) == payloadHash, "NonblockingLzApp: invalid payload");
2022-12-tigris/contracts/GovNFTBridged.sol::138 => failedMessages[_srcChainId][_srcAddress][_nonce] = keccak256(_payload);
2022-12-tigris/contracts/GovNFTBridged.sol::170 => require(keccak256(_payload) == payloadHash, "NonblockingLzApp: invalid payload");
2022-12-tigris/contracts/mock/MockEndpoint.sol::151 => storedPayload[_srcChainId][_srcAddress] = StoredPayload(uint64(_payload.length), _dstAddress, keccak256(_payload));
2022-12-tigris/contracts/mock/MockEndpoint.sol::275 => require(_payload.length == sp.payloadLength && keccak256(_payload) == sp.payloadHash, "LayerZero: invalid payload");
2022-12-tigris/contracts/utils/TradingLibrary.sol::103 => keccak256(abi.encode(_priceData))
```
#### Tools used
Manual Code review

### 5th Bug
Long Revert Strings

#### Impact
Issue Information: 
Shortening revert strings to fit in 32 bytes will decrease gas costs for deployment and gas costs when the revert condition has been met.

If the contract(s) in scope allow using Solidity >=0.8.4, consider using Custom Errors as they are more gas efficient while allowing developers to describe the error in detail using NatSpec.

Example
🤦 Bad:

require(condition, "UniswapV3: The reentrancy guard. A transaction cannot re-enter the pool mid-swap");
🚀 Good (with shorter string):

// TODO: Provide link to a reference of error codes
require(condition, "LOK");
🚀 Good (with custom errors):

/// @notice A transaction cannot re-enter the pool mid-swap.
error NoReentrancy();

// ...

if (!condition) {
    revert NoReentrancy();
}

#### Findings:
```
2022-12-tigris/contracts/BondNFT.sol::4 => import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Enumerable.sol";
2022-12-tigris/contracts/BondNFT.sol::5 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
2022-12-tigris/contracts/BondNFT.sol::6 => import "@openzeppelin/contracts/access/Ownable.sol";
2022-12-tigris/contracts/Forwarder.sol::4 => import "@openzeppelin/contracts/metatx/MinimalForwarder.sol";
2022-12-tigris/contracts/GovNFT.sol::4 => import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Enumerable.sol";
2022-12-tigris/contracts/GovNFT.sol::5 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
2022-12-tigris/contracts/GovNFT.sol::6 => import "./interfaces/ILayerZeroEndpoint.sol";
2022-12-tigris/contracts/GovNFT.sol::7 => import "./interfaces/ILayerZeroReceiver.sol";
2022-12-tigris/contracts/GovNFT.sol::155 => "Must send enough value to cover messageFee"
2022-12-tigris/contracts/GovNFT.sol::185 => require(msg.sender == address(this), "NonblockingLzApp: caller must be app");
2022-12-tigris/contracts/GovNFT.sol::209 => require(payloadHash != bytes32(0), "NonblockingLzApp: no stored message");
2022-12-tigris/contracts/GovNFT.sol::210 => require(keccak256(_payload) == payloadHash, "NonblockingLzApp: invalid payload");
2022-12-tigris/contracts/GovNFTBridged.sol::4 => import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Enumerable.sol";
2022-12-tigris/contracts/GovNFTBridged.sol::5 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
2022-12-tigris/contracts/GovNFTBridged.sol::6 => import "./interfaces/ILayerZeroEndpoint.sol";
2022-12-tigris/contracts/GovNFTBridged.sol::7 => import "./interfaces/ILayerZeroReceiver.sol";
2022-12-tigris/contracts/GovNFTBridged.sol::117 => "Must send enough value to cover messageFee"
2022-12-tigris/contracts/GovNFTBridged.sol::145 => require(msg.sender == address(this), "NonblockingLzApp: caller must be app");
2022-12-tigris/contracts/GovNFTBridged.sol::169 => require(payloadHash != bytes32(0), "NonblockingLzApp: no stored message");
2022-12-tigris/contracts/GovNFTBridged.sol::170 => require(keccak256(_payload) == payloadHash, "NonblockingLzApp: invalid payload");
2022-12-tigris/contracts/Lock.sol::5 => import "@openzeppelin/contracts/access/Ownable.sol";
2022-12-tigris/contracts/Lock.sol::6 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
2022-12-tigris/contracts/NFTSale.sol::4 => import "@openzeppelin/contracts/access/Ownable.sol";
2022-12-tigris/contracts/PairsContract.sol::4 => import "@openzeppelin/contracts/access/Ownable.sol";
2022-12-tigris/contracts/Position.sol::4 => import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Enumerable.sol";
2022-12-tigris/contracts/Position.sol::5 => import "@openzeppelin/contracts/utils/Counters.sol";
2022-12-tigris/contracts/Referrals.sol::4 => import "@openzeppelin/contracts/access/Ownable.sol";
2022-12-tigris/contracts/StableToken.sol::4 => import "@openzeppelin/contracts/token/ERC20/extensions/draft-ERC20Permit.sol";
2022-12-tigris/contracts/StableVault.sol::4 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
2022-12-tigris/contracts/StableVault.sol::5 => import "@openzeppelin/contracts/security/ReentrancyGuard.sol";
2022-12-tigris/contracts/Timelock.sol::4 => import "@openzeppelin/contracts/governance/TimelockController.sol";
2022-12-tigris/contracts/Trading.sol::6 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
2022-12-tigris/contracts/TradingExtension.sol::4 => import "@openzeppelin/contracts/access/Ownable.sol";
2022-12-tigris/contracts/interfaces/ILayerZeroEndpoint.sol::5 => import "./ILayerZeroUserApplicationConfig.sol";
2022-12-tigris/contracts/mock/BadStableVault.sol::4 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
2022-12-tigris/contracts/mock/BadStableVault.sol::5 => import "@openzeppelin/contracts/security/ReentrancyGuard.sol";
2022-12-tigris/contracts/mock/MockERC20.sol::4 => import "@openzeppelin/contracts/token/ERC20/extensions/draft-ERC20Permit.sol";
2022-12-tigris/contracts/mock/MockEndpoint.sol::6 => import "../interfaces/ILayerZeroReceiver.sol";
2022-12-tigris/contracts/mock/MockEndpoint.sol::7 => import "../interfaces/ILayerZeroEndpoint.sol";
2022-12-tigris/contracts/mock/MockEndpoint.sol::86 => require(lzEndpoint != address(0), "LayerZeroMock: destination LayerZero Endpoint not found");
2022-12-tigris/contracts/mock/MockEndpoint.sol::88 => require(msg.value >= nativeFee * _payload.length, "LayerZeroMock: not enough native for fees");
2022-12-tigris/contracts/mock/NoTransferToken.sol::93 => require(newOwner != address(0), "Ownable: new owner is the zero address");
2022-12-tigris/contracts/mock/NoTransferToken.sol::383 => require(currentAllowance >= amount, "ERC20: transfer amount exceeds allowance");
2022-12-tigris/contracts/mock/NoTransferToken.sol::424 => require(currentAllowance >= subtractedValue, "ERC20: decreased allowance below zero");
2022-12-tigris/contracts/mock/NoTransferToken.sol::451 => require(sender != address(0), "ERC20: transfer from the zero address");
2022-12-tigris/contracts/mock/NoTransferToken.sol::452 => require(recipient != address(0), "ERC20: transfer to the zero address");
2022-12-tigris/contracts/mock/NoTransferToken.sol::457 => require(senderBalance >= amount, "ERC20: transfer amount exceeds balance");
2022-12-tigris/contracts/mock/NoTransferToken.sol::501 => require(account != address(0), "ERC20: burn from the zero address");
2022-12-tigris/contracts/mock/NoTransferToken.sol::506 => require(accountBalance >= amount, "ERC20: burn amount exceeds balance");
2022-12-tigris/contracts/mock/NoTransferToken.sol::535 => require(owner != address(0), "ERC20: approve from the zero address");
2022-12-tigris/contracts/mock/NoTransferToken.sol::536 => require(spender != address(0), "ERC20: approve to the zero address");
2022-12-tigris/contracts/utils/MetaContext.sol::4 => import "@openzeppelin/contracts/access/Ownable.sol";
2022-12-tigris/contracts/utils/TradingLibrary.sol::4 => import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";
```
#### Tools used
Manual Code review

### 6th Bug
Use Shift Right/Left instead of Division/Multiplication if possible

#### Impact
Issue Information: 
A division/multiplication by any number x being a power of 2 can be calculated by shifting log2(x) to the right/left.

While the DIV opcode uses 5 gas, the SHR opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

Example
🤦 Bad:

uint256 b = a / 2;
uint256 c = a / 4;
uint256 d = a * 8;
🚀 Good:

uint256 b = a >> 1;
uint256 c = a >> 2;
uint256 d = a << 3;

#### Findings:
```
2022-12-tigris/contracts/GovNFT.sol::175 => (bool success, bytes memory reason) = address(this).excessivelySafeCall(gasleft()*4/5, 150, abi.encodeWithSelector(this.nonblockingLzReceive.selector, _srcChainId, _srcAddress, _nonce, _payload));
2022-12-tigris/contracts/GovNFTBridged.sol::135 => (bool success, bytes memory reason) = address(this).excessivelySafeCall(gasleft()*4/5, 150, abi.encodeWithSelector(this.nonblockingLzReceive.selector, _srcChainId, _srcAddress, _nonce, _payload));
2022-12-tigris/contracts/Trading.sol::717 => _fees.daoFees = _fees.daoFees - _fees.referralFees*2;
2022-12-tigris/contracts/Trading.sol::792 => _daoFeesPaid = _daoFeesPaid-_referralFeesPaid*2;
2022-12-tigris/contracts/mock/NoTransferToken.sol::157 => * https://github.com/ethereum/EIPs/issues/20#issuecomment-263524729
2022-12-tigris/contracts/mock/NoTransferToken.sol::241 => * https://forum.zeppelin.solutions/t/how-to-implement-erc20-supply-mechanisms/226[How
2022-12-tigris/contracts/mock/NoTransferToken.sol::300 => * be displayed to a user as `5.05` (`505 / 10 ** 2`).
2022-12-tigris/contracts/utils/TradingLibrary.sol::117 => _priceData.price < assetChainlinkPrice+assetChainlinkPrice*2/100 &&
2022-12-tigris/contracts/utils/TradingLibrary.sol::118 => _priceData.price > assetChainlinkPrice-assetChainlinkPrice*2/100, "!chainlinkPrice"
```
#### Tools used
Manual Code review