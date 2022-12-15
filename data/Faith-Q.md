# Enums should be used instead of booleans / uints for clearer code

Using enums to define flags / states result in much clearer code.

### Instance one

In the `Position` contract, the `accInterestPerOi` mapping state variable is defined [here](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L34):

```
mapping(uint256 => mapping(address => mapping(bool => int256))) private accInterestPerOi;
```

The `bool => int256` mapping at the end defines the interest (in `int256` form). The `bool` is used to denote either "long" vs "short" interests.

Using a boolean here is not good practice. An enum should be used instead to describe Long vs Short open orders.

### Instance two

In the `Trading` contract, the `Delay` struct's `actionType` should be an enum instead of a boolean. [Here is the code](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L126-L129)

### Instance three

The `TradingExtension` contract's `getVerifiedPrice()` function uses a `uint` parameter named `_withSpreadIsLong` to determine how to calculate the price with spread. [See here](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L167)

This results in an ugly looking function call. [See here](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L181-L182):

```
uint256 _isLong = _tradeInfo.direction ? 1 : 2;
(uint256 _price,) = tradingExtension.getVerifiedPrice(_tradeInfo.asset, _priceData, _signature, _isLong);
```

The use of `1` vs `2` is ambiguous and hard to understand. Use an enum to make this clearer.

### Instance four

In the [`TradeInfo` struct](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ITrading.sol#L15), the `direction` field should be an enum that describes whether the trade is a Long or a Short. Currently, the boolean field makes this unclear.

Use an enum to make this clearer.

### Instance five

In the [`Trade` struct](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPosition.sol#L15), the `orderType` field should be an enum that describes whether the order is a Market, Limit, or Stop order. Currently, the uint field makes this unclear.

Use an enum to make this clearer.

### Instance six

The `modifyLongOi()` and `modifyShortOi()` functions in the `TradingExtension` and `PairsContract` contracts use a boolean to determine if they're being called when an order is being opened, or when an order is being closed. See [here](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L126-L142) and [here](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L154-L185)

When these functions are called, it forces the caller to simply pass in `true` or `false`, which is very ambiguous. Use an enum (with labels such as "OpenOrder" and "CloseOrder") to make this clearer.

### Instance seven

In the `Trading` contract, the `limitClose()` function takes in a `_tp` boolean parameter to determine whether this order is being closed due to a "take profit" or due to a "stop loss". [See here](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L567)

This should be turned into an enum to make the code clearer. Using an enum also saves 4 gas (applies to all other function call arguments that are `bool`).

### Instance eight

In the `Trading` contract, the `_checkDelay()` function requires a boolean `_type` parameter to determine whether the function is being called when an order is being opened or closed. [See here](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L857)

This boolean should be converted to an enum for clearer code (and to save 4 gas).

# Functions called like modifiers should be turned into modifiers

In the `Trading` contract, the `_validateProxy()` function is used in various functions to ensure the person opening the trade is the trader themselves, or a proxy for a different trader.

Instead of calling this function manually each time, it should be turned into a modifier.

- [One out of many call sites](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L172)
- [_validateProxy() definition](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L884-L889)

The same thing applies to the `_checkVault()`, `_checkOwner()`, and `_checkDelay()` functions. However, I'm reluctant to list them here as modifiers are supposed to be as simple as possible to maintain good practices. These three aforementioned functions make external calls, which may or may not be good practice in a modifier.

A judge or someone else can decide if those are also a good idea to turn into modifiers.

# Unnecessary functions can be removed

In the `PairsContract` contract, the `pauseAsset()` function is unnecessary. It sets `allowedAsset` mapping for a specific asset to `false` to pause the asset, and `true` to unpause the asset. [See here](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L115-L118)

Unfortunately, `allowedAsset` is not used anywhere (see my Gas Optimizations report for more information on this), so this function is redundant.

Either use `allowedAsset` correctly (as mentioned in my Gas Optimizations report), or remove this function.

# A "max" limit variable is updated without updating the corresponding variable

In the `PairsContract` contract, `setMaxBaseFundingRate()` can be used by the owner to set the maximum base funding rate. [See here](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L125-L127).

If the new max is less than the current base funding rate, it's likely that the owner would not want the base funding rate to stay at a higher than max level. This function however does not handle that logic. This logic should be added to prevent the owner from requiring two transactions, which they may easily forget. It will also save them some gas.

# For time calculations, use the solidity time designators

In the `Position` contract, inside `updateFunding()`, [the following line of code](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L120) uses a hard constant of `31536000`. Use `365 days` instead to make the code clearer.

# Two functions with identical functionality should not be written differently

In the `Trading` contract, both the [_handleOpenFees()](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L689-L750) and [_handleCloseFees()](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L762-L810) have pretty much identical functionality (except the return value). However, a quick read of the code allows one to easily realize that the actual logic is laid out differently (i.e the logic is the same, but the way the code is written differs).

This makes it confusing if, in the future, some of this logic needs to be changed. The developer will need to modify the logic in the same way for both functions, but since the logic is laid out differently, it'll require more work, and it makes it easier to introduce bugs in the process.

Ideally, either the logic should be laid out in the exact same way, or a separate internal function should be written that abstracts away the underlying logic, and both functions should call that internal function. This will make the code easier to maintain.