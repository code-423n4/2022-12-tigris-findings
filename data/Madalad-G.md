# Gas Report

## Use `{unchecked ++i}` for counters and for loops
This removes built in overflow/underflow checks to save gas, and is safe because these variables cannot overflow.

### `GovNFT.sol`:

|Function|Avg Saving|
|---|---:|
|Deployment|36728|
|`_bridgeMint`|61|
|`approveMany`|204|
|`crossChain`|463|
|`mint`|71|
|`mintMany`|40861|
|`safeTransferFromMany`|347|
|`safeTransferMany`|278|
|`transferFrom`|71|

Instances:
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L53
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L67
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L78
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L95
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L105
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L131
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L200
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L246
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L252
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L258
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L325

### `Lock.sol`:

|Function|Avg Saving|
|---|---:|
|Deployment|13826|
|`claimGovFees`|63|
Instances:
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L113

### `Position.sol`:

Deployment gas saving: 16409
No function savings (instances are in view functions)
Instances:
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L296
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L304

## Cache length before entering for loop
When using a calculation within a for loop declaration, e.g. `assetsLength()` in `for (uint i=0; i<_assetsLength(); i++)`, it is re-calculated with every iteration. It is more cost effective to calculate the value outside the loop, then use that value (stored in memory) within the for loop declaration, like so;
```
uint256 assetsLength = assetsLength();
for (uint i=0; i<assetsLength; i++) {
    //...
}
```

### `GovNFT.sol`:
|Function|Avg Saving|
|---|---:|
|Deployment|-12305|
|`_bridgeMint`|72|
|`crossChain`|246|
|`mint`|98|
|`mintMany`|28809|
|`safeTransferFromMany`|175|
|`safeTransferMany`|139|
|`transferFrom`|72|
Instances:
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L53
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L67
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L78
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L95
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L105
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L131
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L200
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L246
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L252
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L325

### `Lock.sol`
|Function|Avg Saving|
|---|---:|
|Deployment|456|
|`claimGovFees`|-5|
Instances:
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L113

## Use `x = x + y` instead of `x += y`
`x = x + y` costs less gas than `x += y`.

### `BondNFT.sol`
|Function|Avg Saving|
|---|---:|
|Deployment|1284|
|`extendLock`|14|
- Instances
    - https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L117
    - https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L119

## Clean up mappings
Due to the way items of a mapping are allocated in storage, there is technically no need to remove elements once we are done with them. However, to prevent unecessary data being stored on the blockchain, there exists a [gas refund of 15,000 for clearing storage](https://soliditydeveloper.com/design-pattern-solidity-free-up-unused-storage) (setting storage value from non-zero to zero). This means resetting mappings values back to zero when we are done with them can save gas. This is safe to do upon closing an order and the corresponding NFT is burnt.

### `Trading.sol`
```
function initiateCloseOrder(uint _id, ...) external {

    //...

    delete blockDelayPassed[_id];
    delete limitDelay[_id];
}
```
|Function|Avg Saving|
|---|---:|
|Deployment|-8841|
|`initiateCloseOrder`|2131|

Instances:
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L222-L243

### `BondNFT.sol`
Since you cannot call `delete` on a mapping, for this saving the `_burn` function must be altered accordingly:
```
function _burn(
    uint256 _id
) internal override {
    delete _idToBond[_id];

    // new code to clear `bondPaid` mapping:
    address[] memory _assets = assets;
    uint256 assetsLength = _assets.length;
    for (uint256 i = 0; i < assetsLength; ) {
        delete bondPaid[_id][_assets[i]];
        unchecked{ ++i; }
    }

    super._burn(_id);
}
```
This leads to a gas saving when calling the `release` function:

|Function|Avg Saving|
|---|---:|
|Deployment|-51596|
|`release`|4226|
