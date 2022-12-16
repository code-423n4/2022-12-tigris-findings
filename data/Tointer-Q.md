1. Stable Vault uses IERC20Mintable to use stable token, but StableToken.sol does not implement it explicitly
Here is the interface: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/StableVault.sol#L9-L13
Here is the stable token https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/StableToken.sol#L7

2. Stable Vault cannot accept tokens with more then 18 decimals because of this lines:
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/StableVault.sol#L49
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/StableVault.sol#L67
Consider using different conversion formula. For example: `amount * 1e18 / token.decimals()` for deposit and `amount * token.decimals() / 1e18 ` for withdraw
