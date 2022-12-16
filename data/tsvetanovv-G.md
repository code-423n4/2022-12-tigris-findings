## [G-01] UNCHECKING ARITHMETICS OPERATIONS THAT CAN’T UNDERFLOW/OVERFLOW

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block.
[Link](https://docs.soliditylang.org/en/v0.8.10/control-structures.html#checked-or-unchecked-arithmetic)

[Contract](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol)

```
    function userTrades(address _user) external view returns (uint[] memory) {
        uint[] memory _ids = new uint[](balanceOf(_user));
        for (uint i=0; i<_ids.length; i++) {
            _ids[i] = tokenOfOwnerByIndex(_user, i);
        }
        return _ids;
    }
```

```
    function openPositionsSelection(uint _from, uint _to) external view returns (uint[] memory) {
        uint[] memory _ids = new uint[](_to-_from);
        for (uint i=0; i<_ids.length; i++) {
            _ids[i] = _openPositions[i+_from];
        }
        return _ids;
    }
```