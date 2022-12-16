# [01] Use `.call` instead of `.transfer` to send ether

`.transfer` will relay 2300 gas and `.call` will relay all the gas. If the receive/fallback function from the recipient proxy contract has complex logic, using `.transfer` will fail, causing integration issues.

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L588

## Recommendation

Replace `.transfer` with `.call`. Note that the result of `.call` need to be checked. 

# [02] Unbounded loop

New assets are pushed into the state variable `assets` array, at the function `BondNFT.addAsset()`.

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L352

`Lock.claimGovFees()` will iterate all the assets.

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L110-L120

Currently, `assets` can grow indefinitely. E.g. there's no maximum limit and there's no functionality to remove assets.

If the array grows too large, calling `Lack.claimGovFeeds()` might run out of gas and revert. Claiming and distributing rewards will result in a DOS condition.

## Recommendation

Add a functionality to delete assets or add a maximum size limit for assets.

# [03] Use the safe variant and `ERC721.mint`

`.mint` won't check if the recipient is able to receive the NFT. If an incorrect  address is passed, it will result in a silent failure and loss of asset.

OpenZeppelin [recommendation](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC721/ERC721.sol#L277) is to use the safe variant of `_mint`.

## Recommendation

Replace `_mint()` with `_safeMint()`.

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L313

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L56

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L70

# [04] Usage of deprecated chainlink API

`latestAnswer()` from chainlink is deprecated and can return stale data.

## Recommendation

Use `latestRoundData()` instead of `latestAnswer()`. Also, adding checks for [additional fields](https://docs.chain.link/data-feeds/price-feeds/api-reference/#latestrounddata) returned from `latestRoundData()` is recommended. E.g.

```
(uint80 roundID, int256 price,,uint256 timestamp, uint80 answeredInRound) = chainlink.latestRoundData();
require(timestamp != 0, "round not complete");
require(answeredInRound >= roundID, "stale data");
require(price != 0, "chainlink error");
```

# [05] Consider using OpenZeppelin's `SafeERC20` for ERC20 transfers to ensure compatibility with non-standard tokens

The contracts are using IERC20 from OpenZeppelin. Consider using the [SafeERC20](https://docs.openzeppelin.com/contracts/2.x/api/token/erc20#SafeERC20). This will ensure compatibility with tokens that don't revert on failure (safemoon and it's forks), and tokens that don't return a boolean on success (USDT, BNB, OMG).

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L676

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L104

# [06] Lack of checks-effects-interactions

It's recommended to execute external calls after state changes, to prevent reetrancy bugs.

Consider moving the external calls after the state changes on the following instances:

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L72-L73

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L216-L226

# [07] Lack of zero address checks for `Trading.sol` constructor for the variables `_position`, `_gov` and `_pairsContract`

If these variable get configured with address zero, failure to immediately reset the value can result in unexpected behavior for the project.

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L143-L152

# [08] Add an event for critical parameter changes

Adding events for critical parameter changes will facilitate offchain monitoring and indexing.

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L898-L9051

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L912-L920

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L926-L933

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L939-L941

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L952-L969

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L975-L979

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/MetaContext.sol#L9-L11

# [09] Missing unit tests

It is crucial to write tests with possibly 100% coverage for smart contracts.

The following functions are not covered:

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L206-L216

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L311-L313

## Recommendation

It is recommended to write tests for all possible code flows.

# [10] Pragma float

All the contracts in scope are in scope are floating the pragma version.

## Recommendation

Locking the pragma helps to ensure that contracts do not accidentally get deployed using an outdated compiler version.

Note that pragma statements can be allowed to float when a contract is intended for consumption by other developers, as in the case with contracts in a library or a package.

# [11] Contract layout and order of functions

The solidity style guide [recommends](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-layout) declaring state variables before all functions. Consider moving the state variables from the GovNFT instance highlighted bellow to the top of the contract.

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L263-L269

Another [recommendation](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions) is to declare internal functions bellow external functions. 

The instances bellow highlightes internal above external. If possible, consider adding internal functions bellow external functions for the contract layout.

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L884

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L898-L901

Furthermore, it's also recommended to declare pure and view functions at the end of a  a grouping.

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L847

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L857

# [12] Use time units directly

The value `1 days` can be used directly the the constant on [L10](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L10) of `BondNFT.sol` is not needed.

# [13] Avoid shadowing in `GovNFT.sol`

Consider renaming the variable `owner` on [L77](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L77). Currently it's being shadowed by the library `Ownable.owner`.

# [14] Declare interfaces on separate files

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L14-L77

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L9-L13

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L15-L25

# [15] Constants should be upper case

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L96

# [16] Use `private constant` consistently

Replace `constant private` with `private constant`.

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L95

# [17] Add a limit for the maximum number of characters per line

The solidity [documentation](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-length) recommends a maximum of 120 characters.

Consider adding a limit of 120 characters or less to prevent large lines.

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L178

# [18] Declaring a `return named variable` and returning a manual value for the same function

Consider refactoring the function `MetaContext._msgSender` to use `sender` on [L25](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/MetaContext.sol#L25). E.g. `sender = super._msgSender()`. This will make the function more consistent with the usage of the `return named variable` declared in the function header.

# [19] Lack of spacing in comment

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L863

# [20] Critical changes should use two-step procedure

Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.

Consider adding a two-steps pattern on critical changes to avoid mistakenly transferring ownership of roles or critial functionalities to the wrong address.

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L366-L370

# [21] Missing NATSPEC

Consider adding NATSPEC on all public/external functions to improve documentation.

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L190

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L168

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L183

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L349

# [22] Avoid using the optimizer if possible, due to it's potential security bugs which can affect the contracts in scope

Optimizations are being actively developed and are not considered safe. Check the following [audit](https://github.com/trailofbits/publications/blob/master/reviews/SeaportProtocol.pdf) recommending agaist deployments with the optimizer enabled (item 3 in the audit document).

If possible, consider measuring and documenting the tradeoffs when enabling the optimizer.

# [23] Interchangeable usage of uint and uint256

Consider using only one approach throughout the codebase, e.g. only uint or only uint256.

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L223-L224

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L316-L317

# [24] Move require/validation statements to the top of the function when validating input parameters

Consider moving the validation on [L966](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L966) above the conditional on [L955](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L955) for `Trading.setFees()`.

# [25] Remove console.log import in `Lock.sol`

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L4

# [26] Draft openzeppelin dependencies

OpenZeppelin contracts may be considered draft contracts if they have not received adequate security auditing or are liable to change with future development.

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableToken.sol#L4

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableToken.sol#L7

## Recommendation

Consider waiting until the contract is finalized. Otherwise, make sure that development team are aware of the risks of using a draft OpenZeppelin contract and accept the risk-benefit trade-off.

# [27] Named imports can be used

It's possible to name the imports to improve code readability. E.g. `import "@openzeppelin/contracts/token/ERC20/IERC20.sol";` can be rewritten as `import {IERC20} from “import “@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol”;`

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L6

# [28] Imports can be grouped together

Consider importing OZ first, then all interfaces, then all utils.

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L4-L12

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L4-L8

# [29] Constant redefined elsewhere

Consider defining in only one contract so that values cannot become out of sync when only one location is updated. 

A cheap way to store constants in a single location is to create an internal constant in a library. If the variable is a local cache of another contract’s value, consider making the cache variable internal or private, which will require external users to query the contract with the source of truth, so that callers don’t get out of sync.

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L95

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L11

# [30] Convert repeated validation statements into a function modifier to improve code reusability

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L107

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L330

# [31] Large multiples of ten should use scientific notation.

Using scientific notation for large multiples of ten will improve code readability.

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L26
