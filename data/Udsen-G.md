## 1. ++I COSTS LESS GAS COMPARED TO I++

    for (uint i=epoch[_tigAsset]; i<aEpoch; i++) {

Above code line should be changed to following:

    for (uint i=epoch[_tigAsset]; i<aEpoch; ++i) {

Above Issue is found in the following code lines.

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L220

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L284

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L292

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L300

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L342

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L113

## 2. REDUNDANT LOCAL VARIABLE ASSIGNMENT

There is no requirement to assign `amount` local variable the value of `bond.amount`

        amount = bond.amount;

`amount` is not used after this assignment until it is updated as follows:

            amount += _claimAmount;

Hence above line can be directly changed as :

            amount = bond.amount + _claimAmount; 

This will save gas due to reduction of first variable assignment of `amount`.

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L148-153 