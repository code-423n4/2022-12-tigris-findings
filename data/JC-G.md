
---

## summary

- G‑01 Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate | 18 instances
- G‑02 State variables that never change should be declared immutable or constant | 12 instances
- G-03 <x> += <y> costs more gas than <x> = <x> + <y> for state variables | 27 instances
- G-04 Not using the named return variables when a function returns, wastes deployment gas | 44 instances
- G-05 Use a more recent version of solidity | 22 instances

Total: 123 instances in 5 issues

---


## [G‑01] Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate	

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. 
Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. 
Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key’s keccak256 hash (Gkeccak256 - 30 gas) and that calculation’s associated stack operations.

Instances (18)

TradingExtension.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L17
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L18
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L19

Position.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L33
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L34
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L35
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L36
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L37
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L38

StableVault.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L29
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L30

Lock.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L18
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L19

BondNFT.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L32
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L33
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L35
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L37
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L38


## [G‑02] State variables that never change should be declared immutable or constant 

Avoids a Gsset (20000 gas) in the constructor, and replaces the first access in each transaction (Gcoldsload - 2100 gas) and each access thereafter (Gwarmacces - 100 gas) with a PUSH32 (3 gas).

Instances (12):

Trading.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L121
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L123
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L123

TradingExtension.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L13
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L22
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L23
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L24

Position.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L73
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L74

GovNFT.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L18
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L20

BondNFT.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L28


## [G-03] <x> += <y> costs more gas than <x> = <x> + <y> for state variables

Instances (27)

Trading.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L509

TradingExtension.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L185

Position.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L62
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L102
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L109

PairsContract.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L156
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L176

GovNFT.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L52
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L54
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L68
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L79
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L96
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L99
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L278
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L290

Lock.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L73

BondNFT.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L68
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L115
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L117
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L119
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L152
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L180
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L183
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L221
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L225
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L333
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L334




## [G-04] Not using the named return variables when a function returns, wastes deployment gas

Instances (44):

IBondNFT.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IBondNFT.sol#L12
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IBondNFT.sol#L35

IGovNFT.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IGovNFT.sol#L6
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IGovNFT.sol#L7
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IGovNFT.sol#L8

ILayerZeroEndpoint.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ILayerZeroEndpoint.sol#L15
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ILayerZeroEndpoint.sol#L24
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ILayerZeroEndpoint.sol#L50

ILayerZeroReceiver.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ILayerZeroReceiver.sol#L11

ILayerZeroUserApplicationConfig.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ILayerZeroUserApplicationConfig.sol#L11
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ILayerZeroUserApplicationConfig.sol#L15
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ILayerZeroUserApplicationConfig.sol#L19
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ILayerZeroUserApplicationConfig.sol#L24

IPairsContract.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPairsContract.sol#L25
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPairsContract.sol#L26
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPairsContract.sol#L27

IPosition.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPosition.sol#L36
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPosition.sol#L37
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPosition.sol#L38
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPosition.sol#L39
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPosition.sol#L40
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPosition.sol#L41
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPosition.sol#L42
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPosition.sol#L47
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPosition.sol#L48
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPosition.sol#L49
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPosition.sol#L50
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPosition.sol#L51
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPosition.sol#L52
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPosition.sol#L53

IReferrals.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IReferrals.sol#L7
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IReferrals.sol#L8

IStableVault.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IStableVault.sol#L6

ITrading.so
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ITrading.sol#L30
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ITrading.sol#L38
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ITrading.sol#L48
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ITrading.sol#L57
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ITrading.sol#L67
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ITrading.sol#L78
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ITrading.sol#L86
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ITrading.sol#L91
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ITrading.sol#L100
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ITrading.sol#L106
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ITrading.sol#L112



## [G-05] Use a more recent version of solidity

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




