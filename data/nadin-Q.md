[NC-01] NON-LIBRARY/INTERFACE FILES SHOULD USE FIXED COMPILER VERSIONS, NOT FLOATING ONES 
01: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol
02: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol
03: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol
04: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol
05: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol
06: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol
07: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol
08: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableToken.sol
09: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol
10: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol
11: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/MetaContext.sol
12: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol

[N-02] Syntax
01: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L95
Fix: uint private constant DIVISION_CONSTANT = 1e10; // 100%
02: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L10
Fix: uint private constant DAY = 24 * 60 * 60;

[NC-03] Missing checks for address(0) when assigning values to address state variables
01: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L149
02: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L150
03: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L151
04: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L644
05: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/TradingExtension.sol#L96

[NC-04] Functions not used internally could be marked external
01: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/TradingExtension.sol#L122
02: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/TradingExtension.sol#L163-L171
03: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/utils/TradingLibrary.sol#L62
04: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L11
05: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L41

[NC-05] Constants should be defined rather than using magic numbers
01: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L120
02: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L144
03: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L175
04: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/StableVault.sol#L49
05: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/StableVault.sol#L67
06: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/utils/MetaContext.sol#L31

[L-01] Weak PRNG
Weak PRNG due to a modulo on block.timestamp, now or blockhash. These can be influenced by miners to some extent so they should be avoided. Do not use block.timestamp, now or blockhash as a source of randomness
01: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L347

[L-02] abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()
01: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/utils/TradingLibrary.sol#L103-L104