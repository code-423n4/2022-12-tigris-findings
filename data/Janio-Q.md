## Function `StableVault.listToken` accepts `address(0)` as new token

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

## Function `TradingExtension.setNode` accepts `address(0)` as new node

The `setNode(...)` function does not validate the `_node` address.



```solidity
    function setNode(address _node, bool _bool) external onlyOwner {
        isNode[_node] = _bool;
    }
```

**Recommendation:** Ensure the new node `_node` is a valid address.

---

## Missing zero address validation and events 

Constructors in `StableVault` and `TradingExtension` contracts do **NOT**: 

1 - Check if `_stable` local variable is different of `address(0)` and does not emit an event with the `stable` address set, since it's a immutable state variable.

File: [`StableVault.sol#L36`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L36)

```solidity
    constructor(address _stable) {
        stable = _stable;
    }
```

2 - Check `_trading` local variable is different of `address(0)` and does emit event.

File: [`TradingExtension.sol#L35`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L35)

```solidity
    constructor(
        address _trading,
        address _pairsContract,
        address _ref,
        address _position
    )
    {
        trading = _trading;
        pairsContract = IPairsContract(_pairsContract);
        referrals = IReferrals(_ref);
        position = IPosition(_position);
    }
```

**Recommendation:** Consider validating `_stable` and `_trading` addresses and emitting event for state variables changes.

