### G-01 ++I or I++ SHOULD BE UNCHECKED{++I} or UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS

*Number of Instances Identified: 16*

the `unchecked` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. 

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol

```
53: for (uint i=0; i<assetsLength(); i++) {
67: for (uint i=0; i<assetsLength(); i++) {
78: for (uint i=0; i<assetsLength(); i++) {
95: for (uint i=0; i<assetsLength(); i++) {
105: for (uint i=0; i<_amount; i++) {
131: for (uint i=0; i<tokenId.length; i++) {
200: for (uint i=0; i<tokenId.length; i++) {
246: for (uint i=0; i<_ids.length; i++) {
252: for (uint i=0; i<_ids.length; i++) {
258: for (uint i=0; i<_ids.length; i++) {
325: for (uint i=0; i<_ids.length; i++) {
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol

```
113: for (uint i=0; i < assets.length; i++) {
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol

```
296: for (uint i=0; i<_ids.length; i++) {
304: for (uint i=0; i<_ids.length; i++) {
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol

```
70: for (uint i=0; i<_codeOwnersL; i++) {
73: for (uint i=0; i<_referredAL; i++) {
```


### G-02 X += Y COSTS MORE GAS THAN X = X + Y FOR STATE VARIABLES

*Number of Instances Identified: 41*

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol

```
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
52: counter += 1;
54: userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];
68: userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];
79: userDebt[owner][assets[i]] += accRewardsPerNFT[assets[i]];
80: userDebt[owner][assets[i]] -= userPaid[owner][assets[i]]/balanceOf(owner);
81: userPaid[owner][assets[i]] -= userPaid[owner][assets[i]]/balanceOf(owner);
96: userDebt[from][assets[i]] += accRewardsPerNFT[assets[i]];
97: userDebt[from][assets[i]] -= userPaid[from][assets[i]]/balanceOf(from);
98: userPaid[from][assets[i]] -= userPaid[from][assets[i]]/balanceOf(from);
99: userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];
278: userPaid[_msgsender][_tigAsset] += amount;
290: accRewardsPerNFT[_tigAsset] += _amount/totalSupply();
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol

```
73: totalLocked[_asset] += _amount;
103: totalLocked[asset] -= lockAmount;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol

```
156: _idToOi[_asset][_tigAsset].longOi += _amount;
160: _idToOi[_asset][_tigAsset].longOi -= _amount;
176: _idToOi[_asset][_tigAsset].shortOi += _amount;
180: _idToOi[_asset][_tigAsset].shortOi -= _amount;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol

```
62: _trade.accInterest += (int256(_trade.margin*_trade.leverage/1e18)*(accInterestPerOi[_trade.asset][_trade.tigAsset][_trade.direction]+_pendingFunding)/1e18)-initId[_id];

102: accInterestPerOi[_asset][_tigAsset][true] += ((int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(longOi[_asset][_tigAsset]))*int256(1e10-vaultFundingPercent[_asset][_tigAsset])/1e10;

107: accInterestPerOi[_asset][_tigAsset][true] -= (int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(longOi[_asset][_tigAsset]);

109: accInterestPerOi[_asset][_tigAsset][false] += ((int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(shortOi[_asset][_tigAsset]))*int256(1e10-vaultFundingPercent[_asset][_tigAsset])/1e10;

231: _trades[_id].accInterest -= _trades[_id].accInterest*int256(_percent)/int256(DIVISION_CONSTANT);

232: _trades[_id].margin -= _trades[_id].margin*_percent/DIVISION_CONSTANT
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol

```
293: _addMargin -= _fee;
509: trade.price += trade.price * _spread / DIVISION_CONSTANT;
511: trade.price -= trade.price * _spread / DIVISION_CONSTANT;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol

```
185: _price += _price * _spread / DIVISION_CONSTANT;
187: _price -= _price * _spread / DIVISION_CONSTANT;
```


### G-03 REQUIRE or REVERT STRINGS LONGER THAN 32 BYTES COST EXTRA GAS

*Number of Instances Identified: 3*

