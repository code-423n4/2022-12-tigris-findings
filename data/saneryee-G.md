# Gas Optimizations Report

|         | Issue                                                                                                                              | Instances |
| ------- | :--------------------------------------------------------------------------------------------------------------------------------- | :-------: |
| [G-001] | Structs can be packed into fewer storage slots                                                                                     |     3     |
| [G-002] | x += y or x -= y costs more gas than x = x + y or x = x - y for state variables                                                    |     5     |
| [G-003] | Splitting require() statements that use `&&` saves gas                                                                             |     3     |
| [G-004] | `storage` pointer to a structure is cheaper than copying each value of the structure into `memory`, same for `array` and `mapping` |    32     |
| [G-005] | Functions guaranteed to revert when called by normal users can be marked payable                                                   |    60     |
| [G-006] | Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate                 |    11     |
| [G-007] | Using `calldata` instead of memory for read-only arguments in external functions saves gas                                         |     4     |
| [G-008] | internal functions only called once can be inlined to save gas                                                                     |     5     |
| [G-009] | Replace modifier with function                                                                                                     |     5     |
| [G-010] | revert operator should be in the code as early as reasonably possible                                                              |     2     |

## [G-001] Structs can be packed into fewer storage slots

### Impact

Each slot saved can avoid an extra Gsset (20000 gas) for the first setting of the struct. Subsequent reads as well as writes have smaller gas savings.

### Findings

Total:3

