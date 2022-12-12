
### 1st Bug
Unsafe ERC20 Operation(s)

#### Impact
Issue Information: 
ERC20 operations can be unsafe due to different implementations and vulnerabilities in the standard.

It is therefore recommended to always either use OpenZeppelin's SafeERC20 library or at least to wrap each operation in a require statement.

To circumvent ERC20's approve functions race-condition vulnerability use OpenZeppelin's SafeERC20 library's safe{Increase|Decrease}Allowance functions.

In case the vulnerability is of no danger for your implementation, provide enough documentation explaining the reasonings.

Example
🤦 Bad:

IERC20(token).transferFrom(msg.sender, address(this), amount);
🚀 Good (using OpenZeppelin's SafeERC20):

import {SafeERC20} from "openzeppelin/token/utils/SafeERC20.sol";

// ...

IERC20(token).safeTransferFrom(msg.sender, address(this), amount);
🚀 Good (using require):

bool success = IERC20(token).transferFrom(msg.sender, address(this), amount);
require(success, "ERC20 transfer failed");

#### Findings:
```
2022-12-tigris/contracts/GovNFTBridged.sol::235 => IERC20(_tigAsset).transfer(_msgsender, amount);
2022-12-tigris/contracts/GovNFTBridged.sol::240 => try IERC20(_tigAsset).transferFrom(_msgSender(), address(this), _amount) {
2022-12-tigris/contracts/NFTSale.sol::43 => token.transferFrom(msg.sender, owner(), _tokenAmount);
2022-12-tigris/contracts/NFTSale.sol::55 => token.transfer(owner(), token.balanceOf(address(this)));
2022-12-tigris/contracts/NFTSale.sol::69 => IERC20(_tigAsset).transfer(owner(), IERC20(_tigAsset).balanceOf(address(this)));
2022-12-tigris/contracts/mock/WETH.sol::24 => payable(msg.sender).transfer(wad);
```
#### Tools used
Manual Code review

### 2nd Bug
Unspecific Compiler Version Pragma

#### Impact
Issue Information: 
Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

Example
🤦 Bad:

pragma solidity ^0.8.0;
🚀 Good:

pragma solidity 0.8.4;

#### Findings:
```
2022-12-tigris/contracts/BondNFT.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/Faucet.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/Forwarder.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/GovNFT.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/GovNFTBridged.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/Lock.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/NFTSale.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/PairsContract.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/Position.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/Referrals.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/StableToken.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/StableVault.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/Timelock.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/Trading.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/TradingExtension.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/interfaces/IBondNFT.sol::2 => pragma solidity ^0.8.9;
2022-12-tigris/contracts/interfaces/IGovNFT.sol::3 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/interfaces/ILayerZeroEndpoint.sol::3 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/interfaces/ILayerZeroReceiver.sol::3 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/interfaces/ILayerZeroUserApplicationConfig.sol::3 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/interfaces/IPairsContract.sol::3 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/interfaces/IPosition.sol::3 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/interfaces/IReferrals.sol::3 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/interfaces/IStableVault.sol::3 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/interfaces/ITrading.sol::5 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/mock/BadStableVault.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/mock/MetaContextTest.sol::3 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/mock/MockChainlinkFeed.sol::3 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/mock/MockERC20.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/mock/MockEndpoint.sol::3 => pragma solidity ^0.8.4;
2022-12-tigris/contracts/mock/NoTransferToken.sol::8 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/mock/NoTransferToken.sol::35 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/mock/NoTransferToken.sol::113 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/mock/NoTransferToken.sol::198 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/mock/NoTransferToken.sol::228 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/mock/NoTransferToken.sol::586 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/mock/WETH.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/utils/ExcessivelySafeCall.sol::2 => pragma solidity >=0.7.6;
2022-12-tigris/contracts/utils/MetaContext.sol::2 => pragma solidity ^0.8.0;
2022-12-tigris/contracts/utils/TradingLibrary.sol::2 => pragma solidity ^0.8.0;
```
#### Tools used
Manual Code review