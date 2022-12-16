## 1. ++I COSTS LESS GAS COMPARED TO I++

    for (uint i=epoch[_tigAsset]; i<aEpoch; i++) {

Above code line should be changed to following:

    for (uint i=epoch[_tigAsset]; i<aEpoch; ++i) {

Above Issue is found in the following code lines.

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L220

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L342