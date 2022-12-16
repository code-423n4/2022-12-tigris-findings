# c4udit Report

## Files analyzed
- examples\BondNFT.sol
- examples\Faucet.sol
- examples\Forwarder.sol
- examples\GovNFT.sol
- examples\GovNFTBridged.sol
- examples\Lock.sol
- examples\NFTSale.sol
- examples\PairsContract.sol
- examples\Position.sol
- examples\Referrals.sol
- examples\StableToken.sol
- examples\StableVault.sol
- examples\Timelock.sol
- examples\Trading.sol
- examples\TradingExtension.sol
- examples\interfaces\IBondNFT.sol
- examples\interfaces\IGovNFT.sol
- examples\interfaces\ILayerZeroEndpoint.sol
- examples\interfaces\ILayerZeroReceiver.sol
- examples\interfaces\ILayerZeroUserApplicationConfig.sol
- examples\interfaces\IPairsContract.sol
- examples\interfaces\IPosition.sol
- examples\interfaces\IReferrals.sol
- examples\interfaces\IStableVault.sol
- examples\interfaces\ITrading.sol
- examples\mock\BadStableVault.sol
- examples\mock\MetaContextTest.sol
- examples\mock\MockChainlinkFeed.sol
- examples\mock\MockERC20.sol
- examples\mock\MockEndpoint.sol
- examples\mock\NoTransferToken.sol
- examples\mock\WETH.sol
- examples\utils\ExcessivelySafeCall.sol
- examples\utils\MetaContext.sol
- examples\utils\TradingLibrary.sol

## Issues found


### Use immutable for OpenZeppelin AccessControl's Roles Declarations

#### Impact
Issue Information: [G006](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g006---use-immutable-for-openzeppelin-accesscontrols-roles-declarations)

