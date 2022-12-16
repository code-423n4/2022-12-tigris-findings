## 1. Unneeded ternary

Ternaries that use boolean literals are unnecessary if the variable being compared is already a `bool`. Moreover, it also costs more gas.

Here is an example:

File: `BondNFT.sol` [Line 238](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L238)

```solidity
        bond.expired = bond.expireEpoch <= epoch[bond.asset] ? true : false;
```

In the example above, `<=` already returns a `bool`. As such, the code could be omit the ternary:

```solidity
        bond.expired = bond.expireEpoch <= epoch[bond.asset];
```

Here are the rest of the instances:

File `BondNFT.sol` [Line 252](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L252)

## 2. Splitting `require` statements that use `&&` saves gas

Instead of using the `&&` operator to check multiple conditions, use multiple `require()` statements. This will save approximately 3 gas per `&&`.

Here are an example of this issue:

File: `GovNFTBridged.sol` [Line 50](https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/GovNFTBridged.sol#L50)

```
        require(tokenId <= 10000 && tokenId != 0, "BadID");
```

In the example above, the `&&` could be split up:

```
        require(tokenId <= 10000, "BadID");
        require(tokenId != 0, "BadID");
```

Here is one other instance of this issue:

File: `Trading.sol` [Line 887](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L887)

## 3. `x += y` costs more gas than `x = x + y` (same for `-=`)

Using the addition operator instead of plus equals saves gas

- File: `BondNFT.sol` [Line 68](https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/BondNFT.sol#L68)
- File: `BondNFT.sol` [Line 115](https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/BondNFT.sol#L115)
- File: `BondNFT.sol` [Line 117](https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/BondNFT.sol#L117)
- File; `BondNFT.sol` [Line 119](https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/BondNFT.sol#L119)
- File[ `BondNFT.sol` [Line 150](https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/BondNFT.sol#L150)
- File: `BondNFT.sol` [Line 152](https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/BondNFT.sol#L152)
- File: `BondNFT.sol` [Line 180](https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/BondNFT.sol#L180)
- File: `BondNFT.sol` [Line 183](https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/BondNFT.sol#L183)
- File: `BondNFT.sol` [Line 221](https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/BondNFT.sol#L221)
- File: `BondNFT.sol` [Line 225](https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/BondNFT.sol#L225)
- File: `BondNFT.sol` [Line 333](https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/BondNFT.sol#L333)
- File: `BondNFT.sol` [Line 334](https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/BondNFT.sol#L334)

## 4. Using `x += 1` costs more gas than `++x`

When incrementing by 1, the pre-increment operator (`++x`) is more gas efficient than `x += 1`

- File: `BondNFT.sol` [Line 221](https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/BondNFT.sol#L221)
- File `GovNFT.sol` [Line 52](https://github.com/code-423n4/2022-12-tigris/blob/b2ebb8ea1def4927a747e7a185174892506540ab/contracts/GovNFT.sol#L52)

## 5. Using `storage` instead of `memory` for structs/arrays saves gas

When retrieving data from a `storage` location, assigning the data to a `memory` variable will cause all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new `memory` variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declaring the variable with the memory keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incurring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires `memory`, or if the array/struct is being read from another `memory` array/struct

Here are a few examples:

- File: `Trading.sol` [Line 183](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L183)
- File: `Trading.sol` [Line 237](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L237)
- File: `Trading.sol` [Line 270](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L270)
