1 - Lock pragma version
==

Floating pragmas should not be used. Locking the pragma helps to ensure that contracts don't accidentally get deployed using outdated compiler version that might introduce bugs.

change ```pragma solidity ^0.8.0;``` to ```pragma solidity 0.8.0```;

2 - Validates tradingExtension is non-zero
==

In the ```Trading.sol::setTradingExtension()``` add a non-zero address validation.

Links:

- [contracts/Trading.sol#L975](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L975)

3 - Use the official library released
==

Use the official library of [ERC20Permit.sol](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/extensions/ERC20Permit.sol)

Links:

- [contracts/StableToken.sol#L4](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/StableToken.sol#L4)
