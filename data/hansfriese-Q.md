#### [L-01] `GovNFT.setAllowedAsset()` should check if the `_asset` is in `assets` array
- https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/GovNFT.sol#L307-L309

```solidity
    function setAllowedAsset(address _asset, bool _bool) external onlyOwner { 
        _allowedAsset[_asset] = _bool;
    }
```

- https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/GovNFT.sol#L287-L294

```solidity
    function distribute(address _tigAsset, uint _amount) external {
        if (assets.length == 0 || assets[assetsIndex[_tigAsset]] == address(0) || totalSupply() == 0 || !_allowedAsset[_tigAsset]) return;
        try IERC20(_tigAsset).transferFrom(_msgSender(), address(this), _amount) {
            accRewardsPerNFT[_tigAsset] += _amount/totalSupply();
        } catch {
            return;
        }
    }
```

When it checks a valid asset in `distribute()`, it checks if `_tigAsset` is in the `assets` array first.

So even if `_allowedAsset[_asset]` is true, the `_asset` will be considered as invalid if it's not in `assets` array.

Also, it's meaningless to set false to the `_asset` that is not in the array.

We can modify `setAllowedAsset()` like below.

```solidity
    function setAllowedAsset(address _asset, bool _bool) external onlyOwner {
        require(assets[assetsIndex[_asset]] == _asset, "Should be added first"); //++++++++++++++++++
        _allowedAsset[_asset] = _bool;
    }
```

#### [L-02] There is no option to remove elements from `assets` array
- https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/GovNFT.sol#L264

There should be a deletion logic for `assets` even though an asset can be disabled using `_allowedAsset`.

#### [L-03] Needless logic that might bring an uint underflow
- https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/PairsContract.sol#L161
- https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/PairsContract.sol#L181

```solidity
File: 2022-12-tigris\contracts\PairsContract.sol
161:             if (_idToOi[_asset][_tigAsset].longOi < 1e9) {
162:                 _idToOi[_asset][_tigAsset].longOi = 0;
163:             }
```

It resets `longOi/shortOi` for dust amounts and it might bring an uint underflow later because the `longOi/shortOi` are smaller than real values.

I think it would be almost impossible to happen as we check a [minPositionSize](https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/TradingExtension.sol#L218) but such unnecessary logic should be removed.

#### [L-04] Possible rounding issue
- https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/Trading.sol#L295

```solidity
File: 2022-12-tigris\contracts\Trading.sol
295:             uint _newPrice = _trade.price*_trade.margin/_newMargin + _price*_addMargin/_newMargin;
```

There might be a rounding issue as it divides seperately and we can modify like below.

```solidity
uint _newPrice = (_trade.price * _trade.margin + _price * _addMargin) / _newMargin;
```

#### [L-05] There should an lower limit for `maxWinPercent`
- https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/Trading.sol#L926-L933
- https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/Trading.sol#L625

Currently, there is no lower limit of `maxWinPercent` and users will receive only `1/1e10` portion of margin when the admin sets `maxWinPercent = 1` maliciously.

#### [L-06] Too strict requirements for `_margin` 
- https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/Trading.sol#L651-L654

```solidity
File: 2022-12-tigris\contracts\Trading.sol
651:             IERC20(_marginAsset).transferFrom(_trader, address(this), _margin/_marginDecMultiplier);
652:             IERC20(_marginAsset).approve(_stableVault, type(uint).max);
653:             IStableVault(_stableVault).deposit(_marginAsset, _margin/_marginDecMultiplier);
654:             if (tigAsset.balanceOf(address(this)) != _balBefore + _margin) revert BadDeposit(); //@audit bad accuracy
655:             tigAsset.burnFrom(address(this), tigAsset.balanceOf(address(this)));
```

It reverts if the increased amount is the same as `_margin` and users might get unnecessary reverts.

For example, if `_marginAsset` is an `USDC` of 6 decimals, `_margin` should be divided by `1e12` and it will revert otherwise.

It would be confusing for normal users as they can input any `_margin` amount for the `_tigAsset`.

We can modify like below to work for any cases.

```solidity
    IERC20(_marginAsset).transferFrom(_trader, address(this), _margin/_marginDecMultiplier);
    IERC20(_marginAsset).approve(_stableVault, type(uint).max);
    IStableVault(_stableVault).deposit(_marginAsset, (_margin + _marginDecMultiplier - 1)/_marginDecMultiplier); //++++++++++
    if (tigAsset.balanceOf(address(this)) < _balBefore + _margin) revert BadDeposit(); //++++++++++++
```

#### [L-07] Wrong comment
- https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/TradingExtension.sol#L82

```solidity
* @param _tp true if long, else short
```

It should be changed to `@param _tp true if takeprofit, else stoploss`

#### [L-08] Possible underflow
- https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/Trading.sol#L286
- https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/Trading.sol#L519

These parts are inside the `unchecked` block and the final amount after extracting fee would be huge for uint underflow.

This case would be happen when the fee is greater than margin which means `feePercent * leverage > 1`.

Currently it's not possible with the default fee/leverage settings but it might happen for some weird settings.

We should confirm the fee is less than margin for any cases.

#### [L-09] Not implemented like a documentation
- https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/Trading.sol#L295

```solidity
    uint _newMargin = _trade.margin + _addMargin;
    uint _newPrice = _trade.price*_trade.margin/_newMargin + _price*_addMargin/_newMargin;
```

- https://docs.tigris.trade/protocol/trading-and-fees/opening-a-position-on-top-of-another-one

```
If you're adding to a long trade, your opening price will be higher and your liquidation price will be lower.
If you're adding to a short trade, your opening price will be lower and your liquidation price will be higher.
```

But during the `_newPrice` calculation, the new price can be higher or lower than the original price according to current open price.

I am wondering if there should be another requirement to add positions in order to work as the document.

#### [N-01] Typo
- https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/PairsContract.sol#L151
- https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/PairsContract.sol#L171

Change `interesr` to `interest`