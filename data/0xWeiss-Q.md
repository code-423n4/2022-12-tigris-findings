### 1 using a wrong function in the IERC20 interface https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/NFTSale.sol#L12-L16

On line: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/NFTSale.sol#L14
the function transfer returns a bool, so it should be changed from: 
- function transfer(address, uint) external;

to:

-  function transfer(address, uint) external returns(bool);
(according to openzeppelin's standard)

### 2 EVENT INDEXING
There should be some kind of indexing for important parameters in events. There is none in lines:
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L377-L382

### Finding
Add keyword indexed to the _tigAsset variable, just like this:
event Lock(address indexed _tigAsset, uint256 _amount, uint256 _period, address _owner, uint256 _id);

### 3 Don't Initialize Variables with Default Value

#### Impact
Issue Information: [G001](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g001---dont-initialize-variables-with-default-value)

#### Findings:
```
examples/Test.sol::4 => uint256 a = 0;
examples/Test.sol::12 => for (uint256 i = 0; i < array.length; i++) {
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Cache Array Length Outside of Loop

#### Impact
Issue Information: [G002](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g002---cache-array-length-outside-of-loop)

#### Findings:
```
examples/Test.sol::12 => for (uint256 i = 0; i < array.length; i++) {
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Long Revert Strings

#### Impact
Issue Information: [G007](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g007---long-revert-strings)

#### Findings:
```
examples/Test.sol::6 => string b = "aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa";
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Use Shift Right/Left instead of Division/Multiplication if possible

#### Impact
Issue Information: [G008](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md/#g008---use-shift-rightleft-instead-of-divisionmultiplication-if-possible)

#### Findings:
```
examples/Test.sol::13 => i = i / 2;
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Unsafe ERC20 Operation(s)

#### Impact
Issue Information: [L001](https://github.com/byterocket/c4-common-issues/blob/main/2-Low-Risk.md#l001---unsafe-erc20-operations)

#### Findings:
```
examples/Test.sol::16 => token.transferFrom(msg.sender, address(this), 100);
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Unspecific Compiler Version Pragma

#### Impact
Issue Information: [L003](https://github.com/byterocket/c4-common-issues/blob/main/2-Low-Risk.md#l003---unspecific-compiler-version-pragma)

#### Findings:
```
examples/Test.sol::1 => pragma solidity ^0.8.0;
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

