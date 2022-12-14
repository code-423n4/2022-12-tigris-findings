## 1.Increments/decrements can be unchecked in for-loops:-
In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.

code snippet:-
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L220
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L284
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L292
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L300
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L325
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L78
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L304
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L296
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L53
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L67
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L78
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L95
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L200
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L252
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L258



## 2.x = x + y is cheaper than x += y :-

code snippet:-
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L52
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L293
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L509
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L511
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L185
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L187

Recommendation:-
use x=x+y instead of x+=y.

Reference :-
https://code4rena.com/reports/2022-08-olympus/#g-11-x--x--y-is-cheaper-than-x--y-6-instances

## 3.Unchecking arithmetics operations that can’t underflow/overflow:-
Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block: https://docs.soliditylang.org/en/v0.8.10/control-structures.html#checked-or-unchecked-arithmetic.While this is inside an external view function, consider wrapping this in an unchecked statement so that external contracts calling this might save some gas

code snippet:-
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L52
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L179
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L430
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L431

## 4.Functions guaranteed to revert when called by normal users can be marked payable
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

code snippet:-
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol#L65
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L898
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L912
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L926
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L939
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L952
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L975
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L144
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L222
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L231
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L240
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L249
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L264
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L274
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L85

## 5.Reduce the size of error messages (Long revert Strings)

Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.
Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

code snippet:-
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L877
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol#L21
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L155
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L185
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L209
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L210


## 6  Splitting require() statements that use && saves gas:-
&& use two AND opcodes ,one AND consumes 3 gas 
code snippet:-
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L116

