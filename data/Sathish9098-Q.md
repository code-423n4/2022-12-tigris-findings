##
## [NC-1]  USE A MORE RECENT VERSION OF SOLIDITY

Use a solidity version of at least 0.8.12 to get string.concat() to be used instead of abi.encodePacked(<str>,<str>). 
Latest solidity version is 0.8.17 

There are 2 instances of this issue:

         2:  pragma solidity ^0.8.0;

##

## [NC-2]  NON-LIBRARY/INTERFACE FILES SHOULD USE FIXED COMPILER VERSIONS, NOT FLOATING ONES

There are 4 instances of this issue:

[File: 2022-12-tigris/contracts/Trading.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol)

       2:  pragma solidity ^0.8.0;
 

 
















NC-1	Missing checks for address(0) when assigning values to address state variables	5
NC-2	require() / revert() statements should have descriptive reason strings	5
NC-3	Return values of approve() not checked	5
NC-4	Event is missing indexed fields	21
NC-5	Constants should be defined rather than using magic numbers	4
NC-6	Functions not used internally could be marked external	23
L-1	Unsafe ERC20 operation(s)	19