#### Findings:
```
examples\GovNFT.sol::178 => failedMessages[_srcChainId][_srcAddress][_nonce] = keccak256(_payload);
examples\GovNFT.sol::210 => require(keccak256(_payload) == payloadHash, "NonblockingLzApp: invalid payload");
examples\GovNFTBridged.sol::138 => failedMessages[_srcChainId][_srcAddress][_nonce] = keccak256(_payload);
examples\GovNFTBridged.sol::170 => require(keccak256(_payload) == payloadHash, "NonblockingLzApp: invalid payload");
examples\mock\MockEndpoint.sol::151 => storedPayload[_srcChainId][_srcAddress] = StoredPayload(uint64(_payload.length), _dstAddress, keccak256(_payload));
examples\mock\MockEndpoint.sol::275 => require(_payload.length == sp.payloadLength && keccak256(_payload) == sp.payloadHash, "LayerZero: invalid payload");
examples\utils\TradingLibrary.sol::103 => keccak256(abi.encode(_priceData))
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Long Revert Strings

#### Impact
Issue Information: [G007](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g007---long-revert-strings)

#### Findings:
```
examples\BondNFT.sol::4 => import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Enumerable.sol";
examples\BondNFT.sol::5 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
examples\BondNFT.sol::6 => import "@openzeppelin/contracts/access/Ownable.sol";
examples\Forwarder.sol::4 => import "@openzeppelin/contracts/metatx/MinimalForwarder.sol";
examples\GovNFT.sol::4 => import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Enumerable.sol";
examples\GovNFT.sol::5 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
examples\GovNFT.sol::6 => import "./interfaces/ILayerZeroEndpoint.sol";
examples\GovNFT.sol::7 => import "./interfaces/ILayerZeroReceiver.sol";
examples\GovNFT.sol::155 => "Must send enough value to cover messageFee"
examples\GovNFT.sol::185 => require(msg.sender == address(this), "NonblockingLzApp: caller must be app");
examples\GovNFT.sol::209 => require(payloadHash != bytes32(0), "NonblockingLzApp: no stored message");
examples\GovNFT.sol::210 => require(keccak256(_payload) == payloadHash, "NonblockingLzApp: invalid payload");
examples\GovNFTBridged.sol::4 => import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Enumerable.sol";
examples\GovNFTBridged.sol::5 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
examples\GovNFTBridged.sol::6 => import "./interfaces/ILayerZeroEndpoint.sol";
examples\GovNFTBridged.sol::7 => import "./interfaces/ILayerZeroReceiver.sol";
examples\GovNFTBridged.sol::117 => "Must send enough value to cover messageFee"
examples\GovNFTBridged.sol::145 => require(msg.sender == address(this), "NonblockingLzApp: caller must be app");
examples\GovNFTBridged.sol::169 => require(payloadHash != bytes32(0), "NonblockingLzApp: no stored message");
examples\GovNFTBridged.sol::170 => require(keccak256(_payload) == payloadHash, "NonblockingLzApp: invalid payload");
examples\Lock.sol::5 => import "@openzeppelin/contracts/access/Ownable.sol";
examples\Lock.sol::6 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
examples\NFTSale.sol::4 => import "@openzeppelin/contracts/access/Ownable.sol";
examples\PairsContract.sol::4 => import "@openzeppelin/contracts/access/Ownable.sol";
examples\Position.sol::4 => import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Enumerable.sol";
examples\Position.sol::5 => import "@openzeppelin/contracts/utils/Counters.sol";
examples\Referrals.sol::4 => import "@openzeppelin/contracts/access/Ownable.sol";
examples\StableToken.sol::4 => import "@openzeppelin/contracts/token/ERC20/extensions/draft-ERC20Permit.sol";
examples\StableVault.sol::4 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
examples\StableVault.sol::5 => import "@openzeppelin/contracts/security/ReentrancyGuard.sol";
examples\Timelock.sol::4 => import "@openzeppelin/contracts/governance/TimelockController.sol";
examples\Trading.sol::6 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
examples\TradingExtension.sol::4 => import "@openzeppelin/contracts/access/Ownable.sol";
examples\interfaces\ILayerZeroEndpoint.sol::5 => import "./ILayerZeroUserApplicationConfig.sol";
examples\mock\BadStableVault.sol::4 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
examples\mock\BadStableVault.sol::5 => import "@openzeppelin/contracts/security/ReentrancyGuard.sol";
examples\mock\MockERC20.sol::4 => import "@openzeppelin/contracts/token/ERC20/extensions/draft-ERC20Permit.sol";
examples\mock\MockEndpoint.sol::6 => import "../interfaces/ILayerZeroReceiver.sol";
examples\mock\MockEndpoint.sol::7 => import "../interfaces/ILayerZeroEndpoint.sol";
examples\mock\MockEndpoint.sol::86 => require(lzEndpoint != address(0), "LayerZeroMock: destination LayerZero Endpoint not found");
examples\mock\MockEndpoint.sol::88 => require(msg.value >= nativeFee * _payload.length, "LayerZeroMock: not enough native for fees");
examples\mock\NoTransferToken.sol::93 => require(newOwner != address(0), "Ownable: new owner is the zero address");
examples\mock\NoTransferToken.sol::383 => require(currentAllowance >= amount, "ERC20: transfer amount exceeds allowance");
examples\mock\NoTransferToken.sol::424 => require(currentAllowance >= subtractedValue, "ERC20: decreased allowance below zero");
examples\mock\NoTransferToken.sol::451 => require(sender != address(0), "ERC20: transfer from the zero address");
examples\mock\NoTransferToken.sol::452 => require(recipient != address(0), "ERC20: transfer to the zero address");
examples\mock\NoTransferToken.sol::457 => require(senderBalance >= amount, "ERC20: transfer amount exceeds balance");
examples\mock\NoTransferToken.sol::501 => require(account != address(0), "ERC20: burn from the zero address");
examples\mock\NoTransferToken.sol::506 => require(accountBalance >= amount, "ERC20: burn amount exceeds balance");
examples\mock\NoTransferToken.sol::535 => require(owner != address(0), "ERC20: approve from the zero address");
examples\mock\NoTransferToken.sol::536 => require(spender != address(0), "ERC20: approve to the zero address");
examples\utils\MetaContext.sol::4 => import "@openzeppelin/contracts/access/Ownable.sol";
examples\utils\TradingLibrary.sol::4 => import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Use Shift Right/Left instead of Division/Multiplication if possible

#### Impact
Issue Information: [G008](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md/#g008---use-shift-rightleft-instead-of-divisionmultiplication-if-possible)

#### Findings:
```
examples\GovNFT.sol::175 => (bool success, bytes memory reason) = address(this).excessivelySafeCall(gasleft()*4/5, 150, abi.encodeWithSelector(this.nonblockingLzReceive.selector, _srcChainId, _srcAddress, _nonce, _payload));
examples\GovNFTBridged.sol::135 => (bool success, bytes memory reason) = address(this).excessivelySafeCall(gasleft()*4/5, 150, abi.encodeWithSelector(this.nonblockingLzReceive.selector, _srcChainId, _srcAddress, _nonce, _payload));
examples\Trading.sol::717 => _fees.daoFees = _fees.daoFees - _fees.referralFees*2;
examples\Trading.sol::792 => _daoFeesPaid = _daoFeesPaid-_referralFeesPaid*2;
examples\Trading.sol::954 => require(_daoFees >= _botFees+_referralFees*2);
examples\mock\NoTransferToken.sol::157 => * https://github.com/ethereum/EIPs/issues/20#issuecomment-263524729
examples\mock\NoTransferToken.sol::241 => * https://forum.zeppelin.solutions/t/how-to-implement-erc20-supply-mechanisms/226[How
examples\mock\NoTransferToken.sol::300 => * be displayed to a user as `5.05` (`505 / 10 ** 2`).
examples\utils\TradingLibrary.sol::117 => _priceData.price < assetChainlinkPrice+assetChainlinkPrice*2/100 &&
examples\utils\TradingLibrary.sol::118 => _priceData.price > assetChainlinkPrice-assetChainlinkPrice*2/100, "!chainlinkPrice"
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)