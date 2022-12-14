USE A MORE RECENT VERSION OF SOLIDITY
===============
Use a solidity version of at least 0.8.2 to get simple compiler automatic inclining.
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads.
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper than `revert() / require()`.
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the call has a return value.

There are instances of this issue:
- [IGovNFT.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IGovNFT.sol#L3)
- [ILayerZeroEndpoint.sol
](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ILayerZeroEndpoint.sol#L3)
- [ILayerZeroEndpoint.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ILayerZeroEndpoint.sol#L3)
- [ILayerZeroReceiver.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ILayerZeroReceiver.sol#L3)
- [ILayerZeroUserApplicationConfig.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ILayerZeroUserApplicationConfig.sol#L3)
- [IPairsContract.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPairsContract.sol#L3)
- [IPosition.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPosition.sol#L3)
- [IReferrals.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IReferrals.sol#L3)
- [IStableVault.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IStableVault.sol#L3)
- [ITrading.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ITrading.sol#L5)
- [TradingLibrary.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L2)
- [MetaContext.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/MetaContext.sol#L3)
- [Trading.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L2)
- [TradingExtension.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L2)
- [Position.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L2)
- [PairsContract.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L2)
- [Referrals.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol#L2)
- [GovNFT.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L2)
- [StableToken.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableToken.sol#L2)
- [StableVault.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L2)
- [Lock.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L2)
- [BondNFT.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L2)