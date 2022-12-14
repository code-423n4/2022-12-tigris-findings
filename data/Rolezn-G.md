## Summary<a name="Summary">

### Gas Optimizations
| |Issue|Contexts|Estimated Gas Saved|
|-|:-|:-|:-:|
| [GAS&#x2011;1](#GAS&#x2011;1) | Multiple Address Mappings Can Be Combined Into A Single Mapping Of An Address To A Struct, Where Appropriate | 16 | - |
| [GAS&#x2011;2](#GAS&#x2011;2) | Duplicated `require()`/`revert()` Checks Should Be Refactored To A Modifier Or Function | 8 | 224 |
| [GAS&#x2011;3](#GAS&#x2011;3) | `<x> += <y>` Costs More Gas Than `<x> = <x> + <y>` For State Variables | 42 | - |
| [GAS&#x2011;4](#GAS&#x2011;4) | `++i`/`i++` Should Be `unchecked{++i}`/`unchecked{i++}` When It Is Not Possible For Them To Overflow, As Is The Case When Used In For- And While-loops | 16 | 560 |
| [GAS&#x2011;5](#GAS&#x2011;5) | `require()`/`revert()` Strings Longer Than 32 Bytes Cost Extra Gas | 4 | - |
| [GAS&#x2011;6](#GAS&#x2011;6) | Splitting `require()` Statements That Use `&&` Saves Gas | 2 | 18 |
| [GAS&#x2011;7](#GAS&#x2011;7) | `abi.encode()` is less efficient than `abi.encodepacked()` | 2 | 200 |
| [GAS&#x2011;8](#GAS&#x2011;8) | Multiplication/division By Two Should Use Bit Shifting | 5 | - |
| [GAS&#x2011;9](#GAS&#x2011;9) | Public Functions To External | 39 | - |
| [GAS&#x2011;10](#GAS&#x2011;10) | Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead | 1 | - |
| [GAS&#x2011;11](#GAS&#x2011;11) | Optimize names to save gas | 11 | 242 |
| [GAS&#x2011;12](#GAS&#x2011;12) | Use `uint256(1)`/`uint256(2)` instead for `true` and `false` boolean states | 1 | 5000 |
| [GAS&#x2011;13](#GAS&#x2011;13) | Do not calculate constants | 1 | - |
| [GAS&#x2011;14](#GAS&#x2011;14) | `internal` functions only called once can be inlined to save gas | 8 | - |
| [GAS&#x2011;15](#GAS&#x2011;15) | Setting the `constructor` to `payable` | 8 | 104 |
| [GAS&#x2011;16](#GAS&#x2011;16) | Functions guaranteed to revert when called by normal users can be marked `payable` | 71 | 1491 |

Total: 235 contexts over 16 issues

## Gas Optimizations

### <a href="#Summary">[GAS&#x2011;1]</a><a name="GAS&#x2011;1"> Multiple Address Mappings Can Be Combined Into A Single Mapping Of An Address To A Struct, Where Appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot.

i.e. The following can be changed to the following struct:

```solidity
265: mapping(address => bool) private _allowedAsset;
266: mapping(address => uint) private assetsIndex;
269: mapping(address => uint256) private accRewardsPerNFT;
```
https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol

```solidity
    struct assetGovNFT {
        bool _allowAsset;
        uint assetsIndex;
        uint256 accRewardsPerNFT;
    }
```

#### <ins>Proof Of Concept</ins>


```solidity
26: mapping(address => uint256) public epoch;
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L26

```solidity
32: mapping(address => bool) public allowedAsset;
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L32

```solidity
33: mapping(address => uint) private assetsIndex;
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L33

```solidity
37: mapping(address => uint) public totalShares;
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L37

```solidity
265: mapping(address => bool) private _allowedAsset;
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L265

```solidity
266: mapping(address => uint) private assetsIndex;
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L266


```solidity
269: mapping(address => uint256) private accRewardsPerNFT;
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L269

```solidity
18: mapping(address => bool) public allowedAssets;
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Lock.sol#L18

```solidity
19: mapping(address => uint) public totalLocked;
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Lock.sol#L19

```solidity
29: mapping(address => bool) public allowed;
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/StableVault.sol#L29

```solidity
30: mapping(address => uint) private tokenIndex;
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/StableVault.sol#L30

```solidity
134: mapping(address => bool) public allowedVault;
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L134

```solidity
141: mapping(address => Proxy) public proxyApprovals;
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L141

```solidity
17: mapping(address => bool) private isNode;
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L17

```solidity
18: mapping(address => uint) public minPositionSize;
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L18

```solidity
19: mapping(address => bool) public allowedMargin;
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L19



#### <a href="#Summary">[GAS&#x2011;2]</a><a name="GAS&#x2011;2"> Duplicated `require()`/`revert()` Checks Should Be Refactored To A Modifier Or Function

Saves deployment costs

#### <ins>Proof Of Concept</ins>

```solidity
110: (epoch[bond.asset] == block.timestamp/DAY, "Bad epoch");
329: (epoch[bond.asset] == block.timestamp/DAY, "Bad epoch");

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L110

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L329



```solidity
35: (_name.length > 0, "!Asset");
75: (_name.length > 0, "!Asset");
94: (_name.length > 0, "!Asset");
106: (_name.length > 0, "!Asset");
117: (_name.length > 0, "!Asset");
141: (_name.length > 0, "!Asset");

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/PairsContract.sol#L35

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/PairsContract.sol#L75

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/PairsContract.sol#L94

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/PairsContract.sol#L106

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/PairsContract.sol#L117

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/PairsContract.sol#L141








### <a href="#Summary">[GAS&#x2011;3]</a><a name="GAS&#x2011;3"> `<x> += <y>` Costs More Gas Than `<x> = <x> + <y>` For State Variables

#### <ins>Proof Of Concept</ins>


```solidity
68: totalShares[_asset] += shares;

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L68

```solidity
115: totalShares[bond.asset] += shares-bond.shares;
117: _bond.amount += _amount;
119: _bond.period += _period;

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L115

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L117

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L119



```solidity
150: totalShares[bond.asset] -= bond.shares;
152: amount += _claimAmount;

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L150

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L152



```solidity
180: accRewardsPerShare[bond.asset][epoch[bond.asset]] += _pendingDelta*1e18/totalShares[bond.asset];
183: bondPaid[_id][bond.asset] += amount;

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L180

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L183



```solidity
221: epoch[_tigAsset] += 1;
225: accRewardsPerShare[_tigAsset][aEpoch] += _amount * 1e18 / totalShares[_tigAsset];

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L221

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L225



```solidity
333: userDebt[from][bond.asset] += bond.pending;
334: bondPaid[_id][bond.asset] += bond.pending;

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L333-L334

```solidity
52: counter += 1;
54: userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L52

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L54



```solidity
68: userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L68

```solidity
79: userDebt[owner][assets[i]] += accRewardsPerNFT[assets[i]];
80: userDebt[owner][assets[i]] -= userPaid[owner][assets[i]]/balanceOf(owner);
81: userPaid[owner][assets[i]] -= userPaid[owner][assets[i]]/balanceOf(owner);

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L79-L81

```solidity
96: userDebt[from][assets[i]] += accRewardsPerNFT[assets[i]];
97: userDebt[from][assets[i]] -= userPaid[from][assets[i]]/balanceOf(from);
98: userPaid[from][assets[i]] -= userPaid[from][assets[i]]/balanceOf(from);
99: userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L96-L99

```solidity
278: userPaid[_msgsender][_tigAsset] += amount;

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L278

```solidity
290: accRewardsPerNFT[_tigAsset] += _amount/totalSupply();

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L290

```solidity
73: totalLocked[_asset] += _amount;

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Lock.sol#L73

```solidity
103: totalLocked[asset] -= lockAmount;

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Lock.sol#L103

```solidity
156: _idToOi[_asset][_tigAsset].longOi += _amount;
160: _idToOi[_asset][_tigAsset].longOi -= _amount;

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/PairsContract.sol#L156

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/PairsContract.sol#L160



```solidity
176: _idToOi[_asset][_tigAsset].shortOi += _amount;
180: _idToOi[_asset][_tigAsset].shortOi -= _amount;

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/PairsContract.sol#L176

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/PairsContract.sol#L180



```solidity
62: _trade.accInterest += (int256(_trade.margin*_trade.leverage/1e18)*(accInterestPerOi[_trade.asset][_trade.tigAsset][_trade.direction]+_pendingFunding)/1e18)-initId[_id];

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L62

```solidity
102: accInterestPerOi[_asset][_tigAsset][true] += ((int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(longOi[_asset][_tigAsset]))*int256(1e10-vaultFundingPercent[_asset][_tigAsset])/1e10;
104: accInterestPerOi[_asset][_tigAsset][false] -= (int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(shortOi[_asset][_tigAsset]);
107: accInterestPerOi[_asset][_tigAsset][true] -= (int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(longOi[_asset][_tigAsset]);
109: accInterestPerOi[_asset][_tigAsset][false] += ((int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(shortOi[_asset][_tigAsset]))*int256(1e10-vaultFundingPercent[_asset][_tigAsset])/1e10;

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L102

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L104

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L107

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L109



```solidity
231: _trades[_id].accInterest -= _trades[_id].accInterest*int256(_percent)/int256(DIVISION_CONSTANT);
232: _trades[_id].margin -= _trades[_id].margin*_percent/DIVISION_CONSTANT;

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L231-L232

```solidity
293: _addMargin -= _fee;

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L293

```solidity
509: trade.price += trade.price * _spread / DIVISION_CONSTANT;
511: trade.price -= trade.price * _spread / DIVISION_CONSTANT;

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L509

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L511



```solidity
185: _price += _price * _spread / DIVISION_CONSTANT;
187: _price -= _price * _spread / DIVISION_CONSTANT;

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L185

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L187







### <a href="#Summary">[GAS&#x2011;4]</a><a name="GAS&#x2011;4"> `++i`/`i++` Should Be `unchecked{++i}`/`unchecked{i++}` When It Is Not Possible For Them To Overflow, As Is The Case When Used In For- And While-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas PER LOOP

#### <ins>Proof Of Concept</ins>


```solidity
53: for (uint i=0; i<assetsLength(); i++) {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L53

```solidity
67: for (uint i=0; i<assetsLength(); i++) {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L67

```solidity
78: for (uint i=0; i<assetsLength(); i++) {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L78

```solidity
95: for (uint i=0; i<assetsLength(); i++) {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L95

```solidity
105: for (uint i=0; i<_amount; i++) {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L105

```solidity
131: for (uint i=0; i<tokenId.length; i++) {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L131

```solidity
200: for (uint i=0; i<tokenId.length; i++) {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L200

```solidity
246: for (uint i=0; i<_ids.length; i++) {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L246

```solidity
252: for (uint i=0; i<_ids.length; i++) {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L252

```solidity
258: for (uint i=0; i<_ids.length; i++) {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L258

```solidity
325: for (uint i=0; i<_ids.length; i++) {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L325

```solidity
113: for (uint i=0; i < assets.length; i++) {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Lock.sol#L113

```solidity
296: for (uint i=0; i<_ids.length; i++) {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L296

```solidity
304: for (uint i=0; i<_ids.length; i++) {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L304

```solidity
70: for (uint i=0; i<_codeOwnersL; i++) {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Referrals.sol#L70

```solidity
73: for (uint i=0; i<_referredAL; i++) {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Referrals.sol#L73





### <a href="#Summary">[GAS&#x2011;5]</a><a name="GAS&#x2011;5"> `require()`/`revert()` Strings Longer Than 32 Bytes Cost Extra Gas

#### <ins>Proof Of Concept</ins>


```solidity
185: require(msg.sender == address(this), "NonblockingLzApp: caller must be app");
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L185

```solidity
209: require(payloadHash != bytes32(0), "NonblockingLzApp: no stored message");
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L209

```solidity
210: require(keccak256(_payload) == payloadHash, "NonblockingLzApp: invalid payload");
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L210

```solidity
21: require(_referral[_hash] == address(0), "Referral code already exists");
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Referrals.sol#L21




### <a href="#Summary">[GAS&#x2011;6]</a><a name="GAS&#x2011;6"> Splitting `require()` statements that use `&&` saves gas

Instead of using operator `&&` on a single `require`. Using a two `require` can save more gas.

i.e.
for `require(_maxLeverage >= _minLeverage && _minLeverage > 0, "Wrong leverage values");` use:

```
    require(_maxLeverage >= _minLeverage);
    require(_minLeverage > 0);
```

#### <ins>Proof Of Concept</ins>

```solidity
52: require(_maxLeverage >= _minLeverage && _minLeverage > 0, "Wrong leverage values");
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/PairsContract.sol#L52

```solidity
887: require(_proxy.proxy == _msgSender() && _proxy.time >= block.timestamp, "Proxy not approved");
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L887





### <a href="#Summary">[GAS&#x2011;7]</a><a name="GAS&#x2011;7"> `abi.encode()` is less efficient than `abi.encodepacked()`

See for more information: https://github.com/ConnorBlockchain/Solidity-Encode-Gas-Comparison 

#### <ins>Proof Of Concept</ins>


```solidity
141: bytes memory payload = abi.encode(_to, tokenId);
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L141

```solidity
103: keccak256(abi.encode(_priceData))
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/utils/TradingLibrary.sol#L103





### <a href="#Summary">[GAS&#x2011;8]</a><a name="GAS&#x2011;8"> Multiplication/division By Two Should Use Bit Shifting

<x> * 2 is equivalent to <x> << 1 and <x> / 2 is the same as <x> >> 1. The MUL and DIV opcodes cost 5 gas, whereas SHL and SHR only cost 3 gas

#### <ins>Proof Of Concept</ins>


```solidity
717: _fees.daoFees = _fees.daoFees - _fees.referralFees*2;
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L717

```solidity
792: _daoFeesPaid = _daoFeesPaid-_referralFeesPaid*2;
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L792

```solidity
954: require(_daoFees >= _botFees+_referralFees*2);
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L954

```solidity
117: _priceData.price < assetChainlinkPrice+assetChainlinkPrice*2/100 &&
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/utils/TradingLibrary.sol#L117

```solidity
118: _priceData.price > assetChainlinkPrice-assetChainlinkPrice*2/100, "!chainlinkPrice"
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/utils/TradingLibrary.sol#L118







### <a href="#Summary">[GAS&#x2011;9]</a><a name="GAS&#x2011;9"> Public Functions To External

The following functions could be set external to save gas and improve code quality.
External call cost is less expensive than of public functions.

#### <ins>Proof Of Concept</ins>


```solidity
function claim(
        uint _id,
        address _claimer
    ) public onlyManager() returns(uint amount, address tigAsset) {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L168

```solidity
function claimDebt(
        address _user,
        address _tigAsset
    ) public onlyManager() returns(uint amount) {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L196

```solidity
function idToBond(uint256 _id) public view returns (Bond memory bond) {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L235

```solidity
function isExpired(uint256 _id) public view returns (bool) {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L250

```solidity
function pending(
        uint256 _id
    ) public view returns (uint256) {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L260

```solidity
function totalAssets() public view returns (uint256) {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L266

```solidity
function getAssets() public view returns (address[] memory) {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L274

```solidity
function balanceIds(address _user) public view returns (uint[] memory) {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L339

```solidity
function setManager(
        address _manager
    ) public onlyOwner() {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L366

```solidity
function _bridgeMint(address to, uint256 tokenId) public {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L64

```solidity
function crossChain(
        uint16 _dstChainId,
        bytes memory _destination,
        address _to,
        uint256[] memory tokenId
    ) public payable {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L124

```solidity
function nonblockingLzReceive(uint16 _srcChainId, bytes calldata _srcAddress, uint64 _nonce, bytes calldata _payload) public {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L183

```solidity
function retryMessage(uint16 _srcChainId, bytes calldata _srcAddress, uint64 _nonce, bytes calldata _payload) public {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L206

```solidity
function pending(address user, address _tigAsset) public view returns (uint256) {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L296

```solidity
function assetsLength() public view returns (uint256) {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L315

```solidity
function claim(
        uint256 _id
    ) public returns (address) {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Lock.sol#L34

```solidity
function lock(
        address _asset,
        uint _amount,
        uint _period
    ) public {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Lock.sol#L61

```solidity
function extendLock(
        uint _id,
        uint _amount,
        uint _period
    ) public {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Lock.sol#L84

```solidity
function release(
        uint _id
    ) public {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Lock.sol#L98

```solidity
function claimGovFees() public {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Lock.sol#L110

```solidity
function idToAsset(uint256 _asset) public view returns (Asset memory) {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/PairsContract.sol#L17

```solidity
function idToOi(uint256 _asset, address _tigAsset) public view returns (OpenInterest memory) {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/PairsContract.sol#L22

```solidity
function ownerOf(uint _id) public view override(ERC721, IERC721, IPosition) returns (address) {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L11

```solidity
function isMinter(address _address) public view returns (bool) {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L40

```solidity
function trades(uint _id) public view returns (Trade memory) {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L41

```solidity
function openPositions() public view returns (uint256[] memory) {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L66

```solidity
function openPositionsIndexes(uint _id) public view returns (uint256) {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L67

```solidity
function assetOpenPositions(uint _asset) public view returns (uint256[] memory) {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L68

```solidity
function assetOpenPositionsIndexes(uint _asset, uint _id) public view returns (uint256) {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L69

```solidity
function limitOrders(uint _asset) public view returns (uint256[] memory) {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L70

```solidity
function limitOrderIndexes(uint _asset, uint _id) public view returns (uint256) {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L71

```solidity
function burnFrom(
        address account,
        uint256 amount
    ) 
        public 
        virtual 
        onlyMinter() 
    {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/StableToken.sol#L13

```solidity
function mintFor(
        address account,
        uint256 amount
    ) 
        public 
        virtual 
        onlyMinter() 
    {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/StableToken.sol#L24

```solidity
function deposit(address _token, uint256 _amount) public {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/StableVault.sol#L44

```solidity
function _checkGas() public view {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L122

```solidity
function modifyShortOi(
        uint _asset,
        address _tigAsset,
        bool _onOpen,
        uint _size
    ) public onlyProtocol {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L126

```solidity
function modifyLongOi(
        uint _asset,
        address _tigAsset,
        bool _onOpen,
        uint _size
    ) public onlyProtocol {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L135

```solidity
function getVerifiedPrice(
        uint _asset,
        PriceData calldata _priceData,
        bytes calldata _signature,
        uint _withSpreadIsLong
    ) 
        public view
        returns(uint256 _price, uint256 _spread) 
    {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L163

```solidity
function liqPrice(bool _direction, uint _tradePrice, uint _leverage, uint _margin, int _accInterest, uint _liqPercent) public pure returns (uint256 _liqPrice) {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/utils/TradingLibrary.sol#L62






### <a href="#Summary">[GAS&#x2011;10]</a><a name="GAS&#x2011;10"> Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract's gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html
Each operation involving a `uint8` costs an extra 22-28 gas (depending on whether the other operand is also a variable of type `uint8`) as compared to ones involving `uint256`, due to the compiler having to clear the higher bits of the memory word before operating on the `uint8`, as well as the associated stack operations of doing so. Use a larger size then downcast where needed

#### <ins>Proof Of Concept</ins>

```solidity
143: uint16 version = 1;
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L143



### <a href="#Summary">[GAS&#x2011;11]</a><a name="GAS&#x2011;11"> Optimize names to save gas

Contracts most called functions could simply save gas by function ordering via Method ID. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because 22 gas are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions. 

See more <a href="https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92">here</a>

#### <ins>Proof Of Concept</ins>

```solidity
File: /tigris/2022-12-tigris/contracts/BondNFT.sol
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol

```solidity
File: /tigris/2022-12-tigris/contracts/GovNFT.sol
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol

```solidity
File: /tigris/2022-12-tigris/contracts/Lock.sol
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Lock.sol

```solidity
File: /tigris/2022-12-tigris/contracts/PairsContract.sol
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/PairsContract.sol

```solidity
File: /tigris/2022-12-tigris/contracts/Position.sol
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol

```solidity
File: /tigris/2022-12-tigris/contracts/Referrals.sol
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Referrals.sol

```solidity
File: /tigris/2022-12-tigris/contracts/StableToken.sol
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/StableToken.sol

```solidity
File: /tigris/2022-12-tigris/contracts/StableVault.sol
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/StableVault.sol

```solidity
File: /tigris/2022-12-tigris/contracts/Trading.sol
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol

```solidity
File: /tigris/2022-12-tigris/contracts/TradingExtension.sol
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol

```solidity
File: /tigris/2022-12-tigris/contracts/utils/MetaContext.sol
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/utils/MetaContext.sol



#### <ins>Recommended Mitigation Steps</ins>
Find a lower method ID name for the most called functions for example Call() vs. Call1() is cheaper by 22 gas
For example, the function IDs in the Gauge.sol contract will be the most used; A lower method ID may be given.


### <a href="#Summary">[GAS&#x2011;13]</a><a name="GAS&#x2011;13"> Do not calculate constants

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.

#### <ins>Proof Of Concept</ins>

```solidity
10: uint constant private DAY = 24 * 60 * 60;
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L10



### <a href="#Summary">[GAS&#x2011;14]</a><a name="GAS&#x2011;14"> `internal` functions only called once can be inlined to save gas

#### <ins>Proof Of Concept</ins>

```solidity
278: function _baseURI

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L278

```solidity
42: function _baseURI

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L42

```solidity
335: function _msgData

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L335

```solidity
81: function _baseURI

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L81

```solidity
323: function _msgData

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L323

```solidity
62: function _msgData

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/StableToken.sol#L62

```solidity
17: function _msgSender

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/utils/MetaContext.sol#L17

```solidity
29: function _msgData

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/utils/MetaContext.sol#L29






### <a href="#Summary">[GAS&#x2011;15]</a><a name="GAS&#x2011;15"> Setting the `constructor` to `payable`

Saves ~13 gas per instance

#### <ins>Proof Of Concept</ins>

```solidity
40: constructor(
        string memory _setBaseURI,
        string memory _name,
        string memory _symbol
    ) ERC721(_name, _symbol)
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L40

```solidity
32: constructor(
        address _endpoint,
        string memory _setBaseURI,
        string memory _name,
        string memory _symbol
    ) ERC721(_name, _symbol)
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L32

```solidity
21: constructor(
        address _bondNFTAddress,
        address _govNFT
    )
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Lock.sol#L21

```solidity
76: constructor(string memory _setBaseURI, string memory _name, string memory _symbol) ERC721(_name, _symbol)
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L76

```solidity
11: constructor(string memory name_, string memory symbol_) ERC20Permit(name_) ERC20(name_, symbol_)
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/StableToken.sol#L11

```solidity
35: constructor(address _stable)
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/StableVault.sol#L35

```solidity
143: constructor(
        address _position,
        address _gov,
        address _pairsContract
    )
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L143

```solidity
28: constructor(
        address _trading,
        address _pairsContract,
        address _ref,
        address _position
    )
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L28





### <a href="#Summary">[GAS&#x2011;16]</a><a name="GAS&#x2011;16"> Functions guaranteed to revert when called by normal users can be marked `payable`

If a function modifier or require such as onlyOwner/onlyX is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2), DUP1(3), ISZERO(3), PUSH2(3), JUMPI(10), PUSH1(3), DUP1(3), REVERT(0), JUMPDEST(1), POP(2) which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

#### <ins>Proof Of Concept</ins>

```solidity
57: function createLock(
        address _asset,
        uint _amount,
        uint _period,
        address _owner
    ) external onlyManager() returns(uint id) {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L57

```solidity
97: function extendLock(
        uint _id,
        address _asset,
        uint _amount,
        uint _period,
        address _sender
    ) external onlyManager() {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L97

```solidity
137: function release(
        uint _id,
        address _releaser
    ) external onlyManager() returns(uint amount, uint lockAmount, address asset, address _owner) {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L137

```solidity
168: function claim(
        uint _id,
        address _claimer
    ) public onlyManager() returns(uint amount, address tigAsset) {
196: function claim(
        uint _id,
        address _claimer
    ) public onlyManager() returns(uint amount, address tigAsset) {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L168

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L196



```solidity
196: function claimDebt(
        address _user,
        address _tigAsset
    ) public onlyManager() returns(uint amount) {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L196

```solidity
349: function addAsset(address _asset) external onlyOwner {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L349

```solidity
357: function setAllowedAsset(address _asset, bool _bool) external onlyOwner {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L357

```solidity
362: function setBaseURI(string calldata _newBaseURI) external onlyOwner {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L362

```solidity
366: function setManager(
        address _manager
    ) public onlyOwner() {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L366

```solidity
46: function setBaseURI(string calldata _newBaseURI) external onlyOwner {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L46

```solidity
104: function mintMany(uint _amount) external onlyOwner {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L104

```solidity
104: function mint() external onlyOwner {
110: function mint() external onlyOwner {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L104

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L110



```solidity
114: function setTrustedAddress(uint16 _chainId, address _contract, bool _bool) external onlyOwner {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L114

```solidity
236: function setGas(uint _gas) external onlyOwner {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L236

```solidity
240: function setEndpoint(ILayerZeroEndpoint _endpoint) external onlyOwner {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L240

```solidity
300: function addAsset(address _asset) external onlyOwner {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L300

```solidity
307: function setAllowedAsset(address _asset, bool _bool) external onlyOwner {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L307

```solidity
311: function setMaxBridge(uint256 _max) external onlyOwner {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L311

```solidity
127: function editAsset(
        address _tigAsset,
        bool _isAllowed
    ) external onlyOwner() {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Lock.sol#L127

```solidity
138: function sendNFTs(
        uint[] memory _ids
    ) external onlyOwner() {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Lock.sol#L138

```solidity
33: function setAssetChainlinkFeed(uint256 _asset, address _feed) external onlyOwner {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/PairsContract.sol#L33

```solidity
48: function addAsset(uint256 _asset, string memory _name, address _chainlinkFeed, uint256 _minLeverage, uint256 _maxLeverage, uint256 _feeMultiplier, uint256 _baseFundingRate) external onlyOwner {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/PairsContract.sol#L48

```solidity
73: function updateAssetLeverage(uint256 _asset, uint256 _minLeverage, uint256 _maxLeverage) external onlyOwner {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/PairsContract.sol#L73

```solidity
92: function setAssetBaseFundingRate(uint256 _asset, uint256 _baseFundingRate) external onlyOwner {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/PairsContract.sol#L92

```solidity
104: function updateAssetFeeMultiplier(uint256 _asset, uint256 _feeMultiplier) external onlyOwner {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/PairsContract.sol#L104

```solidity
115: function pauseAsset(uint256 _asset, bool _isPaused) external onlyOwner {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/PairsContract.sol#L115

```solidity
125: function setMaxBaseFundingRate(uint256 _maxBaseFundingRate) external onlyOwner {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/PairsContract.sol#L125

```solidity
129: function setProtocol(address _protocol) external onlyOwner {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/PairsContract.sol#L129

```solidity
139: function setMaxOi(uint256 _asset, address _tigAsset, uint256 _maxOi) external onlyOwner {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/PairsContract.sol#L139

```solidity
154: function modifyLongOi(uint256 _asset, address _tigAsset, bool _onOpen, uint256 _amount) external onlyProtocol {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/PairsContract.sol#L154

```solidity
174: function modifyShortOi(uint256 _asset, address _tigAsset, bool _onOpen, uint256 _amount) external onlyProtocol {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/PairsContract.sol#L174

```solidity
85: function setBaseURI(string memory _newBaseURI) external onlyOwner {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L85

```solidity
99: function updateFunding(uint256 _asset, address _tigAsset, uint256 _longOi, uint256 _shortOi, uint256 _baseFundingRate, uint _vaultFundingPercent) external onlyMinter {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L99

```solidity
131: function mint(
        MintTrade memory _mintTrade
    ) external onlyMinter {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L131

```solidity
168: function executeLimitOrder(uint256 _id, uint256 _price, uint256 _newMargin) external onlyMinter {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L168

```solidity
197: function modifyMargin(uint256 _id, uint256 _newMargin, uint256 _newLeverage) external onlyMinter {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L197

```solidity
209: function addToPosition(uint256 _id, uint256 _newMargin, uint256 _newPrice) external onlyMinter {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L209

```solidity
220: function setAccInterest(uint256 _id) external onlyMinter {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L220

```solidity
230: function reducePosition(uint256 _id, uint256 _percent) external onlyMinter {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L230

```solidity
242: function modifyTp(uint _id, uint _tpPrice) external onlyMinter {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L242

```solidity
252: function modifySl(uint _id, uint _slPrice) external onlyMinter {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L252

```solidity
260: function burn(uint _id) external onlyMinter {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L260

```solidity
310: function setMinter(address _minter, bool _bool) external onlyOwner {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L310

```solidity
32: function setReferred(address _referredTrader, bytes32 _hash) external onlyProtocol {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Referrals.sol#L32

```solidity
53: function setProtocol(address _protocol) external onlyOwner {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Referrals.sol#L53

```solidity
60: function initRefs(
        address[] memory _codeOwners,
        bytes32[] memory _ownedCodes,
        address[] memory _referredA,
        bytes32[] memory _referredTo
    ) external onlyOwner {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Referrals.sol#L60

```solidity
13: function burnFrom(
        address account,
        uint256 amount
    ) 
        public 
        virtual 
        onlyMinter() 
    {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/StableToken.sol#L13

```solidity
24: function mintFor(
        address account,
        uint256 amount
    ) 
        public 
        virtual 
        onlyMinter() 
    {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/StableToken.sol#L24

```solidity
38: function setMinter(
        address _address,
        bool _status
    ) 
        public
        onlyOwner()
    {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/StableToken.sol#L38

```solidity
78: function listToken(address _token) external onlyOwner {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/StableVault.sol#L78

```solidity
89: function delistToken(address _token) external onlyOwner {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/StableVault.sol#L89

```solidity
898: function setBlockDelay(
        uint _blockDelay
    )
        external
        onlyOwner
    {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L898

```solidity
912: function setAllowedVault(
        address _stableVault,
        bool _bool
    )
        external
        onlyOwner
    {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L912

```solidity
926: function setMaxWinPercent(
        uint _maxWinPercent
    )
        external
        onlyOwner
    {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L926

```solidity
939: function setLimitOrderPriceRange(uint _range) external onlyOwner {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L939

```solidity
952: function setFees(bool _open, uint _daoFees, uint _burnFees, uint _referralFees, uint _botFees, uint _percent) external onlyOwner {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L952

```solidity
975: function setTradingExtension(
        address _ext
    ) external onlyOwner() {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L975

```solidity
61: function _closePosition(
        uint _id,
        uint _price,
        uint _percent
    ) external onlyProtocol returns (IPosition.Trade memory _trade, uint256 _positionSize, int256 _payout) {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L61

```solidity
126: function modifyShortOi(
        uint _asset,
        address _tigAsset,
        bool _onOpen,
        uint _size
    ) public onlyProtocol {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L126

```solidity
135: function modifyLongOi(
        uint _asset,
        address _tigAsset,
        bool _onOpen,
        uint _size
    ) public onlyProtocol {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L135

```solidity
144: function setMaxGasPrice(uint _maxGasPrice) external onlyOwner {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L144

```solidity
190: function _setReferral(
        bytes32 _referral,
        address _trader
    ) external onlyProtocol {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L190

```solidity
222: function setValidSignatureTimer(
        uint _validSignatureTimer
    )
        external
        onlyOwner
    {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L222

```solidity
231: function setChainlinkEnabled(bool _bool) external onlyOwner {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L231

```solidity
240: function setNode(address _node, bool _bool) external onlyOwner {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L240

```solidity
249: function setAllowedMargin(
        address _tigAsset,
        bool _bool
    ) 
        external
        onlyOwner
    {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L249

```solidity
264: function setMinPositionSize(
        address _tigAsset,
        uint _min
    ) 
        external
        onlyOwner
    {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L264

```solidity
274: function setPaused(bool _paused) external onlyOwner {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L274

```solidity
9: function setTrustedForwarder(address _forwarder, bool _bool) external onlyOwner {

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/utils/MetaContext.sol#L9



#### <ins>Recommended Mitigation Steps</ins>
Functions guaranteed to revert when called by normal users can be marked payable.


