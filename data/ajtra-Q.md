# LOW
1. L01. Use call instead of transfer for address payable

# Non critical
2. NC01. Floating pragma
3. NC02. Shadowed variable name owner between Ownable.owner and GovNFT owner
4. NC03. Use of return and the name return is confusing
5. NC04. It's not necessary to use true or false
6. NC05. Requires that check input parameter should be at the beginning
7. NC06. Missing NatSpec File
8. NC07. Lack of functions NatSpec
9. NC08. Lines are too long

# L01. Use call instead of transfer for address payable
## Description
It is strongly recommended to avoid using payable.transfer, since it can cause the transaction to fail when:

+ The calling contract does not have payable function
+ The calling contract have a payable function but spends more than 2300 gas
+ The calling contract have a payable function and spends less than 2300 gas but is called through proxy which uses over 2300 gas.

Also it might be mandatory for some multisig wallets to use higher than 2300 gas.

## Mitigation
Use call instead of transfer

## Lines in the code
[Trading.sol#L588](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L588)


# NC01. Floating pragma
## Description
Contracts have the pragma solidity directive ^0.8.0. It is recommended to specify a fixed compiler version to ensure that the bytecode produced 
does not vary between builds. 
This is especially important if you rely on bytecode-level verification of the code.

## Mitigation
Lock the pragma.

## Lines in the code
[Trading.sol#L2](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L2)
[TradingExtension.sol#L2](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/TradingExtension.sol#L2)
[TradingLibrary.sol#L2](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/utils/TradingLibrary.sol#L2)
[Position.sol#L2](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L2)
[PairsContract.sol#L2](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/PairsContract.sol#L2)
[Referrals.sol#L2](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Referrals.sol#L2)
[GovNFT.sol#L2](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L2)
[StableToken.sol#L2](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/StableToken.sol#L2)
[StableVault.sol#L2](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/StableVault.sol#L2)
[Lock.sol#L2](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Lock.sol#L2)
[BondNFT.sol#L2](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L2)
[MetaContext.sol#L2](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/utils/MetaContext.sol#L2)
[IBondNFT.sol#L2](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/IBondNFT.sol#L2)
[IGovNFT.sol#L3](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/IGovNFT.sol#L3)
[ILayerZeroEndpoint.sol#L3](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/ILayerZeroEndpoint.sol#L3)
[ILayerZeroReceiver.sol#L3](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/ILayerZeroReceiver.sol#L3)
[ILayerZeroUserApplicationConfig.sol#L3](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/ILayerZeroUserApplicationConfig.sol#L3)
[IPairsContract.sol#L3](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/IPairsContract.sol#L3)
[IPosition.sol#L3](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/IPosition.sol#L3)
[IReferrals.sol#L3](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/IReferrals.sol#L3)
[IStableVault.sol#L3](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/IStableVault.sol#L3)
[ITrading.sol#L5](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/ITrading.sol#L5)
	
# NC02. Shadowed variable name owner between Ownable.owner and GovNFT owner
## Mitigation
Rename owner name.

## Lines in the code
[GovNFT.sol#L77](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L77)
	
# NC03. Use of return and the name return is confusing
## Description
Function _handleCloseFees in Trading is using the function name return and the return. 

```diff
        payout_ = _payout - _daoFeesPaid - _burnFeesPaid - _botFeesPaid;
        IStable(_tigAsset).mintFor(address(this), _daoFeesPaid);
        IStable(_tigAsset).approve(address(gov), type(uint).max);
        gov.distribute(_tigAsset, _daoFeesPaid);
-       return payout_;
```
## Mitigation
Use just the funtion name return

## Lines in the code
[Trading.sol#L805-L809](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L805-L809)

# NC04. It's not necessary to use true or false
BondNFT 238 y 252

```diff
- bond.expired = bond.expireEpoch <= epoch[bond.asset] ? true : false;
+ bond.expired = bond.expireEpoch <= epoch[bond.asset];
```
[BondNFT.sol#L238](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L238)

```diff
- return bond.expireEpoch <= epoch[bond.asset] ? true : false;
+ return bond.expireEpoch <= epoch[bond.asset];
```
[BondNFT.sol#L252](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L252)


# NC05. Requires that check input parameter should be at the beginning
```diff
        require(tokenId.length > 0, "Not bridging");
+       require(isTrustedAddress[_dstChainId][targetAddress], "!Trusted");
        for (uint i=0; i<tokenId.length; i++) {
            require(_msgSender() == ownerOf(tokenId[i]), "Not the owner");
            // burn NFT
            _burn(tokenId[i]);
        }
        address targetAddress;
        assembly {
            targetAddress := mload(add(_destination, 20))
        }
-       require(isTrustedAddress[_dstChainId][targetAddress], "!Trusted");
```
[GovNFT.sol#L140](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L140)

```diff
            require(_daoFees >= _botFees+_referralFees*2);
+           require(_percent <= DIVISION_CONSTANT);
            if (_open) {
                openFees.daoFees = _daoFees;
                openFees.burnFees = _burnFees;
                openFees.referralFees = _referralFees;
                openFees.botFees = _botFees;
            } else {
                closeFees.daoFees = _daoFees;
                closeFees.burnFees = _burnFees;
                closeFees.referralFees = _referralFees;
                closeFees.botFees = _botFees;                
            }
-           require(_percent <= DIVISION_CONSTANT);
```
[Trading.sol#L966](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L966)
   
# NC06. Missing NatSpec File
[Trading.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol)
[TradingExtension.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/TradingExtension.sol)
[TradingLibrary.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/utils/TradingLibrary.sol)
[Position.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol)
[PairsContract.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/PairsContract.sol)
[Referrals.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Referrals.sol)
[GovNFT.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol)
[StableToken.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/StableToken.sol)
[StableVault.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/StableVault.sol)
[Lock.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Lock.sol)
[BondNFT.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol)
[MetaContext.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/utils/MetaContext.sol)
[IBondNFT.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/IBondNFT.sol)
[IGovNFT.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/IGovNFT.sol)
[ILayerZeroEndpoint.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/ILayerZeroEndpoint.sol)
[ILayerZeroReceiver.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/ILayerZeroReceiver.sol)
[ILayerZeroUserApplicationConfig.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/ILayerZeroUserApplicationConfig.sol)
[IPairsContract.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/IPairsContract.sol)
[IPosition.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/IPosition.sol)
[IReferrals.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/IReferrals.sol)
[IStableVault.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/IStableVault.sol)
[ITrading.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/ITrading.sol)

# NC07. Lack of functions NatSpec
## Description
It is important to include NatSpec in every function for a correct interpretation of the code. 
In the following [link](https://docs.soliditylang.org/en/v0.8.17/natspec-format.html) you can find the documentation.

# NC08 - Lines are too long
## Description
Usually lines in source code are limited to 120 characters (version 0.8.17). 
Today's screens are much larger so it's reasonable to stretch this in some cases. 
Solidity reference in the following [link](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-length)

### Lines in the code
[BondNFT.sol#L178](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L178)
[GovNFT.sol#L175](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L175)
[Trading.sol#L178](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L178)
[Trading.sol#L496](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L496)
[Trading.sol#L520](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L520)
[Trading.sol#L543](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L543)
[Trading.sol#L616](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L616)
[TradingLibrary.sol#L64](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/utils/TradingLibrary.sol#L64)
[TradingLibrary.sol#L66](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/utils/TradingLibrary.sol#L66)

