### GovNFT.sol

#### Gas Optimization (Redundant Code)

1.  In `_mint(to, tokenId)`, the tokenId is taken to be the global `counter` variable. As such, it is redundant to supply a `tokenId` to this function. It can just use the `counter` varaible as tokenId. Consider the following:

```
    function _mint(address to) internal {
        require(counter <= MAX, "Exceeds supply");
        counter += 1;
        for (uint i=0; i<assetsLength(); i++) {
            userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];
        }
        super._mint(to, counter);
    }
```

The other functions calling `_mint()` now do not have to send the state variable `counter`. and can just call `_mint(to)`. For instance, the `mintMany(_amount)` function will become:

```
    function mintMany(uint _amount) external onlyOwner {
        for (uint i=0; i<_amount; i++) {
            _mint(_msgSender());
        }
    }
```
