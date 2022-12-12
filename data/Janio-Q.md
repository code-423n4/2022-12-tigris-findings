## Missing zero address validation and events 

The constructor in `StableVault`contract does not check if `_stable` local variable is `address(0)` and does not emit an event with the `stable` address set, since it's a immutable state variable.


```solidity
    constructor(address _stable) {
        stable = _stable;
    }
```

**Recommendation:** Consider validating `_stable` address and emitting an event for state variable changes.