[contracts/utils/TradingLibrary.sol#L12-19](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/utils/TradingLibrary.sol#L12-19)

```solidity
12:    struct PriceData {
13:        address provider;
14:        uint256 asset;
15:        uint256 price;
16:        uint256 spread;
17:        uint256 timestamp;
18:        bool isClosed;
19:    }
```

[contracts/interfaces/ITrading.sol#L9-19](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/interfaces/ITrading.sol#L9-19)

```solidity
9:    struct TradeInfo {
10:            uint256 margin;
11:            address marginAsset;
12:            address stableVault;
13:            uint256 leverage;
14:            uint256 asset;
15:            bool direction;
16:            uint256 tpPrice;
17:            uint256 slPrice;
18:            bytes32 referral;
19:        }
```

[contracts/interfaces/IPosition.sol#L22-33](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/interfaces/IPosition.sol#L22-33)

```solidity
22:    struct MintTrade {
23:            address account;
24:            uint256 margin;
25:            uint256 leverage;
26:            uint256 asset;
27:            bool direction;
28:            uint256 price;
29:            uint256 tp;
30:            uint256 sl;
31:            uint256 orderType;
32:            address tigAsset;
33:        }
```

## [G-002] x += y or x -= y costs more gas than x = x + y or x = x - y for state variables

### Impact

Using the addition operator instead of plus-equals saves 113 gas. Usually does not work with struct and mappings.

### Findings

Total:5

[contracts/Trading.sol#L293](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Trading.sol#L293)

```solidity
293:    _addMargin -= _fee;
```

[contracts/BondNFT.sol#L152](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/BondNFT.sol#L152)

```solidity
152:    amount += _claimAmount;
```

[contracts/TradingExtension.sol#L187](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/TradingExtension.sol#L187)

```solidity
187:    _price -= _price * _spread / DIVISION_CONSTANT;
```

[contracts/TradingExtension.sol#L185](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/TradingExtension.sol#L185)

```solidity
185:    _price += _price * _spread / DIVISION_CONSTANT;
```

[contracts/GovNFT.sol#L52](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/GovNFT.sol#L52)

```solidity
52:    counter += 1;
```

## [G-003] Splitting require() statements that use `&&` saves gas

### Impact

When using `&&` in a `require()` statement, the entire statement will be evaluated before the transaction reverts. If the first condition is false, the second condition will not be evaluated. This means that if the first condition is false, the second condition will not be evaluated, which is a waste of gas. Splitting the `require()` statement into two separate statements will save gas.

### Findings

Total:3

[contracts/Trading.sol#L887](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Trading.sol#L887)

```solidity
887:    require(_proxy.proxy == _msgSender() && _proxy.time >= block.timestamp, "Proxy not approved");
```

[contracts/PairsContract.sol#L52](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/PairsContract.sol#L52)

```solidity
52:    require(_maxLeverage >= _minLeverage && _minLeverage > 0, "Wrong leverage values");
```

[contracts/utils/TradingLibrary.sol#L116-119](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/utils/TradingLibrary.sol#L116-119)

```solidity
116:    require(
117:                        _priceData.price < assetChainlinkPrice+assetChainlinkPrice*2/100 &&
118:                        _priceData.price > assetChainlinkPrice-assetChainlinkPrice*2/100, "!chainlinkPrice"
119:                    );
```

## [G-004] `storage` pointer to a structure is cheaper than copying each value of the structure into `memory`, same for `array` and `mapping`

### Impact

It may not be obvious, but every time you copy a storage `struct/array/mapping` to a `memory` variable, you are literally copying each member by reading it from `storage`, which is expensive. And when you use the `storage` keyword, you are just storing a pointer to the storage, which is much cheaper.

### Findings

Total:32

[contracts/Position.sol#L295](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Position.sol#L295)

```solidity
295:    uint[] memory _ids = new uint[](balanceOf(_user));
```

[contracts/Position.sol#L42](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Position.sol#L42)

```solidity
42:    Trade memory _trade = _trades[_id];
```

[contracts/Position.sol#L303](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Position.sol#L303)

```solidity
303:    uint[] memory _ids = new uint[](_to-_from);
```

[contracts/Lock.sol#L111](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Lock.sol#L111)

```solidity
111:    address[] memory assets = bondNFT.getAssets();
```

[contracts/Trading.sol#L699](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Trading.sol#L699)

```solidity
699:    IPairsContract.Asset memory asset = pairsContract.idToAsset(_asset);
```

[contracts/Trading.sol#L773](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Trading.sol#L773)

```solidity
773:    IPairsContract.Asset memory asset = pairsContract.idToAsset(_asset);
```

[contracts/Trading.sol#L237](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Trading.sol#L237)

```solidity
237:    IPosition.Trade memory _trade = position.trades(_id);
```

[contracts/Trading.sol#L270](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Trading.sol#L270)

```solidity
270:    IPosition.Trade memory _trade = position.trades(_id);
```

[contracts/Trading.sol#L363](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Trading.sol#L363)

```solidity
363:    IPosition.Trade memory _trade = position.trades(_id);
```

[contracts/Trading.sol#L391](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Trading.sol#L391)

```solidity
391:    IPosition.Trade memory _trade = position.trades(_id);
```

[contracts/Trading.sol#L427](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Trading.sol#L427)

```solidity
427:    IPosition.Trade memory _trade = position.trades(_id);
```

[contracts/Trading.sol#L463](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Trading.sol#L463)

```solidity
463:    IPosition.Trade memory _trade = position.trades(_id);
```

[contracts/Trading.sol#L539](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Trading.sol#L539)

```solidity
539:    IPosition.Trade memory _trade = position.trades(_id);
```

[contracts/Trading.sol#L859](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Trading.sol#L859)

```solidity
859:    Delay memory _delay = blockDelayPassed[_id];
```

[contracts/Trading.sol#L393](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Trading.sol#L393)

```solidity
393:    IPairsContract.Asset memory asset = pairsContract.idToAsset(_trade.asset);
```

[contracts/Trading.sol#L429](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Trading.sol#L429)

```solidity
429:    IPairsContract.Asset memory asset = pairsContract.idToAsset(_trade.asset);
```

[contracts/Trading.sol#L612](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Trading.sol#L612)

```solidity
612:    (IPosition.Trade memory _trade, uint256 _positionSize, int256 _payout) = tradingExtension._closePosition(_id, _price, _percent);
```

[contracts/Trading.sol#L492](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Trading.sol#L492)

```solidity
492:    IPosition.Trade memory trade = position.trades(_id);
```

[contracts/Trading.sol#L700](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Trading.sol#L700)

```solidity
700:    Fees memory _fees = openFees;
```

[contracts/Trading.sol#L886](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Trading.sol#L886)

```solidity
886:    Proxy memory _proxy = proxyApprovals[_trader];
```

[contracts/Trading.sol#L55](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Trading.sol#L55)

```solidity
55:    ) external returns (IPosition.Trade memory _trade, uint256 _positionSize, int256 _payout);
```

[contracts/Trading.sol#L774](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Trading.sol#L774)

```solidity
774:    Fees memory _fees = closeFees;
```

[contracts/BondNFT.sol#L104](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/BondNFT.sol#L104)

```solidity
104:    Bond memory bond = idToBond(_id);
```

[contracts/BondNFT.sol#L141](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/BondNFT.sol#L141)

```solidity
141:    Bond memory bond = idToBond(_id);
```

[contracts/BondNFT.sol#L172](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/BondNFT.sol#L172)

```solidity
172:    Bond memory bond = idToBond(_id);
```

[contracts/BondNFT.sol#L328](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/BondNFT.sol#L328)

```solidity
328:    Bond memory bond = idToBond(_id);
```

[contracts/BondNFT.sol#L340](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/BondNFT.sol#L340)

```solidity
340:    uint[] memory _ids = new uint[](balanceOf(_user));
```

[contracts/BondNFT.sol#L251](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/BondNFT.sol#L251)

```solidity
251:    Bond memory bond = _idToBond[_id];
```

[contracts/TradingExtension.sol#L213](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/TradingExtension.sol#L213)

```solidity
213:    IPairsContract.Asset memory asset = pairsContract.idToAsset(_asset);
```

[contracts/TradingExtension.sol#L95](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/TradingExtension.sol#L95)

```solidity
95:    IPosition.Trade memory _trade = position.trades(_id);
```

[contracts/GovNFT.sol#L324](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/GovNFT.sol#L324)

```solidity
324:    uint[] memory _ids = new uint[](balanceOf(_user));
```

[contracts/utils/TradingLibrary.sol#L77](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/utils/TradingLibrary.sol#L77)

```solidity
77:    IPosition.Trade memory _trade = IPosition(_positions).trades(_id);
```

## [G-005] Functions guaranteed to revert when called by normal users can be marked payable

### Impact

If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.
The extra opcodes avoided are `CALLVALUE(2)`,`DUP1(3)`,`ISZERO(3)`,`PUSH2(3)`,`JUMPI(10)`,`PUSH1(3)`,`DUP1(3)`,`REVERT(0)`,`JUMPDEST(1)`,`POP(2)`, which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

### Findings

Total:60

[contracts/Position.sol#L209](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Position.sol#L209)

```solidity
209:    function addToPosition(uint256 _id, uint256 _newMargin, uint256 _newPrice) external onlyMinter {
```

[contracts/Position.sol#L168](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Position.sol#L168)

```solidity
168:    function executeLimitOrder(uint256 _id, uint256 _price, uint256 _newMargin) external onlyMinter {
```

[contracts/Position.sol#L99](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Position.sol#L99)

```solidity
99:    function updateFunding(uint256 _asset, address _tigAsset, uint256 _longOi, uint256 _shortOi, uint256 _baseFundingRate, uint _vaultFundingPercent) external onlyMinter {
```

[contracts/Position.sol#L197](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Position.sol#L197)

```solidity
197:    function modifyMargin(uint256 _id, uint256 _newMargin, uint256 _newLeverage) external onlyMinter {
```

[contracts/Position.sol#L133](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Position.sol#L133)

```solidity
133:    ) external onlyMinter {
```

[contracts/Position.sol#L310](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Position.sol#L310)

```solidity
310:    function setMinter(address _minter, bool _bool) external onlyOwner {
```

[contracts/Position.sol#L220](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Position.sol#L220)

```solidity
220:    function setAccInterest(uint256 _id) external onlyMinter {
```

[contracts/Position.sol#L230](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Position.sol#L230)

```solidity
230:    function reducePosition(uint256 _id, uint256 _percent) external onlyMinter {
```

[contracts/Position.sol#L242](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Position.sol#L242)

```solidity
242:    function modifyTp(uint _id, uint _tpPrice) external onlyMinter {
```

[contracts/Position.sol#L260](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Position.sol#L260)

```solidity
260:    function burn(uint _id) external onlyMinter {
```

[contracts/Position.sol#L85](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Position.sol#L85)

```solidity
85:    function setBaseURI(string memory _newBaseURI) external onlyOwner {
```

[contracts/Position.sol#L252](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Position.sol#L252)

```solidity
252:    function modifySl(uint _id, uint _slPrice) external onlyMinter {
```

[contracts/Lock.sol#L130](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Lock.sol#L130)

```solidity
130:    ) external onlyOwner() {
```

[contracts/Lock.sol#L140](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Lock.sol#L140)

```solidity
140:    ) external onlyOwner() {
```

[contracts/Trading.sol#L977](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Trading.sol#L977)

```solidity
977:    ) external onlyOwner() {
```

[contracts/Trading.sol#L952](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Trading.sol#L952)

```solidity
952:    function setFees(bool _open, uint _daoFees, uint _burnFees, uint _referralFees, uint _botFees, uint _percent) external onlyOwner {
```

[contracts/Trading.sol#L939](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Trading.sol#L939)

```solidity
939:    function setLimitOrderPriceRange(uint _range) external onlyOwner {
```

[contracts/BondNFT.sol#L357](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/BondNFT.sol#L357)

```solidity
357:    function setAllowedAsset(address _asset, bool _bool) external onlyOwner {
```

[contracts/BondNFT.sol#L62](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/BondNFT.sol#L62)

```solidity
62:    ) external onlyManager() returns(uint id) {
```

[contracts/BondNFT.sol#L140](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/BondNFT.sol#L140)

```solidity
140:    ) external onlyManager() returns(uint amount, uint lockAmount, address asset, address _owner) {
```

[contracts/BondNFT.sol#L171](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/BondNFT.sol#L171)

```solidity
171:    ) public onlyManager() returns(uint amount, address tigAsset) {
```

[contracts/BondNFT.sol#L349](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/BondNFT.sol#L349)

```solidity
349:    function addAsset(address _asset) external onlyOwner {
```

[contracts/BondNFT.sol#L103](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/BondNFT.sol#L103)

```solidity
103:    ) external onlyManager() {
```

[contracts/BondNFT.sol#L362](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/BondNFT.sol#L362)

```solidity
362:    function setBaseURI(string calldata _newBaseURI) external onlyOwner {
```

[contracts/BondNFT.sol#L199](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/BondNFT.sol#L199)

```solidity
199:    ) public onlyManager() returns(uint amount) {
```

[contracts/BondNFT.sol#L368](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/BondNFT.sol#L368)

```solidity
368:    ) public onlyOwner() {
```

[contracts/TradingExtension.sol#L131](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/TradingExtension.sol#L131)

```solidity
131:    ) public onlyProtocol {
```

[contracts/TradingExtension.sol#L140](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/TradingExtension.sol#L140)

```solidity
140:    ) public onlyProtocol {
```

[contracts/TradingExtension.sol#L65](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/TradingExtension.sol#L65)

```solidity
65:    ) external onlyProtocol returns (IPosition.Trade memory _trade, uint256 _positionSize, int256 _payout) {
```

[contracts/TradingExtension.sol#L144](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/TradingExtension.sol#L144)

```solidity
144:    function setMaxGasPrice(uint _maxGasPrice) external onlyOwner {
```

[contracts/TradingExtension.sol#L274](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/TradingExtension.sol#L274)

```solidity
274:    function setPaused(bool _paused) external onlyOwner {
```

[contracts/TradingExtension.sol#L231](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/TradingExtension.sol#L231)

```solidity
231:    function setChainlinkEnabled(bool _bool) external onlyOwner {
```

[contracts/TradingExtension.sol#L193](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/TradingExtension.sol#L193)

```solidity
193:    ) external onlyProtocol {
```

[contracts/TradingExtension.sol#L240](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/TradingExtension.sol#L240)

```solidity
240:    function setNode(address _node, bool _bool) external onlyOwner {
```

[contracts/PairsContract.sol#L115](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/PairsContract.sol#L115)

```solidity
115:    function pauseAsset(uint256 _asset, bool _isPaused) external onlyOwner {
```

[contracts/PairsContract.sol#L174](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/PairsContract.sol#L174)

```solidity
174:    function modifyShortOi(uint256 _asset, address _tigAsset, bool _onOpen, uint256 _amount) external onlyProtocol {
```

[contracts/PairsContract.sol#L48](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/PairsContract.sol#L48)

```solidity
48:    function addAsset(uint256 _asset, string memory _name, address _chainlinkFeed, uint256 _minLeverage, uint256 _maxLeverage, uint256 _feeMultiplier, uint256 _baseFundingRate) external onlyOwner {
```

[contracts/PairsContract.sol#L154](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/PairsContract.sol#L154)

```solidity
154:    function modifyLongOi(uint256 _asset, address _tigAsset, bool _onOpen, uint256 _amount) external onlyProtocol {
```

[contracts/PairsContract.sol#L129](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/PairsContract.sol#L129)

```solidity
129:    function setProtocol(address _protocol) external onlyOwner {
```

[contracts/PairsContract.sol#L33](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/PairsContract.sol#L33)

```solidity
33:    function setAssetChainlinkFeed(uint256 _asset, address _feed) external onlyOwner {
```

[contracts/PairsContract.sol#L139](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/PairsContract.sol#L139)

```solidity
139:    function setMaxOi(uint256 _asset, address _tigAsset, uint256 _maxOi) external onlyOwner {
```

[contracts/PairsContract.sol#L125](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/PairsContract.sol#L125)

```solidity
125:    function setMaxBaseFundingRate(uint256 _maxBaseFundingRate) external onlyOwner {
```

[contracts/PairsContract.sol#L92](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/PairsContract.sol#L92)

```solidity
92:    function setAssetBaseFundingRate(uint256 _asset, uint256 _baseFundingRate) external onlyOwner {
```

[contracts/PairsContract.sol#L104](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/PairsContract.sol#L104)

```solidity
104:    function updateAssetFeeMultiplier(uint256 _asset, uint256 _feeMultiplier) external onlyOwner {
```

[contracts/PairsContract.sol#L73](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/PairsContract.sol#L73)

```solidity
73:    function updateAssetLeverage(uint256 _asset, uint256 _minLeverage, uint256 _maxLeverage) external onlyOwner {
```

[contracts/GovNFT.sol#L104](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/GovNFT.sol#L104)

```solidity
104:    function mintMany(uint _amount) external onlyOwner {
```

[contracts/GovNFT.sol#L307](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/GovNFT.sol#L307)

```solidity
307:    function setAllowedAsset(address _asset, bool _bool) external onlyOwner {
```

[contracts/GovNFT.sol#L240](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/GovNFT.sol#L240)

```solidity
240:    function setEndpoint(ILayerZeroEndpoint _endpoint) external onlyOwner {
```

[contracts/GovNFT.sol#L236](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/GovNFT.sol#L236)

```solidity
236:    function setGas(uint _gas) external onlyOwner {
```

[contracts/GovNFT.sol#L114](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/GovNFT.sol#L114)

```solidity
114:    function setTrustedAddress(uint16 _chainId, address _contract, bool _bool) external onlyOwner {
```

[contracts/GovNFT.sol#L300](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/GovNFT.sol#L300)

```solidity
300:    function addAsset(address _asset) external onlyOwner {
```

[contracts/GovNFT.sol#L46](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/GovNFT.sol#L46)

```solidity
46:    function setBaseURI(string calldata _newBaseURI) external onlyOwner {
```

[contracts/GovNFT.sol#L311](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/GovNFT.sol#L311)

```solidity
311:    function setMaxBridge(uint256 _max) external onlyOwner {
```

[contracts/GovNFT.sol#L110](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/GovNFT.sol#L110)

```solidity
110:    function mint() external onlyOwner {
```

[contracts/Referrals.sol#L32](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Referrals.sol#L32)

```solidity
32:    function setReferred(address _referredTrader, bytes32 _hash) external onlyProtocol {
```

[contracts/Referrals.sol#L53](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Referrals.sol#L53)

```solidity
53:    function setProtocol(address _protocol) external onlyOwner {
```

[contracts/Referrals.sol#L65](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Referrals.sol#L65)

```solidity
65:    ) external onlyOwner {
```

[contracts/StableVault.sol#L78](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/StableVault.sol#L78)

```solidity
78:    function listToken(address _token) external onlyOwner {
```

[contracts/StableVault.sol#L89](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/StableVault.sol#L89)

```solidity
89:    function delistToken(address _token) external onlyOwner {
```

[contracts/utils/MetaContext.sol#L9](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/utils/MetaContext.sol#L9)

```solidity
9:    function setTrustedForwarder(address _forwarder, bool _bool) external onlyOwner {
```

## [G-006] Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate

### Impact

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to [not having to recalculate the key's keccak256 hash](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0) (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

### Findings

Total:11

[contracts/Position.sol#L36-38](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Position.sol#L36-38)

```solidity
36:    mapping(uint256 => int256) private initId;
37:        mapping(uint256 => mapping(address => uint256)) private longOi;
38:        mapping(uint256 => mapping(address => uint256)) private shortOi;
```

[contracts/Position.sol#L27-30](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Position.sol#L27-30)

```solidity
27:    mapping(uint256 => mapping(uint256 => uint256)) private _assetOpenPositionsIndexes;
28:
29:        mapping(uint256 => uint256[]) private _limitOrders; // List of limit order nft ids per asset
30:        mapping(uint256 => mapping(uint256 => uint256)) private _limitOrderIndexes; // Keeps track of asset -> id -> array index
```

[contracts/Position.sol#L33-35](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Position.sol#L33-35)

```solidity
33:    mapping(uint256 => mapping(address => int256)) public fundingDeltaPerSec;
34:        mapping(uint256 => mapping(address => mapping(bool => int256))) private accInterestPerOi;
35:        mapping(uint256 => mapping(address => uint256)) private lastUpdate;
```

[contracts/Position.sol#L18-21](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Position.sol#L18-21)

```solidity
18:    mapping(uint => mapping(address => uint)) public vaultFundingPercent;
19:
20:        mapping(address => bool) private _isMinter; // Trading contract should be minter
21:        mapping(uint256 => Trade) private _trades; // NFT id to Trade
```

[contracts/Lock.sol#L18-19](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Lock.sol#L18-19)

```solidity
18:    mapping(address => bool) public allowedAssets;
19:        mapping(address => uint) public totalLocked;
```

[contracts/BondNFT.sol#L36-38](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/BondNFT.sol#L36-38)

```solidity
36:    mapping(uint => Bond) private _idToBond;
37:        mapping(address => uint) public totalShares;
38:        mapping(address => mapping(address => uint)) public userDebt; // user => tigAsset => amount
```

[contracts/BondNFT.sol#L32-35](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/BondNFT.sol#L32-35)

```solidity
32:    mapping(address => bool) public allowedAsset;
33:        mapping(address => uint) private assetsIndex;
34:        mapping(uint256 => mapping(address => uint256)) private bondPaid;
35:        mapping(address => mapping(uint256 => uint256)) private accRewardsPerShare; // tigAsset => epoch => accRewardsPerShare
```

[contracts/TradingExtension.sol#L17-19](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/TradingExtension.sol#L17-19)

```solidity
17:    mapping(address => bool) private isNode;
18:        mapping(address => uint) public minPositionSize;
19:        mapping(address => bool) public allowedMargin;
```

[contracts/GovNFT.sol#L265-269](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/GovNFT.sol#L265-269)

```solidity
265:    mapping(address => bool) private _allowedAsset;
266:        mapping(address => uint) private assetsIndex;
267:        mapping(address => mapping(address => uint256)) private userPaid;
268:        mapping(address => mapping(address => uint256)) private userDebt;
269:        mapping(address => uint256) private accRewardsPerNFT;
```

[contracts/Referrals.sol#L13-14](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Referrals.sol#L13-14)

```solidity
13:    mapping(bytes32 => address) private _referral;
14:        mapping(address => bytes32) private _referred;
```

[contracts/StableVault.sol#L29-30](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/StableVault.sol#L29-30)

```solidity
29:    mapping(address => bool) public allowed;
30:        mapping(address => uint) private tokenIndex;
```

## [G-007] Using `calldata` instead of memory for read-only arguments in external functions saves gas

### Impact

When a function with a `memory` array is called externally, the `abi.decode()` step has to use a `for-loop` to copy each index of the `calldata` to the `memory` index. Each iteration of this `for-loop` costs at least 60 gas (i.e. `60 * <mem_array>.length`). Using `calldata` directly, obliviates the need for such a loop in the contract code and runtime execution. Note that even if an interface defines a function as having `memory` arguments, it's still valid for implementation contracs to use `calldata` arguments instead.<br><br> If the array is passed to an `internal` function which passes the array to another internal function where the array is modified and therefore `memory` is used in the `external` call, it's still more gass-efficient to use calldata when the external function uses modifiers, since the modifiers may prevent the internal functions from being called. Structs have the same overhead as an array of length one.

### Findings

Total:4

[contracts/Lock.sol#L138-139](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Lock.sol#L138-139)

```solidity
138:    function sendNFTs(
139:            uint[] memory _ids
```

[contracts/GovNFT.sol#L124-128](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/GovNFT.sol#L124-128)

```solidity
124:    function crossChain(
125:            uint16 _dstChainId,
126:            bytes memory _destination,
127:            address _to,
128:            uint256[] memory tokenId
```

[contracts/GovNFT.sol#L219-223](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/GovNFT.sol#L219-223)

```solidity
219:    function crossChain(
220:            uint16 _dstChainId,
221:            bytes memory _destination,
222:            address _to,
223:            uint256[] memory tokenId
```

[contracts/Referrals.sol#L60-64](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Referrals.sol#L60-64)

```solidity
60:    function initRefs(
61:            address[] memory _codeOwners,
62:            bytes32[] memory _ownedCodes,
63:            address[] memory _referredA,
64:            bytes32[] memory _referredTo
```

## [G-008] internal functions only called once can be inlined to save gas

### Impact

Not inlining costs 20 to 40 gas because of two extra `JUMP` instructions and additional stack operations needed for function calls.

### Findings

Total:5

[contracts/Position.sol#L323](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Position.sol#L323)

```solidity
323:    function _msgData() internal view override(Context, MetaContext) returns (bytes calldata) {
```

[contracts/GovNFT.sol#L335](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/GovNFT.sol#L335)

```solidity
335:    function _msgData() internal view override(Context, MetaContext) returns (bytes calldata) {
```

[contracts/StableToken.sol#L62](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/StableToken.sol#L62)

```solidity
62:    function _msgData() internal view override(Context, MetaContext) returns (bytes calldata) {
```

[contracts/utils/MetaContext.sol#L17](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/utils/MetaContext.sol#L17)

```solidity
17:    function _msgSender() internal view virtual override returns (address sender) {
```

[contracts/utils/MetaContext.sol#L29](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/utils/MetaContext.sol#L29)

```solidity
29:    function _msgData() internal view virtual override returns (bytes calldata) {
```

## [G-009] Replace modifier with function

### Impact

Modifiers make code more elegant, but cost more than normal functions.

### Findings

Total:5

[contracts/Position.sol#L314](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Position.sol#L314)

```solidity
314:    modifier onlyMinter() {
```

[contracts/BondNFT.sol#L372](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/BondNFT.sol#L372)

```solidity
372:    modifier onlyManager() {
```

[contracts/PairsContract.sol#L189](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/PairsContract.sol#L189)

```solidity
189:    modifier onlyProtocol() {
```

[contracts/StableToken.sol#L51](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/StableToken.sol#L51)

```solidity
51:    modifier onlyMinter() {
```

[contracts/Referrals.sol#L80](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Referrals.sol#L80)

```solidity
80:    modifier onlyProtocol() {
```

## [G-010] revert operator should be in the code as early as reasonably possible

### Impact

`revert` operator should be in the code as early as reasonably possible

### Findings

Total:2

[contracts/Trading.sol#L654](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Trading.sol#L654)

```solidity
654:    if (tigAsset.balanceOf(address(this)) != _balBefore + _margin) revert BadDeposit();
```

[contracts/Trading.sol#L675](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Trading.sol#L675)

```solidity
675:    if (IERC20(_outputToken).balanceOf(address(this)) != _balBefore + _toMint/(10**(18-ExtendedIERC20(_outputToken).decimals()))) revert BadWithdraw();
```
