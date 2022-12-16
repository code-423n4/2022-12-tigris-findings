# Gas Report
## Finding Summary
||Issue|Instances|
|-|-|-|
|[G-01]|`uint256` Iterator Checked Each Iteration|21|
|[G-02]|&& In If Statement(s)|9|
|[G-03]|Structs Can Be Packed Into Fewer Slots|4|
|[G-04]|`uint<size>` Used Rather Than `uint256`|3|
|[G-05]|Bit Shift(s) Not Used For MUL/DIV/MOD of Powers of 2|3|
|[G-06]|&& In Require Statement(s)|2|

### [G-01] `uint256` Iterator Checked Each Iteration

A `uint256` iterator will not overflow before the check variable overflows. Unchecking the iterator increment saves gas.
**Example**
```solidity
//From
for (uint256 i; i < len; ++i) {
	//Do Something
}
//To
for (uint256 i; i < len;) {
	//Do Something
	unchecked { ++i; }
}
```

#### Findings:

*/contracts/Position.sol*
Links: [296](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L296), [304](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L304).
```solidity
296:	for (uint i=0; i<_ids.length; i++) {
304:	for (uint i=0; i<_ids.length; i++) {
```

*/contracts/Referrals.sol*
Links: [70](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol#L70), [73](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol#L73).
```solidity
70:	for (uint i=0; i<_codeOwnersL; i++) {
73:	for (uint i=0; i<_referredAL; i++) {
```

*/contracts/GovNFT.sol*
Links: [53](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L53), [67](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L67), [78](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L78), [95](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L95), [105](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L105), [131](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L131), [200](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L200), [246](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L246), [252](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L252), [258](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L258), [325](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L325).
```solidity
53:	for (uint i=0; i<assetsLength(); i++) {
67:	for (uint i=0; i<assetsLength(); i++) {
78:	for (uint i=0; i<assetsLength(); i++) {
95:	for (uint i=0; i<assetsLength(); i++) {
105:	for (uint i=0; i<_amount; i++) {
131:	for (uint i=0; i<tokenId.length; i++) {
200:	for (uint i=0; i<tokenId.length; i++) {
246:	for (uint i=0; i<_ids.length; i++) {
252:	for (uint i=0; i<_ids.length; i++) {
258:	for (uint i=0; i<_ids.length; i++) {
325:	for (uint i=0; i<_ids.length; i++) {
```

*/contracts/Lock.sol*
Links: [113](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L113).
```solidity
113:	for (uint i=0; i < assets.length; i++) {
```

*/contracts/BondNFT.sol*
Links: [220](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L220), [284](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L284), [292](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L292), [300](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L300), [342](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L342).
```solidity
220:	for (uint i=epoch[_tigAsset]; i<aEpoch; i++) {
284:	for (uint i=0; i<_ids.length; i++) {
292:	for (uint i=0; i<_ids.length; i++) {
300:	for (uint i=0; i<_ids.length; i++) {
342:	for (uint i=0; i<_ids.length; i++) {
```

### [G-02] && In If Statement(s)

Seperating if statements saves gas.
**Example**
```solidity
//From
if (a != HIGH && b != LOW) {
	//Do Something
}
//To
if (a != HIGH) {
	if (b != LOW) {
		//Do Something
	}
}
```

#### Findings:

*/contracts/Trading.sol*
Links: [497](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L497), [499](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L499), [501](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L501), [625](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L625), [838](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L838).
```solidity
497:	if (trade.direction && trade.orderType == 1) {
499:	} else if (!trade.direction && trade.orderType == 1) {
501:	} else if (!trade.direction && trade.orderType == 2) {
625:	if (maxWinPercent > 0 && _toMint > _trade.margin*maxWinPercent/DIVISION_CONSTANT) {
838:	if (_sl < _price && _sl != 0) revert("3");
```

*/contracts/utils/TradingLibrary.sol*
Links: [39](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L39), [41](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L41), [43](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L43), [112](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L112).
```solidity
39:	if (_direction && _currentPrice >= _price) {
41:	} else if (_direction && _currentPrice < _price) {
43:	} else if (!_direction && _currentPrice <= _price) {
112:	if (_chainlinkEnabled && _chainlinkFeed != address(0)) {
```

### [G-03] Structs Can Be Packed Into Fewer Slots

#### Findings

The `PriceData` struct on [L12](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/utils/TradingLibrary.sol#L12) of [TradingLibrary.sol](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/utils/TradingLibrary.sol) uses 6 32 byte slots: [[20], [32], [32], [32], [32], [1]]. The last variable `bool` can be moved to the start to allow for 5 slots (thus saving gas): [[1, 20], [32], [32], [32], [32]].

The `Bond` struct on [L12](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L12) of [BondNFT.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol) uses 11 32 byte slots: [[32], [20], [20], [32], [32], [32], [32], [32], [32], [32], [1]]. The last variable `bool` can be moved to the start to allow for 10 slots (thus saving gas): [[1, 20], [20], [32], [32], [32], [32], [32], [32], [32], [32]].

The `Trade` struct on [L7](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/interfaces/IPosition.sol#L7) of [IPosition.sol](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/interfaces/IPosition.sol) uses 12 32 byte slots: [[32], [32], [32], [1], [32], [32], [32], [32], [20], [32], [20], [32]]. The middle variable `bool` can be moved to the start to allow for 11 slots (thus saving gas): [[1, 20], [20], [32], [32], [32], [32], [32], [32], [32], [32], [32]].

The `MintTrade` struct on [L22](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/interfaces/IPosition.sol#L22) of [IPosition.sol](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/interfaces/IPosition.sol) uses 11 32 byte slots: [[20], [32], [32], [32], [1], [32], [32], [32], [32], [20]]. The middle variable `bool` can be moved to the start to allow for 10 slots (thus saving gas): [[1, 20], [20], [32], [32], [32], [32], [32], [32], [32]].

### [G-04] `uint<size>` Used Rather Than `uint256`

The EVM deals with 32 bytes at a time. For `uint<size>`s less than 32 bytes the EVM performs more operations as a result of the needed size reduction from `uint256` to `uint<size>`. Consider using `uint256`.

#### Findings:

*/contracts/GovNFT.sol*
Links: [22](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L22), [23](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L23), [143](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L143).
```solidity
22:	mapping(uint16 => mapping(address => bool)) public isTrustedAddress;
23:	mapping(uint16 => mapping(bytes => mapping(uint64 => bytes32))) public failedMessages;
143:	uint16 version = 1;
```

### [G-05] Bit Shift(s) Not Used For MUL/DIV/MOD of Powers of 2

Bitwise operations usually save on gas. Expressions that deal with unsigned integers, a power of 2 and the operations MUL/DIV/MOD can often be re-written with bitwise shifts.

#### Findings:

*/contracts/Trading.sol*
Links: [717](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L717), [792](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L792), [954](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L954).
```solidity
717:	_fees.daoFees = _fees.daoFees - _fees.referralFees*2;
792:	_daoFeesPaid = _daoFeesPaid-_referralFeesPaid*2;
954:	require(_daoFees >= _botFees+_referralFees*2);
```
**Suggested Change**
```solidity
717:	_fees.daoFees = _fees.daoFees - _fees.referralFees << 1;
792:	_daoFeesPaid = _daoFeesPaid-_referralFeesPaid << 1;
954:	require(_daoFees >= _botFees+_referralFees << 1);
```

### [G-06] && In Require Statement(s)

Seperating require statements saves gas.
**Example**
```solidity
//From
require(a != HIGH && b != LOW);
//To
require(a != HIGH);
require(b != LOW);
```

#### Findings:

*/contracts/Trading.sol*
Links: [887](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L887).
```solidity
887:	require(_proxy.proxy == _msgSender() && _proxy.time >= block.timestamp, "Proxy not approved");
```