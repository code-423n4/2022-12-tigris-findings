## Strings over 32 bytes cost extra gas
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L155
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L185
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L209
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L210

I suggest shortening the revert strings to fit in 32 bytes or using custom errors.

## Functions not called by the contract should be declared external instead
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L129
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L183
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L206
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L199
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L250
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L262
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L266
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L274
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L339
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableToken.sol#L17
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableToken.sol#L28
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableToken.sol#L42
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L65
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L88
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L100
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L17
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L22
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L40
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L66-L71

Change function accessibility from `public` to `external`.


## Caching storage values in memory
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L53
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L67
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L78
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L95

Calling `assetsLength()` in each iteration of the loop is quite expensive. Store `assetsLength()` into a `memory` variable instead.


https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L54
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L68
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L96-L99

Accessing `assets[i]` more than 1 time in each iteration of the loop is expensive. Store `assets[i]` into a `memory` variable instead.


## It costs less gas to use ++i compared to i++
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L53
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L67
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L78
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L95
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L105
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L131
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L200
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L246
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L252
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L258
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L325
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L220
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L284
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L292
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L300
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L342
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L113
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L296
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L304
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol#L70
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol#L73

Use `++i` instead of `i++`, especially in loops.