### [G01] Revert Strings > 32 Bytes in Length
**Description:**
If choosing not to update revert strings to custom errors, you can save some gas by shortening them to be under 32 bytes in length.

**Instances:**
[GovNFT.sol#L185](https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/GovNFT.sol#L185) 
[GovNFT.sol#L209-L210](https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/GovNFT.sol#L209-L210) 

**Gas Savings:**
| Contract | Pre Changes | Post Changes | Savings |
|------|------|------|------|
| GovNFT Deployment | 5,071,743 | 5,047,813 | 23,930 |


### [G02] Using Local Variables instead of State Variables
**Description:**
When there are either local variables or function arguements that have the same value as a state variable it is cheaper to reference them instead.

**Instances:**
[Position.sol#L187](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L187) - can replace trade.margin with newMargin.
[BondNFT.sol#L122](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L122) - can replace bond.shares with shares.

**Gas Savings:** 
| Contract | Pre Changes | Post Changes | Savings |
|------|------|------|------|
| Position Deployment | 4,675,491 | 4,675,083 | 408 |
| Position:executeLimitOrder | 88,665 | 88,614 | 51 |
| BondNFT Deployment | 4,429,164 | 4,428,300 | 864 |


### [G03] Unchecked Increments in For Loops.
**Description:**
When updating the increments in for loops there is no chance of overflow and they can be wrapped in unchecked blocks to save gas.

**Instances:**
[GovNFT.sol#L53](https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/GovNFT.sol#L53) 
[GovNFT.sol#L67](https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/GovNFT.sol#L67) 
[GovNFT.sol#L78](https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/GovNFT.sol#L78) 
[GovNFT.sol#L95](https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/GovNFT.sol#L95) 
[GovNFT.sol#L105](https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/GovNFT.sol#L105) 
[GovNFT.sol#L131](https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/GovNFT.sol#L131) 
[GovNFT.sol#L200](https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/GovNFT.sol#L200) 
[GovNFT.sol#L246](https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/GovNFT.sol#L246) 
[GovNFT.sol#L252](https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/GovNFT.sol#L252) 
[GovNFT.sol#L258](https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/GovNFT.sol#L258) 
[GovNFT.sol#L325](https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/GovNFT.sol#L325) 
[Lock.sol#L113](https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/Lock.sol#L113) 
[Position.sol#L296](https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/Position.sol#L296) 
[Position.sol#L304](https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/Position.sol#L304) 
[Referrals.sol#L70](https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/Referrals.sol#L70) 
[Referrals.sol#L73](https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/Referrals.sol#L73) 

**Gas Savings:** 
| Contract | Pre Changes | Post Changes | Savings |
|------|------|------|------|
| Referrals Deployment | 716,318 | 688,561 | 27,757 |
| Referrals:initRefs | 53,132 | 53,061 | 71 |
| Position Deployment | 4,675,491 | 4,659,082 | 16,409 |
| Lock Deployment | 1,411,485 | 1,397,887 | 13,598 |
| Lock:claimGovFees | 113,858 | 113,799 | 59 |
| GovNFT Deployment | 5,071,743 | 5,035,015 | 36,728 |
| GovNFT:bridgeMint | 122,914 | 122,843 | 71 |
| GovNFT:approveMany | 106,250 | 106,046 | 204 |
| GovNFT:crossChain | 268,976 | 268,513 | 463 |
| GovNFT:mint | 168,835 | 168,764 | 71 |
| GovNFT:mintMany | 14,530,331 | 14,513,099 | 17,232 |
| GovNFT:safeTransferFromMany | 192,092 | 191,742 | 347 |
| GovNFT:safeTransferMany | 102,839 | 102,697 | 142 |
| GovNFT:transferFrom | 130,090 | 130,019 | 71 |


### [G04] X = X + Y is Cheaper than X += Y for State Variables.

**Description:**
When increasing/decreasing X's value by Y you can save a small amount of gas by using the pattern x = x + y instead of x += y. This is only true for state variables.

**Instances:**
[BondNFT.sol#L117](https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/BondNFT.sol#L117) 
[BondNFT.sol#L119](https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/BondNFT.sol#L119) 
[BondNFT.sol#L221](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L221) 
[GovNFT.sol#L52](https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/GovNFT.sol#L52) 
[PairsContract.sol#L156](https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/PairsContract.sol#L156) 
[PairsContract.sol#L176](https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/PairsContract.sol#L176) 
[PairsContract.sol#L160](https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/PairsContract.sol#L160) 
[PairsContract.sol#L180](https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/PairsContract.sol#L180) 
[Position.sol#L231-L232](https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/Position.sol#L231-L232) 

**Gas Savings:** 
| Contract | Pre Changes | Post Changes | Savings |
|------|------|------|------|
| BondNFT Deployment | 4,429,164 | 4,427,880 | 1,284 |
| GovNFT Deployment | 5,071,743 | 5,069,991 | 1,752 |
| GovNFT: Mint | 168,835 | 168,813 | 22 |
| PairsContract Deployment | 1,707,839 | 1,700,932 | 6,907 |
| PairsContract:modifyLongOi | 39,746 | 39,627 | 119 |
| PairsContract:modifyShortOi | 39,760 | 39,641 | 119 | 
| Position Deployment | 4,675,491 | 4,672,683 | 2,808 |
| Position:reducePosition | 57,854 | 57,719 | 135 |


### [G05] Caching State Variables that are used multiple times. 

**Description:** 
Whenever a state variable is used more than once in a given scope without being modified you can save gas by caching the variable and using that value instead.

**Instances:**
[BondNFT.sol#L110-L122](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L110-L122) - epoch[bond.asset] is referenced 3 times & can be cached.

**Gas Savings:** 
| Contract | Pre Changes | Post Changes | Savings |
|------|------|------|------|
| BondNFT Deployment | 4,429,164 | 4,400,666 | 28,498 |
