# [N-01] LARGE MULTIPLES OF TEN SHOULD USE SCIENTIFIC NOTATION

Use (e.g. 1e12) rather than decimal literals (e.g. 1000000000000), for better code readability.

contracts/TradingExtension.sol: [L26](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L26)
contracts/GovNFT.sol: [L16](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L16), [L17](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L17)

# [N-02] EXPLICITLY MARK UINT SIZES

contracts/Trading.sol: 107 instances
contracts/TradingExtension.sol:  21 instances
contracts/utils/TradingLibrary.sol: 13 instances
contracts/Position.sol: 34 instances
contracts/PairsContract.sol: 1 instance
contracts/Referrals.sol: 4 instances
contracts/GovNFT.sol: 21 instances
contracts/StableVault.sol: 4 instances
contracts/Lock.sol: 17 instances
contracts/BondNFT.sol: 45 instances
contracts/interfaces/IBondNFT.sol: 14 instances
contracts/interfaces/IGovNFT.sol: 2 instances
contracts/interfaces/ILayerZeroEndpoint.sol: 4 instances
contracts/interfaces/ILayerZeroUserApplicationConfig.sol: 1 instance
contracts/interfaces/IPairsContract.sol: 1 instance
contracts/interfaces/IPosition.sol: 15 instances
contracts/interfaces/IStableVault.sol: 2 instances
contracts/interfaces/ITrading.sol: 9 instances

