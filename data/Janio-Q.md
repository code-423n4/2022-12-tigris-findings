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

## `StableToken.setMinter` can add zero address as a minter

```solidity
    function setMinter(
        address _address,
        bool _status
    ) 
        public
        onlyOwner()
    {
        isMinter[_address] = _status;
    }
```



## Functions in `TradingExtension` do not validate addresses

File: [`TradingExtension.sol#L240-L272`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L240-L272)

The following functions accepts `address(0)`:

1 - As new node -  `setNode(...)` function

```solidity
    function setNode(address _node, bool _bool) external onlyOwner {
        isNode[_node] = _bool;
    }
```

2 - As allowed tigAssed:  `setAllowedMargin(...)` function

```solidity
    function setAllowedMargin(
        address _tigAsset,
        bool _bool
    ) 
        external
        onlyOwner
    {
        allowedMargin[_tigAsset] = _bool;
    }
```
3 - Change the minimum position size: `setMinPositionSize(...)` function
```solidity
    function setMinPositionSize(
        address _tigAsset,
        uint _min
    ) 
        external
        onlyOwner
    {
        minPositionSize[_tigAsset] = _min;
    }
```

**Recommendation:** Ensure the new node `_node` and `_tigAsset` are valid addresses.

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

## The function `Lock.release` don't check for the owner of the asset 

The function `Lock.release` don't check if the `msg.sender` is the owner of the asset of id `_id`. Therefore, anyone can trye to release the bond. However, it calls `BondNFT.release` which checks if the releaser is the owner (`if (_releaser != bond.owner) {` ). But it lets the `_releaser` release the bond if the `bond.expireEpoch + 7` is equals or bigger than `epoch[bond.asset]`. It then calls `BondNFT.claim`, pasing `bond.owner` as the owner, which will succeed. A third party could, then, release the bond for his owner, when that should be done only by the owner itself.

[`Lock.sol#L98`](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Lock.sol#L98)

```solidity
file: Lock.sol
 function release( 
     ...
        (uint amount, uint lockAmount, address asset, address _owner) = bondNFT.release(_id, msg.sender);
     ...
 }
```

[`BondNFT.sol#L137`](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L137)
```solidity
file: BondNFT.sol
function release(
    ...
    (uint256 _claimAmount,) = claim(_id, bond.owner);
    ...
}
```
    
**Recommendation:** Consider checking if the `msg.sender` is the `owner` of the asset, on `Lock.release` before calling `bondNFT.release`
