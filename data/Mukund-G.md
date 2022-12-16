## Index
|    | Issue                                                                                        |
|----|----------------------------------------------------------------------------------------------|
| 1  | Use `unchecked{}` for operation that cannot overflow/underflow                               |
| 2  | not using the named return variables when a function returns, wastes deployment gas          |
| 3  | `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables                       |
| 4  | use more recent version of solidity                                                          |
| 5  | `abi.encode()` is less efficient than `abi.encodedpacked()`                                  |
| 6  | usage of uint/int smaller than 32 bytes (256 bits) incurs overhead                           |
| 7  | Using `calldata` instead of memory for read-only arguments in `external` functions saves gas |
| 8  | The result of function calls should be cached rather than re-calling the function            |
| 9  | splitting && statement in require function and writing them separately saves gas             |
| 10 | Functions guaranteed to revert when called by normal users can be marked payable             |

## 1.Use `unchecked{}` for operation that cannot overflow/underflow
for example use it for i++ in every loop 
[GovNFT.sol#L67](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L67)
[GovNFT.sol#L78](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L78)
[GovNFT.sol#L95](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L95)
[GovNFT.sol#L105](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L105)
[GovNFT.sol#L131](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L131)
[GovNFT.sol#L200](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L200)
[GovNFT.sol#L246](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L246)
[GovNFT.sol#L325](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L325)
[Lock.sol#L113](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L113)
[Position.sol#L296](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L296)
[Position.sol#L304](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L304)

## 2. not using the named return variables when a function returns, wastes deployment gas
[GovNFT.sol#L225](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L225)

## 3.`<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables
[BondNFT.sol#L152](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L152)
[TradingExtension.sol#L185](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L185)

## 4.use more recent version of solidity
 solidity 0.8.17 is available now

## 5.`abi.encode()` is less efficient than `abi.encodedpacked()`
[GovNFT.sol#L141](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L141)

## 6.usage of uint/int smaller than 32 bytes (256 bits) incurs overhead
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.
https://docs.soliditylang.org/en/v0.8.0/internals/layout_in_storage.html Use a larger size then downcast where needed
[GovNFT.sol#L22](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L22)
[GovNFT.sol#L23](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L23)
[GovNFT.sol#L53](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L53)
[GovNFT.sol#L67](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L67)
[GovNFT.sol#L78](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L78)
[GovNFT.sol#L95](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L95)
[GovNFT.sol#L105](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L105)
[GovNFT.sol#L105](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L131)
[BondNFT.sol#L16-L22](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L16-L22)
[BondNFT.sol#L27](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L27)
[BondNFT.sol#L100-L101](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L100-L101)

there is lot of instances of this issue in contract i haven't mentioned all of them

## 7.Using `calldata` instead of memory for read-only arguments in `external` functions saves gas
When a function with a memory array is called externally, the abi.decode() step has to use a for-loop to copy each index of the calldata to the memory index. Each iteration of this for-loop costs at least 60 gas (i.e. 60 * <mem_array>.length). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution. Note that even if an interface defines a function as having memory arguments, it's still valid for implementation contracs to use calldata arguments instead.

If the array is passed to an internal function which passes the array to another internal function where the array is modified and therefore memory is used in the external call, it's still more gass-efficient to use calldata when the external function uses modifiers, since the modifiers may prevent the internal functions from being called. Structs have the same overhead as an array of length one
[GovNFT.sol#L323](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L323)
[Position.sol#L294](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L294)
[Position.sol#L302](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L302)
[IBondNFT.sol#L43](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IBondNFT.sol#L43)
[IPosition.sol#L42](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPosition.sol#L42)

## 8.The result of function calls should be cached rather than re-calling the function
[GovNFT.sol#L53](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L53)
[GovNFT.sol#L67](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L67)
[GovNFT.sol#L78](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L78)
[GovNFT.sol#L95](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L95)

## 9.splitting && statement in require function and writing them separately saves gas
[PairsContract.sol#L52](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L52)

## 10.Functions guaranteed to revert when called by normal users can be marked payable
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are
CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost
[BondNFT.sol#L57-L62](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L57-L62)
[BondNFT.sol#L97-L103](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L97-L103)
[BondNFT.sol#L137-L140](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L137-L140)
[BondNFT.sol#L168-L171](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L168-L171)
[BondNFT.sol#L196-L199](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L196-L199)
[BondNFT.sol#L349](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L349)
[BondNFT.sol#L357]https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L357)
[BondNFT.sol#L362](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L362)
[GovNFT.sol#L46](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L46)
[GovNFT.sol#L104](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L104)
[GovNFT.sol#L110](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L110)
[GovNFT.sol#L114](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L114)
[GovNFT.sol#L236](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L236)

Note there are lot of instances in codebase
