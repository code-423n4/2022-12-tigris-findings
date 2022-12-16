# Tigris Trade Low Risk and Non-Critical Issues
## Summary
| Risk      | Title | File | Instances
| ----------- | ----------- | ----------- | ----------- |
| L-00      | Unlocked pragma | - | 22 |
| L-01      | Ownable: Owner can renounce ownership | - | 6 |
| L-02      | Ownable: Does not implement 2-Step-Process for transferring ownership | - | 6 |
| L-03      | Only approve the amount of ERC20 that needs to be transferred | - | 3 |
| L-04      | Missing check that _baseFundingRate <= maxBaseFundingRate | PairsContract.sol | 1 |
| L-05      | Chainlink Feed: `latestAnswer` function is deprecated | TradingLibrary.sol | 1 |
| L-06      | Not approving `gov` to transfer funds in `_handleOpenFees` function leads to small loss of fees | Trading.sol | 1 |
| L-07      | Use `call` instead of `transfer` | Trading.sol | 1 |
| L-08      | Remove `maxWinPercent` check | Trading.sol | 1 |
| N-00      | Lock.lock should return bond id | Lock.sol | 1 |
| N-01      | Use Solidity units | - | 3 |
| N-02      | Variables can be declared `immutable` | - | 4 |
| N-03      | Redundant type cast | PairsContract.sol | 1 |
| N-04      | Allow SL and TP of Stop and Limit orders to be modified | Trading.sol | 1 |
| N-05      | Remove deprecated `initRefs` function | Referrals.sol | 1 |
| N-06      | Lines too long | - | 23 |

## [L-00] Unlocked pragma
Currently the Solidity source files will compile with any version `>=0.8.0` and `<0.9.0`.  
It is best practice to use a fixed compiler version.  

There are 22 instances of this issue:  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L2](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L2)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/TradingExtension.sol#L2](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/TradingExtension.sol#L2)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/utils/TradingLibrary.sol#L2](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/utils/TradingLibrary.sol#L2)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L2](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L2)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L2](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L2)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Referrals.sol#L2](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Referrals.sol#L2)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L2](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L2)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/StableToken.sol#L2](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/StableToken.sol#L2)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/StableVault.sol#L2](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/StableVault.sol#L2)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Lock.sol#L2](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Lock.sol#L2)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L2](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L2)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/utils/MetaContext.sol#L2](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/utils/MetaContext.sol#L2)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/IBondNFT.sol#L2](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/IBondNFT.sol#L2)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/IGovNFT.sol#L3](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/IGovNFT.sol#L3)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/ILayerZeroEndpoint.sol#L3](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/ILayerZeroEndpoint.sol#L3)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/ILayerZeroReceiver.sol#L3](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/ILayerZeroReceiver.sol#L3)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/ILayerZeroUserApplicationConfig.sol#L3](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/ILayerZeroUserApplicationConfig.sol#L3)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/IPairsContract.sol#L3](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/IPairsContract.sol#L3)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/IPosition.sol#L3](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/IPosition.sol#L3)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/IReferrals.sol#L3](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/IReferrals.sol#L3)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/IStableVault.sol#L3](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/IStableVault.sol#L3)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/ITrading.sol#L5](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/ITrading.sol#L5)  

