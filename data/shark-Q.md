## 1. Big numbers are hard to read

Consider using underscores or scientific notation on large numbers to increase readability.

For example:

File: `TradingExtension.sol` [Line 26](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L26)

The instance above could be refactored to use scientific notation:

```solidity
    uint public maxGasPrice = 1e+12; // 1000 gwei
```

Another option is separating with underscores:

```solidity
    uint public maxGasPrice = 1_000_000_000_000; // 1000 gwei
```

## 2. Named return is unnecessarily explicitly returning

Named returns can return a named variable instead of using the `return` keyword to do so.

For example:

File: `GovNFTBridged.sol` [Line 283 - Line 285](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFTBridged.sol#L283-L285)

```solidity
    function _msgSender() internal view override(Context, MetaContext) returns (address sender) {
        return MetaContext._msgSender();
    }
```

The instance above could be refactored as:

```solidity
    function _msgSender() internal view override(Context, MetaContext) returns (address sender) {
        sender = MetaContext._msgSender();
    }
```

## 3. Use delete to clear variables instead of zero assignment

A better way to indicate that you are clearing a variable is to use the [`delete`](https://docs.soliditylang.org/en/v0.8.17/types.html#delete) keyword.

For instance:

```solidity
            _fees.botFees = 0;
```

File: `Trading.sol` [Line 730](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L730)

The above instance could be refactored as such:

```solidity
            delete _fees.botFees;
```

## 4. Unspecific Pragma Version

Locking the pragma helps ensure that contracts don't get deployed with unintended versions, for example, the latest compiler which could have higher risks of undiscovered bugs.

For example, `pragma solidity ^0.8.0;` is very unspecific.

## 5. Use `uint256` instead of `uint`

To be explicit, consider replacing any instances of `uint` with `uint256` where possible.

For example:

```solidity
        for (uint i=0; i<_amount; i++) {
```

File: `NFTSale.sol` [Line 45](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/NFTSale.sol#L45)

In the instance above, `i` is of type `uint`. As such, the above could be refactored as:

```solidity
        for (uint256 i=0; i<_amount; i++) {
```

## 6. Limit line length

To comply with the [Solidity Style Guide](https://docs.soliditylang.org/en/develop/style-guide.html#maximum-line-length), lines should be limited to **120** characters max.

The instances listed below are over 120 characters long:

File: `Trading.sol` [Line 178](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L178)
File: `Trading.sol` [Line 496](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L496)
File: `Trading.sol` [Line 543](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L543)
File: `Trading.sol` [Line 643](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L643)

File: `Position.sol` [Line 102](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L102)
File: `Position.sol` [Line 104](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L104)
File: `Position.sol` [Line 109](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L109)
File: `Position.sol` [Line 107](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L107)

## 7. Typos

File: `PairsContract.sol` ([Line 44](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L44), [Line 151](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L151), [Line 171](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L171))

```
/// @audit "maximimum" -> "maximum"
44:     * @param _maxLeverage maximimum allowed leverage
...
/// @audit "interesr" -> "interest"
151:     * @param _onOpen true if adding to open interesr
...
/// @audit See typo above (line 151)
171:     * @param _onOpen true if adding to open interesr
```

## 8. Use of an OpenZeppelin draft contract

The following instance imports an OpenZeppelin draft contract ([`ERC20Permit`](https://docs.openzeppelin.com/contracts/3.x/api/drafts#ERC20Permit)) that isn't ready for mainnet use. Due to the nature of drafts, they may change and are not guaranteed stability. Using a draft contract in production can cause the permit function to fail unexpectedly and impact the protocol and its users.

File: StableToken.sol [Line 4](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableToken.sol#L4)

```solidity
import "@openzeppelin/contracts/token/ERC20/extensions/draft-ERC20Permit.sol";
```
