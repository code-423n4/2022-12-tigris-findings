# 1. Lines too long

In general, it is a good practice to keep lines of source code within 80 characters in length.

However, some flexibility is allowed and it is reasonable to allow lines to be up to 120 characters in certain cases.

On modern screens, it is even possible to go beyond this limit.

However, it is recommended to split lines when they reach a length of 164 characters or more, as this is the point at which GitHub will introduce a scroll bar to view the code.

This can help to make the code more readable and easier to work with.

This is present in 2 files:

1. [Trading.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol)

   - [Trading.sol#L496](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L496)

   - [Trading.sol#L543](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L543)

   - [Trading.sol#L643](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L643)

2. [Position.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol)

   - [Position.sol#L99-L109](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L99-L109)

# 2. Unspecific Compiler Version Pragma

It is generally not recommended to use floating pragmas (i.e. pragmas that do not specify a specific compiler version) in contracts that are not intended to be used as libraries.

This is because using floating pragmas in application contracts can pose a security risk.

For example, a known vulnerable compiler version may be selected by mistake, or security tools might revert to an older compiler version that produces a different EVM compilation than the one intended to be deployed on the blockchain.

To avoid these potential issues, it is recommended to specify a specific compiler version in your pragmas.

As an example, instead of using a floating pragma like `pragma solidity ^0.8.0;`, it is better to use a concrete compiler version like `pragma solidity 0.8.4;`.

This is present in 22 files:

- ### contracts/

  1. [TradingExtension.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/TradingExtension.sol#L2)

  2. [Trading.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L2)

  3. [Position.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L2)

  4. [PairsContract.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/PairsContract.sol#L2)

  5. [contracts/Referrals.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Referrals.sol#L2)

  6. [GovNFT.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L2)

  7. [StableToken.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/StableToken.sol#L2)

  8. [StableVault.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/StableVault.sol#L2)

  9. [Lock.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Lock.sol#L2)

  10. [BondNFT.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L2)

- ### contracts/interfaces/

  1. [IBondNFT.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/IBondNFT.sol#L2)

  2. [IGovNFT.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/IGovNFT.sol#L3)

  3. [ILayerZeroEndpoint.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/ILayerZeroEndpoint.sol#L3)

  4. [ILayerZeroReceiver.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/ILayerZeroReceiver.sol#L3)

  5. [ILayerZeroUserApplicationConfig.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/ILayerZeroUserApplicationConfig.sol#L3)

  6. [IPairsContract.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/IPairsContract.sol#L3)

  7. [IPosition.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/IPosition.sol#L3)

  8. [IReferrals.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/IReferrals.sol#L3)

  9. [IStableVault.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/IStableVault.sol#L3)

  10. [ITrading.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/ITrading.sol#L5)

- ### contracts/utils/

  1. [TradingLibrary.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/utils/TradingLibrary.sol#L2)

  2. [MetaContext.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/utils/MetaContext.sol#L2)

# 3. Use mixedCase

It is recommended to use the mixedCase naming convention for improving the readability of source code.

This convention involves combining words with no spaces, with the first letter of each word capitalized except for the first word.

For more information, see the Solidity style guide at the following links:

- [Naming Convention For Function Names](https://docs.soliditylang.org/en/v0.5.3/style-guide.html#function-names)

- [Naming Convention For Local and State Variable Names](https://docs.soliditylang.org/en/v0.5.3/style-guide.html#local-and-state-variable-names.)

This is present in 1 file:

1. [GovNFT.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol)
   - [GovNFT.sol#L276](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L276)

Note: although naming `_msgsender` to `_msgSender` in [GovNFT.sol#L276](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L276) would not work due to naming conflicts you could name it as `_messageSender` or something else that follows the mixedCase convention

# 4. Typos

1. [Lock.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Lock.sol)

   - [Lock.sol#L135](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Lock.sol#L135)

```
Line 135:    * @notice Owner can retreive Gov NFTs
```

2. [PairsContract.sol](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/PairsContract.sol)

   - [PairsContract.sol#L44](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/PairsContract.sol#L44)

   - [PairsContract.sol#L151](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/PairsContract.sol#L151)

   - [PairsContract.sol#L171](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/PairsContract.sol#L171)

```
Line  44:    * @param _maxLeverage maximimum allowed leverage
Line 151:    * @param _onOpen true if adding to open interesr
Line 171:    * @param _onOpen true if adding to open interesr
```

Consider changing:

- `retreive` To `retrieve`
- `maximimum` To `maximum`
- `interesr` To `interest`
