## DUPLICATED  `REQUIRE()`/`REVERT()`  CHECKS SHOULD BE REFACTORED TO A MODIFIER OR FUNCTION

Saves deployment costs
Instance:
[PairsContract.sol#L35](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L35)
```
35:    require(_name.length > 0, "!Asset");
```
[]()https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol