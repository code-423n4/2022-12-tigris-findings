# LOWS - NC - QA

## Low


### Lack of validation for `_amount`, `_period` & `_owner` on function `createLock`
On [BondNFT.sol#L57-L61](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L57-L61) consider adding a threashold to `_amount`,`_period` and add a check for `address(0)` to `_owner`

### `_safeMint` is recommend instead of `_mint`
Currently, the contract uses the _mint() function to mint new tokens which is discouraged by the OpenZeppelin documentation because there is no check as to whether that token was received. 
It's recommended that `_safeMint()` is used when handling ERC721 tokens as this guarantees the end user has received them.

Lines affected;
- [BondNFT.sol#L83](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L83)
- [GovNFT.sol#L70](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L70)
- [GovNFT.sol#L106](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L106)
- [GovNFT.sol#L111](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L111)

### Possible reentrancy (if minted is setup as expected with `_safeMint`)
On function `initiateLimitOrder` [Trading.sol#L314-L349](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L314-L349) you are not following the Checks [Effects Interactions pattern](https://fravoll.github.io/solidity-patterns/checks_effects_interactions.html) after the mint you update the state on [line 347](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L347), you can add a reentrancy guard or just simple move the mint after updating the state.

### Missing Zero-address Validation

Lack of zero-address validation on address parameters may lead to transaction reverts, waste gas, require resubmission of transactions and may even force contract redeployments in certain cases within the protocol.

Consider adding explicit zero-address validation on input parameters of address type.

Files:
- [PairsContract.sol:53](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L53)  missing address(0) check for _chainlinkFeed
- [Trading.sol:97](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L97)8  missing address(0) check for _ext
- [Referrals.sol:53](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol#L53) missing address(0) check for _protocol
- [StableVault.sol:36](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L36) missing address(0) check for _stable
- [PairsContract.sol#L130](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L130) missing address(0) check for _protocol


### Missing threashold leaving to user/owner put any desired value even if not valid
- [Trading.sol#L904](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L904) `_blockDelay` can be any value  `type(uint256).max`
- [Trading.sol#L932](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L932) `_maxWinPercent` can be any value  `type(uint256).max`
- [Trading.sol#L940](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L940) `_range` can be any value  `type(uint256).max`
- [PairsContract.sol#L48](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L48) `_maxLeverage`, `_feeMultiplier`, `_baseFundingRate` can be any value
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L126 _maxBaseFundingRate
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L139 _maxOi
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L107 _feeMultiplier
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L73 `_minLeverage` can be greater than `_maxLeverage`! and `_minLeverage`, `_maxLeverage` can be any value
- 


## QA

### Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

```
Affected lines;

contracts/Position.sol:2:pragma solidity ^0.8.0;
contracts/TradingExtension.sol:2:pragma solidity ^0.8.0;
contracts/Forwarder.sol:2:pragma solidity ^0.8.0;
contracts/Lock.sol:2:pragma solidity ^0.8.0;
contracts/StableToken.sol:2:pragma solidity ^0.8.0;
contracts/GovNFT.sol:2:pragma solidity ^0.8.0;
contracts/PairsContract.sol:2:pragma solidity ^0.8.0;
contracts/BondNFT.sol:2:pragma solidity ^0.8.0;
contracts/Trading.sol:2:pragma solidity ^0.8.0;
contracts/Referrals.sol:2:pragma solidity ^0.8.0;
contracts/StableVault.sol:2:pragma solidity ^0.8.0;
```


### Missing event emission on critical function

On `Lock.sol`

- [Lock.sol#L127](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L127) `editAsset`
- [Lock.sol#L138](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L138) `sendNFTs`



### Be explicit declaring types
Be explicit declaring types, instead of `uint` use `uint256`, `uint` is an alias of `uint256`, but its preffer just to use `uint256`. Example (not only this line but the whole file);
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L10

## Non Critical
Use named imports;
import 


### Avoid magic numbers, its better use named constant
Instead of `1e9` try to replace it with a name constant to understand what is `1e9`;
[PairsContract.sol#L161](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L161)
[](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L181)

### Follow solidity best practice ordering contract elements
The best-practice layout for a contract should follow the following order: state variables, events, modifiers, constructor and functions. Function ordering helps readers identify which functions they can call and find constructor and fallback functions easier. Functions should be grouped according to their visibility and ordered as: constructor, receive function (if exists), fallback function (if exists), external, public, internal, private. Some constructs deviate from this recommended best-practice.

Inside each contract, library or interface, use the following order:

```
Type declarations
State variables
Events
Modifiers
Functions
```

Example 1, on [Position.sol#L69-L83](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L69-L83) your layout is;
```
Functions
State variables
Functions
State variables
Functions
Modifiers
```

Example 2, on [GovNFT.sol#L257-L281](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L257-L281) your layout is;
```
State variables
Events
Functions
State variables
Functions
```

Example 3, on [Trading.sol#L980-L1052](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L980-L1052), you have all events at the end of the contract layout.

Example 4, on [PairsContract.sol#L194-L199](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L194-L199), you have events at the end of the contract layout.

### Separate interfaces in different files
Its a best practice to have one file per interface, you shoul move this interfaces into a separate file;
[StableVault.sol#L8-L25](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L8-L25)


### Use `1 DAYS` instead of calculating the `24 * 60 * 60`
Use `1 DAYS` instead of calculating the `24 * 60 * 60`
Example [BondNFT.sol#L10](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L10)

More on [units-and-global-variables.html#time-units](https://docs.soliditylang.org/en/v0.8.17/units-and-global-variables.html#time-units)

### Use `ether-units` instead of raw number
Use `1000 gwei` insteado of `1000000000000`
Example 1 [TradingExtension.sol#L26](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L26)
Example 2 [GovNFT.sol#L17](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L17)

More on [ether-units](https://docs.soliditylang.org/en/v0.8.17/units-and-global-variables.html#ether-units)

### `public` or `external` functions shouldnt start with underscore
On [GovNFT.sol#L64](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L64) rename `_bridgeMint` to `bridgeMint`

### Shadow variables `owner`
On `_burn` [GovNFT.sol#L76](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L76= variable of owner is shadowing owner() from ownable, consider rename internal variable `owner` to `_owner` to avoid collisions.

### Lack of event emission on critical functions

Several functions update critical parameters that are missing event emission. These should be performed to ensure tracking of changes of such critical parameters.

Consider adding events to functions that change critical parameters.

- [GovNFT.sol:308](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L308)
- [GovNFT.sol:312](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L312)
- [PairsContract.sol:36](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L36)
- [PairsContract.sol:84](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L84)
- [PairsContract.sol:96](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L96)
- [PairsContract.sol:107](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L107)
- [PairsContract.sol:118](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L118)
- [PairsContract.sol:125](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L125)
- [PairsContract.sol:130](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L130)
- [PairsContract.sol:142](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L142)
- [BondNFT.sol:354](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L354)
- [BondNFT.sol:357](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L357)
- [BondNFT.sol:363](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L363)
- [BondNFT.sol:369](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L369)
- [Referrals.sol:54](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol#L54)
- [Position.sol:310](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L310)
- [TradingExtension.sol:228](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L228)
- [TradingExtension.sol:232](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L232)
- [TradingExtension.sol:241](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L241)
- [TradingExtension.sol:275](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L275)
- [Lock.sol#L131](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L131)
- [Lock.sol#L141](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L141)

Also while initing refererals on [Referrals.sol#L71](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol#L71) function `initRefs`, [Referrals.sol#L71](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol#L71) should emit ReferralCreated and [Referrals.sol#L74](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol#L74) should emit Referred
