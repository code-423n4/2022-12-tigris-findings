1.All contracts should have a latest solidity pragma , 
 is currently 0.8.17

==================================================================================================================================
2.In all solidity files, license keyword should be mentioned as // SPDX-License-Identifier: UNLICENSED.
Тука не съм много сигорен :) 


====================================
3.FUNCTIONS, PARAMETERS AND VARIABLES IN SNAKE CASE
Use camel case for all functions, parameters and variables and snake case or Upercase for constants ,be consistent.

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L95-L96

=====================================================================================================================

4.Missing non-zero address checks in the constructor  on Trading.sol
https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L143-L152

5.Index event fields make the field more quickly accessible to off-chain tools that parse events.
 However, note that each index field costs extra gas during emission, so it’s not necessarily best to 
 index the maximum allowed per event (three fields). Each event should use three indexed fields if there 
 are three or more fields, and gas usage is not particularly of concern for the events in question. 
 If there are fewer than three fields, all of the fields should be indexed.
 Всеки ред по отделно ли трябва да се отбележи ?
https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L981-L1052

in event function up to three parameters can receive the attribute indexed which will cause the respective arguments to be treated as log topics instead of data. 
The hash of the signature of the event is always one of the topics. All non-indexed arguments will be stored in the data part of the log.


==============================================================================
6.Interface must гoing to folder for Interfaces
https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/utils/TradingLibrary.sol#L7-L11

7.USE LATEST OPEN ZEPPELIN CONTRACTS

8.USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE() STRINGS TO SAVE GAS
Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they’re 
hit by avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas.
https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L50-L52
https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L35
https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L75
https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L94-L95
https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L106
https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L117
https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L141

