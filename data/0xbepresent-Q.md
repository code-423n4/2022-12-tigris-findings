1 - Lock pragma version
==

Floating pragmas should not be used. Locking the pragma helps to ensure that contracts don't accidentally get deployed using outdated compiler version that might introduce bugs.

change ```pragma solidity ^0.8.0;``` to ```pragma solidity 0.8.0```;

2 - Validates tradingExtension is non-zero
==

In the ```Trading.sol::setTradingExtension()``` add a non-zero address validation.

Links:

- [contracts/Trading.sol#L975](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L975)