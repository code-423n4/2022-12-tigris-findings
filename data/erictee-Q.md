### [L-01] ```approve()``` and ```safeApprove()``` should be replaced with ```safeIncreaseAllowance()``` / ```safeDecreaseAllowance()```


#### Impact
```approve()``` & ```safeApprove()``` are deprecated and subject to a known front-running attack. Consider using  ```safeIncreaseAllowance()``` & ```safeDecreaseAllowance()``` instead.


#### Findings:
```
contracts/Lock.sol:L117                     IERC20(assets[i]).approve(address(bondNFT), type(uint256).max);

contracts/Trading.sol:L652                     IERC20(_marginAsset).approve(_stableVault, type(uint).max);

contracts/Trading.sol:L807                 IStable(_tigAsset).approve(address(gov), type(uint).max);

```

### [L-02] ```decimals()``` not part of ERC20 standard.


#### Impact
```decimals()``` is not part of the official ERC20 standard and might fall for tokens that do not implement it. While in practice it is very unlikely, as usually most of the tokens implement it, this should still be considered as a potential issue.


#### Findings:
```
contracts/StableVault.sol:L49            _amount*(10**(18-IERC20Mintable(_token).decimals()))

contracts/StableVault.sol:L67        _output = _amount/10**(18-IERC20Mintable(_token).decimals());

contracts/Trading.sol:L650            uint _marginDecMultiplier = 10**(18-ExtendedIERC20(_marginAsset).decimals());

contracts/Trading.sol:L675            if (IERC20(_outputToken).balanceOf(address(this)) != _balBefore + _toMint/(10**(18-ExtendedIERC20(_outputToken).decimals()))) revert BadWithdraw();

contracts/utils/TradingLibrary.sol:L115                uint256 assetChainlinkPrice = uint256(assetChainlinkPriceInt) * 10**(18 - IPrice(_chainlinkFeed).decimals());

```

### [L-03] Avoid floating pragmas for non-library contracts.


#### Impact
While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

#### Findings:
```
contracts/Lock.sol:L2     pragma solidity ^0.8.0;

contracts/BondNFT.sol:L2     pragma solidity ^0.8.0;

contracts/StableToken.sol:L2     pragma solidity ^0.8.0;

contracts/StableVault.sol:L2     pragma solidity ^0.8.0;

contracts/Position.sol:L2     pragma solidity ^0.8.0;

contracts/TradingExtension.sol:L2     pragma solidity ^0.8.0;

contracts/Referrals.sol:L2     pragma solidity ^0.8.0;

contracts/PairsContract.sol:L2     pragma solidity ^0.8.0;

contracts/GovNFT.sol:L2     pragma solidity ^0.8.0;

contracts/Trading.sol:L2     pragma solidity ^0.8.0;

contracts/utils/MetaContext.sol:L2     pragma solidity ^0.8.0;

contracts/utils/TradingLibrary.sol:L2     pragma solidity ^0.8.0;

contracts/interfaces/IStableVault.sol:L3     pragma solidity ^0.8.0;

contracts/interfaces/IPosition.sol:L3     pragma solidity ^0.8.0;

contracts/interfaces/ILayerZeroUserApplicationConfig.sol:L3     pragma solidity ^0.8.0;

contracts/interfaces/IReferrals.sol:L3     pragma solidity ^0.8.0;

contracts/interfaces/ILayerZeroEndpoint.sol:L3     pragma solidity ^0.8.0;

contracts/interfaces/ITrading.sol:L5     pragma solidity ^0.8.0;

contracts/interfaces/IBondNFT.sol:L2     pragma solidity ^0.8.9;

contracts/interfaces/ILayerZeroReceiver.sol:L3     pragma solidity ^0.8.0;

contracts/interfaces/IPairsContract.sol:L3     pragma solidity ^0.8.0;

contracts/interfaces/IGovNFT.sol:L3     pragma solidity ^0.8.0;

```

### [L-04] ```_safemint()``` should be used rather than ```_mint()``` wherever possible


#### Impact
```_mint()``` is [discouraged](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271) in favor of ```_safeMint()``` which ensures that the recipient is either an EOA or implements ```IERC721Receiver```. Both [OpenZeppelin](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L238-L250) and [solmate](https://github.com/transmissions11/solmate/blob/4eaf6b68202e36f67cab379768ac6be304c8ebde/src/tokens/ERC721.sol#L180) have versions of this function


#### Findings:
```
contracts/BondNFT.sol:L83            _mint(_owner, _bond);

contracts/BondNFT.sol:L306    function _mint(

contracts/BondNFT.sol:L313        _mint(to, bond.id);

contracts/StableToken.sol:L32        _mint(account, amount);

contracts/GovNFT.sol:L50    function _mint(address to, uint256 tokenId) internal override {

contracts/GovNFT.sol:L56        super._mint(to, tokenId);

contracts/GovNFT.sol:L70        super._mint(to, tokenId);

contracts/GovNFT.sol:L106            _mint(_msgSender(), counter);

contracts/GovNFT.sol:L111        _mint(_msgSender(), counter);

```
