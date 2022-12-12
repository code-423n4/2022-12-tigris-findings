# QA report

## Low Risk
| L-N    |Issue|Instances|
|:------:|:----|:-------:|
| [L&#x2011;01] | Missing zero address check in the constructor in `Trading.sol` and `TradingExtension.sol` | 3 |
| [L&#x2011;02] | Unsafe ERC20 operations | 19 |

Total: 22 instances over 2 issues

## Non-critical

| N-N    |Issue|Instances|
|:------:|:----|:-------:|
| [N&#x2011;01] | License comment is misspelled, consider changing it to: // SPDX-License-Identifier: UNLICENSED | 5 |
| [N&#x2011;02] | Use latest Solidity version with a stable pragma statement | 22 |
| [N&#x2011;03] | Constructor and modifiers are not declared in the proper place | 1 |
| [N&#x2011;04] | Code is not formatted properly | 1 |
| [N&#x2011;05] | Constants should be capitalized | 1 |
| [N&#x2011;06] | Constants should be defined rather than using magic numbers | 7 |
| [N&#x2011;07] | Consider naming certain variables with something more appropriate than their type | 2 |
| [N&#x2011;08] | Avoid nested If blocks | 1 |
| [N&#x2011;09] | Typo in comments | 2 |
| [N&#x2011;10] | Wrong parameter in the NatSpec | 1 |
| [N&#x2011;11]  | Use solidity's native units for dealing with time (day, week, month) | 1 |
| [N&#x2011;12] | Interface `ITradingExtension.sol` in `Trading.sol` should be considered being a separate contract in the `interfaces` folder | 1 |
| [N&#x2011;13] | Consider moving `IERC20.sol` import above the other imports in `Trading.sol` | 1 |
| [N&#x2011;14] | Events, structs and custom errors declaration | 6 |
| [N&#x2011;15] | Event is missing indexed fields | 6 |
| [N&#x2011;16] | Unclear comment | 1 |
| [N&#x2011;17] | Its a convention to use uint256 instead of uint alone | 12 |
| [N&#x2011;18] | Visibility modifier should occur before `constant` | 1 |
| [N&#x2011;19] | Use custom errors | 1 |
| [N&#x2011;20] | Consider creating a variable that hold the value of the decimal | 1 |
| [N&#x2011;21] | `maxGasPrice` can be 1e12 for better readability | 1 |
| [N&#x2011;22] |  Lack of event emitting | ~ |

Total: 76 instances over 22 issues

## Low Risk

# \[L-01\] Missing zero address check in the constructor in `Trading.sol` and `TradingExtension.sol`

```solidity
constructor(
        address _position,
        address _gov,
        address _pairsContract
    )
    {
        position = IPosition(_position);
        gov = IGovNFT(_gov);
        pairsContract = IPairsContract(_pairsContract);
```

```solidity
constructor(
        address _trading,
        address _pairsContract,
        address _ref,
        address _position
    )
    {
        trading = _trading;
        pairsContract = IPairsContract(_pairsContract);
        referrals = IReferrals(_ref);
        position = IPosition(_position);
```

```solidity
function addAsset(address `_asset`) external onlyOwner {
        require(assets.length == 0 || assets[assetsIndex[_asset]] != _asset, "Already added");
        assetsIndex[_asset] = assets.length;
        assets.push(`_asset`);
///missing address zero check
        _allowedAsset[_asset] = true;
    } 
```

Lines of code: 

- https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/Trading.sol#L143

- https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/TradingExtension.sol#L28

- https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/GovNFT.sol#L303

Consider adding zero address checks.



# \[L-02\] Unsafe ERC20 operations

ERC20 operations can be unsafe due to different implementations and vulnerabilities in the standard.

It is therefore recommended to always either use OpenZeppelin's SafeERC20 library or at least to wrap each operation in a require statement.

To circumvent ERC20's approve functions race-condition vulnerability use OpenZeppelin's SafeERC20 library's safe{Increase|Decrease}Allowance functions.
Also consider using safeTransfer()/safeTransferFrom() instead of transfer()/transferFrom().

The problem is present in: `BondNFT.sol`, `GovNFT.sol`, `Lock.sol`, `StableVault.sol`, `Trading.sol`

Example:

File: `Trading.sol`

```solidity
IERC20(_marginAsset).transferFrom(_trader, address(this), _margin/_marginDecMultiplier);

IERC20(_marginAsset).approve(_stableVault, type(uint).max);

IERC20(_outputToken).transfer(_trade.trader, _toMint);
```

Lines of code:

- https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/Trading.sol#L651

- https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/Trading.sol#L652

- https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/Trading.sol#L671


## Non-critical

# \[NC-01\] License comment is misspelled, consider changing it to: // SPDX-License-Identifier: UNLICENSED

Files where the issue is present:

`Faucet.sol`, `Referrals.sol`, `PairsContract.sol`, `TradingExtension.sol`, `Trading.sol`

Example:

```solidity
//SPDX-License-Identifier: Unlicense
```

# \[NC-02\] Use latest Solidity version with a stable pragma statement

Using a floating pragma ^0.8.0 statement is discouraged as code can compile to different bytecodes with different compiler versions. Use a stable pragma statement to get a deterministic bytecode. Also use latest Solidity version to get all compiler features, bugfixes and optimizations.

The issue is present in all of the contracts.

# \[NC-03\] Constructor and modifiers are not declared in the proper place

File: `Position.sol`

Consider placing the constructor and modifiers before the functions not in the middle or at the end of the contract.

Check the solidity documentation for more information on how to properly structure a smart contract.

# \[NC-04\] Code is not formatted properly

Use a code formatter to keep code clean & tidy, I’d suggest adding the forge fmt command to your pre-commit hook

# \[NC-05\] Constants should be capitalized

File: `Trading.sol`

```solidity
uint private constant liqPercent = 9e9; // 90%
```

Line of code:

- https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/Trading.sol#L96


# \[NC-06\] Constants should be defined rather than using magic numbers

File: `Trading.sol`

```solidity
uint256 _isLong = _tradeInfo.direction ? 1 : 2;
```
```solidity
limitDelay[_id] = block.timestamp + 4;
```

Lines of code:

- https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/Trading.sol#L181

- https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/Trading.sol#L347

File: `TradingLibrary.sol`

```solidity
_liqPrice = _tradePrice - ((_tradePrice*1e18/_leverage) * uint(int(_margin)+_accInterest) / _margin) * _liqPercent / 1e10;
```
```solidity
_priceData.price < assetChainlinkPrice+assetChainlinkPrice*2/100 
```
Lines of code:

- https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/utils/TradingLibrary.sol#L64

- https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/utils/TradingLibrary.sol#L117

File: `BondNFT.sol`

```solidity
uint shares = _amount * _period / 365;
```
```solidity
require(bond.expireEpoch + 7 < epoch[bond.asset], "Bond owner priority");
```
```solidity
require(block.timestamp > bond.mintTime + 300, "Recent update");
```

Lines of code:

- https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/BondNFT.sol#L65

- https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/BondNFT.sol#L145

- https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/BondNFT.sol#L332

# \[NC-07\] Consider naming certain variables with something more appropriate than their type

File: `TradingExtension.sol`

```solidity
function setNode(address _node, bool `_bool`) external onlyOwner {
```
```solidity
function setAllowedMargin(
        address _tigAsset,
        bool _bool
```

Lines of code:

- https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/TradingExtension.sol#L231

- https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/TradingExtension.sol#L251

# \[NC-08\] Avoid nested If blocks

File: `TradingExtension.sol`

```solidity
if (_referral != bytes32(0)) {
            if (referrals.getReferral(_referral) != address(0)) {
                if (referrals.getReferred(_trader) == bytes32(0)) {
                    referrals.setReferred(_trader, _referral);
```

Line of code:

- https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/TradingExtension.sol#L195

# \[NC-09\] Typo in comments

File: `PairsContract.sol`

```solidity
 * @param _maxLeverage maximimum allowed leverage
``` 
- maximum *

```solidity
* @param _onOpen true if adding to open interesr
```
- interest *

Lines of code:

- https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/PairsContract.sol#L44

- https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/PairsContract.sol#L151


# \[NC-10\] Wrong parameter in the NatSpec

File: `PairsContract.sol`

```solidity
* @param _maxLeverage maximimum allowed leverage
* @param _maxLeverage minimum allowed leverage  
```

The second param should be `_minLeverage`

Line of code:

- https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/PairsContract.sol#L45


# \[NC-11\] Use native time units such as seconds, minutes, hours, days, weeks and years

File: `BondNFT.sol`

```solidity
uint constant private DAY = 24 * 60 * 60;
```
Consider changing it to: DAY = 1 day;

Line of code: 

- https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/BondNFT.sol#L10

# \[NC-12\] Interface `ITradingExtension.sol` in `Trading.sol` should be considered being a separate contract in the `interfaces` folder




# \[NC-13\] Consider moving `IERC20.sol` import above the other imports in `Trading.sol`

File: `Trading.sol`

```solidity
import "./utils/MetaContext.sol";
import "./interfaces/ITrading.sol";
import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
import "./interfaces/IPairsContract.sol";
import "./interfaces/IReferrals.sol";
import "./interfaces/IPosition.sol";
import "./interfaces/IGovNFT.sol";
import "./interfaces/IStableVault.sol";
import "./utils/TradingLibrary.sol";
```

# \[NC-14\] Events, structs and custom errors declaration

It is a best practice to be declared in the interface not in the implementation contract.

File: `Trading.sol`, `Referrals.sol`, `PairsContract.sol`, `GovNFTBridged.sol`, `GovNFT.sol`, `BondNFT.sol`

# \[NC-15\] Event is missing indexed fields

Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

All events in the following files: `Trading.sol`, `Referrals.sol`, `PairsContract.sol`, `GovNFTBridged.sol`, `GovNFT.sol`, `BondNFT.sol`

# \[NC-16\] Unclear comment

File: `Trading.sol`

```solidity
error LimitNotSet(); //7
```

Line of code:

- https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/Trading.sol#L81


# \[NC-17\] Its a convention to use uint256 instead of uint alone

This problem is present all through the contracts.

# \[NC-18\] Visibility modifier should occur before `constant`

File: `Trading.sol`

```solidity
uint constant private DIVISION_CONSTANT = 1e10; // 100%
```

Line of code: 

- https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/Trading.sol#L95

# \[NC-19\] Use custom errors

Almost all revert statements in `Trading.sol` should be substitued by custom erros

Example:

```solidity
if (_trade.orderType != 0) revert("4"); //IsLimit
```

Line of code:

- https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/Trading.sol#L273

# \[NC-20\] Consider creating a variable that holds the value of the decimal 

This line of code is too long and hard to read, consider creating an extra variable for better readability

File: `Trading.sol`

```solidity
if (IERC20(_outputToken).balanceOf(address(this)) != _balBefore + _toMint/(10**(18-ExtendedIERC20(_outputToken).decimals()))) revert BadWithdraw();
```

Line of code:

- https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/Trading.sol#L675

Consider creating a variable as follows: 

```solidity
uint256 outputTokenDecimal = ExtendedIERC20(_outputToken).decimals();
``` 
and then include it in the `if` statement:

```solidity
if(IERC20......_toMint/(10**(18 - outputTokenDecimal) revert BadWithdraw();
```


# \[NC-21\] `maxGasPrice` can be 1e12 for better readability

File: `TradingExtension.sol`

```solidity
uint public maxGasPrice = 1000000000000; // 1000 gwei
```

Line of code:

- https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/TradingExtension.sol#L26

# \[NC-22\] Lack of event emitting 

Contracts do not emit relevant events on setter functions.
Consider emitting events after sensitive changes take place, to facilitate tracking and notify off-chain clients following the contract’s activity

File: `TradingExtension.sol`

Example:

```solidity
function setNode(address _node, bool _bool) external onlyOwner {
        isNode[_node] = _bool;
    }
```
Line of code:

- https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/TradingExtension.sol#L240