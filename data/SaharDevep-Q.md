
### Unsafe ERC20 Operation(s)

#### Impact
Issue Information: [L001](https://github.com/byterocket/c4-common-issues/blob/main/2-Low-Risk.md#l001---unsafe-erc20-operations)

#### Findings:
```
examples\BondNFT.sol::185 => IERC20(tigAsset).transfer(manager, amount);
examples\BondNFT.sol::202 => IERC20(_tigAsset).transfer(manager, amount);
examples\BondNFT.sol::216 => IERC20(_tigAsset).transferFrom(_msgSender(), address(this), _amount);
examples\GovNFT.sol::279 => IERC20(_tigAsset).transfer(_msgsender, amount);
examples\GovNFT.sol::289 => try IERC20(_tigAsset).transferFrom(_msgSender(), address(this), _amount) {
examples\GovNFTBridged.sol::235 => IERC20(_tigAsset).transfer(_msgsender, amount);
examples\GovNFTBridged.sol::240 => try IERC20(_tigAsset).transferFrom(_msgSender(), address(this), _amount) {
examples\Lock.sol::39 => IERC20(_tigAsset).transfer(msg.sender, _amount);
examples\Lock.sol::52 => IERC20(_tigAsset).transfer(msg.sender, amount);
examples\Lock.sol::72 => IERC20(_asset).transferFrom(msg.sender, address(this), _amount);
examples\Lock.sol::90 => IERC20(_asset).transferFrom(msg.sender, address(this), _amount);
examples\Lock.sol::104 => IERC20(asset).transfer(_owner, amount);
examples\Lock.sol::117 => IERC20(assets[i]).approve(address(bondNFT), type(uint256).max);
examples\NFTSale.sol::43 => token.transferFrom(msg.sender, owner(), _tokenAmount);
examples\NFTSale.sol::55 => token.transfer(owner(), token.balanceOf(address(this)));
examples\NFTSale.sol::69 => IERC20(_tigAsset).transfer(owner(), IERC20(_tigAsset).balanceOf(address(this)));
examples\StableVault.sol::46 => IERC20(_token).transferFrom(_msgSender(), address(this), _amount);
examples\StableVault.sol::68 => IERC20(_token).transfer(
examples\Trading.sol::588 => payable(_proxy).transfer(msg.value);
examples\Trading.sol::651 => IERC20(_marginAsset).transferFrom(_trader, address(this), _margin/_marginDecMultiplier);
examples\Trading.sol::652 => IERC20(_marginAsset).approve(_stableVault, type(uint).max);
examples\Trading.sol::671 => IERC20(_outputToken).transfer(_trade.trader, _toMint);
examples\Trading.sol::676 => IERC20(_outputToken).transfer(_trade.trader, IERC20(_outputToken).balanceOf(address(this)) - _balBefore);
examples\Trading.sol::807 => IStable(_tigAsset).approve(address(gov), type(uint).max);
examples\mock\WETH.sol::24 => payable(msg.sender).transfer(wad);
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Unspecific Compiler Version Pragma

#### Impact
Issue Information: [L003](https://github.com/byterocket/c4-common-issues/blob/main/2-Low-Risk.md#l003---unspecific-compiler-version-pragma)

#### Findings:
```
examples\BondNFT.sol::2 => pragma solidity ^0.8.0;
examples\Faucet.sol::2 => pragma solidity ^0.8.0;
examples\Forwarder.sol::2 => pragma solidity ^0.8.0;
examples\GovNFT.sol::2 => pragma solidity ^0.8.0;
examples\GovNFTBridged.sol::2 => pragma solidity ^0.8.0;
examples\Lock.sol::2 => pragma solidity ^0.8.0;
examples\NFTSale.sol::2 => pragma solidity ^0.8.0;
examples\PairsContract.sol::2 => pragma solidity ^0.8.0;
examples\Position.sol::2 => pragma solidity ^0.8.0;
examples\Referrals.sol::2 => pragma solidity ^0.8.0;
examples\StableToken.sol::2 => pragma solidity ^0.8.0;
examples\StableVault.sol::2 => pragma solidity ^0.8.0;
examples\Timelock.sol::2 => pragma solidity ^0.8.0;
examples\Trading.sol::2 => pragma solidity ^0.8.0;
examples\TradingExtension.sol::2 => pragma solidity ^0.8.0;
examples\interfaces\IBondNFT.sol::2 => pragma solidity ^0.8.9;
examples\interfaces\IGovNFT.sol::3 => pragma solidity ^0.8.0;
examples\interfaces\ILayerZeroEndpoint.sol::3 => pragma solidity ^0.8.0;
examples\interfaces\ILayerZeroReceiver.sol::3 => pragma solidity ^0.8.0;
examples\interfaces\ILayerZeroUserApplicationConfig.sol::3 => pragma solidity ^0.8.0;
examples\interfaces\IPairsContract.sol::3 => pragma solidity ^0.8.0;
examples\interfaces\IPosition.sol::3 => pragma solidity ^0.8.0;
examples\interfaces\IReferrals.sol::3 => pragma solidity ^0.8.0;
examples\interfaces\IStableVault.sol::3 => pragma solidity ^0.8.0;
examples\interfaces\ITrading.sol::5 => pragma solidity ^0.8.0;
examples\mock\BadStableVault.sol::2 => pragma solidity ^0.8.0;
examples\mock\MetaContextTest.sol::3 => pragma solidity ^0.8.0;
examples\mock\MockChainlinkFeed.sol::3 => pragma solidity ^0.8.0;
examples\mock\MockERC20.sol::2 => pragma solidity ^0.8.0;
examples\mock\MockEndpoint.sol::3 => pragma solidity ^0.8.4;
examples\mock\NoTransferToken.sol::8 => pragma solidity ^0.8.0;
examples\mock\NoTransferToken.sol::35 => pragma solidity ^0.8.0;
examples\mock\NoTransferToken.sol::113 => pragma solidity ^0.8.0;
examples\mock\NoTransferToken.sol::198 => pragma solidity ^0.8.0;
examples\mock\NoTransferToken.sol::228 => pragma solidity ^0.8.0;
examples\mock\NoTransferToken.sol::586 => pragma solidity ^0.8.0;
examples\mock\WETH.sol::2 => pragma solidity ^0.8.0;
examples\utils\ExcessivelySafeCall.sol::2 => pragma solidity >=0.7.6;
examples\utils\MetaContext.sol::2 => pragma solidity ^0.8.0;
examples\utils\TradingLibrary.sol::2 => pragma solidity ^0.8.0;
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

