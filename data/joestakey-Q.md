## Summary
### Low Risk
|      | Issue                                                                                                 |
|------|-------------------------------------------------------------------------------------------------------|
| L-01 | Avoid performing multiplications after division in Solidity, as this can lead to a loss of precision  |
| L-02 | `Trading.setBlockDelay` does not enforce the block delay requirements                                 |
| L-03 | Wrong error string in `Escher._grantRole` for any other role than `CREATOR_ROLE`                      |
| L-04 | `_bridgeMint` access control is incorrect                                                             |
| L-05 | `addAssets` can DOS `GovNFT` minting                                                                  |
| L-06 | `Position.setMinter` can be abused by `owner` and rug `Trading` users.                                |
| L-07 | remove deprecated functions                                                                           |
| L-08 | `StableToken.setMinter` can be abused by `owner` to steal vault underlying tokens from users          |

### Non Critical
|      | Issue                                                                            |
|------|----------------------------------------------------------------------------------|
| N-01 | Use a more recent version of Solidity                                            |
| N-02 | Function naming                                                                  |
| N-03 | Constants instead of magic numbers                                               |
| N-04 | Line length                                                                      |


## Low
## [L‑01] Avoid performing multiplications after division in Solidity, as this can lead to a loss of precision


If possible - ie when overflowing is impossible/not likely - divide after performing all the multiplications

https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/TradingExtension.sol#L71
```solidity
70:         if (_trade.direction) {
71:                 modifyLongOi(_trade.asset, _trade.tigAsset, false, (_trade.margin*_trade.leverage/1e18)*_percent/DIVISION_CONSTANT); //@audit can lead to precision loss
72:             } else {
73:                 modifyShortOi(_trade.asset, _trade.tigAsset, false, (_trade.margin*_trade.leverage/1e18)*_percent/DIVISION_CONSTANT); //@audit can lead to precision loss     
74:             }
```

https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L65
```solidity
65: uint shares = _amount * _period / 365; //@audit consider removing divison by 365, amount * period cannot overflow anyway
```

## [L‑02] `Trading.setBlockDelay` does not enforce the block delay requirements  


The README states that:

```
BlockDelay would be ~2x valid signature time in blocks.
```

But looking at `Trading.setBlockDelay`, `blockDelay` can be set to any value.

```solidity
898:     function setBlockDelay( //@audit C: owner can set any block delay
899:         uint _blockDelay
900:     )
901:         external
902:         onlyOwner
903:     {
904:         blockDelay = _blockDelay;
905:     }
906: 
```

### Recommendation

Add appropriate checks in `Trading.setBlockDelay` to ensure `blockDelay` is approximately twice the valid signature time in blocks.

## [L‑03] Possible DOS in `BondNFT.distribute`

This function loops through `aEpoch - epoch[_tigAsset]` times to distribute rewards to bonds.

```solidity
219:             if (aEpoch > epoch[_tigAsset]) {
220:                 for (uint i=epoch[_tigAsset]; i<aEpoch; i++) {
221:                     epoch[_tigAsset] += 1;
222:                     accRewardsPerShare[_tigAsset][i+1] = accRewardsPerShare[_tigAsset][i];
223:                 }
224:             }
```

This difference corresponds to the number of days since `epoch[_tigAsset]` was last updated. If this number is high enough, `distribute()` gas consumption will exceed the block gas limit and revert.

This will lead to `Lock.claimGovFees` reverting, meaning all core functions of `Lock` (`lock()`, `release()`, `claim()`, `claimDebt()`) reverting, breaking the entire `BondNFT` system.

Each iteration adds a minimum of ~`10,000` gas (2 SSTORE operations). The block gas limit on Polygon is 30M, meaning this DOS happens if `aEpoch - epoch[_tigAsset]` is approximately over 8 years, so I consider this issue to be of low severity.


## [L‑04] `_bridgeMint` access control is incorrect


```solidity
59:     /**
60:      * @dev should only be called by layer zero
61:      * @param to the address to receive the bridged NFTs
62:      * @param tokenId the NFT id
63:      */
64:     function _bridgeMint(address to, uint256 tokenId) public { 
65:         require(msg.sender == address(this) || _msgSender() == owner(), "NotBridge");
```