Each extra chunk of bytes past the original 32 [incurs an MSTORE](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#consider-having-short-revert-strings) which costs 3 gas

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol

```
185: require(msg.sender == address(this), "NonblockingLzApp: caller must be app");
209: require(payloadHash != bytes32(0), "NonblockingLzApp: no stored message");
210: require(keccak256(_payload) == payloadHash, "NonblockingLzApp: invalid payload");
```


### G-04 USAGE OF UINTS or INTS SMALLER THAN 32 BYTES - 256 BITS INCURS OVERHEAD

*Number of Instances Identified: 4*

> When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html) Use a larger size then downcast where needed

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol

```
125: uint16 _dstChainId
143: uint16 version = 1
169: uint16 _srcChainId
171: uint64 _nonce
```


### G-05 USING BOTH NAMED RETURNS AND A RETURN STATEMENT ISN'T NECESSARY

*Number of Instances Identified: 5*

Removing unused named returns variables can reduce gas usage (MSTOREs/MLOADs) and improve code clarity. To save gas and improve code quality: consider using only one of those.

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol

```
219-234:     function estimateFees(
        uint16 _dstChainId,
        address _userApplication,
        bytes calldata _payload,
        bool _payInZRO,
        bytes calldata _adapterParams
    ) external view returns (uint256 nativeFee, uint256 zroFee) {
        return
            endpoint.estimateFees(
                _dstChainId,
                _userApplication,
                _payload,
                _payInZRO,
                _adapterParams
            );
    }

332-334:     function _msgSender() internal view override(Context, MetaContext) returns (address sender) {
        return MetaContext._msgSender();
    }

```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol

```
320-322:     function _msgSender() internal view override(Context, MetaContext) returns (address sender) { 
        return MetaContext._msgSender();
    }
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableToken.sol

```
57-60:     function _msgSender() internal view override(Context, MetaContext) returns (address sender) {
        return MetaContext._msgSender();
    }
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/MetaContext.sol

```
17-27:     function _msgSender() internal view virtual override returns (address sender) {
        if (_isTrustedForwarder[msg.sender]) {
            // The assembly code is more direct than the Solidity version using `abi.decode`.
            /// @solidity memory-safe-assembly
            assembly {
                sender := shr(96, calldataload(sub(calldatasize(), 20)))
            }
        } else {
            return super._msgSender();
        }
    }
```


### G-06 SPLITTING REQUIRE() STATEMENTS THAT USE && SAVES GAS

*Number of Instances Identified: 2*

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol

```
53: require(_maxLeverage >= _minLeverage && _minLeverage > 0, "Wrong leverage values");
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol

```
116-119: require(
                    _priceData.price < assetChainlinkPrice+assetChainlinkPrice*2/100 &&
                    _priceData.price > assetChainlinkPrice-assetChainlinkPrice*2/100, "!chainlinkPrice"
                );
```


### G-07 MULTIPLICATION or DIVISION BY TWO SHOULD USE BIT SHIFTING

*Number of Instances Identified: 4*

`<x> * 2` is equivalent to `<x> << 1` and `<x> / 2` is the same as `<x> >> 1`. The `MUL` and `DIV` opcodes cost 5 gas, whereas `SHL` and `SHR` only cost 3 gas

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol

```
717: _fees.daoFees = _fees.daoFees - _fees.referralFees*2;
792: _daoFeesPaid = _daoFeesPaid-_referralFeesPaid*2;
954: require(_daoFees >= _botFees+_referralFees*2);
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol

```
116-119: require(
                    _priceData.price < assetChainlinkPrice+assetChainlinkPrice*2/100 &&
                    _priceData.price > assetChainlinkPrice-assetChainlinkPrice*2/100, "!chainlinkPrice"
                );
```

### G-08  DUPLICATED REQUIRE() OR REVERT() CHECKS SHOULD BE REFACTORED TO A MODIFIER OR FUNCTION

*Number of Instances Identified: 6*

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol

```
35: require(_name.length > 0, "!Asset");
75: require(_name.length > 0, "!Asset");
94: require(_name.length > 0, "!Asset");
106: require(_name.length > 0, "!Asset");
117: require(_name.length > 0, "!Asset");
141: require(_name.length > 0, "!Asset");
```
