# Tigris Trade Gas Optimization Report

This finding acts as a gas optimization report of the Tigris codebase and will only showcase findings that lead to measureable gas reductions.

## TradingLibrary.sol

The `PriceData` struct's variables can be re-ordered to take advantage of tight-packing as the `provider` variable and the `isClosed` variable can be packed into a single slot ([#1](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L12-L19)), reducing the total data space of the struct by a full `32` bytes.

## BondNFT.sol

The `Bond` struct's members can be similarly packed ([#1](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L12-L24)) by relocating the `expired` variable either above, below, or in-between the `owner` and `asset` variables, the former of which is advisable.

The `isExpired` function loads the full `Bond` struct to `memory` prior to accessing only a single of its variables ([#1](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L251)). We advise its specifier to be set as `storage` significantly reducing its execution cost as it will only perform a single `SLOAD` operation instead of performing an `SLOAD` for all storage slots occupied by the struct.

The code suffers from inefficient `mapping` access statements ([#1](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L178), [#2](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L180), [#3](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L180), [#4](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L183), [#5](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L200-L201), [#6](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L222), [#7](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L225)) that can be significantly optimized by caching the `mapping` declarations themselves to local `storage` variables. Below is a tangible example:

```solidity
mapping (uint256 => mapping (uint256 => bool)) public someMap;

function optimized(uint256 a_, uint256 b_) external {
    mapping (uint256 => bool) storage tempMap = someMap[a_];
    for (uint256 i = 0; i < 5; i++) {
        tempMap[b_ + i] = true;
    }
}

function suboptimal(uint256 a_, uint256 b_) external {
    for (uint256 i = 0; i < 5; i++) {
        someMap[a_][b_ + i] = true;
    }
}
```

## GovNFT.sol

The linked `for` loops utilize safe arithmetics for the iterator's incremention ([#1](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L53), [#2](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L67), [#3](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L78), [#4](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L95), [#5](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L105), [#6](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L131), [#7](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L200), [#8](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L246), [#9](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L252), [#10](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L258), [#11](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L325)) which can be avoided by wrapping the incremention statement in an `unchecked` code block, reducing each iteration's gas cost.

Similar `mapping` related exhibits to `BondNFT` exist in this contract as well ([#1](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L54), [#2](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L68), [#3](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L79-L81), [#4](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L96-L99), [#5](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L208), [#6](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L212)).