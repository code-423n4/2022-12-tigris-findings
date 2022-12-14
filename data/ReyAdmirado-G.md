| | issue |
| ----------- | ----------- |
| 1 | [State variables only set in the constructor should be declared immutable.](#1-state-variables-only-set-in-the-constructor-should-be-declared-immutable) |
| 2 | [Structs can be packed into fewer storage slots](#2-structs-can-be-packed-into-fewer-storage-slots) |
| 3 | [state variables can be packed into fewer storage slots](#3-state-variables-can-be-packed-into-fewer-storage-slots) |
| 4 | [state variables should be cached in stack variables rather than re-reading them from storage](#4-state-variables-should-be-cached-in-stack-variables-rather-than-re-reading-them-from-storage-the-ones-that-wasnt-found) |
| 5 | [Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if` statement](#5-add-unchecked--for-subtractions-where-the-operands-cannot-underflow-because-of-a-previous-require-or-if-statement) |
| 6 | [`<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables](#6-x--y-costs-more-gas-than-x--x--y-for-state-variables) |
| 7 | [not using the named return variables when a function returns, wastes deployment gas](#7-not-using-the-named-return-variables-when-a-function-returns-wastes-deployment-gas) |
| 8 | [can make the variable outside the loop to save gas](#8-can-make-the-variable-outside-the-loop-to-save-gas) |
| 9 | [`++i/i++` should be `unchecked{++i}/unchecked{i++}` when it is not possible for them to overflow, as is the case when used in for-loop and while-loops](#9-ii-should-be-uncheckediuncheckedi-when-it-is-not-possible-for-them-to-overflow-as-is-the-case-when-used-in-for-loop-and-while-loops) |
| 10 | [require()/revert() strings longer than 32 bytes cost extra gas](#10-requirerevert-strings-longer-than-32-bytes-cost-extra-gas) |
| 11 | [splitting require() statements that use `&&` saves gas](#11-splitting-require-statements-that-use--saves-gas) |
| 12 | [require() or revert() statements that check input arguments should be at the top of the function](#12-require-or-revert-statements-that-check-input-arguments-should-be-at-the-top-of-the-function) |
| 13 | [Avoid a SLOAD optimistically](#13-swap-positions-of-two-side-of-or-operator) |
| 14 | [use a more recent version of solidity](#14-use-a-more-recent-version-of-solidity) |
| 15 | [abi.encode() is less efficient than abi.encodepacked()](#15-abiencode-is-less-efficient-than-abiencodepacked) |
| 16 | [usage of uint/int smaller than 32 bytes (256 bits) incurs overhead](#16-usage-of-uintint-smaller-than-32-bytes-256-bits-incurs-overhead) |
| 17 | [public library function should be made private/internal](#17-public-library-function-should-be-made-privateinternal) |
| 18 | [should use arguments instead of state variable](#18-should-use-arguments-instead-of-state-variable) |
| 19 | [before some functions we should check some variables for possible gas save](#19-before-some-functions-we-should-check-some-variables-for-possible-gas-save) |
| 20 | [use existing cached version of state var](#20-use-existing-cached-version-of-state-var) |
| 21 | [dont use a function to get the length value inside to for statement just cache it before the loop](#21-dont-use-a-function-to-get-the-length-value-inside-to-for-statement-just-cache-it-before-the-loop) |

## 1. State variables only set in the constructor should be declared immutable.

Avoids a Gsset (20000 gas) in the constructor, and replaces each Gwarmaccess (100 gas) with a PUSH32 (3 gas).

- [Trading.sol#L121](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L121)
- [Trading.sol#L122](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L122)
- [Trading.sol#L123](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L123)

- [TradingExtension.sol#L13](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L13)
- [TradingExtension.sol#L22](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L22)
- [TradingExtension.sol#L23](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L23)
- [TradingExtension.sol#L24](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L24)


## 2. Structs can be packed into fewer storage slots

Each slot saved can avoid an extra Gsset (20000 gas) for the first setting of the struct. Subsequent reads as well as writes have smaller gas savings

move `isClosed` near `provider` so they can be in one slot and the struct uses one less slot
- [TradingLibrary.sol#L12-L19](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L12-L19)

move `expired` near `owner or asset` so they can be in one slot and the struct uses one less slot
- [BondNFT.sol#L12-L23](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L)


## 3. state variables can be packed into fewer storage slots

If variables occupying the same slot are both written the same function or by the constructor, avoids a separate Gsset (20000 gas). Reads of the variables are also cheaper.

`chainlinkEnabled` and `paused` should be declared near `trading` so they will be in one slot instead of 3 slots
- [TradingExtension.sol#L13-L20](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L13-L20)


## 4. state variables should be cached in stack variables rather than re-reading them from storage (the ones that wasnt found)

The instances below point to the second+ access of a state variable within a function. Caching of a state variable replace each Gwarmaccess (100 gas) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses. 

`longOi[_trade.asset][_trade.tigAsset]` will be used twice at line 48 and 49 if the if statement passes so should cache it inside the if statement of line 47
- [Position.sol#L48-L49](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L48-L49)

`shortOi[_trade.asset][_trade.tigAsset]` will be used twice at line 55 and 56 if the if statement passes so should cache it inside the if statement of line 54 
- [Position.sol#L55-L56](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L55-L56)

`longOi[_asset][_tigAsset]` will be checked both in if statement of line 100 and 106 so its a good idea to cache the value before the if statement of line 100 which will possibly have 2 less storage read if the L100 condition passes and 1 less storage read if the L106 condition passes
- [Position.sol#L100-L111](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L100-L111)

`shortOi[_asset][_tigAsset]` will be checked both in if statement of line 100 and 106 so its a good idea to cache the value before the if statement of line 100 which will possibly have 1 less storage read if the L100 condition passes and 2 less storage read if the L106 condition passes
- [Position.sol#L100-L111](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L100-L111)

caching `lastUpdate[_asset][_tigAsset]` will save lots of gas at a low risk of losing small gas for a stack var (both if statements of L101 and L108 should fail to lose the small amount of gas). cache it before L100
- [Position.sol#L100-L111](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L100-L111)

caching `fundingDeltaPerSec[_asset][_tigAsset]` will save lots of gas at a low risk of losing small gas for a stack var (both if statements of L101 and L108 should fail to lose the small amount of gas). cache it before L100
- [Position.sol#L100-L111](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L100-L111)

cache `bondPaid[_id][bond.asset]` before this line
- [BondNFT.sol#L178](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L178)

highly possible gas save with caching `totalShares[bond.asset]` for a small risk of losing a small amount of gas.
cache before the if statement
- [BondNFT.sol#L179-L180](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L179-L180)

`epoch[bond.asset]` cache before this line
- [BondNFT.sol#L238](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L238)

make a stack var as a place holder for `assets[i]` before the for loop and put the `assets[i]` inside that place holder inside the for loop. saves lotta gas because its a for loop
- [GovNFT.sol#L54](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L54)
- [GovNFT.sol#L68](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L68)
- [GovNFT.sol#L79-81](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L79-81)
- [GovNFT.sol#L96-99](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L96-99)

cache `userPaid[owner][assets[i]]/balanceOf(owner)` whole for a big gas save because its inside the loop
- [GovNFT.sol#L80](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L80)

cache `userPaid[from][assets[i]]/balanceOf(from)` whole for a big gas save because its inside the loop
- [GovNFT.sol#L97](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L97)

cache `accRewardsPerNFT[assets[i]]` whole for a big gas save because its inside the loop
- [GovNFT.sol#L96](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L96)

cache `tokenIndex[_token]` read in line 91 and 92 can be read once
- [StableVault.sol#L91](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L91)

cache `tokens[tokens.length-1]` read in line 91 and 92 can be read once
- [StableVault.sol#L91](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L91)


## 5. Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if` statement

require(a <= b); x = b - a => require(a <= b); unchecked { x = b - a }
if(a <= b); x = b - a => if(a <= b); unchecked { x = b - a }
this will stop the check for overflow and underflow so it will save gas

- [Position.sol#L115](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L115)
- [Position.sol#L117](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L117)

- [Trading.sol#L616](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L616)


## 6. `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables
Using the addition operator instead of plus-equals saves gas

- [Position.sol#L102](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L102)
- [Position.sol#L104](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L104)
- [Position.sol#L107](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L107)
- [Position.sol#L109](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L109)
- [Position.sol#L231](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L231)
- [Position.sol#L232](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L232)

- [PairsContract.sol#L156](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L156)
- [PairsContract.sol#L176](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L176)
- [PairsContract.sol#L160](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L160)
- [PairsContract.sol#L180](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L180)

- [Lock.sol#L73](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L73)
- [Lock.sol#L103](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L103)

- [BondNFT.sol#L150](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L150)
- [BondNFT.sol#L68](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L68)
- [BondNFT.sol#L115](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L115)
- [BondNFT.sol#L117](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L117)
- [BondNFT.sol#L119](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L119)
- [BondNFT.sol#L180](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L180)
- [BondNFT.sol#L183](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L183)
- [BondNFT.sol#L221](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L221)
- [BondNFT.sol#L225](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L225)
- [BondNFT.sol#L333](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L333)
- [BondNFT.sol#L334](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L334)

- [GovNFT.sol#L80](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L80)
- [GovNFT.sol#L81](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L81)
- [GovNFT.sol#L97](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L97)
- [GovNFT.sol#L98](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L98)
- [GovNFT.sol#L52](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L52)
- [GovNFT.sol#L54](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L54)
- [GovNFT.sol#L68](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L68)
- [GovNFT.sol#L79](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L79)
- [GovNFT.sol#L96](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L96)
- [GovNFT.sol#L99](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L99)
- [GovNFT.sol#L278](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L278)
- [GovNFT.sol#L290](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L290)


## 7. not using the named return variables when a function returns, wastes deployment gas

- [Position.sol#L320](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L320)

- [Trading.sol#L771](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L771)

- [GovNFT.sol#L225](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L225)
- [GovNFT.sol#L332](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L332)

- [StableToken.sol#L57](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableToken.sol#L57)


## 8. can make the variable outside the loop to save gas

- [Lock.sol#L114](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L114)
- [Lock.sol#L116](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L116)


## 9. `++i/i++` should be `unchecked{++i}/unchecked{i++}` when it is not possible for them to overflow, as is the case when used in for-loop and while-loops

In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.

- [Position.sol#L296](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L296)
- [Position.sol#L304](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L304)

- [Referrals.sol#L70](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol#L70)
- [Referrals.sol#L73](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol#L73)

- [Lock.sol#L113](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L113)

- [GovNFT.sol#L53](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L53)
- [GovNFT.sol#L67](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L67)
- [GovNFT.sol#L78](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L78)
- [GovNFT.sol#L95](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L95)
- [GovNFT.sol#L105](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L105)
- [GovNFT.sol#L131](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L131)
- [GovNFT.sol#L200](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L200)
- [GovNFT.sol#L246](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L246)
- [GovNFT.sol#L252](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L252)
- [GovNFT.sol#L258](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L258)
- [GovNFT.sol#L325](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L325)


## 10. require()/revert() strings longer than 32 bytes cost extra gas

Each extra memory word of bytes past the original 32 incurs an MSTORE which costs 3 gas

- [GovNFT.sol#L153](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L153)
- [GovNFT.sol#L185](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L185)
- [GovNFT.sol#L209](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L209)
- [GovNFT.sol#L210](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L210)


## 11. splitting require() statements that use `&&` saves gas

this will have a large deployment gas cost but with enough runtime calls the split require version will be 3 gas cheaper

- [PairsContract.sol#L52](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L52)

- [Trading.sol#L887](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L887)

- [TradingLibrary.sol#L116](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L116)


## 12. require() or revert() statements that check input arguments should be at the top of the function

Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting a Gcoldsload (2100 gas*) in a function that may ultimately revert in the unhappy case.

require in L106 in particular is more expensive than the rest so put it as the last require check
- [TradingLibrary.sol#L106](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L106)

swap 110 and 111
- [BondNFT.sol#L110](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L110)

329 should be the last require out of the 3 requires
- [BondNFT.sol#L329](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L329)

swap 66 and 65
- [GovNFT.sol#L65](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L)


## 13. swap positions of two side of `or operator`

There is a chance that the first part will be true so the second part doesn’t need to be checked, so it is better to use the part that we have instead of the part that needs to be called.

- [PairsContract.sol#L157](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L157)
- [PairsContract.sol#L177](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L177)


## 14. use a more recent version of solidity

use one specific solidity version

Use a solidity version of at least 0.8.0 to get overflow protection without SafeMath
Use a solidity version of at least 0.8.2 to get compiler automatic inlining
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings
Use a solidity version of at least 0.8.10 to have `external` calls skip contract existence checks if the external call has a return value
Use a solidity version of at least 0.8.12 to get string.concat() to be used instead of abi.encodePacked(<str>,<str>)
Use a solidity version of at least 0.8.13 to get the ability to use `using for` with a list of free functions


## 15. abi.encode() is less efficient than abi.encodepacked()

- [GovNFT.sol#L141](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L141)


## 16. usage of uint/int smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.
Each operation involving a uint8 costs an extra 22-28 gas (depending on whether the other operand is also a variable of type uint8) as compared to ones involving uint256, due to the compiler having to clear the higher bits of the memory word before operating on the uint8, as well as the associated stack operations of doing so. Use a larger size then downcast where needed
https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html Use a larger size then downcast where needed

- [GovNFT.sol#L22](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L22)
- [GovNFT.sol#L23](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L23)


## 17. public library function should be made private/internal

Changing from public will remove the compiler-introduced checks for msg.value and decrease the contract’s method ID table size

`liqPrice`
- [TradingLibrary.sol#L62](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L62)


## 18. should use arguments instead of state variable

This will save near 97 gas

use `_newMargin` instead of `_trade.margin` in
- [Position.sol#L187](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L187)


## 19. before some functions we should check some variables for possible gas save

before transfer we should check for amount being 0 so the function doesnt run when its not gonna do anything

`_margin/_marginDecMultiplier` shouldnt be 0
- [Trading.sol#L651](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L651)

`amount`
- [Lock.sol#L39](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L39)
- [Lock.sol#L52](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L52)
- [Lock.sol#L72](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L72)
- [Lock.sol#L90](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L90)
- [Lock.sol#L104](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L104)

- [BondNFT.sol#L185](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L185)
- [BondNFT.sol#L202](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L202)
- [BondNFT.sol#L216](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L216)

- [GovNFT.sol#L279](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L279)
- [GovNFT.sol#L289](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L289)

- [StableVault.sol#L46](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L46)


## 20. use existing cached version of state var

instead of `_trade.asset` use `_asset`
- [Position.sol#L187](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L187)


## 21. dont use a function to get the length value inside to for statement just cache it before the loop

cache the value of `assetsLength()` before to save gas

- [GovNFT.sol#L53](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L53)
- [GovNFT.sol#L67](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L67)
- [GovNFT.sol#L78](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L78)
- [GovNFT.sol#L95](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L95)