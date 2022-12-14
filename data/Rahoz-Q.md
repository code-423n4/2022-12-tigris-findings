## N. Missing checks for address(0) when assigning values to address state variables
contracts/Faucet.sol Line 14: usd = IERC20(_usd);
contracts/GovNFT.sol Line 38: endpoint = ILayerZeroEndpoint(_endpoint);
contracts/GovNFTBridged.sol Line 36: endpoint = ILayerZeroEndpoint(_endpoint);
contracts/Lock.sol Line 25: bondNFT = IBondNFT(_bondNFTAddress);
contracts/Lock.sol Line 26: govNFT = IGovNFT(_govNFT);
contracts/NFTSale.sol Line 27: nft = _nft;
contracts/NFTSale.sol Line 28: token = _token;
contracts/StableVault.sol Line 36: stable = _stable;
contracts/Trading.sol Line 149: position = IPosition(_position);
contracts/Trading.sol Line 150: gov = IGovNFT(_gov);
contracts/Trading.sol Line 151: pairsContract = IPairsContract(_pairsContract);
contracts/TradingExtension.sol Line 35: trading = _trading;
contracts/TradingExtension.sol Line 36: pairsContract = IPairsContract(_pairsContract);
contracts/TradingExtension.sol Line 37: referrals = IReferrals(_ref);
contracts/TradingExtension.sol Line 38: position = IPosition(_position);
### Proof of Concept
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Faucet.sol#L14
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L38
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFTBridged.sol#L36
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Lock.sol#L25-L26
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/NFTSale.sol#L27-L28
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/StableVault.sol#L36
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L149-L151
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/TradingExtension.sol#L35-L38
### Recommended Mitigation Steps
Add zero check
