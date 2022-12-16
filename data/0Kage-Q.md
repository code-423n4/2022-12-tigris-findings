_Issue_

To close a trade, `burn(id)` function is called in [Line 260 of Position.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L260). All mappings related to the trade ID are deleted except `initId` which stores accrued interest values for the given trade.

This does not materially impact calculations but leads to uneccessary storage overload

_Recommendation_
Add the following immediately after [Line 279](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L279)

```
delete initId[_id];

```