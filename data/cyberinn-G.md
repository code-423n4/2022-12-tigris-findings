# Gas Optimizations

## [G-01] Splitting require() statements that use `&&` saves gas

### Impact

When using `&&` in a `require()` statement, the entire statement will be evaluated before the transaction reverts. If the first condition is false, the second condition will not be evaluated. This means that if the first condition is false, the second condition will not be evaluated, which is a waste of gas. Splitting the `require()` statement into two separate statements will save gas.

### Findings

[contracts/utils/TradingLibrary.sol#L116-119](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/utils/TradingLibrary.sol#L116-119)

```solidity
116:    require(
117:        _priceData.price < assetChainlinkPrice+assetChainlinkPrice*2/100 &&
118:       _priceData.price > assetChainlinkPrice-assetChainlinkPrice*2/100, "!chainlinkPrice"
119:    );
```

[contracts/Trading.sol#L887](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Trading.sol#L887)

```solidity
887:    require(_proxy.proxy == _msgSender() && _proxy.time >= block.timestamp, "Proxy not approved");
```

[contracts/PairsContract.sol#L52](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/PairsContract.sol#L52)

```solidity
52:    require(_maxLeverage >= _minLeverage && _minLeverage > 0, "Wrong leverage values");
```

Total:3

## [G-02] Structs can be packed into fewer storage slots

### Impact

Each slot saved can avoid an extra Gsset (20000 gas) for the first setting of the struct. Subsequent reads as well as writes have smaller gas savings.

### Findings


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

Fix:

```solidity
12:    struct PriceData {
           bool isClosed;
13:        address provider;
14:        uint256 asset;
15:        uint256 price;
16:        uint256 spread;
17:        uint256 timestamp;
18:        
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

Fix:

```solidity
9:    struct TradeInfo {
10:            uint256 margin;
               bool direction;
11:            address marginAsset;
12:            address stableVault;
13:            uint256 leverage;
14:            uint256 asset;
15:
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

Fix:

```solidity
22:    struct MintTrade {
               bool direction;
23:            address account;
24:            uint256 margin;
25:            uint256 leverage;
26:            uint256 asset;
27: 
28:            uint256 price;
29:            uint256 tp;
30:            uint256 sl;
31:            uint256 orderType;
32:            address tigAsset;
33:        }
```

Total:3

## [G-03] Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct

### Impact

Saves a storage slot for the `mapping`. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to [not having to recalculate the key's keccak256 hash](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0) (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

### Findings

[contracts/Position.sol#L36-38](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Position.sol#L36-38)

```solidity
36:        mapping(uint256 => int256) private initId;
37:        mapping(uint256 => mapping(address => uint256)) private longOi;
38:        mapping(uint256 => mapping(address => uint256)) private shortOi;
```

[contracts/Position.sol#L33-35](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Position.sol#L33-35)

```solidity
33:        mapping(uint256 => mapping(address => int256)) public fundingDeltaPerSec;
34:        mapping(uint256 => mapping(address => mapping(bool => int256))) private accInterestPerOi;
35:        mapping(uint256 => mapping(address => uint256)) private lastUpdate;
```

[contracts/Lock.sol#L18-19](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Lock.sol#L18-19)

```solidity
18:        mapping(address => bool) public allowedAssets;
19:        mapping(address => uint) public totalLocked;
```

[contracts/BondNFT.sol#L36-38](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/BondNFT.sol#L36-38)

```solidity
36:        mapping(uint => Bond) private _idToBond;
37:        mapping(address => uint) public totalShares;
38:        mapping(address => mapping(address => uint)) public userDebt; // user => tigAsset => amount
```

[contracts/BondNFT.sol#L32-35](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/BondNFT.sol#L32-35)

```solidity
32:        mapping(address => bool) public allowedAsset;
33:        mapping(address => uint) private assetsIndex;
34:        mapping(uint256 => mapping(address => uint256)) private bondPaid;
35:        mapping(address => mapping(uint256 => uint256)) private accRewardsPerShare; // tigAsset => epoch => accRewardsPerShare
```

[contracts/TradingExtension.sol#L17-19](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/TradingExtension.sol#L17-19)

```solidity
17:        mapping(address => bool) private isNode;
18:        mapping(address => uint) public minPositionSize;
19:        mapping(address => bool) public allowedMargin;
```

[contracts/GovNFT.sol#L265-269](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/GovNFT.sol#L265-269)

```solidity
265:        mapping(address => bool) private _allowedAsset;
266:        mapping(address => uint) private assetsIndex;
267:        mapping(address => mapping(address => uint256)) private userPaid;
268:        mapping(address => mapping(address => uint256)) private userDebt;
269:        mapping(address => uint256) private accRewardsPerNFT;
```

[contracts/Referrals.sol#L13-14](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/Referrals.sol#L13-14)

```solidity
13:        mapping(bytes32 => address) private _referral;
14:        mapping(address => bytes32) private _referred;
```

[contracts/StableVault.sol#L29-30](https://github.com/code-423n4/2022-12-tigris/tree/main//contracts/StableVault.sol#L29-30)

```solidity
29:        mapping(address => bool) public allowed;
30:        mapping(address => uint) private tokenIndex;
```

Total: 9
