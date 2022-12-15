# Redundant or unnecessarily complex checks / functions

### Instance one

In `TradingExtension`, the `_setReferral` function checks to make sure the trader doesn't already have a referrer set:
```solidity
function _setReferral(
    bytes32 _referral,
    address _trader
) external onlyProtocol {
    if (_referral != bytes32(0)) {
        if (referrals.getReferral(_referral) != address(0)) {
            // checks for this
            if (referrals.getReferred(_trader) == bytes32(0)) {
                referrals.setReferred(_trader, _referral);
            }
        }
    }
}
```

This is unnecessary since `setReferred` in the `Referral` contract already checks for this:
```
function setReferred(address _referredTrader, bytes32 _hash) external onlyProtocol {
    if (_referred[_referredTrader] != bytes32(0)) { // HERE
        return;
    }
    if (_referredTrader == _referral[_hash]) {
        return;
    }
    _referred[_referredTrader] = _hash;
    emit Referred(_referredTrader, _hash);
}
```

Remove this check to save gas.

- [TradingExtension](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L190-L202)
- [Referral](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol#L32-L41)

### Instance two

The `executeLimitOrder()` function in the `Position` contract checks that the order isn't a market order (type 0):

```
function executeLimitOrder(
    uint256 _id,
    uint256 _price,
    uint256 _newMargin
) external onlyMinter {
    Trade storage _trade = _trades[_id];

    if (_trade.orderType == 0) {
        return;
    }
    // [ ... ]
}
```

This check is unnecessary because the caller of this function already checks for this:
```
function executeLimitOrder(
    uint _id,
    PriceData calldata _priceData,
    bytes calldata _signature
) external {
        // [ ... ]

        if (trade.orderType == 0) revert("5");

        // [ ... ]

        position.executeLimitOrder(_id, trade.price, trade.margin - _fee);
}
```

Remove this check to save gas.

- [Position](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol#L32-L41)
- [Trading](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L495)

### Instance three

When adding an asset to the `PairsContract` contract, `addAsset()` is called. This function, amongst other things, [will set a boolean in a mapping to `true` to signify that an asset with this ID is allowed to be used](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L54):

```
function addAsset(
    uint256 _asset,
    string memory _name,
    address _chainlinkFeed,
    uint256 _minLeverage,
    uint256 _maxLeverage,
    uint256 _feeMultiplier,
    uint256 _baseFundingRate
) external onlyOwner {
    bytes memory _assetName = bytes(_idToAsset[_asset].name);
    require(_assetName.length == 0, "Already exists");
    require(bytes(_name).length > 0, "No name");
    require(
        _maxLeverage >= _minLeverage && _minLeverage > 0,
        "Wrong leverage values"
    );

    allowedAsset[_asset] = true; // HERE
    
    // [ ... ]
}
```

However, this mapping is not used anywhere else in the code. Here are all the instances where it should be used to save gas (all in the `PairsContract` contract). All the instances are the same, so I'll show one example and just list the other functions out.

Example - [`updateAssetLeverage()`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L73):
```
function updateAssetLeverage(
    uint256 _asset,
    uint256 _minLeverage,
    uint256 _maxLeverage
) external onlyOwner {
    // Replace this with `require(allowedAsset[_idToAsset], "!Asset");`
    bytes memory _name = bytes(_idToAsset[_asset].name);
    require(_name.length > 0, "!Asset");

    // [ ... ]
}
```

Remaining instances:

- `setAssetBaseFundingRate(https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L92)`
- `updateAssetFeeMultiplier(https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L104)`
- `pauseAsset(https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L115)` 
- `setMaxOi(https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L139)`

### Instance four

In the `Trading` contract, `_handleOpenFees()` and `_handleCloseFees()` have pretty much identical functionality, but the logic, albeit being identical, is laid out differently for some reason (see my QA report for more details on this).

Nonetheless, at the end of both functions, the DAO fees are distributed to the governance contract:

```
function _handleOpenFees(/* [ ... ] */) internal returns (uint _feePaid) {
    // [ ... ]
    gov.distribute(_tigAsset, IStable(_tigAsset).balanceOf(address(this)));
}

function _handleCloseFees(/* [ ... ] */) internal returns (uint payout_) {
    // [ ... ]
    gov.distribute(_tigAsset, _daoFeesPaid);
}
```

As seen above, `_handleCloseFees` simply uses the stack variable `_daoFeesPaid` to distribute the required number of tokens. `_handleOpenFees()` also has this variable, but does not use it. Making a function call to `balanceOf()` is more expensive and thus gas can be saved here by using `_daoFeesPaid` in `_handleOpenFees()` as well.

- [_handleOpenFees()](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L749)
- [_handleCloseFees()](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L808)

### Instance five

In the `StableVault` contract, the `depositWithPermit()` function allows a user to pass in a `_permitMax` boolean:

```
    function depositWithPermit(
        // [ ... ]
        bool _permitMax,
        uint256 _amount,
        // [ ... ]
    ) external {
        uint _toAllow = _amount;
        if (_permitMax) _toAllow = type(uint).max;
        
    // [ ... ]
    }
```

This variable is unnecessary. If a user wants to approve the max amount, they can just set the `_amount` variable to `type(uint).max` themselves. Remove this to save gas.

- [StableVault](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L53)

# Repeated unnecessary calls to `approve()`

There are a few instances where `approve()` is called multiple times, when it can be called once.

### Instance one

The `_tigAsset` in question should be approved whenever it was created and denoted as a valid tigAsset within the Tigris ecosystem.
```
    function _handleCloseFees(
        uint _asset,
        uint _payout,
        address _tigAsset,
        uint _positionSize,
        address _trader,
        bool _isBot
    ) internal returns (uint payout_) {
        // [...]

        IStable(_tigAsset).approve(address(gov), type(uint).max);
        gov.distribute(_tigAsset, _daoFeesPaid);
        return payout_;
    }
```

As it stands currently, any time an order is closed (i.e `_handleCloseFees()` is called), the `approve()` function is called. This is unnecessary and a lot of gas would be saved by calling it once.

- [_handleCloseFees()](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L807)

### Instance two

The `Lock` contract is used whenever a user wants to lock their funds in for bonds. The `claimGovFees()` function is called pretty often:

```
function claimGovFees() public {
    address[] memory assets = bondNFT.getAssets();

    for (uint i = 0; i < assets.length; i++) {
        uint balanceBefore = IERC20(assets[i]).balanceOf(address(this));
        IGovNFT(govNFT).claim(assets[i]);
        uint balanceAfter = IERC20(assets[i]).balanceOf(address(this));

        IERC20(assets[i]).approve(address(bondNFT), type(uint256).max);
        bondNFT.distribute(assets[i], balanceAfter - balanceBefore);
    }
}
```

The call to `approve()` on the second last line can be done just once on initialization of the assets themselves (one `approve()` call for each asset). As it stands, a lot of gas is being used for every single approve here.

- [claimGovFees()](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L110)

# Gas-expensive checks should come first before others

There are a few instances where a more gas-expensive check (that reverts) comes before a less gas-expensive check.

### Instance one

In the `Trading` contract, inside `addToPosition()`:
```
function addToPosition(
    // [ ... ]
) external {
    // [ ... ]

    IPosition.Trade memory _trade = position.trades(_id);

    // Gas expensive check here
    tradingExtension.validateTrade(
        _trade.asset,
        _trade.tigAsset,
        _trade.margin + _addMargin,
        _trade.leverage
    );

    // The following two checks can be moved to before the
    // gas expensive check above
    _checkVault(_stableVault, _marginAsset);
    if (_trade.orderType != 0) revert("4"); //IsLimit

    // [ ... ]
}
```

See comments above. The two checks can be moved to before the gas expensive check to save on gas.

- [addToPosition()](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L271-L273)

### Instance two

In the `Trading` contract, inside `initiateLimitOrder()`:

```
function initiateLimitOrder(
    // [ ... ]
) external {
    _validateProxy(_trader);
    address _tigAsset = IStableVault(_tradeInfo.stableVault).stable();

    // Check this last
    tradingExtension.validateTrade(
        _tradeInfo.asset,
        _tigAsset,
        _tradeInfo.margin,
        _tradeInfo.leverage
    );

    // Check this second
    _checkVault(_tradeInfo.stableVault, _tradeInfo.marginAsset);

    // Check these first
    if (_orderType == 0) revert("5");
    if (_price == 0) revert NoPrice();
   
    // [ ... ]
}
```

I've added comments in the code excerpt to show the most gas-efficient way to re-order these checks.

- [initiateLimitOrder()](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L325-L328)

### Instance three

In the `Trading` contract, inside `executeLimitOrder()`:

```
function executeLimitOrder(
    uint _id,
    PriceData calldata _priceData,
    bytes calldata _signature
) external {
        // [ ... ]

        // Do these checks before calling _handleOpenFees() and getVerifiedPrice()
        if (trade.orderType == 0) revert("5");

        // Two more sets of checks come immediately after this, they should also
        // be moved up

        // [ ... ]
}
```

This function calls `_handleOpenFees()` and `tradingExtension.getVerifiedPrice()` before running a few sets of checks that can potentially revert. The checks should be moved before the function calls to save gas.

- [executeLimitOrder()](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L493-L507)

# State variables can be made immutable to save gas

1. [In the `NFTSale` contract, both the `nft` and `token` state variables can be made immutable.](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/NFTSale.sol#L21-L22)
2. [In the `Trading` contract, the `pairsContract`, `position`, and `gov` state variables can be made immutable.](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L121-L123)

# Check for `amount == 0` before calling `transfer()` or `transferFrom()`

There are instances in the code where a 0 amount of tokens is transferred. Gas can be saved by not performing these transfer calls at all.

In the `BondNFT` contract:

- [`claim()`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L185)
- [`_claimDebt()`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L202)

In the `Lock` contract:

- `claim()`
- `claimDebt()`

# Internal functions called only once can be inlined

There is one internal function that is only called once. The function call itself uses up some gas, so this function call can be inlined.

Specifically, the function is the `_mint()` internal function in the `BondNFT` contract. The only call site is in the `createLock()` function in the same contract.

- [_mint() function](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L306)
- [_mint() callsite](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L83)