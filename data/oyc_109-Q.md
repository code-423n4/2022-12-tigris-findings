

## [L-01] Unspecific Compiler Version Pragma

Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

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
```

## [L-02] Use of Block.timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

```
2022-12-tigris/contracts/BondNFT.sol::75 => block.timestamp,// mint timestamp
2022-12-tigris/contracts/BondNFT.sol::110 => require(epoch[bond.asset] == block.timestamp/DAY, "Bad epoch");
2022-12-tigris/contracts/BondNFT.sol::114 => uint expireEpoch = block.timestamp/DAY + bond.period + _period;
2022-12-tigris/contracts/BondNFT.sol::120 => _bond.mintTime = block.timestamp;
2022-12-tigris/contracts/BondNFT.sol::218 => uint aEpoch = block.timestamp / DAY;
2022-12-tigris/contracts/BondNFT.sol::329 => require(epoch[bond.asset] == block.timestamp/DAY, "Bad epoch");
2022-12-tigris/contracts/BondNFT.sol::332 => require(block.timestamp > bond.mintTime + 300, "Recent update");
2022-12-tigris/contracts/BondNFT.sol::354 => epoch[_asset] = block.timestamp/DAY;
2022-12-tigris/contracts/Position.sol::48 => _pendingFunding = (int256(block.timestamp-lastUpdate[_trade.asset][_trade.tigAsset])*fundingDeltaPerSec[_trade.asset][_trade.tigAsset])*1e18/int256(longOi[_trade.asset][_trade.tigAsset]);
2022-12-tigris/contracts/Position.sol::55 => _pendingFunding = (int256(block.timestamp-lastUpdate[_trade.asset][_trade.tigAsset])*fundingDeltaPerSec[_trade.asset][_trade.tigAsset])*1e18/int256(shortOi[_trade.asset][_trade.tigAsset]);
2022-12-tigris/contracts/Position.sol::102 => accInterestPerOi[_asset][_tigAsset][true] += ((int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(longOi[_asset][_tigAsset]))*int256(1e10-vaultFundingPercent[_asset][_tigAsset])/1e10;
2022-12-tigris/contracts/Position.sol::104 => accInterestPerOi[_asset][_tigAsset][false] -= (int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(shortOi[_asset][_tigAsset]);
2022-12-tigris/contracts/Position.sol::107 => accInterestPerOi[_asset][_tigAsset][true] -= (int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(longOi[_asset][_tigAsset]);
2022-12-tigris/contracts/Position.sol::109 => accInterestPerOi[_asset][_tigAsset][false] += ((int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(shortOi[_asset][_tigAsset]))*int256(1e10-vaultFundingPercent[_asset][_tigAsset])/1e10;
2022-12-tigris/contracts/Position.sol::112 => lastUpdate[_asset][_tigAsset] = block.timestamp;
2022-12-tigris/contracts/Trading.sol::132 => mapping(uint => uint) public limitDelay; // id => block.timestamp
2022-12-tigris/contracts/Trading.sol::347 => limitDelay[_id] = block.timestamp + 4;
2022-12-tigris/contracts/Trading.sol::491 => require(block.timestamp >= limitDelay[_id]);
2022-12-tigris/contracts/Trading.sol::887 => require(_proxy.proxy == _msgSender() && _proxy.time >= block.timestamp, "Proxy not approved");
```

## [L-03] decimals() not part of ERC20 standard

decimals() is not part of the official ERC20 standard and might fail for tokens that do not implement it. While in practice it is very unlikely, as usually most of the tokens implement it, this should still be considered as a potential issue.

```
2022-12-tigris/contracts/StableVault.sol::49 => _amount*(10**(18-IERC20Mintable(_token).decimals()))
2022-12-tigris/contracts/StableVault.sol::67 => _output = _amount/10**(18-IERC20Mintable(_token).decimals());
2022-12-tigris/contracts/Trading.sol::650 => uint _marginDecMultiplier = 10**(18-ExtendedIERC20(_marginAsset).decimals());
2022-12-tigris/contracts/Trading.sol::675 => if (IERC20(_outputToken).balanceOf(address(this)) != _balBefore + _toMint/(10**(18-ExtendedIERC20(_outputToken).decimals()))) revert BadWithdraw();
```

## [L-04] approve should be replaced with safeApprove or safeIncreaseAllowance() / safeDecreaseAllowance()

approve is subject to a known front-running attack. Consider using safeApprove() or safeIncreaseAllowance() or safeDecreaseAllowance() instead

```
2022-12-tigris/contracts/Lock.sol::117 => IERC20(assets[i]).approve(address(bondNFT), type(uint256).max);
2022-12-tigris/contracts/Trading.sol::652 => IERC20(_marginAsset).approve(_stableVault, type(uint).max);
2022-12-tigris/contracts/Trading.sol::807 => IStable(_tigAsset).approve(address(gov), type(uint).max);
```

## [L-05] Unsafe use of transfer()/transferFrom() with IERC20

Some tokens do not implement the ERC20 standard properly but are still accepted by most code that accepts ERC20 tokens. For example Tether (USDT)'s transfer() and transferFrom() functions do not return booleans as the specification requires, and instead have no return value. When these sorts of tokens are cast to IERC20, their function signatures do not match and therefore the calls made, revert. Use OpenZeppelin’s SafeERC20's safeTransfer()/safeTransferFrom() instead

```
2022-12-tigris/contracts/BondNFT.sol::185 => IERC20(tigAsset).transfer(manager, amount);
2022-12-tigris/contracts/BondNFT.sol::202 => IERC20(_tigAsset).transfer(manager, amount);
2022-12-tigris/contracts/BondNFT.sol::216 => IERC20(_tigAsset).transferFrom(_msgSender(), address(this), _amount);
2022-12-tigris/contracts/GovNFT.sol::279 => IERC20(_tigAsset).transfer(_msgsender, amount);
2022-12-tigris/contracts/GovNFT.sol::289 => try IERC20(_tigAsset).transferFrom(_msgSender(), address(this), _amount) {
2022-12-tigris/contracts/GovNFTBridged.sol::235 => IERC20(_tigAsset).transfer(_msgsender, amount);
2022-12-tigris/contracts/GovNFTBridged.sol::240 => try IERC20(_tigAsset).transferFrom(_msgSender(), address(this), _amount) {
2022-12-tigris/contracts/Lock.sol::39 => IERC20(_tigAsset).transfer(msg.sender, _amount);
2022-12-tigris/contracts/Lock.sol::52 => IERC20(_tigAsset).transfer(msg.sender, amount);
2022-12-tigris/contracts/Lock.sol::72 => IERC20(_asset).transferFrom(msg.sender, address(this), _amount);
2022-12-tigris/contracts/Lock.sol::90 => IERC20(_asset).transferFrom(msg.sender, address(this), _amount);
2022-12-tigris/contracts/Lock.sol::104 => IERC20(asset).transfer(_owner, amount);
2022-12-tigris/contracts/NFTSale.sol::43 => token.transferFrom(msg.sender, owner(), _tokenAmount);
2022-12-tigris/contracts/NFTSale.sol::55 => token.transfer(owner(), token.balanceOf(address(this)));
2022-12-tigris/contracts/NFTSale.sol::69 => IERC20(_tigAsset).transfer(owner(), IERC20(_tigAsset).balanceOf(address(this)));
2022-12-tigris/contracts/StableVault.sol::46 => IERC20(_token).transferFrom(_msgSender(), address(this), _amount);
2022-12-tigris/contracts/StableVault.sol::68 => IERC20(_token).transfer(
2022-12-tigris/contracts/Trading.sol::651 => IERC20(_marginAsset).transferFrom(_trader, address(this), _margin/_marginDecMultiplier);
2022-12-tigris/contracts/Trading.sol::671 => IERC20(_outputToken).transfer(_trade.trader, _toMint);
2022-12-tigris/contracts/Trading.sol::676 => IERC20(_outputToken).transfer(_trade.trader, IERC20(_outputToken).balanceOf(address(this)) - _balBefore);
```

## [L-06] Events not emitted for important state changes

When changing state variables events are not emitted. Emitting events allows monitoring activities with off-chain monitoring tools.

```
2022-12-tigris/contracts/BondNFT.sol::357 => function setAllowedAsset(address _asset, bool _bool) external onlyOwner {
2022-12-tigris/contracts/BondNFT.sol::362 => function setBaseURI(string calldata _newBaseURI) external onlyOwner {
2022-12-tigris/contracts/BondNFT.sol::366 => function setManager(
2022-12-tigris/contracts/GovNFT.sol::46 => function setBaseURI(string calldata _newBaseURI) external onlyOwner {
2022-12-tigris/contracts/GovNFT.sol::114 => function setTrustedAddress(uint16 _chainId, address _contract, bool _bool) external onlyOwner {
2022-12-tigris/contracts/GovNFT.sol::236 => function setGas(uint _gas) external onlyOwner {
2022-12-tigris/contracts/GovNFT.sol::240 => function setEndpoint(ILayerZeroEndpoint _endpoint) external onlyOwner {
2022-12-tigris/contracts/GovNFT.sol::307 => function setAllowedAsset(address _asset, bool _bool) external onlyOwner {
2022-12-tigris/contracts/GovNFT.sol::311 => function setMaxBridge(uint256 _max) external onlyOwner {
2022-12-tigris/contracts/GovNFTBridged.sol::44 => function setBaseURI(string calldata _newBaseURI) external onlyOwner {
2022-12-tigris/contracts/GovNFTBridged.sol::82 => function setTrustedAddress(uint16 _chainId, address _contract, bool _bool) external onlyOwner {
2022-12-tigris/contracts/GovNFTBridged.sol::196 => function setGas(uint _gas) external onlyOwner {
2022-12-tigris/contracts/GovNFTBridged.sol::200 => function setEndpoint(ILayerZeroEndpoint _endpoint) external onlyOwner {
2022-12-tigris/contracts/GovNFTBridged.sol::258 => function setAllowedAsset(address _asset, bool _bool) external onlyOwner {
2022-12-tigris/contracts/GovNFTBridged.sol::262 => function setMaxBridge(uint256 _max) external onlyOwner {
2022-12-tigris/contracts/NFTSale.sol::32 => function setPrice(uint _price) external onlyOwner {
2022-12-tigris/contracts/NFTSale.sol::63 => function setIds(uint[] calldata _ids) external onlyOwner {
2022-12-tigris/contracts/PairsContract.sol::33 => function setAssetChainlinkFeed(uint256 _asset, address _feed) external onlyOwner {
2022-12-tigris/contracts/PairsContract.sol::92 => function setAssetBaseFundingRate(uint256 _asset, uint256 _baseFundingRate) external onlyOwner {
2022-12-tigris/contracts/PairsContract.sol::125 => function setMaxBaseFundingRate(uint256 _maxBaseFundingRate) external onlyOwner {
2022-12-tigris/contracts/PairsContract.sol::129 => function setProtocol(address _protocol) external onlyOwner {
2022-12-tigris/contracts/PairsContract.sol::139 => function setMaxOi(uint256 _asset, address _tigAsset, uint256 _maxOi) external onlyOwner {
2022-12-tigris/contracts/Position.sol::85 => function setBaseURI(string memory _newBaseURI) external onlyOwner {
2022-12-tigris/contracts/Position.sol::220 => function setAccInterest(uint256 _id) external onlyMinter {
2022-12-tigris/contracts/Position.sol::310 => function setMinter(address _minter, bool _bool) external onlyOwner {
2022-12-tigris/contracts/Referrals.sol::53 => function setProtocol(address _protocol) external onlyOwner {
2022-12-tigris/contracts/StableToken.sol::38 => function setMinter(
2022-12-tigris/contracts/Trading.sol::898 => function setBlockDelay(
2022-12-tigris/contracts/Trading.sol::912 => function setAllowedVault(
2022-12-tigris/contracts/Trading.sol::926 => function setMaxWinPercent(
2022-12-tigris/contracts/Trading.sol::939 => function setLimitOrderPriceRange(uint _range) external onlyOwner {
2022-12-tigris/contracts/Trading.sol::952 => function setFees(bool _open, uint _daoFees, uint _burnFees, uint _referralFees, uint _botFees, uint _percent) external onlyOwner {
2022-12-tigris/contracts/Trading.sol::975 => function setTradingExtension(
2022-12-tigris/contracts/TradingExtension.sol::144 => function setMaxGasPrice(uint _maxGasPrice) external onlyOwner {
2022-12-tigris/contracts/TradingExtension.sol::222 => function setValidSignatureTimer(
2022-12-tigris/contracts/TradingExtension.sol::231 => function setChainlinkEnabled(bool _bool) external onlyOwner {
2022-12-tigris/contracts/TradingExtension.sol::240 => function setNode(address _node, bool _bool) external onlyOwner {
2022-12-tigris/contracts/TradingExtension.sol::249 => function setAllowedMargin(
2022-12-tigris/contracts/TradingExtension.sol::264 => function setMinPositionSize(
2022-12-tigris/contracts/TradingExtension.sol::274 => function setPaused(bool _paused) external onlyOwner {
```

## [L-07] _safeMint() should be used rather than _mint() wherever possible

Some tokens do not implement the ERC20 standard properly but are still accepted by most code that accepts ERC20 tokens. For example Tether (USDT)'s transfer() and transferFrom() functions do not return booleans as the specification requires, and instead have no return value. When these sorts of tokens are cast to IERC20, their function signatures do not match and therefore the calls made, revert. Use OpenZeppelin’s SafeERC20's safeTransfer()/safeTransferFrom() instead

```
2022-12-tigris/contracts/BondNFT.sol::83 => _mint(_owner, _bond);
2022-12-tigris/contracts/BondNFT.sol::306 => function _mint(
2022-12-tigris/contracts/BondNFT.sol::313 => _mint(to, bond.id);
2022-12-tigris/contracts/GovNFT.sol::50 => function _mint(address to, uint256 tokenId) internal override {
2022-12-tigris/contracts/GovNFT.sol::56 => super._mint(to, tokenId);
2022-12-tigris/contracts/GovNFT.sol::70 => super._mint(to, tokenId);
2022-12-tigris/contracts/GovNFT.sol::106 => _mint(_msgSender(), counter);
2022-12-tigris/contracts/GovNFT.sol::111 => _mint(_msgSender(), counter);
2022-12-tigris/contracts/GovNFTBridged.sol::54 => super._mint(to, tokenId);
2022-12-tigris/contracts/StableToken.sol::32 => _mint(account, amount);
```

## [N-01] Use a more recent version of solidity

Use a solidity version of at least 0.8.4 to get bytes.concat() instead of abi.encodePacked(<bytes>,<bytes>)
Use a solidity version of at least 0.8.12 to get string.concat() instead of abi.encodePacked(<str>,<str>)
Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions

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
```

## [N-02] Large multiples of ten should use scientific notation

Use (e.g. 1e6) rather than decimal literals (e.g. 1000000), for better code readability

```
2022-12-tigris/contracts/Faucet.sol::20 => usd.mintFor(msg.sender, 10000e18);
2022-12-tigris/contracts/GovNFT.sol::16 => uint256 private constant MAX = 10000;
2022-12-tigris/contracts/GovNFT.sol::66 => require(tokenId <= 10000, "BadID");
2022-12-tigris/contracts/GovNFTBridged.sol::50 => require(tokenId <= 10000 && tokenId != 0, "BadID");
2022-12-tigris/contracts/TradingExtension.sol::26 => uint public maxGasPrice = 1000000000000; // 1000 gwei
```

## [N-03] Event is missing indexed fields

Each event should use three indexed fields if there are three or more fields

```
2022-12-tigris/contracts/BondNFT.sol::377 => event Distribution(address _tigAsset, uint256 _amount);
2022-12-tigris/contracts/BondNFT.sol::378 => event Lock(address _tigAsset, uint256 _amount, uint256 _period, address _owner, uint256 _id);
2022-12-tigris/contracts/BondNFT.sol::379 => event ExtendLock(uint256 _period, uint256 _amount, address _owner, uint256 _id);
2022-12-tigris/contracts/BondNFT.sol::380 => event Release(address _tigAsset, uint256 _amount, address _owner, uint256 _id);
2022-12-tigris/contracts/BondNFT.sol::381 => event ClaimFees(address _tigAsset, uint256 _amount, address _claimer, uint256 _id);
2022-12-tigris/contracts/BondNFT.sol::382 => event ClaimDebt(address _tigAsset, uint256 _amount, address _owner);
2022-12-tigris/contracts/GovNFT.sol::24 => event MessageFailed(uint16 _srcChainId, bytes _srcAddress, uint64 _nonce, bytes _payload, bytes _reason);
2022-12-tigris/contracts/GovNFT.sol::25 => event RetryMessageSuccess(uint16 _srcChainId, bytes _srcAddress, uint64 _nonce, bytes32 _payloadHash);
2022-12-tigris/contracts/GovNFTBridged.sol::22 => event MessageFailed(uint16 _srcChainId, bytes _srcAddress, uint64 _nonce, bytes _payload, bytes _reason);
2022-12-tigris/contracts/GovNFTBridged.sol::23 => event RetryMessageSuccess(uint16 _srcChainId, bytes _srcAddress, uint64 _nonce, bytes32 _payloadHash);
2022-12-tigris/contracts/Referrals.sol::85 => event ReferralCreated(address _referrer, bytes32 _hash);
2022-12-tigris/contracts/Referrals.sol::86 => event Referred(address _referredTrader, bytes32 _hash);
```

## [N-04] Missing NatSpec

Code should include NatSpec

```
2022-12-tigris/contracts/Faucet.sol::1 => //SPDX-License-Identifier: Unlicense
2022-12-tigris/contracts/Forwarder.sol::1 => //SPDX-License-Identifier: MIT
2022-12-tigris/contracts/GovNFTBridged.sol::1 => // SPDX-License-Identifier: MIT
2022-12-tigris/contracts/NFTSale.sol::1 => //SPDX-License-Identifier: MIT
2022-12-tigris/contracts/Timelock.sol::1 => //SPDX-License-Identifier: MIT
```

## [N-05] Missing event for critical parameter change

Emitting events after sensitive changes take place, to facilitate tracking and notify off-chain clients following changes to the contract.

```
2022-12-tigris/contracts/BondNFT.sol::357 => function setAllowedAsset(address _asset, bool _bool) external onlyOwner {
2022-12-tigris/contracts/BondNFT.sol::362 => function setBaseURI(string calldata _newBaseURI) external onlyOwner {
2022-12-tigris/contracts/BondNFT.sol::366 => function setManager(
2022-12-tigris/contracts/GovNFT.sol::46 => function setBaseURI(string calldata _newBaseURI) external onlyOwner {
2022-12-tigris/contracts/GovNFT.sol::114 => function setTrustedAddress(uint16 _chainId, address _contract, bool _bool) external onlyOwner {
2022-12-tigris/contracts/GovNFT.sol::236 => function setGas(uint _gas) external onlyOwner {
2022-12-tigris/contracts/GovNFT.sol::240 => function setEndpoint(ILayerZeroEndpoint _endpoint) external onlyOwner {
2022-12-tigris/contracts/GovNFT.sol::307 => function setAllowedAsset(address _asset, bool _bool) external onlyOwner {
2022-12-tigris/contracts/GovNFT.sol::311 => function setMaxBridge(uint256 _max) external onlyOwner {
2022-12-tigris/contracts/GovNFTBridged.sol::44 => function setBaseURI(string calldata _newBaseURI) external onlyOwner {
2022-12-tigris/contracts/GovNFTBridged.sol::82 => function setTrustedAddress(uint16 _chainId, address _contract, bool _bool) external onlyOwner {
2022-12-tigris/contracts/GovNFTBridged.sol::196 => function setGas(uint _gas) external onlyOwner {
2022-12-tigris/contracts/GovNFTBridged.sol::200 => function setEndpoint(ILayerZeroEndpoint _endpoint) external onlyOwner {
2022-12-tigris/contracts/GovNFTBridged.sol::258 => function setAllowedAsset(address _asset, bool _bool) external onlyOwner {
2022-12-tigris/contracts/GovNFTBridged.sol::262 => function setMaxBridge(uint256 _max) external onlyOwner {
2022-12-tigris/contracts/NFTSale.sol::32 => function setPrice(uint _price) external onlyOwner {
2022-12-tigris/contracts/NFTSale.sol::63 => function setIds(uint[] calldata _ids) external onlyOwner {
2022-12-tigris/contracts/PairsContract.sol::33 => function setAssetChainlinkFeed(uint256 _asset, address _feed) external onlyOwner {
2022-12-tigris/contracts/PairsContract.sol::92 => function setAssetBaseFundingRate(uint256 _asset, uint256 _baseFundingRate) external onlyOwner {
2022-12-tigris/contracts/PairsContract.sol::125 => function setMaxBaseFundingRate(uint256 _maxBaseFundingRate) external onlyOwner {
2022-12-tigris/contracts/PairsContract.sol::129 => function setProtocol(address _protocol) external onlyOwner {
2022-12-tigris/contracts/PairsContract.sol::139 => function setMaxOi(uint256 _asset, address _tigAsset, uint256 _maxOi) external onlyOwner {
2022-12-tigris/contracts/Position.sol::85 => function setBaseURI(string memory _newBaseURI) external onlyOwner {
2022-12-tigris/contracts/Position.sol::220 => function setAccInterest(uint256 _id) external onlyMinter {
2022-12-tigris/contracts/Position.sol::310 => function setMinter(address _minter, bool _bool) external onlyOwner {
2022-12-tigris/contracts/Referrals.sol::53 => function setProtocol(address _protocol) external onlyOwner {
2022-12-tigris/contracts/StableToken.sol::38 => function setMinter(
2022-12-tigris/contracts/Trading.sol::898 => function setBlockDelay(
2022-12-tigris/contracts/Trading.sol::912 => function setAllowedVault(
2022-12-tigris/contracts/Trading.sol::926 => function setMaxWinPercent(
2022-12-tigris/contracts/Trading.sol::939 => function setLimitOrderPriceRange(uint _range) external onlyOwner {
2022-12-tigris/contracts/Trading.sol::952 => function setFees(bool _open, uint _daoFees, uint _burnFees, uint _referralFees, uint _botFees, uint _percent) external onlyOwner {
2022-12-tigris/contracts/Trading.sol::975 => function setTradingExtension(
2022-12-tigris/contracts/TradingExtension.sol::144 => function setMaxGasPrice(uint _maxGasPrice) external onlyOwner {
2022-12-tigris/contracts/TradingExtension.sol::222 => function setValidSignatureTimer(
2022-12-tigris/contracts/TradingExtension.sol::231 => function setChainlinkEnabled(bool _bool) external onlyOwner {
2022-12-tigris/contracts/TradingExtension.sol::240 => function setNode(address _node, bool _bool) external onlyOwner {
2022-12-tigris/contracts/TradingExtension.sol::249 => function setAllowedMargin(
2022-12-tigris/contracts/TradingExtension.sol::264 => function setMinPositionSize(
2022-12-tigris/contracts/TradingExtension.sol::274 => function setPaused(bool _paused) external onlyOwner {
```

## [N-06] Lines are too long

Usually lines in source code are limited to 80 characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length

```
2022-12-tigris/contracts/BondNFT.sol::178 => uint _pendingDelta = (bond.shares * accRewardsPerShare[bond.asset][epoch[bond.asset]] / 1e18 - bondPaid[_id][bond.asset]) - (bond.shares * accRewardsPerShare[bond.asset][bond.expireEpoch-1] / 1e18 - bondPaid[_id][bond.asset]);
2022-12-tigris/contracts/GovNFT.sol::175 => (bool success, bytes memory reason) = address(this).excessivelySafeCall(gasleft()*4/5, 150, abi.encodeWithSelector(this.nonblockingLzReceive.selector, _srcChainId, _srcAddress, _nonce, _payload));
2022-12-tigris/contracts/GovNFTBridged.sol::135 => (bool success, bytes memory reason) = address(this).excessivelySafeCall(gasleft()*4/5, 150, abi.encodeWithSelector(this.nonblockingLzReceive.selector, _srcChainId, _srcAddress, _nonce, _payload));
2022-12-tigris/contracts/PairsContract.sol::48 => function addAsset(uint256 _asset, string memory _name, address _chainlinkFeed, uint256 _minLeverage, uint256 _maxLeverage, uint256 _feeMultiplier, uint256 _baseFundingRate) external onlyOwner {
2022-12-tigris/contracts/Position.sol::48 => _pendingFunding = (int256(block.timestamp-lastUpdate[_trade.asset][_trade.tigAsset])*fundingDeltaPerSec[_trade.asset][_trade.tigAsset])*1e18/int256(longOi[_trade.asset][_trade.tigAsset]);
2022-12-tigris/contracts/Position.sol::55 => _pendingFunding = (int256(block.timestamp-lastUpdate[_trade.asset][_trade.tigAsset])*fundingDeltaPerSec[_trade.asset][_trade.tigAsset])*1e18/int256(shortOi[_trade.asset][_trade.tigAsset]);
2022-12-tigris/contracts/Position.sol::62 => _trade.accInterest += (int256(_trade.margin*_trade.leverage/1e18)*(accInterestPerOi[_trade.asset][_trade.tigAsset][_trade.direction]+_pendingFunding)/1e18)-initId[_id];
2022-12-tigris/contracts/Position.sol::99 => function updateFunding(uint256 _asset, address _tigAsset, uint256 _longOi, uint256 _shortOi, uint256 _baseFundingRate, uint _vaultFundingPercent) external onlyMinter {
2022-12-tigris/contracts/Position.sol::102 => accInterestPerOi[_asset][_tigAsset][true] += ((int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(longOi[_asset][_tigAsset]))*int256(1e10-vaultFundingPercent[_asset][_tigAsset])/1e10;
2022-12-tigris/contracts/Position.sol::104 => accInterestPerOi[_asset][_tigAsset][false] -= (int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(shortOi[_asset][_tigAsset]);
2022-12-tigris/contracts/Position.sol::107 => accInterestPerOi[_asset][_tigAsset][true] -= (int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(longOi[_asset][_tigAsset]);
2022-12-tigris/contracts/Position.sol::109 => accInterestPerOi[_asset][_tigAsset][false] += ((int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(shortOi[_asset][_tigAsset]))*int256(1e10-vaultFundingPercent[_asset][_tigAsset])/1e10;
2022-12-tigris/contracts/Position.sol::153 => initId[newTokenID] = accInterestPerOi[_mintTrade.asset][_mintTrade.tigAsset][_mintTrade.direction]*int256(_mintTrade.margin*_mintTrade.leverage/1e18)/1e18;
2022-12-tigris/contracts/Position.sol::233 => initId[_id] = accInterestPerOi[_trades[_id].asset][_trades[_id].tigAsset][_trades[_id].direction]*int256(_trades[_id].margin*_trades[_id].leverage/1e18)/1e18;
2022-12-tigris/contracts/Trading.sol::496 => if (_price > trade.price+trade.price*limitOrderPriceRange/DIVISION_CONSTANT || _price < trade.price-trade.price*limitOrderPriceRange/DIVISION_CONSTANT) revert("6"); //LimitNotMet
2022-12-tigris/contracts/Trading.sol::543 => (uint256 _positionSizeAfterPrice, int256 _payout) = TradingLibrary.pnl(_trade.direction, _price, _trade.price, _trade.margin, _trade.leverage, _trade.accInterest);
2022-12-tigris/contracts/Trading.sol::624 => _toMint = _handleCloseFees(_trade.asset, uint256(_payout)*_percent/DIVISION_CONSTANT, _trade.tigAsset, _positionSize*_percent/DIVISION_CONSTANT, _trade.trader, _isBot);
2022-12-tigris/contracts/Trading.sol::643 => function _handleDeposit(address _tigAsset, address _marginAsset, uint256 _margin, address _stableVault, ERC20PermitData calldata _permitData, address _trader) internal {
```

