# QA Report

## Use `SafeERC20` for ERC20 transfers
`SafeERC20` is a wrapper around ERC20 operations that throw on failure (when the token contract returns false). This prevents calls executing if a token transfer is unsuccessful. Simply add `using SafeERC20 for ERC20`.
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L651
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L671
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L676
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L46
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L68
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L39
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L52
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L72
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L90
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L104

## Use `_safeMint` instead of `_mint`
Concerning ERC721 tokens, `_mint` can result in tokens being lost if they are sent to a contract that does not implement `IERC721Receiver.onERC721Received`. OpenZeppelin encourages the use of `_safeMint` over `_mint` for this reason.
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L83
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L306-L314
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L50-L57
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L70
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L106
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L111

## `payable.transfer` can lead to loss of funds
Issues with `transfer`: https://consensys.net/diligence/blog/2019/09/stop-using-soliditys-transfer-now/. Instead, use either `call.value(amount)` or OpenZeppelin's `Address.sendValue` (https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/Address.sol#L60).

## No check on invalid take profit value
Currently a user can set tp < price for a long, or a tp > price for a short. This would lead to the position being closed immediately, and so a rational user would never willingly do this. However they may erroneously do so consider adding a new function similar to `Trading:_checkSl()` in following functions:
- `Trading:updateTpSl()`
- `Trading:initiateMarketOrder()`
- `Trading:initiateLimitOrder()`

## Reward distribution is executed manually, presenting centralization/trust risks

The `distribute()` functions for `GovNFT` and `BondNFT` are the method by which rewards are distributed to the NFT holders. In each case, the function must be called manually and the amount rewarded is left as a user input. In other words, user reward amounts and frequency are decided entirely at the discretion of the team. This requires a level of trust that may not be properly communicated with the end user.

Either make sure users are aware of the inherent trust required to hold these NFTs, or:
- Alter the protocol structure such that reward amounts are calculated and stored on chain
- Instead of transferring rewards from the caller of `distribute()`, transfer directly from the relevant contract instead (e.g. `StableVault`)

Instances:
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L211-L228
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L287-L294

## Use solidity built in time constants
Instead of writing `DAY = 60 * 60 * 24` we can write `DAY = 1 days`, which has the same effect but reduces margin for error and improves readability.
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L10

## Remove unused imports
Only using necessary imports improves a codebases readability.
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L4
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L5
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L6

## For non-interface contracts, use fixed compiler version, not floating one
A floating pragma risks a different compiler version being used in production vs testing, which poses security risks.
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L2
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L2
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L2
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L2
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L2
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol#L2
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableToken.sol#L2
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L2
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L2
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L2

## Function ordering
It is best practice to use the following order within smart contracts: (state variables > events > constructor > public functions > internal functions > getters/setters > fallback/receive). This makes the code more readable.

## Include NatSpec comments in interface contracts
NatSpec comments on functions give developers and users alike a better idea of how your protocol works. Detailed NatSpec comments are usually placed within a contracts interface.

## Remove depracated code
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol#L57-L76

## Use more detailed error messages
A number of `revert` statements have single figure error strings, which are not self-explanatory. Proper error messages are very important in the debug features of DApps like Tenderly.
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L101
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L104
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L106
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L108
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L112
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L114
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L116
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L123
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L238
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L273
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L327
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L364
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L392
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L428
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L464
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L495
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L496
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L498
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L500
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L502
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L505
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L540
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L836
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L838
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L848
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L863

## Lines too long
[Solidity's style guide](https://docs.soliditylang.org/en/v0.8.10/style-guide.html#maximum-line-length) states lines should be kept within 79 characters. Also, if lines exceed 164 characters then a horizontal scroll bar will be required when viewing the file on Github. Extensions such as prettier are a simple solution.