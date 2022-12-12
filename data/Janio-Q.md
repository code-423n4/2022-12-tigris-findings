## Function StableVault.listToken accept address(0) as new token

The `listToken(...)` function does not validate the `_token` address.

```solidity
    function listToken(address _token) external onlyOwner {
        require(!allowed[_token], "Already added");
        tokenIndex[_token] = tokens.length;
        tokens.push(_token);
        allowed[_token] = true;
    }
```
**Recommendation:** Ensure the new token `_token` is a valid address.

---

## Missing zero address validation and events 

The constructor in `StableVault`contract does not check if `_stable` local variable is `address(0)` and does not emit an event with the `stable` address set, since it's a immutable state variable.


```solidity
    constructor(address _stable) {
        stable = _stable;
    }
```

**Recommendation:** Consider validating `_stable` address and emitting an event for state variable changes.

## Try using uint instead of bools for storage to avoid overhead

[`Trading.sol#L128`](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L128)
```solidity
   struct Delay {
        ...
        bool actionType; // True for open, False for close
    }
```
[`TradingLibrary.sol#L18`](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/utils/TradingLibrary.sol#L18)
```solidity
struct PriceData {
    ...
    bool isClosed;
}
```
[`Position.sol#L20`](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L20)
```solidity
mapping(address => bool) private _isMinter; // Trading contract should be minter
```
[`Position.sol#L34`](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L34)
```solidity
mapping(uint256 => mapping(address => mapping(bool => int256))) private accInterestPerOi;
```
[`BondNFT.sol#L23`](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L23)
```solidity
    struct Bond {
        ...
        bool expired;
       }
```
[`IPosition.sol#L11`](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/IPosition.sol#L11)
```solidity
struct Trade {
        ...
        bool direction;
        ...
    }
```
[`IPosition.sol#L27`](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/IPosition.sol#L27)
```solidity
 struct MintTrade {
        ...
        bool direction;
        ...
    }
```
[`ITrading.sol#L15`](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/ITrading.sol#L15)
```solidity
struct TradeInfo {
        ...
        bool direction;
        ...
}
```
[`ITrading.sol#L27`](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/ITrading.sol#L27)
```solidity
    struct ERC20PermitData {
        ...
        bool usePermit;
   }
```

**Recommendation:** Consider using uint256(1) and uint256(2) for true and false to avoid wasting gas when changing from false to true, after having been true in the past
