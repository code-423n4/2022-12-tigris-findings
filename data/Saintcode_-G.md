## USE CALLDATA INSTEAD OF MEMORY

When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for-loop to copy each index of the `calldata` to the `memory` index. Each iteration of this for-loop costs at least 60 gas (i.e. `60 * <mem_array>.length`). Using `calldata` directly, obliviates the need for such a loop in the contract code and runtime execution.

When arguments are read-only on external functions, the data location should be `calldata`

Instances:
-BondNFT.sol line 308
-GovNFT.sol lines 126, 128, 170, 172, 189
-Lock.sol line 139
-PairsContract.sol line 48
-Position.sol lines 86, 132, 
-Referals.sol lines 61, 62, 63, 64
-Trading.sol lines 668

## <ARRAY>.LENGTH SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A FOR-LOOP

Reading array length at each iteration of the loop consumes more gas than necessary.
In the best case scenario (length read on a memory variable), caching the array length in the stack saves around 3 gas per iteration. In the worst case scenario (external calls at each iteration), the amount of gas wasted can be massive.

Consider storing the array’s length in a variable before the for-loop, and use this new variable instead

Instances:
-BondNFT.sol lines 284, 292, 300, 342
-GovNFT.sol lines 131, 200, 246, 252, 258, 325
-Lock.sol line 113
-Position.sol line 296, 304

## USING STORAGE INSTEAD OF MEMORY FOR STRUCTS/ARRAYS SAVES GAS

When fetching data from a storage location, assigning the data to a `memory` variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new `memory` variable, they incur an additional `MLOAD` rather than a cheap stack read. Instead of declearing the variable with the `memory` keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires `memory`, or if the array/struct is being read from another memory array/struct

Instances:
-BondNFT.sol line 250
-Position.sol line 42
-Trading.sol lines 859, 886

## <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y> FOR STATE VARIABLES

Instances:
-BondNFT.sol lines 68, 115, 117, 119, 150, 152, 180, 183, 221, 225, 333, 334
-GovNFT.sol lines 52, 54, 68, 79, 80, 81, 96, 97, 98, 99, 278, 290
-Lock.sol line 73, 103
-PairsContract.sol lines 156, 160, 180,176
-Position.sol lines 102, 104, 107, 109, 231, 232

## `++I` INSTEAD OF `I++` (OR USE ASSEMBLY WHEN APPLICABLE)

Use `++i` instead of ` i++`. This is especially useful in for loops but this optimization can be used anywhere in your code. 

Instances:
-BondNFT.sol lines 220, 284, 292, 300, 342
-GovNFT.sol lines 53, 67, 78, 95, 105, 131, 200, 246, 252, 258, 325
-Lock.sol line 113
-Position.sol lines 296, 304
-Referrals.sol lines 70, 73

## USE MULTIPLE `REQUIRE()` STATMENTS INSTEAD OF `REQUIRE(EXPRESSION && EXPRESSION && ...)`

-PairsContract.sol line 52
-TradingLibrary.sol line 116

## IT COSTS MORE GAS TO INITIALIZE VARIABLES WITH THEIR DEFAULT VALUE THAN LETTING THE DEFAULT VALUE BE APPLIED

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

As an example: for (uint256 i = 0; i < numIterations; ++i) { should be replaced with for (uint256 i; i < numIterations; ++i) {

Instances:
-BondNFT.sol lines 284, 292, 300, 342
-GovNFT.sol lines 53, 67, 78, 95, 105, 131, 200, 246, 252, 258, 325
-Lock.sol line 113
-Position.sol lines 296, 304
-Referrals.sol lines 70, 73

## `>=` COSTS LESS GAS THAN `>`

The compiler uses opcodes `GT` and `ISZERO` for solidity code that uses `>`, but only requires `LT` for `>=`, which saves 3 gas

Instances:
-BondNFT.sol lines 179, 130
-PairsContract.sol line 35, 51, 52, 75, 77, 80, 94, 106, 117, 141
-Position.sol lines 44, 47, 54, 108, 263
-Trading.sol lines 622, 625
-TradingLibrary.sol line 111

## `++I/I++` SHOULD BE `UNCHECKED{++I}`/`UNCHECKED{I++}` WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop
`
   for (uint256 i = 0; i < orders.length; /** NOTE: Removed i++ **/ ) {
           // Do the thing
           // Unchecked pre-increment is cheapest
           unchecked { ++i; }   
}  `

Instances:
-BondNFT.sol lines 220, 284, 292, 300, 342
-GovNFT.sol lines 53, 67, 78, 95, 105, 131, 200, 246, 252, 258, 325
-Lock.sol line 113
-Position.sol lines 296, 304
-Referrals.sol lines 70, 73

## USING `PRIVATE` RATHER THAN `PUBLIC` FOR CONSTANTS, SAVES GAS

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

Instances:
-Lock.sol line 12, 13

## USE A MORE RECENT VERSION OF SOLIDITY

The latest version of solidity is 0.8.17, and the version used in this project is 0.8.0. The latest version of solidity has many optimizations that are not available in older versions

## USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE() STRINGS TO SAVE GAS
Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they’re hitby avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas

## RESULTS OF CALLS TO _MSGSENDER() NOT CACHED
The result of `_msgsender()` is not cached, so it is recommended to store it in a local variable to save gas

Instances:
-BondNFT.sol line 285
-GovNFT.sol lines 106, 132, 247, 
-Trading.sol lines 885, 887


## FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED PAYABLE

If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are `CALLVALUE`(2),`DUP1`(3),`ISZERO`(3),`PUSH2`(3),`JUMPI`(10),`PUSH1`(3),`DUP1`(3),`REVERT`(0),`JUMPDEST`(1),`POP`(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

