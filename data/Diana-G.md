
## G-01 INCREMENTS CAN BE UNCHECKED

In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline

Prior to Solidity 0.8.0, arithmetic operations would always wrap in case of under- or overflow leading to widespread use of libraries that introduce additional checks.

Since Solidity 0.8.0, all arithmetic operations revert on over- and underflow by default, thus making the use of these libraries unnecessary.

To obtain the previous behaviour, an unchecked block can be used

_There are 16 instances of this issue_

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol

```
File: contracts/GovNFT.sol

53: for (uint i=0; i<assetsLength(); i++) {
67: for (uint i=0; i<assetsLength(); i++) {
78: for (uint i=0; i<assetsLength(); i++) {
95: for (uint i=0; i<assetsLength(); i++) {
105: for (uint i=0; i<_amount; i++) {
131: for (uint i=0; i<tokenId.length; i++) {
200: for (uint i=0; i<tokenId.length; i++) {
246: for (uint i=0; i<_ids.length; i++) {
252: for (uint i=0; i<_ids.length; i++) {
258: for (uint i=0; i<_ids.length; i++) {
325: for (uint i=0; i<_ids.length; i++) {
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol

```
File: contracts/Lock.sol

113: for (uint i=0; i < assets.length; i++) {
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol

```
File: contracts/Position.sol

296: for (uint i=0; i<_ids.length; i++) {
304: for (uint i=0; i<_ids.length; i++) {
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol

```
File: contracts/Referrals.sol

70: for (uint i=0; i<_codeOwnersL; i++) {
73: for (uint i=0; i<_referredAL; i++) {
```

------------

## G-02 x += y COSTS MORE GAS THAN x = x + y FOR STATE VARIABLES (SAME APPLIES FOR x -= y , x = x - y)

_There are 41 instances of this issue_

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol

```
File: contracts/BondNFT.sol

68: totalShares[_asset] += shares;
115: totalShares[bond.asset] += shares-bond.shares;
117: _bond.amount += _amount;
119: _bond.period += _period;
150: totalShares[bond.asset] -= bond.shares;
152: amount += _claimAmount;
180: accRewardsPerShare[bond.asset][epoch[bond.asset]] += _pendingDelta*1e18/totalShares[bond.asset];
183: bondPaid[_id][bond.asset] += amount;
221: epoch[_tigAsset] += 1;
225: accRewardsPerShare[_tigAsset][aEpoch] += _amount * 1e18 / totalShares[_tigAsset];
333: userDebt[from][bond.asset] += bond.pending;
334: bondPaid[_id][bond.asset] += bond.pending;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol

```
File: contracts/GovNFT.sol

52: counter += 1;
54: userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];
68: userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];
79: userDebt[owner][assets[i]] += accRewardsPerNFT[assets[i]];
80: userDebt[owner][assets[i]] -= userPaid[owner][assets[i]]/balanceOf(owner);
81: userPaid[owner][assets[i]] -= userPaid[owner][assets[i]]/balanceOf(owner);
96: userDebt[from][assets[i]] += accRewardsPerNFT[assets[i]];
97: userDebt[from][assets[i]] -= userPaid[from][assets[i]]/balanceOf(from);
98: userPaid[from][assets[i]] -= userPaid[from][assets[i]]/balanceOf(from);
99: userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];
278: userPaid[_msgsender][_tigAsset] += amount;
290: accRewardsPerNFT[_tigAsset] += _amount/totalSupply();
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol

```
File: contracts/Lock.sol

73: totalLocked[_asset] += _amount;
103: totalLocked[asset] -= lockAmount;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol

```
File: contracts/PairsContract.sol

156: _idToOi[_asset][_tigAsset].longOi += _amount;
160: _idToOi[_asset][_tigAsset].longOi -= _amount;
176: _idToOi[_asset][_tigAsset].shortOi += _amount;
180: _idToOi[_asset][_tigAsset].shortOi -= _amount;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol

```
File: contracts/Position.sol

62:  _trade.accInterest += (int256(_trade.margin*_trade.leverage/1e18)*(accInterestPerOi[_trade.asset][_trade.tigAsset][_trade.direction]+_pendingFunding)/1e18)-initId[_id];
102: accInterestPerOi[_asset][_tigAsset][true] += ((int256(block.timestamp-lastUpdate[_asset][_tigAsset])
107: accInterestPerOi[_asset][_tigAsset][true] -= (int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(longOi[_asset][_tigAsset]);
109: accInterestPerOi[_asset][_tigAsset][false] += ((int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(shortOi[_asset][_tigAsset]))*int256(1e10-vaultFundingPercent[_asset][_tigAsset])/1e10;
231: _trades[_id].accInterest -= _trades[_id].accInterest*int256(_percent)/int256(DIVISION_CONSTANT);
232: _trades[_id].margin -= _trades[_id].margin*_percent/DIVISION_CONSTANT
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol

```
File: contracts/Trading.sol

293: _addMargin -= _fee;
509: trade.price += trade.price * _spread / DIVISION_CONSTANT;
511: trade.price -= trade.price * _spread / DIVISION_CONSTANT;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol

```
File: contracts/TradingExtension.sol

185: _price += _price * _spread / DIVISION_CONSTANT;
187: _price -= _price * _spread / DIVISION_CONSTANT;
```

------

## G-03 DUPLICATED REQUIRE() OR REVERT() CHECKS SHOULD BE REFACTORED TO A MODIFIER OR FUNCTION

_There are 8 instances of this issue_

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol

```
File: contracts/BondNFT.sol

107: require(!bond.expired, "Expired");
330: require(!bond.expired, "Expired!");
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol

```
File: contracts/PairsContract.sol

35: require(_name.length > 0, "!Asset");
75: require(_name.length > 0, "!Asset");
94: require(_name.length > 0, "!Asset");
106: require(_name.length > 0, "!Asset");
117: require(_name.length > 0, "!Asset");
141: require(_name.length > 0, "!Asset");
```

-----

## G-04 USAGE OF UINTS OR INTS SMALLER THAN 32 BYTES INCURS OVERHEAD

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html) Use a larger size then downcast where needed

_There are 28 instances of this issue_

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol

```
File: contracts/GovNFT.sol

23: mapping(uint16 => mapping(bytes => mapping(uint64 => bytes32))) public failedMessages;

24: event MessageFailed(uint16 _srcChainId, bytes _srcAddress, uint64 _nonce, bytes _payload, bytes _reason);

25: event RetryMessageSuccess(uint16 _srcChainId, bytes _srcAddress, uint64 _nonce, bytes32 _payloadHash);

27: uint16 _srcChainId,

125: uint16 _dstChainId,

143: uint16 version = 1;

169: uint16 _srcChainId,

171: uint64 _nonce,

183: function nonblockingLzReceive(uint16 _srcChainId, bytes calldata _srcAddress, uint64 _nonce, bytes calldata _payload) public {

189: function _nonblockingLzReceive(uint16 _srcChainId, bytes memory _srcAddress, uint64, bytes memory _payload) internal {

206: function retryMessage(uint16 _srcChainId, bytes calldata _srcAddress, uint64 _nonce, bytes calldata _payload) public {

220: uint16 _dstChainId,
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol

```
File: contracts/StableVault.sol

21: uint8 v,

53: function depositWithPermit(address _token, uint256 _amount, uint256 _deadline, bool _permitMax, uint8 v, bytes32 r, bytes32 s) external {
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol

```
File: contracts/Trading.sol

73: uint8 v,
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ILayerZeroEndpoint.sol

```
File: contracts/interfaces/ILayerZeroEndpoint.sol

15: function send(uint16 _dstChainId, bytes calldata _destination, bytes calldata _payload, address payable _refundAddress, address _zroPaymentAddress, bytes calldata _adapterParams) external payable;

24: function receivePayload(uint16 _srcChainId, bytes calldata _srcAddress, address _dstAddress, uint64 _nonce, uint _gasLimit, bytes calldata _payload) external;

29: function getInboundNonce(uint16 _srcChainId, bytes calldata _srcAddress) external view returns (uint64);

33: function getOutboundNonce(uint16 _dstChainId, address _srcAddress) external view returns (uint64);

41: function estimateFees(uint16 _dstChainId, address _userApplication, bytes calldata _payload, bool _payInZRO, bytes calldata _adapterParam) external view returns (uint nativeFee, 
uint zroFee);

50: function retryPayload(uint16 _srcChainId, bytes calldata _srcAddress, bytes calldata _payload) external;

55: function hasStoredPayload(uint16 _srcChainId, bytes calldata _srcAddress) external view returns (bool);

78: function getConfig(uint16 _version, uint16 _chainId, address _userApplication, uint _configType) external view returns (bytes memory);
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ILayerZeroReceiver.sol

```
File: contracts/interfaces/ILayerZeroReceiver.sol

11: function lzReceive(uint16 _srcChainId, bytes calldata _srcAddress, uint64 _nonce, bytes calldata _payload) external;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ILayerZeroUserApplicationConfig.sol

```
File: contracts/interfaces/ILayerZeroUserApplicationConfig.sol

11: function setConfig(uint16 _version, uint16 _chainId, uint _configType, bytes calldata _config) external;

15: function setSendVersion(uint16 _version) external;

19: function setReceiveVersion(uint16 _version) external;

24: function forceResumeReceive(uint16 _srcChainId, bytes calldata _srcAddress) external;
```

------

## G-05 REQUIRE() OR REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS

Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.

Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

There are 3 instances of this issue

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol

```
File: contracts/GovNFT.sol

185: require(msg.sender == address(this), "NonblockingLzApp: caller must be app");
209: require(payloadHash != bytes32(0), "NonblockingLzApp: no stored message"
210: require(keccak256(_payload) == payloadHash, "NonblockingLzApp: invalid payload");
```

-------

## G-06 USING BOTH NAMED RETURNS AND A RETURN STATEMENT ISN'T NECESSARY

Removing unused named returns variables can reduce gas usage (MSTOREs/MLOADs) and improve code clarity. To save gas and improve code quality: consider using only one of those.

_There are 6 instances of this issue

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol

```
File: contracts/GovNFT.sol

219: function estimateFees(
332: function _msgSender() internal view override(Context, MetaContext) returns (address sender) {
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol

```
File: contracts/Position.sol

320: function _msgSender() internal view override(Context, MetaContext) returns (address sender) {
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableToken.sol

```
File: contracts/StableToken.sol

57: function _msgSender() internal view override(Context, MetaContext) returns (address sender) {
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol

```
File: contracts/Trading.sol

762: function _handleCloseFees(
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/MetaContext.sol

```
File: contracts/utils/MetaContext.sol

17: function _msgSender() internal view virtual override returns (address sender) {
```

---------

## G-07 SPLITTING REQURE() STATEMENTS THAT USE && SAVES GAS

_There are 3 instances of this issue_

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol

```
File: contracts/PairsContract.sol

52: require(_maxLeverage >= _minLeverage && _minLeverage > 0, "Wrong leverage values")
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol

```
File: contracts/Trading.sol

887: require(_proxy.proxy == _msgSender() && _proxy.time >= block.timestamp, "Proxy not approved");
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol

```
File: contracts/utils/TradingLibrary.sol

116-117: require(
                    _priceData.price < assetChainlinkPrice+assetChainlinkPrice*2/100 &&
```

-----

## G-08 Multiplication or division by 2 should use bit shifting

`<x> * 2` is equivalent to `<x> << 1` and `<x> / 2` is the same as `<x> >> 1`. The `MUL` and `DIV` opcodes cost 5 gas, whereas `SHL` and `SHR` only cost 3 gas

_There are 5 instances of this issue_

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol

```
File: contracts/Trading.sol

717: _fees.daoFees = _fees.daoFees - _fees.referralFees*2;
792: _daoFeesPaid = _daoFeesPaid-_referralFeesPaid*2;
954: require(_daoFees >= _botFees+_referralFees*2);
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol

```
File: contracts/utils/TradingLibrary.sol

117: _priceData.price < assetChainlinkPrice+assetChainlinkPrice*2/100 
118: _priceData.price > assetChainlinkPrice-assetChainlinkPrice*2/100, "!chainlinkPrice"
```

----
