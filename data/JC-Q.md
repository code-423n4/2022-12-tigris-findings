
---

## summary

- N-01 Use a more recent version of solidity | 22 instances
- N-02 File is missing NatSpec | 8 instances
- N-03  The visibility for constructor is ignored | 8 instances

Total: 38 instances in 3 issues.

---


## [N-01] Use a more recent version of solidity

Use a solidity version of at least 0.8.0 to get overflow protection without SafeMath. 
Use a solidity version of at least 0.8.2 to get compiler automatic inlining.
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads.
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings. 
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value. 
Use a solidity version of at least 0.8.12 to get string.concat() instead of abi.encodePacked(<str>,<str>). 
Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions.

Instances (22):

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L2
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L2
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L2
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L2
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol#L2
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L2
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableToken.sol#L2
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L2
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L2
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L2

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/MetaContext.sol#L2
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IBondNFT.sol#L2
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IGovNFT.sol#L3
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ILayerZeroEndpoint.sol#L3
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ILayerZeroReceiver.sol#L3
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ILayerZeroUserApplicationConfig.sol#L3
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPairsContract.sol#L3
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPosition.sol#L3
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IReferrals.sol#L3
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IStableVault.sol#L3
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ITrading.sol#L5

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L2



## [N-02]  File is missing NatSpec

Instances (8):

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/MetaContext.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IBondNFT.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IGovNFT.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPairsContract.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPosition.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IReferrals.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IStableVault.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ITrading.sol


## [N-03]  The visibility for constructor is ignored

Instances (8):

Trading.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L143

TradingExtension.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L28

Position.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L76

GovNFT.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L32

StableToken.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableToken.sol#L11

StableVault.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L35

Lock.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L21

BondNFT.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L40

