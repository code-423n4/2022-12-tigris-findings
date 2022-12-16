
## Price data signing is untyped
It's recommended to use [EIP-712](https://eips.ethereum.org/EIPS/eip-712) for data signing.
Using unsigned data can cause signature created for one case to be used for another unintended case.
There's no such scenario within the project, but the chance of having a such a signature from outside the project isn't zero, therefore it's important to use typed signature.


## No verification that `orderType` isn't greater than 2
At `initiateLimitOrder()` [there's verification](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L327) that `orderType` isn't zero, but not that it's greater than 2.
That means an order can be created with `orderType=5`.
