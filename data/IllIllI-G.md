
## Summary

### Gas Optimizations
| |Issue|Instances|Total Gas Saved|
|-|:-|:-:|:-:|
| [G&#x2011;01] | Multiple `address`/ID mappings can be combined into a single `mapping` of an `address`/ID to a `struct`, where appropriate | 5 |  - |
| [G&#x2011;02] | State variables only set in the constructor should be declared `immutable` | 7 |  14679 |
| [G&#x2011;03] | State variables can be packed into fewer storage slots | 1 |  - |
| [G&#x2011;04] | Structs can be packed into fewer storage slots | 6 |  - |
| [G&#x2011;05] | Using `storage` instead of `memory` for structs/arrays saves gas | 2 |  8400 |
| [G&#x2011;06] | Avoid contract existence checks by using low level calls | 40 |  4000 |
| [G&#x2011;07] | Multiple accesses of a mapping/array should use a local variable cache | 39 |  1638 |
| [G&#x2011;08] | The result of function calls should be cached rather than re-calling the function | 1 |  - |
| [G&#x2011;09] | `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables | 1 |  113 |
| [G&#x2011;10] | `internal` functions only called once can be inlined to save gas | 2 |  40 |
| [G&#x2011;11] | Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement | 1 |  85 |
| [G&#x2011;12] | `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops | 16 |  960 |
| [G&#x2011;13] | `require()`/`revert()` strings longer than 32 bytes cost extra gas | 4 |  - |
| [G&#x2011;14] | Optimize names to save gas | 25 |  550 |
| [G&#x2011;15] | Use a more recent version of solidity | 21 |  - |
| [G&#x2011;16] | Splitting `require()` statements that use `&&` saves gas | 3 |  9 |
| [G&#x2011;17] | Don't compare boolean expressions to boolean literals | 2 |  18 |
| [G&#x2011;18] | Ternary unnecessary | 2 |  - |
| [G&#x2011;19] | `require()` or `revert()` statements that check input arguments should be at the top of the function | 1 |  - |
| [G&#x2011;20] | Use custom errors rather than `revert()`/`require()` strings to save gas | 2 |  - |
| [G&#x2011;21] | Functions guaranteed to revert when called by normal users can be marked `payable` | 69 |  1449 |
| [G&#x2011;22] | Don't use `_msgSender()` if not supporting EIP-2771 | 30 |  480 |

Total: 280 instances over 22 issues with **32421 gas** saved

Gas totals use lower bounds of ranges and count two iterations of each `for`-loop. All values above are runtime, not deployment, values; deployment values are listed in the individual issue descriptions. The table above as well as its gas numbers do not include any of the excluded findings.





## Gas Optimizations

### [G&#x2011;01]  Multiple `address`/ID mappings can be combined into a single `mapping` of an `address`/ID to a `struct`, where appropriate
Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (**20000 gas**) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save **~42 gas per access** due to [not having to recalculate the key's keccak256 hash](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0) (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

*There are 5 instances of this issue:*

```solidity
File: contracts/BondNFT.sol

32        mapping(address => bool) public allowedAsset;
33:       mapping(address => uint) private assetsIndex;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L32-L33

```solidity
File: contracts/GovNFT.sol

265       mapping(address => bool) private _allowedAsset;
266       mapping(address => uint) private assetsIndex;
267       mapping(address => mapping(address => uint256)) private userPaid;
268       mapping(address => mapping(address => uint256)) private userDebt;
```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L265-L269

```solidity
File: contracts/Position.sol

26        mapping(uint256 => uint256[]) private _assetOpenPositions;
27        mapping(uint256 => mapping(uint256 => uint256)) private _assetOpenPositionsIndexes;
28    
29        mapping(uint256 => uint256[]) private _limitOrders; // List of limit order nft ids per asset
30        mapping(uint256 => mapping(uint256 => uint256)) private _limitOrderIndexes; // Keeps track of asset -> id -> array index
31    
32        // Funding
33        mapping(uint256 => mapping(address => int256)) public fundingDeltaPerSec;
34        mapping(uint256 => mapping(address => mapping(bool => int256))) private accInterestPerOi;
35        mapping(uint256 => mapping(address => uint256)) private lastUpdate;
36        mapping(uint256 => int256) private initId;
37        mapping(uint256 => mapping(address => uint256)) private longOi;
38:       mapping(uint256 => mapping(address => uint256)) private shortOi;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L24-L38

```solidity
File: contracts/StableVault.sol

29        mapping(address => bool) public allowed;
30:       mapping(address => uint) private tokenIndex;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/StableVault.sol#L29-L30

```solidity
File: contracts/TradingExtension.sol

17        mapping(address => bool) private isNode;
18        mapping(address => uint) public minPositionSize;
19:       mapping(address => bool) public allowedMargin;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/TradingExtension.sol#L17-L19

### [G&#x2011;02]  State variables only set in the constructor should be declared `immutable`
Avoids a Gsset (**20000 gas**) in the constructor, and replaces the first access in each transaction (Gcoldsload - **2100 gas**) and each access thereafter (Gwarmacces - **100 gas**) with a `PUSH32` (**3 gas**). 

While `string`s are not value types, and therefore cannot be `immutable`/`constant` if not hard-coded outside of the constructor, the same behavior can be achieved by making the current contract `abstract` with `virtual` functions for the `string` accessors, and having a child contract override the functions with the hard-coded implementation-specific values.

*There are 7 instances of this issue:*

```solidity
File: contracts/TradingExtension.sol

/// @audit trading (constructor)
35:           trading = _trading;

/// @audit pairsContract (constructor)
36:           pairsContract = IPairsContract(_pairsContract);

/// @audit referrals (constructor)
37:           referrals = IReferrals(_ref);

/// @audit position (constructor)
38:           position = IPosition(_position);

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/TradingExtension.sol#L35

```solidity
File: contracts/Trading.sol

/// @audit pairsContract (constructor)
151:          pairsContract = IPairsContract(_pairsContract);

/// @audit position (constructor)
149:          position = IPosition(_position);

/// @audit gov (constructor)
150:          gov = IGovNFT(_gov);

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L151

### [G&#x2011;03]  State variables can be packed into fewer storage slots
If variables occupying the same slot are both written the same function or by the constructor, avoids a separate Gsset (**20000 gas**). Reads of the variables can also be cheaper

*There is 1 instance of this issue:*

```solidity
File: contracts/TradingExtension.sol

/// @audit Variable ordering with 9 slots instead of the current 10:
///           uint256(32):validSignatureTimer, mapping(32):isNode, mapping(32):minPositionSize, mapping(32):allowedMargin, uint256(32):maxGasPrice, address(20):trading, bool(1):chainlinkEnabled, bool(1):paused, address(20):pairsContract, address(20):referrals, address(20):position
13:       address public trading;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/TradingExtension.sol#L13

### [G&#x2011;04]  Structs can be packed into fewer storage slots
Each slot saved can avoid an extra Gsset (**20000 gas**) for the first setting of the struct. Subsequent reads as well as writes have smaller gas savings

*There are 6 instances of this issue:*

```solidity
File: contracts/BondNFT.sol

/// @audit Variable ordering with 10 slots instead of the current 11:
///           uint256(32):id, uint256(32):amount, uint256(32):mintEpoch, uint256(32):mintTime, uint256(32):expireEpoch, uint256(32):pending, uint256(32):shares, uint256(32):period, address(20):owner, bool(1):expired, address(20):asset
12        struct Bond {
13            uint id;
14            address owner;
15            address asset;
16            uint amount;
17            uint mintEpoch;
18            uint mintTime;
19            uint expireEpoch;
20            uint pending;
21            uint shares;
22            uint period;
23            bool expired;
24:       }

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L12-L24

```solidity
File: contracts/interfaces/IPosition.sol

/// @audit Variable ordering with 11 slots instead of the current 12:
///           uint256(32):margin, uint256(32):leverage, uint256(32):asset, uint256(32):price, uint256(32):tpPrice, uint256(32):slPrice, uint256(32):orderType, uint256(32):id, int256(32):accInterest, address(20):trader, bool(1):direction, address(20):tigAsset
7         struct Trade {
8             uint margin;
9             uint leverage;
10            uint asset;
11            bool direction;
12            uint price;
13            uint tpPrice;
14            uint slPrice;
15            uint orderType;
16            address trader;
17            uint id;
18            address tigAsset;
19            int accInterest;
20:       }

/// @audit Variable ordering with 9 slots instead of the current 10:
///           uint256(32):margin, uint256(32):leverage, uint256(32):asset, uint256(32):price, uint256(32):tp, uint256(32):sl, uint256(32):orderType, address(20):account, bool(1):direction, address(20):tigAsset
22        struct MintTrade {
23            address account;
24            uint256 margin;
25            uint256 leverage;
26            uint256 asset;
27            bool direction;
28            uint256 price;
29            uint256 tp;
30            uint256 sl;
31            uint256 orderType;
32            address tigAsset;
33:       }

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/IPosition.sol#L7-L20

```solidity
File: contracts/interfaces/ITrading.sol

/// @audit Variable ordering with 8 slots instead of the current 9:
///           uint256(32):margin, uint256(32):leverage, uint256(32):asset, uint256(32):tpPrice, uint256(32):slPrice, bytes32(32):referral, address(20):marginAsset, bool(1):direction, address(20):stableVault
9         struct TradeInfo {
10            uint256 margin;
11            address marginAsset;
12            address stableVault;
13            uint256 leverage;
14            uint256 asset;
15            bool direction;
16            uint256 tpPrice;
17            uint256 slPrice;
18            bytes32 referral;
19:       }

/// @audit Variable ordering with 5 slots instead of the current 6:
///           uint256(32):deadline, uint256(32):amount, bytes32(32):r, bytes32(32):s, uint8(1):v, bool(1):usePermit
21        struct ERC20PermitData {
22            uint256 deadline;
23            uint256 amount;
24            uint8 v;
25            bytes32 r;
26            bytes32 s;
27            bool usePermit;
28:       }

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/ITrading.sol#L9-L19

```solidity
File: contracts/utils/TradingLibrary.sol

/// @audit Variable ordering with 5 slots instead of the current 6:
///           uint256(32):asset, uint256(32):price, uint256(32):spread, uint256(32):timestamp, address(20):provider, bool(1):isClosed
12    struct PriceData {
13        address provider;
14        uint256 asset;
15        uint256 price;
16        uint256 spread;
17        uint256 timestamp;
18        bool isClosed;
19:   }

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/utils/TradingLibrary.sol#L12-L19

### [G&#x2011;05]  Using `storage` instead of `memory` for structs/arrays saves gas
When fetching data from a storage location, assigning the data to a `memory` variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (**2100 gas**) for *each* field of the struct/array. If the fields are read from the new memory variable, they incur an additional `MLOAD` rather than a cheap stack read. Instead of declearing the variable with the `memory` keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires `memory`, or if the array/struct is being read from another `memory` array/struct

*There are 2 instances of this issue:*

```solidity
File: contracts/Trading.sol

700:          Fees memory _fees = openFees;

774:          Fees memory _fees = closeFees;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L700

### [G&#x2011;06]  Avoid contract existence checks by using low level calls
Prior to 0.8.10 the compiler inserted extra code, including `EXTCODESIZE` (**100 gas**), to check for contract existence for external function calls. In more recent solidity versions, the compiler will not insert these checks if the external call has a return value. Similar behavior can be achieved in earlier versions by using low-level calls, since low level calls never check for contract existence

*There are 40 instances of this issue:*

```solidity
File: contracts/BondNFT.sol

/// @audit transfer()
185:          IERC20(tigAsset).transfer(manager, amount);

/// @audit transfer()
202:          IERC20(_tigAsset).transfer(manager, amount);

/// @audit transferFrom()
216:          IERC20(_tigAsset).transferFrom(_msgSender(), address(this), _amount);

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L185

```solidity
File: contracts/GovNFT.sol

/// @audit excessivelySafeCall()
175:          (bool success, bytes memory reason) = address(this).excessivelySafeCall(gasleft()*4/5, 150, abi.encodeWithSelector(this.nonblockingLzReceive.selector, _srcChainId, _srcAddress, _nonce, _payload));

/// @audit transfer()
279:          IERC20(_tigAsset).transfer(_msgsender, amount);

/// @audit transferFrom()
289:          try IERC20(_tigAsset).transferFrom(_msgSender(), address(this), _amount) {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L175

```solidity
File: contracts/Lock.sol

/// @audit transfer()
39:           IERC20(_tigAsset).transfer(msg.sender, _amount);

/// @audit transfer()
52:           IERC20(_tigAsset).transfer(msg.sender, amount);

/// @audit transferFrom()
72:           IERC20(_asset).transferFrom(msg.sender, address(this), _amount);

/// @audit transferFrom()
90:           IERC20(_asset).transferFrom(msg.sender, address(this), _amount);

/// @audit transfer()
104:          IERC20(asset).transfer(_owner, amount);

/// @audit balanceOf()
114:              uint balanceBefore = IERC20(assets[i]).balanceOf(address(this));

/// @audit claim()
115:              IGovNFT(govNFT).claim(assets[i]);

/// @audit balanceOf()
116:              uint balanceAfter = IERC20(assets[i]).balanceOf(address(this));

/// @audit approve()
117:              IERC20(assets[i]).approve(address(bondNFT), type(uint256).max);

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Lock.sol#L39

```solidity
File: contracts/StableVault.sol

/// @audit transferFrom()
46:           IERC20(_token).transferFrom(_msgSender(), address(this), _amount);

/// @audit decimals()
49:               _amount*(10**(18-IERC20Mintable(_token).decimals()))

/// @audit decimals()
67:           _output = _amount/10**(18-IERC20Mintable(_token).decimals());

/// @audit transfer()
68:           IERC20(_token).transfer(

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/StableVault.sol#L46

```solidity
File: contracts/Trading.sol

/// @audit stable()
175:          address _tigAsset = IStableVault(_tradeInfo.stableVault).stable();

/// @audit stable()
324:          address _tigAsset = IStableVault(_tradeInfo.stableVault).stable();

/// @audit transfer()
588:          payable(_proxy).transfer(msg.value);

/// @audit decimals()
650:              uint _marginDecMultiplier = 10**(18-ExtendedIERC20(_marginAsset).decimals());

/// @audit transferFrom()
651:              IERC20(_marginAsset).transferFrom(_trader, address(this), _margin/_marginDecMultiplier);

/// @audit approve()
652:              IERC20(_marginAsset).approve(_stableVault, type(uint).max);

/// @audit transfer()
671:              IERC20(_outputToken).transfer(_trade.trader, _toMint);

/// @audit balanceOf()
673:              uint256 _balBefore = IERC20(_outputToken).balanceOf(address(this));

/// @audit withdraw()
674:              IStableVault(_stableVault).withdraw(_outputToken, _toMint);

/// @audit balanceOf()
/// @audit decimals()
675:              if (IERC20(_outputToken).balanceOf(address(this)) != _balBefore + _toMint/(10**(18-ExtendedIERC20(_outputToken).decimals()))) revert BadWithdraw();

/// @audit transfer()
/// @audit balanceOf()
676:              IERC20(_outputToken).transfer(_trade.trader, IERC20(_outputToken).balanceOf(address(this)) - _balBefore);

/// @audit balanceOf()
749:          gov.distribute(_tigAsset, IStable(_tigAsset).balanceOf(address(this)));

/// @audit approve()
807:          IStable(_tigAsset).approve(address(gov), type(uint).max);

/// @audit stable()
/// @audit allowed()
877:          require(_token == IStableVault(_stableVault).stable() || IStableVault(_stableVault).allowed(_token), "Token not approved in vault");

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L175

```solidity
File: contracts/utils/TradingLibrary.sol

/// @audit trades()
77:           IPosition.Trade memory _trade = IPosition(_positions).trades(_id);

/// @audit recover()
102           address _provider = (
103               keccak256(abi.encode(_priceData))
104:          ).toEthSignedMessageHash().recover(_signature);

/// @audit latestAnswer()
113:              int256 assetChainlinkPriceInt = IPrice(_chainlinkFeed).latestAnswer();

/// @audit decimals()
115:                  uint256 assetChainlinkPrice = uint256(assetChainlinkPriceInt) * 10**(18 - IPrice(_chainlinkFeed).decimals());

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/utils/TradingLibrary.sol#L77

### [G&#x2011;07]  Multiple accesses of a mapping/array should use a local variable cache
The instances below point to the second+ access of a value inside a mapping/array, within a function. Caching a mapping's value in a local `storage` or `calldata` variable when the value is accessed [multiple times](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0), saves **~42 gas per access** due to not having to recalculate the key's keccak256 hash (Gkeccak256 - **30 gas**) and that calculation's associated stack operations. Caching an array's struct avoids recalculating the array offsets into memory/calldata

*There are 39 instances of this issue:*

```solidity
File: contracts/PairsContract.sol

/// @audit _idToAsset[_asset] on line 34
36:           _idToAsset[_asset].chainlinkFeed = _feed;

/// @audit _idToAsset[_asset] on line 49
55:           _idToAsset[_asset].name = _name;

/// @audit _idToAsset[_asset] on line 55
57:           _idToAsset[_asset].chainlinkFeed = _chainlinkFeed;

/// @audit _idToAsset[_asset] on line 57
59:           _idToAsset[_asset].minLeverage = _minLeverage;

/// @audit _idToAsset[_asset] on line 59
60:           _idToAsset[_asset].maxLeverage = _maxLeverage;

/// @audit _idToAsset[_asset] on line 60
61:           _idToAsset[_asset].feeMultiplier = _feeMultiplier;

/// @audit _idToAsset[_asset] on line 61
62:           _idToAsset[_asset].baseFundingRate = _baseFundingRate;

/// @audit _idToAsset[_asset] on line 74
78:               _idToAsset[_asset].maxLeverage = _maxLeverage;

/// @audit _idToAsset[_asset] on line 78
81:               _idToAsset[_asset].minLeverage = _minLeverage;

/// @audit _idToAsset[_asset] on line 81
/// @audit _idToAsset[_asset] on line 84
84:           require(_idToAsset[_asset].maxLeverage >= _idToAsset[_asset].minLeverage, "Wrong leverage values");

/// @audit _idToAsset[_asset] on line 93
96:           _idToAsset[_asset].baseFundingRate = _baseFundingRate;

/// @audit _idToAsset[_asset] on line 105
107:          _idToAsset[_asset].feeMultiplier = _feeMultiplier;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L36

```solidity
File: contracts/Position.sol

/// @audit _limitOrders[<etc>] on line 150
151:              _limitOrderIndexes[_mintTrade.asset][newTokenID] = _limitOrders[_mintTrade.asset].length-1;

/// @audit _assetOpenPositions[<etc>] on line 157
158:              _assetOpenPositionsIndexes[_mintTrade.asset][newTokenID] = _assetOpenPositions[_mintTrade.asset].length-1;

/// @audit _limitOrders[_asset] on line 177
178:          _limitOrders[_asset][_limitOrderIndexes[_asset][_id]] = _limitOrders[_asset][_limitOrders[_asset].length-1];

/// @audit _limitOrders[_asset] on line 178
180:          _limitOrders[_asset].pop();

/// @audit _assetOpenPositions[_asset] on line 184
185:          _assetOpenPositionsIndexes[_asset][_id] = _assetOpenPositions[_asset].length-1;

/// @audit _trades[_id] on line 198
199:          _trades[_id].leverage = _newLeverage;

/// @audit _trades[_id] on line 210
211:          _trades[_id].price = _newPrice;

/// @audit _trades[_id] on line 211
/// @audit _trades[_id] on line 212
/// @audit _trades[_id] on line 212
/// @audit _trades[_id] on line 212
212:          initId[_id] = accInterestPerOi[_trades[_id].asset][_trades[_id].tigAsset][_trades[_id].direction]*int256(_newMargin*_trades[_id].leverage/1e18)/1e18;

/// @audit _trades[_id] on line 231
231:          _trades[_id].accInterest -= _trades[_id].accInterest*int256(_percent)/int256(DIVISION_CONSTANT);

/// @audit _trades[_id] on line 231
/// @audit _trades[_id] on line 232
232:          _trades[_id].margin -= _trades[_id].margin*_percent/DIVISION_CONSTANT;

/// @audit _trades[_id] on line 232
/// @audit _trades[_id] on line 233
/// @audit _trades[_id] on line 233
/// @audit _trades[_id] on line 233
/// @audit _trades[_id] on line 233
233:          initId[_id] = accInterestPerOi[_trades[_id].asset][_trades[_id].tigAsset][_trades[_id].direction]*int256(_trades[_id].margin*_trades[_id].leverage/1e18)/1e18;

/// @audit _trades[_id] on line 262
263:          if (_trades[_id].orderType > 0) {

/// @audit _limitOrders[_asset] on line 264
265:              _limitOrders[_asset][_limitOrderIndexes[_asset][_id]] = _limitOrders[_asset][_limitOrders[_asset].length-1];

/// @audit _limitOrders[_asset] on line 265
267:              _limitOrders[_asset].pop();            

/// @audit _assetOpenPositions[_asset] on line 269
270:              _assetOpenPositions[_asset][_assetOpenPositionsIndexes[_asset][_id]] = _assetOpenPositions[_asset][_assetOpenPositions[_asset].length-1];

/// @audit _assetOpenPositions[_asset] on line 270
272:              _assetOpenPositions[_asset].pop();  

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L151

```solidity
File: contracts/Trading.sol

/// @audit blockDelayPassed[_id] on line 861
864:                  blockDelayPassed[_id].delay = block.number + blockDelay;

/// @audit blockDelayPassed[_id] on line 864
865:                  blockDelayPassed[_id].actionType = _type;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L864

### [G&#x2011;08]  The result of function calls should be cached rather than re-calling the function
The instances below point to the second+ call of the function within a single function

*There is 1 instance of this issue:*

```solidity
File: contracts/Trading.sol

/// @audit position.getCount() on line 173
208:              emit PositionOpened(_tradeInfo, 0, _price, position.getCount()-1, _trader, _marginAfterFees);

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L208

### [G&#x2011;09]  `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables
Using the addition operator instead of plus-equals saves **[113 gas](https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8)**

*There is 1 instance of this issue:*

```solidity
File: contracts/GovNFT.sol

52:           counter += 1;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L52

### [G&#x2011;10]  `internal` functions only called once can be inlined to save gas
Not inlining costs **20 to 40 gas** because of two extra `JUMP` instructions and additional stack operations needed for function calls.

*There are 2 instances of this issue:*

```solidity
File: contracts/BondNFT.sol

323       function _transfer(
324           address from,
325           address to,
326:          uint256 _id

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L323-L326

```solidity
File: contracts/GovNFT.sol

89        function _transfer(
90            address from,
91            address to,
92:           uint256 tokenId

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L89-L92

### [G&#x2011;11]  Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement
`require(a <= b); x = b - a` => `require(a <= b); unchecked { x = b - a }`

*There is 1 instance of this issue:*

```solidity
File: contracts/Trading.sol

/// @audit if-condition on line 615
616:              if ((_trade.margin*_trade.leverage*(DIVISION_CONSTANT-_percent)/DIVISION_CONSTANT)/1e18 < tradingExtension.minPos(_trade.tigAsset)) revert("!size");

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L616

### [G&#x2011;12]  `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops
The `unchecked` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves **30-40 gas [per loop](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)**

*There are 16 instances of this issue:*

```solidity
File: contracts/GovNFT.sol

53:           for (uint i=0; i<assetsLength(); i++) {

67:           for (uint i=0; i<assetsLength(); i++) {

78:           for (uint i=0; i<assetsLength(); i++) {

95:           for (uint i=0; i<assetsLength(); i++) {

105:          for (uint i=0; i<_amount; i++) {

131:          for (uint i=0; i<tokenId.length; i++) {

200:          for (uint i=0; i<tokenId.length; i++) {

246:          for (uint i=0; i<_ids.length; i++) {

252:          for (uint i=0; i<_ids.length; i++) {

258:          for (uint i=0; i<_ids.length; i++) {

325:          for (uint i=0; i<_ids.length; i++) {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L53

```solidity
File: contracts/Lock.sol

113:          for (uint i=0; i < assets.length; i++) {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Lock.sol#L113

```solidity
File: contracts/Position.sol

296:          for (uint i=0; i<_ids.length; i++) {

304:          for (uint i=0; i<_ids.length; i++) {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L296

```solidity
File: contracts/Referrals.sol

70:           for (uint i=0; i<_codeOwnersL; i++) {

73:           for (uint i=0; i<_referredAL; i++) {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Referrals.sol#L70

### [G&#x2011;13]  `require()`/`revert()` strings longer than 32 bytes cost extra gas
Each extra memory word of bytes past the original 32 [incurs an MSTORE](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#consider-having-short-revert-strings) which costs **3 gas**

*There are 4 instances of this issue:*

```solidity
File: contracts/GovNFT.sol

153           require(
154               msg.value >= messageFee,
155               "Must send enough value to cover messageFee"
156:          );

185:          require(msg.sender == address(this), "NonblockingLzApp: caller must be app");

209:          require(payloadHash != bytes32(0), "NonblockingLzApp: no stored message");

210:          require(keccak256(_payload) == payloadHash, "NonblockingLzApp: invalid payload");

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L153-L156

### [G&#x2011;14]  Optimize names to save gas
`public`/`external` function names and `public` member variable names can be optimized to save gas. See [this](https://gist.github.com/IllIllI000/a5d8b486a8259f9f77891a919febd1a9) link for an example of how it works. Below are the interfaces/abstract contracts that can be optimized so that the most frequently-called functions use the least amount of gas possible during method lookup. Method IDs that have two leading zero bytes can save **128 gas** each during deployment, and renaming functions to have lower method IDs will save **22 gas** per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)

*There are 25 instances of this issue:*

```solidity
File: contracts/BondNFT.sol

/// @audit createLock(), extendLock(), release(), claim(), claimDebt(), distribute(), idToBond(), isExpired(), pending(), getAssets(), safeTransferMany(), safeTransferFromMany(), approveMany(), balanceIds(), addAsset(), setAllowedAsset(), setBaseURI(), setManager()
8:    contract BondNFT is ERC721Enumerable, Ownable {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L8

```solidity
File: contracts/GovNFT.sol

/// @audit setBaseURI(), _bridgeMint(), mintMany(), setTrustedAddress(), crossChain(), nonblockingLzReceive(), retryMessage(), estimateFees(), setGas(), setEndpoint(), safeTransferMany(), safeTransferFromMany(), approveMany(), claim(), distribute(), pending(), addAsset(), setAllowedAsset(), setMaxBridge(), assetsLength(), allowedAsset(), balanceIds()
12:   contract GovNFT is ERC721Enumerable, ILayerZeroReceiver, MetaContext, IGovNFT {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L12

```solidity
File: contracts/interfaces/IBondNFT.sol

/// @audit createLock(), extendLock(), claim(), claimDebt(), release(), distribute(), ownerOf(), getAssets()
4:    interface IBondNFT {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/IBondNFT.sol#L4

```solidity
File: contracts/interfaces/IGovNFT.sol

/// @audit distribute(), safeTransferMany(), claim(), pending()
5:    interface IGovNFT {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/IGovNFT.sol#L5

```solidity
File: contracts/interfaces/ILayerZeroEndpoint.sol

/// @audit send(), receivePayload(), getInboundNonce(), getOutboundNonce(), estimateFees(), getChainId(), retryPayload(), hasStoredPayload(), getSendLibraryAddress(), getReceiveLibraryAddress(), isSendingPayload(), isReceivingPayload(), getConfig(), getSendVersion(), getReceiveVersion()
7:    interface ILayerZeroEndpoint is ILayerZeroUserApplicationConfig {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/ILayerZeroEndpoint.sol#L7

```solidity
File: contracts/interfaces/ILayerZeroReceiver.sol

/// @audit lzReceive()
5:    interface ILayerZeroReceiver {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/ILayerZeroReceiver.sol#L5

```solidity
File: contracts/interfaces/ILayerZeroUserApplicationConfig.sol

/// @audit setConfig(), setSendVersion(), setReceiveVersion(), forceResumeReceive()
5:    interface ILayerZeroUserApplicationConfig {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/ILayerZeroUserApplicationConfig.sol#L5

```solidity
File: contracts/interfaces/IPairsContract.sol

/// @audit allowedAsset(), idToAsset(), idToOi(), setAssetBaseFundingRate(), modifyLongOi(), modifyShortOi()
5:    interface IPairsContract {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/IPairsContract.sol#L5

```solidity
File: contracts/interfaces/IPosition.sol

/// @audit trades(), executeLimitOrder(), modifyMargin(), addToPosition(), reducePosition(), assetOpenPositions(), assetOpenPositionsIndexes(), limitOrders(), limitOrderIndexes(), assetOpenPositionsLength(), limitOrdersLength(), ownerOf(), mint(), burn(), modifyTp(), modifySl(), getCount(), updateFunding(), setAccInterest()
5:    interface IPosition {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/IPosition.sol#L5

```solidity
File: contracts/interfaces/IReferrals.sol

/// @audit createReferralCode(), setReferred(), getReferred(), getReferral()
5:    interface IReferrals {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/IReferrals.sol#L5

```solidity
File: contracts/interfaces/IStableVault.sol

/// @audit deposit(), withdraw(), allowed(), stable()
5:    interface IStableVault {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/IStableVault.sol#L5

```solidity
File: contracts/interfaces/ITrading.sol

/// @audit initiateMarketOrder(), initiateCloseOrder(), addMargin(), removeMargin(), addToPosition(), initiateLimitOrder(), cancelLimitOrder(), updateTpSl(), executeLimitOrder(), liquidatePosition(), limitClose()
7:    interface ITrading {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/ITrading.sol#L7

```solidity
File: contracts/Lock.sol

/// @audit claim(), claimDebt(), lock(), extendLock(), release(), claimGovFees(), editAsset(), sendNFTs()
10:   contract Lock is Ownable{

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Lock.sol#L10

```solidity
File: contracts/PairsContract.sol

/// @audit idToAsset(), idToOi(), setAssetChainlinkFeed(), addAsset(), updateAssetLeverage(), setAssetBaseFundingRate(), updateAssetFeeMultiplier(), pauseAsset(), setMaxBaseFundingRate(), setProtocol(), setMaxOi(), modifyLongOi(), modifyShortOi()
8:    contract PairsContract is Ownable, IPairsContract {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L8

```solidity
File: contracts/Position.sol

/// @audit isMinter(), trades(), openPositions(), openPositionsIndexes(), assetOpenPositions(), assetOpenPositionsIndexes(), limitOrders(), limitOrderIndexes(), setBaseURI(), updateFunding(), mint(), executeLimitOrder(), modifyMargin(), addToPosition(), setAccInterest(), reducePosition(), modifyTp(), modifySl(), burn(), assetOpenPositionsLength(), limitOrdersLength(), getCount(), userTrades(), openPositionsSelection(), setMinter()
9:    contract Position is ERC721Enumerable, MetaContext, IPosition {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L9

```solidity
File: contracts/Referrals.sol

/// @audit createReferralCode(), setReferred(), getReferred(), getReferral(), setProtocol(), initRefs()
7:    contract Referrals is Ownable, IReferrals {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Referrals.sol#L7

```solidity
File: contracts/StableToken.sol

/// @audit mintFor(), setMinter()
7:    contract StableToken is ERC20Permit, MetaContext {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/StableToken.sol#L7

```solidity
File: contracts/StableVault.sol

/// @audit mintFor()
9:    interface IERC20Mintable is IERC20 {

/// @audit deposit(), depositWithPermit(), withdraw(), listToken(), delistToken()
27:   contract StableVault is MetaContext, IStableVault {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/StableVault.sol#L9

```solidity
File: contracts/TradingExtension.sol

/// @audit minPos(), _closePosition(), _limitClose(), _checkGas(), modifyShortOi(), modifyLongOi(), setMaxGasPrice(), getRef(), getVerifiedPrice(), _setReferral(), validateTrade(), setValidSignatureTimer(), setChainlinkEnabled(), setNode(), setAllowedMargin(), setMinPositionSize(), setPaused()
10:   contract TradingExtension is Ownable{

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/TradingExtension.sol#L10

```solidity
File: contracts/Trading.sol

/// @audit getVerifiedPrice(), getRef(), _setReferral(), validateTrade(), isPaused(), minPos(), modifyLongOi(), modifyShortOi(), _limitClose(), _checkGas(), _closePosition()
14:   interface ITradingExtension {

/// @audit burnFrom(), mintFor()
58:   interface IStable is IERC20 {

/// @audit initiateMarketOrder(), initiateCloseOrder(), addToPosition(), initiateLimitOrder(), cancelLimitOrder(), addMargin(), removeMargin(), updateTpSl(), executeLimitOrder(), liquidatePosition(), limitClose(), approveProxy(), setBlockDelay(), setAllowedVault(), setMaxWinPercent(), setLimitOrderPriceRange(), setFees(), setTradingExtension()
79:   contract Trading is MetaContext, ITrading {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L14

```solidity
File: contracts/utils/MetaContext.sol

/// @audit setTrustedForwarder(), isTrustedForwarder()
6:    contract MetaContext is Ownable {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/utils/MetaContext.sol#L6

```solidity
File: contracts/utils/TradingLibrary.sol

/// @audit pnl(), liqPrice(), getLiqPrice(), verifyPrice()
21:   library TradingLibrary {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/utils/TradingLibrary.sol#L21

### [G&#x2011;15]  Use a more recent version of solidity
Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than `revert()/require()` strings
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

*There are 21 instances of this issue:*

```solidity
File: contracts/BondNFT.sol

2:    pragma solidity ^0.8.0;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L2

```solidity
File: contracts/GovNFT.sol

2:    pragma solidity ^0.8.0;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L2

```solidity
File: contracts/interfaces/IGovNFT.sol

3:    pragma solidity ^0.8.0;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/IGovNFT.sol#L3

```solidity
File: contracts/interfaces/ILayerZeroEndpoint.sol

3:    pragma solidity ^0.8.0;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/ILayerZeroEndpoint.sol#L3

```solidity
File: contracts/interfaces/ILayerZeroReceiver.sol

3:    pragma solidity ^0.8.0;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/ILayerZeroReceiver.sol#L3

```solidity
File: contracts/interfaces/ILayerZeroUserApplicationConfig.sol

3:    pragma solidity ^0.8.0;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/ILayerZeroUserApplicationConfig.sol#L3

```solidity
File: contracts/interfaces/IPairsContract.sol

3:    pragma solidity ^0.8.0;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/IPairsContract.sol#L3

```solidity
File: contracts/interfaces/IPosition.sol

3:    pragma solidity ^0.8.0;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/IPosition.sol#L3

```solidity
File: contracts/interfaces/IReferrals.sol

3:    pragma solidity ^0.8.0;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/IReferrals.sol#L3

```solidity
File: contracts/interfaces/IStableVault.sol

3:    pragma solidity ^0.8.0;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/IStableVault.sol#L3

```solidity
File: contracts/interfaces/ITrading.sol

5:    pragma solidity ^0.8.0;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/ITrading.sol#L5

```solidity
File: contracts/Lock.sol

2:    pragma solidity ^0.8.0;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Lock.sol#L2

```solidity
File: contracts/PairsContract.sol

2:    pragma solidity ^0.8.0;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L2

```solidity
File: contracts/Position.sol

2:    pragma solidity ^0.8.0;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L2

```solidity
File: contracts/Referrals.sol

2:    pragma solidity ^0.8.0;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Referrals.sol#L2

```solidity
File: contracts/StableToken.sol

2:    pragma solidity ^0.8.0;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/StableToken.sol#L2

```solidity
File: contracts/StableVault.sol

2:    pragma solidity ^0.8.0;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/StableVault.sol#L2

```solidity
File: contracts/TradingExtension.sol

2:    pragma solidity ^0.8.0;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/TradingExtension.sol#L2

```solidity
File: contracts/Trading.sol

2:    pragma solidity ^0.8.0;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L2

```solidity
File: contracts/utils/MetaContext.sol

2:    pragma solidity ^0.8.0;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/utils/MetaContext.sol#L2

```solidity
File: contracts/utils/TradingLibrary.sol

2:    pragma solidity ^0.8.0;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/utils/TradingLibrary.sol#L2

### [G&#x2011;16]  Splitting `require()` statements that use `&&` saves gas
See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper by **3 gas**

*There are 3 instances of this issue:*

```solidity
File: contracts/PairsContract.sol

52:           require(_maxLeverage >= _minLeverage && _minLeverage > 0, "Wrong leverage values");

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L52

```solidity
File: contracts/Trading.sol

887:              require(_proxy.proxy == _msgSender() && _proxy.time >= block.timestamp, "Proxy not approved");

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L887

```solidity
File: contracts/utils/TradingLibrary.sol

116                   require(
117                       _priceData.price < assetChainlinkPrice+assetChainlinkPrice*2/100 &&
118                       _priceData.price > assetChainlinkPrice-assetChainlinkPrice*2/100, "!chainlinkPrice"
119:                  );

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/utils/TradingLibrary.sol#L116-L119

### [G&#x2011;17]  Don't compare boolean expressions to boolean literals
`if (<x> == true)` => `if (<x>)`, `if (<x> == false)` => `if (!<x>)`

*There are 2 instances of this issue:*

```solidity
File: contracts/BondNFT.sol

238:          bond.expired = bond.expireEpoch <= epoch[bond.asset] ? true : false;

252:          return bond.expireEpoch <= epoch[bond.asset] ? true : false;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L238

### [G&#x2011;18]  Ternary unnecessary
`z = (x == y) ? true : false` => `z = (x == y)`

*There are 2 instances of this issue:*

```solidity
File: contracts/BondNFT.sol

238:          bond.expired = bond.expireEpoch <= epoch[bond.asset] ? true : false;

252:          return bond.expireEpoch <= epoch[bond.asset] ? true : false;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L238

### [G&#x2011;19]  `require()` or `revert()` statements that check input arguments should be at the top of the function
Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting a Gcoldsload (**2100 gas***) in a function that may ultimately revert in the unhappy case.

*There is 1 instance of this issue:*

```solidity
File: contracts/GovNFT.sol

/// @audit expensive op on line 65
66:           require(tokenId <= 10000, "BadID");

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L66

### [G&#x2011;20]  Use custom errors rather than `revert()`/`require()` strings to save gas
Custom errors are available from solidity version 0.8.4. Custom errors save [**~50 gas**](https://gist.github.com/IllIllI000/ad1bd0d29a0101b25e57c293b4b0c746) each time they're hit by [avoiding having to allocate and store the revert string](https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth). Not defining the strings also save deployment gas

*There are 2 instances of this issue:*

```solidity
File: contracts/GovNFT.sol

153           require(
154               msg.value >= messageFee,
155               "Must send enough value to cover messageFee"
156:          );

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L153-L156

```solidity
File: contracts/utils/TradingLibrary.sol

116                   require(
117                       _priceData.price < assetChainlinkPrice+assetChainlinkPrice*2/100 &&
118                       _priceData.price > assetChainlinkPrice-assetChainlinkPrice*2/100, "!chainlinkPrice"
119:                  );

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/utils/TradingLibrary.sol#L116-L119

### [G&#x2011;21]  Functions guaranteed to revert when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are 
`CALLVALUE`(2),`DUP1`(3),`ISZERO`(3),`PUSH2`(3),`JUMPI`(10),`PUSH1`(3),`DUP1`(3),`REVERT`(0),`JUMPDEST`(1),`POP`(2), which costs an average of about **21 gas per call** to the function, in addition to the extra deployment cost

*There are 69 instances of this issue:*

```solidity
File: contracts/BondNFT.sol

57        function createLock(
58            address _asset,
59            uint _amount,
60            uint _period,
61            address _owner
62:       ) external onlyManager() returns(uint id) {

97        function extendLock(
98            uint _id,
99            address _asset,
100           uint _amount,
101           uint _period,
102           address _sender
103:      ) external onlyManager() {

137       function release(
138           uint _id,
139           address _releaser
140:      ) external onlyManager() returns(uint amount, uint lockAmount, address asset, address _owner) {

168       function claim(
169           uint _id,
170           address _claimer
171:      ) public onlyManager() returns(uint amount, address tigAsset) {

196       function claimDebt(
197           address _user,
198           address _tigAsset
199:      ) public onlyManager() returns(uint amount) {

349:      function addAsset(address _asset) external onlyOwner {

357:      function setAllowedAsset(address _asset, bool _bool) external onlyOwner {

362:      function setBaseURI(string calldata _newBaseURI) external onlyOwner {

366       function setManager(
367           address _manager
368:      ) public onlyOwner() {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L57-L62

```solidity
File: contracts/GovNFT.sol

46:       function setBaseURI(string calldata _newBaseURI) external onlyOwner {

104:      function mintMany(uint _amount) external onlyOwner {

110:      function mint() external onlyOwner {

114:      function setTrustedAddress(uint16 _chainId, address _contract, bool _bool) external onlyOwner {

236:      function setGas(uint _gas) external onlyOwner {

240:      function setEndpoint(ILayerZeroEndpoint _endpoint) external onlyOwner {

300:      function addAsset(address _asset) external onlyOwner {

307:      function setAllowedAsset(address _asset, bool _bool) external onlyOwner {

311:      function setMaxBridge(uint256 _max) external onlyOwner {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L46

```solidity
File: contracts/Lock.sol

127       function editAsset(
128           address _tigAsset,
129           bool _isAllowed
130:      ) external onlyOwner() {

138       function sendNFTs(
139           uint[] memory _ids
140:      ) external onlyOwner() {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Lock.sol#L127-L130

```solidity
File: contracts/PairsContract.sol

33:       function setAssetChainlinkFeed(uint256 _asset, address _feed) external onlyOwner {

48:       function addAsset(uint256 _asset, string memory _name, address _chainlinkFeed, uint256 _minLeverage, uint256 _maxLeverage, uint256 _feeMultiplier, uint256 _baseFundingRate) external onlyOwner {

73:       function updateAssetLeverage(uint256 _asset, uint256 _minLeverage, uint256 _maxLeverage) external onlyOwner {

92:       function setAssetBaseFundingRate(uint256 _asset, uint256 _baseFundingRate) external onlyOwner {

104:      function updateAssetFeeMultiplier(uint256 _asset, uint256 _feeMultiplier) external onlyOwner {

115:      function pauseAsset(uint256 _asset, bool _isPaused) external onlyOwner {

125:      function setMaxBaseFundingRate(uint256 _maxBaseFundingRate) external onlyOwner {

129:      function setProtocol(address _protocol) external onlyOwner {

139:      function setMaxOi(uint256 _asset, address _tigAsset, uint256 _maxOi) external onlyOwner {

154:      function modifyLongOi(uint256 _asset, address _tigAsset, bool _onOpen, uint256 _amount) external onlyProtocol {

174:      function modifyShortOi(uint256 _asset, address _tigAsset, bool _onOpen, uint256 _amount) external onlyProtocol {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L33

```solidity
File: contracts/Position.sol

85:       function setBaseURI(string memory _newBaseURI) external onlyOwner {

99:       function updateFunding(uint256 _asset, address _tigAsset, uint256 _longOi, uint256 _shortOi, uint256 _baseFundingRate, uint _vaultFundingPercent) external onlyMinter {

131       function mint(
132           MintTrade memory _mintTrade
133:      ) external onlyMinter {

168:      function executeLimitOrder(uint256 _id, uint256 _price, uint256 _newMargin) external onlyMinter {

197:      function modifyMargin(uint256 _id, uint256 _newMargin, uint256 _newLeverage) external onlyMinter {

209:      function addToPosition(uint256 _id, uint256 _newMargin, uint256 _newPrice) external onlyMinter {

220:      function setAccInterest(uint256 _id) external onlyMinter {

230:      function reducePosition(uint256 _id, uint256 _percent) external onlyMinter {

242:      function modifyTp(uint _id, uint _tpPrice) external onlyMinter {

252:      function modifySl(uint _id, uint _slPrice) external onlyMinter {

260:      function burn(uint _id) external onlyMinter {

310:      function setMinter(address _minter, bool _bool) external onlyOwner {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L85

```solidity
File: contracts/Referrals.sol

32:       function setReferred(address _referredTrader, bytes32 _hash) external onlyProtocol {

53:       function setProtocol(address _protocol) external onlyOwner {

60        function initRefs(
61            address[] memory _codeOwners,
62            bytes32[] memory _ownedCodes,
63            address[] memory _referredA,
64            bytes32[] memory _referredTo
65:       ) external onlyOwner {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Referrals.sol#L32

```solidity
File: contracts/StableToken.sol

13        function burnFrom(
14            address account,
15            uint256 amount
16        ) 
17            public 
18            virtual 
19:           onlyMinter() 

24        function mintFor(
25            address account,
26            uint256 amount
27        ) 
28            public 
29            virtual 
30:           onlyMinter() 

38        function setMinter(
39            address _address,
40            bool _status
41        ) 
42            public
43:           onlyOwner()

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/StableToken.sol#L13-L19

```solidity
File: contracts/StableVault.sol

78:       function listToken(address _token) external onlyOwner {

89:       function delistToken(address _token) external onlyOwner {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/StableVault.sol#L78

```solidity
File: contracts/TradingExtension.sol

61        function _closePosition(
62            uint _id,
63            uint _price,
64            uint _percent
65:       ) external onlyProtocol returns (IPosition.Trade memory _trade, uint256 _positionSize, int256 _payout) {

126       function modifyShortOi(
127           uint _asset,
128           address _tigAsset,
129           bool _onOpen,
130           uint _size
131:      ) public onlyProtocol {

135       function modifyLongOi(
136           uint _asset,
137           address _tigAsset,
138           bool _onOpen,
139           uint _size
140:      ) public onlyProtocol {

144:      function setMaxGasPrice(uint _maxGasPrice) external onlyOwner {

190       function _setReferral(
191           bytes32 _referral,
192           address _trader
193:      ) external onlyProtocol {

222       function setValidSignatureTimer(
223           uint _validSignatureTimer
224       )
225           external
226:          onlyOwner

231:      function setChainlinkEnabled(bool _bool) external onlyOwner {

240:      function setNode(address _node, bool _bool) external onlyOwner {

249       function setAllowedMargin(
250           address _tigAsset,
251           bool _bool
252       ) 
253           external
254:          onlyOwner

264       function setMinPositionSize(
265           address _tigAsset,
266           uint _min
267       ) 
268           external
269:          onlyOwner

274:      function setPaused(bool _paused) external onlyOwner {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/TradingExtension.sol#L61-L65

```solidity
File: contracts/Trading.sol

898       function setBlockDelay(
899           uint _blockDelay
900       )
901           external
902:          onlyOwner

912       function setAllowedVault(
913           address _stableVault,
914           bool _bool
915       )
916           external
917:          onlyOwner

926       function setMaxWinPercent(
927           uint _maxWinPercent
928       )
929           external
930:          onlyOwner

939:      function setLimitOrderPriceRange(uint _range) external onlyOwner {

952:      function setFees(bool _open, uint _daoFees, uint _burnFees, uint _referralFees, uint _botFees, uint _percent) external onlyOwner {

975       function setTradingExtension(
976           address _ext
977:      ) external onlyOwner() {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L898-L902

```solidity
File: contracts/utils/MetaContext.sol

9:        function setTrustedForwarder(address _forwarder, bool _bool) external onlyOwner {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/utils/MetaContext.sol#L9

### [G&#x2011;22]  Don't use `_msgSender()` if not supporting EIP-2771
Use `msg.sender` if the code does not implement [EIP-2771 trusted forwarder](https://eips.ethereum.org/EIPS/eip-2771) support

*There are 30 instances of this issue:*

```solidity
File: contracts/BondNFT.sol

216:          IERC20(_tigAsset).transferFrom(_msgSender(), address(this), _amount);

285:                  _transfer(_msgSender(), _to, _ids[i]);

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L216

```solidity
File: contracts/GovNFT.sol

65:           require(msg.sender == address(this) || _msgSender() == owner(), "NotBridge");

106:              _mint(_msgSender(), counter);

111:          _mint(_msgSender(), counter);

132:              require(_msgSender() == ownerOf(tokenId[i]), "Not the owner");

161:              payable(_msgSender()),

174:          require(_msgSender() == address(endpoint), "!Endpoint");

247:              _transfer(_msgSender(), _to, _ids[i]);

276:          address _msgsender = _msgSender();

289:          try IERC20(_tigAsset).transferFrom(_msgSender(), address(this), _amount) {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L65

```solidity
File: contracts/PairsContract.sol

190:          require(_msgSender() == address(protocol), "!Protocol");

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L190

```solidity
File: contracts/Position.sol

315:          require(_isMinter[_msgSender()], "!Minter");

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L315

```solidity
File: contracts/Referrals.sol

22:           _referral[_hash] = _msgSender();

23:           emit ReferralCreated(_msgSender(), _hash);

81:           require(_msgSender() == address(protocol), "!Protocol");

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Referrals.sol#L22

```solidity
File: contracts/StableToken.sol

52:           require(isMinter[_msgSender()], "!Minter");

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/StableToken.sol#L52

```solidity
File: contracts/StableVault.sol

46:           IERC20(_token).transferFrom(_msgSender(), address(this), _amount);

48:               _msgSender(),

56:           ERC20Permit(_token).permit(_msgSender(), address(this), _toAllow, _deadline, v, r, s);

66:           IERC20Mintable(stable).burnFrom(_msgSender(), _amount);

69:               _msgSender(),

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/StableVault.sol#L46

```solidity
File: contracts/Trading.sol

520:              emit LimitOrderExecuted(trade.asset, trade.direction, trade.price, trade.leverage, trade.margin - _fee, _id, trade.trader, _msgSender());

554:              emit PositionLiquidated(_id, _trade.trader, _msgSender());

584:          proxyApprovals[_msgSender()] = Proxy(

631:          emit PositionClosed(_id, _price, _percent, _toMint, _trade.trader, _isBot ? _msgSender() : _trade.trader);

722:                      _msgSender(),

798:                      _msgSender(),

885:          if (_trader != _msgSender()) {

887:              require(_proxy.proxy == _msgSender() && _proxy.time >= block.timestamp, "Proxy not approved");

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L520


___

## Excluded findings
These findings are excluded from awards calculations because there are publicly-available automated tools that find them. The valid ones appear here for completeness

## Summary

### Gas Optimizations
| |Issue|Instances|Total Gas Saved|
|-|:-|:-:|:-:|
| [G&#x2011;01] | Using `calldata` instead of `memory` for read-only arguments in `external` functions saves gas | 12 |  1440 |
| [G&#x2011;02] | State variables should be cached in stack variables rather than re-reading them from storage | 20 |  1940 |
| [G&#x2011;03] | `<array>.length` should not be looked up in every loop of a `for`-loop | 13 |  39 |
| [G&#x2011;04] | Using `bool`s for storage incurs overhead | 16 |  273600 |
| [G&#x2011;05] | Using `> 0` costs more gas than `!= 0` when used on a `uint` in a `require()` statement | 1 |  6 |
| [G&#x2011;06] | `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too) | 21 |  105 |
| [G&#x2011;07] | Using `private` rather than `public` for constants, saves gas | 3 |  - |
| [G&#x2011;08] | Use custom errors rather than `revert()`/`require()` strings to save gas | 64 |  - |

Total: 150 instances over 8 issues with **277130 gas** saved

Gas totals use lower bounds of ranges and count two iterations of each `for`-loop. All values above are runtime, not deployment, values; deployment values are listed in the individual issue descriptions. The table above as well as its gas numbers do not include any of the excluded findings.





## Gas Optimizations

### [G&#x2011;01]  Using `calldata` instead of `memory` for read-only arguments in `external` functions saves gas
When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for-loop to copy each index of the `calldata` to the `memory` index. **Each iteration of this for-loop costs at least 60 gas** (i.e. `60 * <mem_array>.length`). Using `calldata` directly, obliviates the need for such a loop in the contract code and runtime execution. Note that even if an interface defines a function as having `memory` arguments, it's still valid for implementation contracs to use `calldata` arguments instead. 

If the array is passed to an `internal` function which passes the array to another internal function where the array is modified and therefore `memory` is used in the `external` call, it's still more gass-efficient to use `calldata` when the `external` function uses modifiers, since the modifiers may prevent the internal functions from being called. Structs have the same overhead as an array of length one

Note that I've also flagged instances where the function is `public` but can be marked as `external` since it's not called by the contract, and cases where a constructor is involved

*There are 12 instances of this issue:*

```solidity
File: contracts/GovNFT.sol

/// @audit _destination - (valid but excluded finding)
/// @audit tokenId - (valid but excluded finding)
124       function crossChain(
125           uint16 _dstChainId,
126           bytes memory _destination,
127           address _to,
128:          uint256[] memory tokenId

/// @audit _srcAddress - (valid but excluded finding)
/// @audit _payload - (valid but excluded finding)
168       function lzReceive(
169           uint16 _srcChainId,
170           bytes memory _srcAddress,
171           uint64 _nonce,
172:          bytes memory _payload

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L124-L128

```solidity
File: contracts/Lock.sol

/// @audit _ids - (valid but excluded finding)
138       function sendNFTs(
139           uint[] memory _ids
140:      ) external onlyOwner() {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Lock.sol#L138-L140

```solidity
File: contracts/PairsContract.sol

/// @audit _name - (valid but excluded finding)
48:       function addAsset(uint256 _asset, string memory _name, address _chainlinkFeed, uint256 _minLeverage, uint256 _maxLeverage, uint256 _feeMultiplier, uint256 _baseFundingRate) external onlyOwner {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L48

```solidity
File: contracts/Position.sol

/// @audit _newBaseURI - (valid but excluded finding)
85:       function setBaseURI(string memory _newBaseURI) external onlyOwner {

/// @audit _mintTrade - (valid but excluded finding)
131       function mint(
132           MintTrade memory _mintTrade
133:      ) external onlyMinter {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L85

```solidity
File: contracts/Referrals.sol

/// @audit _codeOwners - (valid but excluded finding)
/// @audit _ownedCodes - (valid but excluded finding)
/// @audit _referredA - (valid but excluded finding)
/// @audit _referredTo - (valid but excluded finding)
60        function initRefs(
61            address[] memory _codeOwners,
62            bytes32[] memory _ownedCodes,
63            address[] memory _referredA,
64            bytes32[] memory _referredTo
65:       ) external onlyOwner {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Referrals.sol#L60-L65

### [G&#x2011;02]  State variables should be cached in stack variables rather than re-reading them from storage
The instances below point to the second+ access of a state variable within a function. Caching of a state variable replaces each Gwarmaccess (**100 gas**) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

*There are 20 instances of this issue:*

```solidity
File: contracts/GovNFT.sol

/// @audit assets on line 54 - (valid but excluded finding)
54:               userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];

/// @audit assets on line 68 - (valid but excluded finding)
68:               userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];

/// @audit assets on line 79 - (valid but excluded finding)
79:               userDebt[owner][assets[i]] += accRewardsPerNFT[assets[i]];

/// @audit assets on line 79 - (valid but excluded finding)
/// @audit assets on line 80 - (valid but excluded finding)
80:               userDebt[owner][assets[i]] -= userPaid[owner][assets[i]]/balanceOf(owner);

/// @audit assets on line 80 - (valid but excluded finding)
/// @audit assets on line 81 - (valid but excluded finding)
81:               userPaid[owner][assets[i]] -= userPaid[owner][assets[i]]/balanceOf(owner);            

/// @audit assets on line 96 - (valid but excluded finding)
96:               userDebt[from][assets[i]] += accRewardsPerNFT[assets[i]];

/// @audit assets on line 96 - (valid but excluded finding)
/// @audit assets on line 97 - (valid but excluded finding)
97:               userDebt[from][assets[i]] -= userPaid[from][assets[i]]/balanceOf(from);

/// @audit assets on line 97 - (valid but excluded finding)
/// @audit assets on line 98 - (valid but excluded finding)
98:               userPaid[from][assets[i]] -= userPaid[from][assets[i]]/balanceOf(from);

/// @audit assets on line 98 - (valid but excluded finding)
/// @audit assets on line 99 - (valid but excluded finding)
99:               userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L54

```solidity
File: contracts/PairsContract.sol

/// @audit _idToAsset[_asset].minLeverage on line 81 - (valid but excluded finding)
84:           require(_idToAsset[_asset].maxLeverage >= _idToAsset[_asset].minLeverage, "Wrong leverage values");

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L84

```solidity
File: contracts/Position.sol

/// @audit _trades[_id].margin on line 232 - (valid but excluded finding)
233:          initId[_id] = accInterestPerOi[_trades[_id].asset][_trades[_id].tigAsset][_trades[_id].direction]*int256(_trades[_id].margin*_trades[_id].leverage/1e18)/1e18;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L233

```solidity
File: contracts/Trading.sol

/// @audit limitOrderPriceRange on line 496 - (valid but excluded finding)
496:              if (_price > trade.price+trade.price*limitOrderPriceRange/DIVISION_CONSTANT || _price < trade.price-trade.price*limitOrderPriceRange/DIVISION_CONSTANT) revert("6"); //LimitNotMet

/// @audit maxWinPercent on line 625 - (valid but excluded finding)
625:                  if (maxWinPercent > 0 && _toMint > _trade.margin*maxWinPercent/DIVISION_CONSTANT) {

/// @audit maxWinPercent on line 625 - (valid but excluded finding)
626:                      _toMint = _trade.margin*maxWinPercent/DIVISION_CONSTANT;

/// @audit blockDelay on line 861 - (valid but excluded finding)
864:                  blockDelayPassed[_id].delay = block.number + blockDelay;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L496

### [G&#x2011;03]  `<array>.length` should not be looked up in every loop of a `for`-loop
The overheads outlined below are _PER LOOP_, excluding the first loop
* storage arrays incur a Gwarmaccess (**100 gas**)
* memory arrays use `MLOAD` (**3 gas**)
* calldata arrays use `CALLDATALOAD` (**3 gas**)

Caching the length changes each of these to a `DUP<N>` (**3 gas**), and gets rid of the extra `DUP<N>` needed to store the stack offset

*There are 13 instances of this issue:*

```solidity
File: contracts/BondNFT.sol

/// @audit (valid but excluded finding)
284:              for (uint i=0; i<_ids.length; i++) {

/// @audit (valid but excluded finding)
292:              for (uint i=0; i<_ids.length; i++) {

/// @audit (valid but excluded finding)
300:              for (uint i=0; i<_ids.length; i++) {

/// @audit (valid but excluded finding)
342:              for (uint i=0; i<_ids.length; i++) {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L284

```solidity
File: contracts/GovNFT.sol

/// @audit (valid but excluded finding)
131:          for (uint i=0; i<tokenId.length; i++) {

/// @audit (valid but excluded finding)
200:          for (uint i=0; i<tokenId.length; i++) {

/// @audit (valid but excluded finding)
246:          for (uint i=0; i<_ids.length; i++) {

/// @audit (valid but excluded finding)
252:          for (uint i=0; i<_ids.length; i++) {

/// @audit (valid but excluded finding)
258:          for (uint i=0; i<_ids.length; i++) {

/// @audit (valid but excluded finding)
325:          for (uint i=0; i<_ids.length; i++) {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L131

```solidity
File: contracts/Lock.sol

/// @audit (valid but excluded finding)
113:          for (uint i=0; i < assets.length; i++) {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Lock.sol#L113

```solidity
File: contracts/Position.sol

/// @audit (valid but excluded finding)
296:          for (uint i=0; i<_ids.length; i++) {

/// @audit (valid but excluded finding)
304:          for (uint i=0; i<_ids.length; i++) {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L296

### [G&#x2011;04]  Using `bool`s for storage incurs overhead
```solidity
    // Booleans are more expensive than uint256 or any type that takes up a full
    // word because each write operation emits an extra SLOAD to first read the
    // slot's contents, replace the bits taken up by the boolean, and then write
    // back. This is the compiler's defense against contract upgrades and
    // pointer aliasing, and it cannot be disabled.
```
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27
Use `uint256(1)` and `uint256(2)` for true/false to avoid a Gwarmaccess (**[100 gas](https://gist.github.com/IllIllI000/1b70014db712f8572a72378321250058)**) for the extra SLOAD, and to avoid Gsset (**20000 gas**) when changing from `false` to `true`, after having been `true` in the past

*There are 16 instances of this issue:*

```solidity
File: contracts/BondNFT.sol

/// @audit (valid but excluded finding)
32:       mapping(address => bool) public allowedAsset;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L32

```solidity
File: contracts/GovNFT.sol

/// @audit (valid but excluded finding)
22:       mapping(uint16 => mapping(address => bool)) public isTrustedAddress;

/// @audit (valid but excluded finding)
265:      mapping(address => bool) private _allowedAsset;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L22

```solidity
File: contracts/Lock.sol

/// @audit (valid but excluded finding)
18:       mapping(address => bool) public allowedAssets;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Lock.sol#L18

```solidity
File: contracts/PairsContract.sol

/// @audit (valid but excluded finding)
12:       mapping(uint256 => bool) public allowedAsset;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L12

```solidity
File: contracts/Position.sol

/// @audit (valid but excluded finding)
20:       mapping(address => bool) private _isMinter; // Trading contract should be minter

/// @audit (valid but excluded finding)
34:       mapping(uint256 => mapping(address => mapping(bool => int256))) private accInterestPerOi;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L20

```solidity
File: contracts/Referrals.sol

/// @audit (valid but excluded finding)
9:        bool private isInit;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Referrals.sol#L9

```solidity
File: contracts/StableToken.sol

/// @audit (valid but excluded finding)
9:        mapping(address => bool) public isMinter;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/StableToken.sol#L9

```solidity
File: contracts/StableVault.sol

/// @audit (valid but excluded finding)
29:       mapping(address => bool) public allowed;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/StableVault.sol#L29

```solidity
File: contracts/TradingExtension.sol

/// @audit (valid but excluded finding)
15:       bool public chainlinkEnabled;

/// @audit (valid but excluded finding)
17:       mapping(address => bool) private isNode;

/// @audit (valid but excluded finding)
19:       mapping(address => bool) public allowedMargin;

/// @audit (valid but excluded finding)
20:       bool public paused;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/TradingExtension.sol#L15

```solidity
File: contracts/Trading.sol

/// @audit (valid but excluded finding)
134:      mapping(address => bool) public allowedVault;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L134

```solidity
File: contracts/utils/MetaContext.sol

/// @audit (valid but excluded finding)
7:        mapping(address => bool) private _isTrustedForwarder;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/utils/MetaContext.sol#L7

### [G&#x2011;05]  Using `> 0` costs more gas than `!= 0` when used on a `uint` in a `require()` statement
This change saves **[6 gas](https://aws1.discourse-cdn.com/business6/uploads/zeppelin/original/2X/3/363a367d6d68851f27d2679d10706cd16d788b96.png)** per instance. The optimization works until solidity version [0.8.13](https://gist.github.com/IllIllI000/bf2c3120f24a69e489f12b3213c06c94) where there is a regression in gas costs.

*There is 1 instance of this issue:*

```solidity
File: contracts/GovNFT.sol

/// @audit (valid but excluded finding)
130:          require(tokenId.length > 0, "Not bridging");

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L130

### [G&#x2011;06]  `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too)
Saves **5 gas per loop**

*There are 21 instances of this issue:*

```solidity
File: contracts/BondNFT.sol

/// @audit (valid but excluded finding)
220:                  for (uint i=epoch[_tigAsset]; i<aEpoch; i++) {

/// @audit (valid but excluded finding)
284:              for (uint i=0; i<_ids.length; i++) {

/// @audit (valid but excluded finding)
292:              for (uint i=0; i<_ids.length; i++) {

/// @audit (valid but excluded finding)
300:              for (uint i=0; i<_ids.length; i++) {

/// @audit (valid but excluded finding)
342:              for (uint i=0; i<_ids.length; i++) {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L220

```solidity
File: contracts/GovNFT.sol

/// @audit (valid but excluded finding)
53:           for (uint i=0; i<assetsLength(); i++) {

/// @audit (valid but excluded finding)
67:           for (uint i=0; i<assetsLength(); i++) {

/// @audit (valid but excluded finding)
78:           for (uint i=0; i<assetsLength(); i++) {

/// @audit (valid but excluded finding)
95:           for (uint i=0; i<assetsLength(); i++) {

/// @audit (valid but excluded finding)
105:          for (uint i=0; i<_amount; i++) {

/// @audit (valid but excluded finding)
131:          for (uint i=0; i<tokenId.length; i++) {

/// @audit (valid but excluded finding)
200:          for (uint i=0; i<tokenId.length; i++) {

/// @audit (valid but excluded finding)
246:          for (uint i=0; i<_ids.length; i++) {

/// @audit (valid but excluded finding)
252:          for (uint i=0; i<_ids.length; i++) {

/// @audit (valid but excluded finding)
258:          for (uint i=0; i<_ids.length; i++) {

/// @audit (valid but excluded finding)
325:          for (uint i=0; i<_ids.length; i++) {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L53

```solidity
File: contracts/Lock.sol

/// @audit (valid but excluded finding)
113:          for (uint i=0; i < assets.length; i++) {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Lock.sol#L113

```solidity
File: contracts/Position.sol

/// @audit (valid but excluded finding)
296:          for (uint i=0; i<_ids.length; i++) {

/// @audit (valid but excluded finding)
304:          for (uint i=0; i<_ids.length; i++) {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L296

```solidity
File: contracts/Referrals.sol

/// @audit (valid but excluded finding)
70:           for (uint i=0; i<_codeOwnersL; i++) {

/// @audit (valid but excluded finding)
73:           for (uint i=0; i<_referredAL; i++) {

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Referrals.sol#L70

### [G&#x2011;07]  Using `private` rather than `public` for constants, saves gas
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that [returns a tuple](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPair.sol#L156-L178) of the values of all currently-public constants. Saves **3406-3606 gas** in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

*There are 3 instances of this issue:*

```solidity
File: contracts/Lock.sol

/// @audit (valid but excluded finding)
12:       uint public constant minPeriod = 7;

/// @audit (valid but excluded finding)
13:       uint public constant maxPeriod = 365;

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Lock.sol#L12

```solidity
File: contracts/Position.sol

/// @audit (valid but excluded finding)
16:       uint constant public DIVISION_CONSTANT = 1e10; // 100%

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L16

### [G&#x2011;08]  Use custom errors rather than `revert()`/`require()` strings to save gas
Custom errors are available from solidity version 0.8.4. Custom errors save [**~50 gas**](https://gist.github.com/IllIllI000/ad1bd0d29a0101b25e57c293b4b0c746) each time they're hit by [avoiding having to allocate and store the revert string](https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth). Not defining the strings also save deployment gas

*There are 64 instances of this issue:*

```solidity
File: contracts/BondNFT.sol

/// @audit (valid but excluded finding)
63:           require(allowedAsset[_asset], "!Asset");

/// @audit (valid but excluded finding)
106:          require(bond.owner == _sender, "!owner");

/// @audit (valid but excluded finding)
107:          require(!bond.expired, "Expired");

/// @audit (valid but excluded finding)
108:          require(bond.asset == _asset, "!BondAsset");

/// @audit (valid but excluded finding)
110:          require(epoch[bond.asset] == block.timestamp/DAY, "Bad epoch");

/// @audit (valid but excluded finding)
111:          require(bond.period+_period <= 365, "MAX PERIOD");

/// @audit (valid but excluded finding)
142:          require(bond.expired, "!expire");

/// @audit (valid but excluded finding)
145:                  require(bond.expireEpoch + 7 < epoch[bond.asset], "Bond owner priority");

/// @audit (valid but excluded finding)
173:          require(_claimer == bond.owner, "!owner");

/// @audit (valid but excluded finding)
329:          require(epoch[bond.asset] == block.timestamp/DAY, "Bad epoch");

/// @audit (valid but excluded finding)
330:          require(!bond.expired, "Expired!");

/// @audit (valid but excluded finding)
332:              require(block.timestamp > bond.mintTime + 300, "Recent update");

/// @audit (valid but excluded finding)
350:          require(assets.length == 0 || assets[assetsIndex[_asset]] != _asset, "Already added");

/// @audit (valid but excluded finding)
358:          require(assets[assetsIndex[_asset]] == _asset, "Not added");

/// @audit (valid but excluded finding)
373:          require(msg.sender == manager, "!manager");

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L63

```solidity
File: contracts/GovNFT.sol

/// @audit (valid but excluded finding)
51:           require(counter <= MAX, "Exceeds supply");

/// @audit (valid but excluded finding)
65:           require(msg.sender == address(this) || _msgSender() == owner(), "NotBridge");

/// @audit (valid but excluded finding)
66:           require(tokenId <= 10000, "BadID");

/// @audit (valid but excluded finding)
94:           require(ownerOf(tokenId) == from, "!Owner");

/// @audit (valid but excluded finding)
130:          require(tokenId.length > 0, "Not bridging");

/// @audit (valid but excluded finding)
132:              require(_msgSender() == ownerOf(tokenId[i]), "Not the owner");

/// @audit (valid but excluded finding)
140:          require(isTrustedAddress[_dstChainId][targetAddress], "!Trusted");

/// @audit (valid but excluded finding)
174:          require(_msgSender() == address(endpoint), "!Endpoint");

/// @audit (valid but excluded finding)
185:          require(msg.sender == address(this), "NonblockingLzApp: caller must be app");

/// @audit (valid but excluded finding)
194:          require(isTrustedAddress[_srcChainId][fromAddress], "!TrustedAddress");

/// @audit (valid but excluded finding)
209:          require(payloadHash != bytes32(0), "NonblockingLzApp: no stored message");

/// @audit (valid but excluded finding)
210:          require(keccak256(_payload) == payloadHash, "NonblockingLzApp: invalid payload");

/// @audit (valid but excluded finding)
241:          require(address(_endpoint) != address(0), "ZeroAddress");

/// @audit (valid but excluded finding)
301:          require(assets.length == 0 || assets[assetsIndex[_asset]] != _asset, "Already added");

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L51

```solidity
File: contracts/Lock.sol

/// @audit (valid but excluded finding)
66:           require(_period <= maxPeriod, "MAX PERIOD");

/// @audit (valid but excluded finding)
67:           require(_period >= minPeriod, "MIN PERIOD");

/// @audit (valid but excluded finding)
68:           require(allowedAssets[_asset], "!asset");

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Lock.sol#L66

```solidity
File: contracts/PairsContract.sol

/// @audit (valid but excluded finding)
35:           require(_name.length > 0, "!Asset");

/// @audit (valid but excluded finding)
50:           require(_assetName.length == 0, "Already exists");

/// @audit (valid but excluded finding)
51:           require(bytes(_name).length > 0, "No name");

/// @audit (valid but excluded finding)
52:           require(_maxLeverage >= _minLeverage && _minLeverage > 0, "Wrong leverage values");

/// @audit (valid but excluded finding)
75:           require(_name.length > 0, "!Asset");

/// @audit (valid but excluded finding)
84:           require(_idToAsset[_asset].maxLeverage >= _idToAsset[_asset].minLeverage, "Wrong leverage values");

/// @audit (valid but excluded finding)
94:           require(_name.length > 0, "!Asset");

/// @audit (valid but excluded finding)
95:           require(_baseFundingRate <= maxBaseFundingRate, "baseFundingRate too high");

/// @audit (valid but excluded finding)
106:          require(_name.length > 0, "!Asset");

/// @audit (valid but excluded finding)
117:          require(_name.length > 0, "!Asset");

/// @audit (valid but excluded finding)
141:          require(_name.length > 0, "!Asset");

/// @audit (valid but excluded finding)
157:              require(_idToOi[_asset][_tigAsset].longOi <= _idToOi[_asset][_tigAsset].maxOi || _idToOi[_asset][_tigAsset].maxOi == 0, "MaxLongOi");

/// @audit (valid but excluded finding)
177:              require(_idToOi[_asset][_tigAsset].shortOi <= _idToOi[_asset][_tigAsset].maxOi || _idToOi[_asset][_tigAsset].maxOi == 0, "MaxShortOi");

/// @audit (valid but excluded finding)
190:          require(_msgSender() == address(protocol), "!Protocol");

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L35

```solidity
File: contracts/Position.sol

/// @audit (valid but excluded finding)
315:          require(_isMinter[_msgSender()], "!Minter");

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L315

```solidity
File: contracts/Referrals.sol

/// @audit (valid but excluded finding)
21:           require(_referral[_hash] == address(0), "Referral code already exists");

/// @audit (valid but excluded finding)
81:           require(_msgSender() == address(protocol), "!Protocol");

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Referrals.sol#L21

```solidity
File: contracts/StableToken.sol

/// @audit (valid but excluded finding)
52:           require(isMinter[_msgSender()], "!Minter");

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/StableToken.sol#L52

```solidity
File: contracts/StableVault.sol

/// @audit (valid but excluded finding)
45:           require(allowed[_token], "Token not listed");

/// @audit (valid but excluded finding)
79:           require(!allowed[_token], "Already added");

/// @audit (valid but excluded finding)
90:           require(allowed[_token], "Not added");

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/StableVault.sol#L45

```solidity
File: contracts/TradingExtension.sol

/// @audit (valid but excluded finding)
279:          require(msg.sender == trading, "!protocol");

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/TradingExtension.sol#L279

```solidity
File: contracts/Trading.sol

/// @audit (valid but excluded finding)
876:          require(allowedVault[_stableVault], "Unapproved stablevault");

/// @audit (valid but excluded finding)
877:          require(_token == IStableVault(_stableVault).stable() || IStableVault(_stableVault).allowed(_token), "Token not approved in vault");

/// @audit (valid but excluded finding)
887:              require(_proxy.proxy == _msgSender() && _proxy.time >= block.timestamp, "Proxy not approved");

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L876

```solidity
File: contracts/utils/TradingLibrary.sol

/// @audit (valid but excluded finding)
105:          require(_provider == _priceData.provider, "BadSig");

/// @audit (valid but excluded finding)
106:          require(_isNode[_provider], "!Node");

/// @audit (valid but excluded finding)
107:          require(_asset == _priceData.asset, "!Asset");

/// @audit (valid but excluded finding)
108:          require(!_priceData.isClosed, "Closed");

/// @audit (valid but excluded finding)
109:          require(block.timestamp >= _priceData.timestamp, "FutSig");

/// @audit (valid but excluded finding)
110:          require(block.timestamp <= _priceData.timestamp + _validSignatureTimer, "ExpSig");

/// @audit (valid but excluded finding)
111:          require(_priceData.price > 0, "NoPrice");

```
https:/github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/utils/TradingLibrary.sol#L105
