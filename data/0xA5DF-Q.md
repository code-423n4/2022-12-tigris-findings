
## Price data signing is untyped
It's recommended to use [EIP-712](https://eips.ethereum.org/EIPS/eip-712) for data signing.
Using unsigned data can cause signature created for one case to be used for another unintended case.
There's no such scenario within the project, but the chance of having a such a signature from outside the project isn't zero, therefore it's important to use typed signature.


## No verification that `orderType` isn't greater than 2
At `initiateLimitOrder()` [there's verification](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L327) that `orderType` isn't zero, but not that it's greater than 2.
That means an order can be created with `orderType=5`.

## Closing referral fees go to the last ref rather than opening order ref
It seems fair that referral fees should go to the referrer from the opening order.
However, if a second order is created in the meanwhile, the closing referral fees would go to the 2nd order ref rather than original ref.

### Mitigation
Store ref address with trade details, rather than per trader.

## No incentive to use `BondNFT.release()` instead of `claim()`
As for the user, there's no difference between the `claim()` and `release()` function once the bond expires.
This means users might simply call `claim()` instead of `release()`, leaving the bond open and affecting other users' shares.
As a mitigation it might be better to force use `release()` when the bond expires.


## Protocol might reach insolvency from traders profit
Traders profiting too much might cause the protocol to mint more StableToken than collateral that's locked in StableVault.
In such case the last LP to withdraw his funds will not be able to redeem StableToken for StableVault.
It might be better to spread the loss among all LPs to avoid instability.