## [L-01] Ownable: Owner can renounce ownership
Multiple of the in-scope contracts inherit from openzeppelin's `Ownable` contract.  
The contract contains the `renounceOwnership` function ([https://github.com/OpenZeppelin/openzeppelin-contracts/blob/3d7a93876a2e5e1d7fe29b5a0e96e222afdc4cfa/contracts/access/Ownable.sol#L61](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/3d7a93876a2e5e1d7fe29b5a0e96e222afdc4cfa/contracts/access/Ownable.sol#L61)) that allows the owner of the contract to transfer ownership to the zero address. So ownership of the contract is lost.  

You should consider implementing this function in the child contracts to disable it, i.e. override it and revert when it is called.  

There are 6 instances of this issue:  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/TradingExtension.sol#L10](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/TradingExtension.sol#L10)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L8](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L8)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Referrals.sol#L7](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Referrals.sol#L7)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Lock.sol#L10](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Lock.sol#L10)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L8](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L8)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/utils/MetaContext.sol#L6](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/utils/MetaContext.sol#L6)  

## [L-02] Ownable: Does not implement 2-Step-Process for transferring ownership
Multiple of the in-scope contracts inherit from openzeppelin's `Ownable` contract.  
This contract does not implement a `2-Step-Process` for transferring ownership.  
So ownership of a contract can easily be lost when making a mistake when transferring ownership.  

Consider using the `Ownable2Step` contract ([https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable2Step.sol](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable2Step.sol)) instead.  

**Note:**  
Switching to `Ownable2Step` does not solve the [L-01] issue. So also override and disable the `renounceOwnership` function.  

There are 6 instances of this issue:  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/TradingExtension.sol#L10](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/TradingExtension.sol#L10)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L8](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L8)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Referrals.sol#L7](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Referrals.sol#L7)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Lock.sol#L10](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Lock.sol#L10)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L8](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L8)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/utils/MetaContext.sol#L6](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/utils/MetaContext.sol#L6)  

## [L-03] Only approve the amount of ERC20 that needs to be transferred
The `Lock.claimGovFees` function approves `type(uint256).max` to be transferred by the `bondNFT` ([https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Lock.sol#L117](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Lock.sol#L117)).  

Currently this is not an issue but it is a risk if there are changes made to the project in the future. It could allow the `bondNFT` to transfer more tokens than it should be.  

Instead only the amount `balanceAfter - balanceBefore` should be approved.  

There are also instances of this in `Trading.sol` where it is better to approve an exact amount:  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L652](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L652)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L807](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L807)  

## [L-04] Missing check that _baseFundingRate <= maxBaseFundingRate
[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L48-L65](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L48-L65)  

The `PairsContract.addAsset` function does not check that the `_baseFundingRate` is not bigger than the `maxBaseFundingRate`.  

This can lead to large funding fees if a large funding rate is set by mistake.  

Fix:  
```solidity
         require(_assetName.length == 0, "Already exists");
         require(bytes(_name).length > 0, "No name");
         require(_maxLeverage >= _minLeverage && _minLeverage > 0, "Wrong leverage values");
+        require(_baseFundingRate <= maxBaseFundingRate, "baseFundingRate too high");
 
         allowedAsset[_asset] = true;
         _idToAsset[_asset].name = _name;
```

## [L-05] Chainlink Feed: `latestAnswer` function is deprecated
The `TradingLibrary.verifyPrice` function uses the `latestAnswer` function to get the latest price from the Chainlink Feed:  
[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/utils/TradingLibrary.sol#L113](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/utils/TradingLibrary.sol#L113)  

According to the Chainlink documentation, the `latestAnswer` function is deprecated and should no longer be used:  
[https://docs.chain.link/data-feeds/price-feeds/api-reference/#latestanswer](https://docs.chain.link/data-feeds/price-feeds/api-reference/#latestanswer)  

Instead you should use the `latestRoundData` function.  
Apart from the `answer` which is the price, the `latestRoundData` function also returns the `roundId` and `updatedAt`.  
Both these values should be checked to make sure that the price is up-to-date.  

If `roundId` and `updatedAt` are not checked, the off-chain oracle might provide valid price data but the Chainlink price feed can be stale and therefore the difference between the two prices are so big that the price data is not accepted.  

So the off-chain oracle price should only be checked against the Chainlink Feed price if the Chainlink Feed price is not stale.  

## [L-06] Not approving `gov` to transfer funds in `_handleOpenFees` function leads to small loss of fees
The `Trading._handleOpenFees` function ([https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L689-L750](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L689-L750)) calls the `gov.distribute` function without approving the `gov` contract to transfer funds from the `Trading` contract.  

This means the `gov` contract cannot distribute fees and will `return` without doing anything ([https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L292](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L292)).  

The place where the `gov` contract is approved to transfer funds is in the `Trading._handleCloseFees` function. There the `type(uint).max` amount is approved ([https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L808](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L808)). So when the contracts are first deployed the `gov` will not receive fees until the first time that `Trading._handleCloseFees` is called which is obviously after `Trading._handleOpenFees` is called.  

Any fees generated before that can be lost. E.g. if `Trading._handleDeposit` is called which burns any balance the `Trading` contract has ([https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L655](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L655)).  

Since funds can only be lost when the contracts are first deployed and the amount lost is probably very small, this is a Low severity finding.  

The fix for this issue is to move the token approval from the `Trading._handleCloseFees` function into the `Trading._handleOpenFees` function.  

## [L-07] Use `call` instead of `transfer`
**Note**: While the code that is referenced here is also mentioned in the automated findings of this contest, the automation tool has mistakenly recognized this as a ERC20 operation.  

The `transfer` function allows the callee to spend only 2300 Gas. This was introduced in order to mitigate Reentrancy attacks.  

However using the `transfer` function introduces issues for two reasons:
1. Function call reverts if callee runs code upon receiving ETH that uses more than 2300 Gas
2. Gas costs may change significantly in the future so using `transfer` may break code that runs successfully now

For the two reasons stated above, it must be assumed that calling the `transfer` function can fail which leads to DOS in the affected contract.  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L588](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L588)  

Fix:  
```solidity
(bool success, ) = payable(_proxy).call{value: msg.value}("");
require(success, "Transfer Failed");
```

## [L-08] Remove `maxWinPercent` check
The `Trading._closePosition` function only pays out winning trades that don't exceed a certain percentage of `trade.margin`. E.g. a trader might only gain 500% of the margin that is associated with the trade.  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L624-L627](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L624-L627)  

If a winning trade has won so much that the `maxWinPercent` is exceeded, the trader can bypass this restriction by taking a flash loan, increasing the margin via the `Trading.addMargin` function, closing the trade and then paying back the flash loan.  

So the `maxWinPercent` does only punish the traders that do not know how to bypass it.  

The reason why the `maxWinPercent` exists is to mitigate the risk of the Vaults to run out of assets.  

However there are more effective measures. E.g. the funding rate can be increased such that the open interest does not get out of balance so much as to make it a risk.  

## [N-00] Lock.lock should return bond id
Currently the `Lock.lock` ([https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Lock.sol#L61](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Lock.sol#L61)) function does not return anything.  
It should however return the id of the bond that is created.  

At the moment a second call to `BondNFT.balanceIds` is necessary to get the bond ids owned by a user.  

This makes interacting with the `Lock` contract more complex than it needs to be and is also more gas consuming than just returning the bond id.  

So the line `bondNFT.createLock( _asset, _amount, _period, msg.sender);` should be changed to `return bondNFT.createLock( _asset, _amount, _period, msg.sender);`.  

## [N-01] Use Solidity units
Solidity offers a variety of units that improve code readability:  
[https://docs.soliditylang.org/en/v0.8.17/units-and-global-variables.html](https://docs.soliditylang.org/en/v0.8.17/units-and-global-variables.html)  

You should use those instead of calculating units with integers.  

### Use `1 days` instead of `24 * 60 * 60`
[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L10](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L10)  

### Use `1000 gwei` instead of `1000000000000`
[https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/TradingExtension.sol#L26](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/TradingExtension.sol#L26)  

### Use `365 days` instead of `31536000`
[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L120](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L120)  

## [N-02] Variables can be declared `immutable`
Variables that are only assigned in the constructor can and should be declared immutable to make the intent clear that they cannot be changed after initial assignment.  

[https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/TradingExtension.sol#L13](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/TradingExtension.sol#L13)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L122](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L122)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L123](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L123)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L124](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L124)  

## [N-03] Redundant cast
Casting a variable to the same type it already has is redundant.  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L190](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L190)  

## [N-04] Allow SL and TP of Stop and Limit orders to be modified
The `Trading.updateTpSl` function ([https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L451-L473](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L451-L473)) allows the TP and SL price of a trade to modified. However it is required that the `orderType == 0`. This makes sure that only TP and SL of trades that are already executed can be modified.  

This restriction limits the flexibility of the application unnecessarily. There is no downside to allowing TP and SL of Limit and Stop orders to be modified and it brings about the upside of more flexbility for the users.  

Currently if a user has a pending order (Limit or Stop) and wants to modify the TP or SL, he has to cancel the order and create a new one.  

So the line `if (_trade.orderType != 0) revert("4");` should be removed.  

## [N-05] Remove deprecated `initRefs` function
According to [this](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Referrals.sol#L58) comment the `Referrals.initRefs` function ([https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Referrals.sol#L60-L76](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Referrals.sol#L60-L76)) is deprecated.  

It should be removed in order to save Gas and also because it is an unsafe way of accessing the `_referral` mapping.  

## [N-06] Lines too long
The maximum line length should be 164 characters.  
That's because GiHub will not display lines longer than that on the screen without scrolling.  

There are 23 instances of this.  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L643](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L643)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L624](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L624)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L543](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L543)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L496](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L496)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/utils/TradingLibrary.sol#L62](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/utils/TradingLibrary.sol#L62)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/utils/TradingLibrary.sol#L36](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/utils/TradingLibrary.sol#L36)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L233](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L233)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L153](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L153)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L109](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L109)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L107](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L107)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L104](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L104)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L102](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L102)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L99](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L99)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L62](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L62)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L55](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L55)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L48](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L48)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L48](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L48)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L175](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L175)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L178](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L178)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/ILayerZeroEndpoint.sol#L41](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/ILayerZeroEndpoint.sol#L41)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/ILayerZeroEndpoint.sol#L24](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/ILayerZeroEndpoint.sol#L24)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/ILayerZeroEndpoint.sol#L15](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/ILayerZeroEndpoint.sol#L15)  

[https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/IPosition.sol#L52](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/IPosition.sol#L52)  

