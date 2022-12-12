## Missing array length checks
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol#L71
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol#L74

if `_ownedCodes`  and `_codeOwners`  or `_referredA` and `_referredTo` have different lengths `initRefs` will revert.


## Missing `super` call
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L313

Should be `super._mint` instead of just `_mint`.


## Potential Integer overflow
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L67
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L117
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L119
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L396


## Use of floating pragma
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L2

This issue appears in every Solidity file.

Fixed solidity version should be used instead.


## Missing zero address checks
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L80-L82
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol#L54
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L130
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L351-L354
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L978