`GovNFT._bridgeMint` should only be called by layer zero, but if can be called by `owner`, ie 'deployer' as per [the deploy scripts](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/deploy/main/05.GovNFT.js#L11).
This is a logic error (ie the function does not behave as per the natspec comment), but not a rugging vector, as the `owner` can mint all the assets/ any asset they want with `mint` and `mintMany`.


Note that the error string "notBridge" is also misleading, as it can also be called by owner.

### Recommendation

```diff
64:     function _bridgeMint(address to, uint256 tokenId) public { 
-65:         require(msg.sender == address(this) || _msgSender() == owner(), "NotBridge");
+65:         require(msg.sender == address(this), "NotBridge");
```

## [L‑05] `addAssets` can DOS `GovNFT` minting

https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L303
The `owner` can add `assets`, but cannot remove them.

If the `assets` array is large enough, `_mint`, `_transfer`, `_burn` gas consumption will exceed the gas limit and revert, breaking `GovNFT`.

Because it is contingent on admin setting enough allowed assets and adding them for this to happen, I consider this a low severity issue.

## [L‑06] `Position.setMinter` can be abused by `owner` and rug `Trading` users.


Most `Position` functions have access control to restrict calls to `minters`, which allow `Trading` external calls to it.

The issue is that `Position.setMinter` allows the `owner` to set any address as a minter, including an EOA.

The problem is that a malicious minter can use `Position` functions to modify `Trading` positions, bypassing `Trading` checks.

For instance, they could call `Position.addToPosition` to change margin and entry prices using arbitrary values, bypassing all the checks in `Trading.addToPosition`

### Recommendation

Ensure minters added in `Position.setMinter` implement `ITrading`.

## [L‑07] remove deprecated functions

`Referrals.initRefs` is deprecated as per the comments. This function can however still be called and write in storage.

```solidity
57: /**
58:     * @notice deprecated @audit deprecated functions should be removed
59:     */
60:     function initRefs(
61:         address[] memory _codeOwners,
62:         bytes32[] memory _ownedCodes,
63:         address[] memory _referredA,
64:         bytes32[] memory _referredTo
65:     ) external onlyOwner {
```

Remove this function

## [L‑08] `StableToken.setMinter` can be abused by `owner` to steal vault underlying tokens from users.

`StableToken` functions `burnFrom` and `mintFor` have access control, which allow `StableVault` external calls to them.

The issue is that `StableToken.setMinter` allows the `owner` to set any address as a minter, including an EOA.

This gives the opportunity for a malicious minter to rug users of their underlying tokens:

Let us take the example of Alice, who just deposited `1,000 USDT` in the Arbitrum Vault by calling `deposit()`.
She received `1,000 tigUSD`.

- maliciousMinter calls `burnFrom(Alice, 1000)`, burning Alice's `tigUSD`.
- maliciousMinter calls then `mintFor(maliciousOwner, 1000)`, minting themselves `1000 tigUSD` for free.
- maliciousMinter calls finally `StableVault.withdraw(token, 1000)`, withdrawing `1000 USDT` from the vault and burning the `1000 tigUSD` they just minted.

The malicious minter just stole `1,000 USDT` from `Alice`.

### Recommendation

Ensure minters added in `StableToken.setMinter` implement `IStableVault` or `ITrading`.

## Non-critical
## [N-01] Use a more recent version of Solidity
All contracts have a Solidity version of: `pragma solidity ^0.8.0`

For security, it is best practice to use the latest Solidity version. For the security fix list in the versions; https://github.com/ethereum/solidity/blob/develop/Changelog.md

Consider updating the version to `0.8.17`



## [N-02] Function naming
For readability and to ensure best practice, public functions should not start with a dash.

https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L64
```solidity
64: function _bridgeMint(address to, uint256 tokenId) public {
    //@audit - should be 'bridgeMint'
```

## [N-03] Constants instead of magic numbers
For readability, consider using constants instead of magic numbers
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L120
```solidity
120: fundingDeltaPerSec[_asset][_tigAsset] = (_oiDelta*int256(_baseFundingRate)/int256(DIVISION_CONSTANT))/31536000
//@audit - 31536000
```

## [N-04] Line length
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L178

GitHub starts using a scroll bar in all cases when the length is over 164 characters.


