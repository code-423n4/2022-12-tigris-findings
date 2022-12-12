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

