## Summary<a name="Summary">

### Low Risk Issues
| |Issue|Contexts|
|-|:-|:-:|
| [LOW&#x2011;1](#LOW&#x2011;1) | The Contract Should `approve(0)` First | 2 |
| [LOW&#x2011;2](#LOW&#x2011;2) | Use `_safeMint` instead of `_mint` | 5 |
| [LOW&#x2011;3](#LOW&#x2011;3) | `decimals()` not part of ERC20 standard | 4 |
| [LOW&#x2011;4](#LOW&#x2011;4) | Contracts are not using their OZ Upgradeable counterparts | 18 |
| [LOW&#x2011;5](#LOW&#x2011;5) | Missing parameter validation in `constructor` | 9 |

Total: 38 contexts over 5 issues

### Non-critical Issues
| |Issue|Contexts|
|-|:-|:-:|
| [NC&#x2011;1](#NC&#x2011;1) | Critical Changes Should Use Two-step Procedure | 34 |
| [NC&#x2011;2](#NC&#x2011;2) | Use a more recent version of Solidity | 22 |
| [NC&#x2011;3](#NC&#x2011;3) | Missing event for critical parameter change | 33 |
| [NC&#x2011;4](#NC&#x2011;4) | Implementation contract may not be initialized | 8 |
| [NC&#x2011;5](#NC&#x2011;5) | Large multiples of ten should use scientific notation | 3 |
| [NC&#x2011;6](#NC&#x2011;6) | Avoid Floating Pragmas: The Version Should Be Locked | 22 |
| [NC&#x2011;7](#NC&#x2011;7) | Use of Block.Timestamp | 7 |
| [NC&#x2011;8](#NC&#x2011;8) | Non-usage of specific imports | 29 |
| [NC&#x2011;9](#NC&#x2011;9) | Use `bytes.concat()` | 1 |
| [NC&#x2011;10](#NC&#x2011;10) | Use `delete` to Clear Variables | 5 |
| [NC&#x2011;11](#NC&#x2011;11) | Use Underscores for Number Literals  | 3 |

Total: 167 contexts over 11 issues

## Low Risk Issues


### <a href="#Summary">[LOW&#x2011;1]</a><a name="LOW&#x2011;1"> The Contract Should `approve(0)` First

Some tokens (like USDT L199) do not work when changing the allowance from an existing non-zero allowance value. They must first be approved by zero and then the actual allowance must be approved.

#### <ins>Proof Of Concept</ins>


```solidity
117: IERC20(assets[i]).approve(address(bondNFT), type(uint256).max);
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Lock.sol#L117

```solidity
652: IERC20(_marginAsset).approve(_stableVault, type(uint).max);
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L652




#### <ins>Recommended Mitigation Steps</ins>

Approve with a zero amount first before setting the actual amount.



### <a href="#Summary">[LOW&#x2011;2]</a><a name="LOW&#x2011;2"> Use `_safeMint` instead of `_mint`

According to openzepplin's ERC721, the use of `_mint` is discouraged, use _safeMint whenever possible.
https://docs.openzeppelin.com/contracts/3.x/api/token/erc721#ERC721-_mint-address-uint256-

#### <ins>Proof Of Concept</ins>


```solidity
83: _mint(_owner, _bond);
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L83

```solidity
313: _mint(to, bond.id);
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L313


```solidity
106: _mint(_msgSender(), counter);
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L106

```solidity
111: _mint(_msgSender(), counter);
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L111

```solidity
32: _mint(account, amount);
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/StableToken.sol#L32



#### <ins>Recommended Mitigation Steps</ins>

Use `_safeMint` whenever possible instead of `_mint`



### <a href="#Summary">[LOW&#x2011;3]</a><a name="LOW&#x2011;3"> `decimals()` not part of ERC20 standard

`decimals()` is not part of the official ERC20 standard and might fail for tokens that do not implement it. While in practice it is very unlikely, as usually most of the tokens implement it, this should still be considered as a potential issue.

#### <ins>Proof Of Concept</ins>


```solidity
49: _amount*(10**(18-IERC20Mintable(_token).decimals()
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/StableVault.sol#L49

```solidity
67: _output = _amount/10**(18-IERC20Mintable(_token).decimals()
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/StableVault.sol#L67

```solidity
650: uint _marginDecMultiplier = 10**(18-ExtendedIERC20(_marginAsset).decimals()
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L650

```solidity
675: if (IERC20(_outputToken).balanceOf(address(this)) != _balBefore + _toMint/(10**(18-ExtendedIERC20(_outputToken).decimals()
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L675







### <a href="#Summary">[LOW&#x2011;4]</a><a name="LOW&#x2011;4"> Contracts are not using their OZ Upgradeable counterparts

The non-upgradeable standard version of OpenZeppelin’s library are inherited / used by the contracts.
It would be safer to use the upgradeable versions of the library contracts to avoid unexpected behaviour.

#### <ins>Proof of Concept</ins>

```solidity
4: import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Enumerable.sol";
5: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
6: import "@openzeppelin/contracts/access/Ownable.sol";

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L4-L6

```solidity
4: import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Enumerable.sol";
5: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L4-L5

```solidity
5: import "@openzeppelin/contracts/access/Ownable.sol";
6: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Lock.sol#L5-L6

```solidity
4: import "@openzeppelin/contracts/access/Ownable.sol";

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/PairsContract.sol#L4

```solidity
4: import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Enumerable.sol";
5: import "@openzeppelin/contracts/utils/Counters.sol";

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L4-L5

```solidity
4: import "@openzeppelin/contracts/access/Ownable.sol";

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Referrals.sol#L4

```solidity
4: import "@openzeppelin/contracts/token/ERC20/extensions/draft-ERC20Permit.sol";

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/StableToken.sol#L4

```solidity
4: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
5: import "@openzeppelin/contracts/security/ReentrancyGuard.sol";

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/StableVault.sol#L4-L5

```solidity
6: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L6

```solidity
4: import "@openzeppelin/contracts/access/Ownable.sol";

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L4

```solidity
4: import "@openzeppelin/contracts/access/Ownable.sol";

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/utils/MetaContext.sol#L4

```solidity
4: import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/utils/TradingLibrary.sol#L4



#### <ins>Recommended Mitigation Steps</ins>

Where applicable, use the contracts from `@openzeppelin/contracts-upgradeable` instead of `@openzeppelin/contracts`.



### <a href="#Summary">[LOW&#x2011;5]</a><a name="LOW&#x2011;5"> Missing parameter validation in `constructor`

Some parameters of constructors are not checked for invalid values.

#### <ins>Proof Of Concept</ins>

```solidity
33: address _endpoint

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L33

```solidity
22: address _bondNFTAddress
23: address _govNFT

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Lock.sol#L22-L23

```solidity
35: address _stable

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/StableVault.sol#L35

```solidity
144: address _position
145: address _gov

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L144-L145

```solidity
29: address _trading
30: address _pairsContract
31: address _ref

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L29-L31



#### <ins>Recommended Mitigation Steps</ins>

Validate the parameters.





## Non Critical Issues

### <a href="#Summary">[NC&#x2011;1]</a><a name="NC&#x2011;1"> Critical Changes Should Use Two-step Procedure

The critical procedures should be two step process.

See similar findings in previous Code4rena contests for reference:
https://code4rena.com/reports/2022-06-illuminate/#2-critical-changes-should-use-two-step-procedure

#### <ins>Proof Of Concept</ins>

```solidity
357: function setAllowedAsset(address _asset, bool _bool) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L357

```solidity
362: function setBaseURI(string calldata _newBaseURI) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L362

```solidity
366: function setManager(
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L366

```solidity
46: function setBaseURI(string calldata _newBaseURI) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L46

```solidity
114: function setTrustedAddress(uint16 _chainId, address _contract, bool _bool) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L114

```solidity
236: function setGas(uint _gas) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L236

```solidity
240: function setEndpoint(ILayerZeroEndpoint _endpoint) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L240

```solidity
307: function setAllowedAsset(address _asset, bool _bool) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L307

```solidity
311: function setMaxBridge(uint256 _max) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L311

```solidity
33: function setAssetChainlinkFeed(uint256 _asset, address _feed) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/PairsContract.sol#L33

```solidity
92: function setAssetBaseFundingRate(uint256 _asset, uint256 _baseFundingRate) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/PairsContract.sol#L92

```solidity
125: function setMaxBaseFundingRate(uint256 _maxBaseFundingRate) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/PairsContract.sol#L125

```solidity
129: function setProtocol(address _protocol) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/PairsContract.sol#L129

```solidity
139: function setMaxOi(uint256 _asset, address _tigAsset, uint256 _maxOi) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/PairsContract.sol#L139

```solidity
85: function setBaseURI(string memory _newBaseURI) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L85

```solidity
220: function setAccInterest(uint256 _id) external onlyMinter {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L220

```solidity
310: function setMinter(address _minter, bool _bool) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L310

```solidity
32: function setReferred(address _referredTrader, bytes32 _hash) external onlyProtocol {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Referrals.sol#L32

```solidity
53: function setProtocol(address _protocol) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Referrals.sol#L53

```solidity
38: function setMinter(
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/StableToken.sol#L38

```solidity
898: function setBlockDelay(
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L898

```solidity
912: function setAllowedVault(
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L912

```solidity
926: function setMaxWinPercent(
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L926

```solidity
939: function setLimitOrderPriceRange(uint _range) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L939

```solidity
952: function setFees(bool _open, uint _daoFees, uint _burnFees, uint _referralFees, uint _botFees, uint _percent) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L952

```solidity
975: function setTradingExtension(
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L975

```solidity
144: function setMaxGasPrice(uint _maxGasPrice) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L144

```solidity
222: function setValidSignatureTimer(
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L222

```solidity
231: function setChainlinkEnabled(bool _bool) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L231

```solidity
240: function setNode(address _node, bool _bool) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L240

```solidity
249: function setAllowedMargin(
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L249

```solidity
264: function setMinPositionSize(
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L264

```solidity
274: function setPaused(bool _paused) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L274

```solidity
9: function setTrustedForwarder(address _forwarder, bool _bool) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/utils/MetaContext.sol#L9



#### <ins>Recommended Mitigation Steps</ins>

Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.



### <a href="#Summary">[NC&#x2011;2]</a><a name="NC&#x2011;2"> Use a more recent version of Solidity

<a href="https://blog.soliditylang.org/2021/04/21/solidity-0.8.4-release-announcement/">0.8.4</a>:
bytes.concat() instead of abi.encodePacked(<bytes>,<bytes>)

<a href="https://blog.soliditylang.org/2022/02/16/solidity-0.8.12-release-announcement/">0.8.12</a>: 
string.concat() instead of abi.encodePacked(<str>,<str>)

<a href="https://blog.soliditylang.org/2022/03/16/solidity-0.8.13-release-announcement/">0.8.13</a>: 
Ability to use using for with a list of free functions

<a href="https://blog.soliditylang.org/2022/05/18/solidity-0.8.14-release-announcement/">0.8.14</a>:

ABI Encoder: When ABI-encoding values from calldata that contain nested arrays, correctly validate the nested array length against calldatasize() in all cases.
Override Checker: Allow changing data location for parameters only when overriding external functions.

<a href="https://blog.soliditylang.org/2022/06/15/solidity-0.8.15-release-announcement/">0.8.15</a>:

Code Generation: Avoid writing dirty bytes to storage when copying bytes arrays.
Yul Optimizer: Keep all memory side-effects of inline assembly blocks.

<a href="https://blog.soliditylang.org/2022/08/08/solidity-0.8.16-release-announcement/">0.8.16</a>:

Code Generation: Fix data corruption that affected ABI-encoding of calldata values represented by tuples: structs at any nesting level; argument lists of external functions, events and errors; return value lists of external functions. The 32 leading bytes of the first dynamically-encoded value in the tuple would get zeroed when the last component contained a statically-encoded array.

<a href="https://blog.soliditylang.org/2022/09/08/solidity-0.8.17-release-announcement/">0.8.17</a>:

Yul Optimizer: Prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call.

#### <ins>Proof Of Concept</ins>


```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L2

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L2

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Lock.sol#L2

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/PairsContract.sol#L2

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L2

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Referrals.sol#L2

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/StableToken.sol#L2

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/StableVault.sol#L2

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L2

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L2

```solidity
pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/interfaces/IBondNFT.sol#L2

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/interfaces/IGovNFT.sol#L3

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/interfaces/ILayerZeroEndpoint.sol#L3

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/interfaces/ILayerZeroReceiver.sol#L3

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/interfaces/ILayerZeroUserApplicationConfig.sol#L3

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/interfaces/IPairsContract.sol#L3

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/interfaces/IPosition.sol#L3

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/interfaces/IPosition.sol#L3

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/interfaces/IReferrals.sol#L3

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/interfaces/IStableVault.sol#L3

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/utils/MetaContext.sol#L2

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/utils/TradingLibrary.sol#L2



#### <ins>Recommended Mitigation Steps</ins>

Consider updating to a more recent solidity version.


### <a href="#Summary">[NC&#x2011;3]</a><a name="NC&#x2011;3"> Missing event for critical parameter change

When changing state variables events are not emitted. Emitting events allows monitoring activities with off-chain monitoring tools.

#### <ins>Proof Of Concept</ins>


```solidity
357: function setAllowedAsset(address _asset, bool _bool) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L357

```solidity
362: function setBaseURI(string calldata _newBaseURI) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L362

```solidity
366: function setManager(
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L366

```solidity
46: function setBaseURI(string calldata _newBaseURI) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L46

```solidity
114: function setTrustedAddress(uint16 _chainId, address _contract, bool _bool) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L114

```solidity
236: function setGas(uint _gas) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L236

```solidity
240: function setEndpoint(ILayerZeroEndpoint _endpoint) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L240

```solidity
307: function setAllowedAsset(address _asset, bool _bool) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L307

```solidity
311: function setMaxBridge(uint256 _max) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L311

```solidity
33: function setAssetChainlinkFeed(uint256 _asset, address _feed) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/PairsContract.sol#L33

```solidity
92: function setAssetBaseFundingRate(uint256 _asset, uint256 _baseFundingRate) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/PairsContract.sol#L92

```solidity
125: function setMaxBaseFundingRate(uint256 _maxBaseFundingRate) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/PairsContract.sol#L125

```solidity
129: function setProtocol(address _protocol) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/PairsContract.sol#L129

```solidity
139: function setMaxOi(uint256 _asset, address _tigAsset, uint256 _maxOi) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/PairsContract.sol#L139

```solidity
85: function setBaseURI(string memory _newBaseURI) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L85

```solidity
220: function setAccInterest(uint256 _id) external onlyMinter {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L220

```solidity
310: function setMinter(address _minter, bool _bool) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L310

```solidity
53: function setProtocol(address _protocol) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Referrals.sol#L53

```solidity
38: function setMinter(
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/StableToken.sol#L38

```solidity
898: function setBlockDelay(
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L898

```solidity
912: function setAllowedVault(
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L912

```solidity
926: function setMaxWinPercent(
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L926

```solidity
939: function setLimitOrderPriceRange(uint _range) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L939

```solidity
952: function setFees(bool _open, uint _daoFees, uint _burnFees, uint _referralFees, uint _botFees, uint _percent) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L952

```solidity
975: function setTradingExtension(
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L975

```solidity
144: function setMaxGasPrice(uint _maxGasPrice) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L144

```solidity
222: function setValidSignatureTimer(
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L222

```solidity
231: function setChainlinkEnabled(bool _bool) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L231

```solidity
240: function setNode(address _node, bool _bool) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L240

```solidity
249: function setAllowedMargin(
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L249

```solidity
264: function setMinPositionSize(
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L264

```solidity
274: function setPaused(bool _paused) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L274

```solidity
9: function setTrustedForwarder(address _forwarder, bool _bool) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/utils/MetaContext.sol#L9




### <a href="#Summary">[NC&#x2011;4]</a><a name="NC&#x2011;4"> Implementation contract may not be initialized

OpenZeppelin recommends that the initializer modifier be applied to constructors. 
Per OZs Post implementation contract should be initialized to avoid potential griefs or exploits.
https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680/5

#### <ins>Proof Of Concept</ins>


```solidity
40: constructor(
        string memory _setBaseURI,
        string memory _name,
        string memory _symbol
    ) ERC721(_name, _symbol)
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L40

```solidity
32: constructor(
        address _endpoint,
        string memory _setBaseURI,
        string memory _name,
        string memory _symbol
    ) ERC721(_name, _symbol)
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L32

```solidity
21: constructor(
        address _bondNFTAddress,
        address _govNFT
    )
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Lock.sol#L21

```solidity
76: constructor(string memory _setBaseURI, string memory _name, string memory _symbol) ERC721(_name, _symbol)
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L76

```solidity
11: constructor(string memory name_, string memory symbol_) ERC20Permit(name_) ERC20(name_, symbol_)
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/StableToken.sol#L11

```solidity
35: constructor(address _stable)
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/StableVault.sol#L35

```solidity
143: constructor(
        address _position,
        address _gov,
        address _pairsContract
    )
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L143

```solidity
28: constructor(
        address _trading,
        address _pairsContract,
        address _ref,
        address _position
    )
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L28





### <a href="#Summary">[NC&#x2011;5]</a><a name="NC&#x2011;5"> Large multiples of ten should use scientific notation

Use (e.g. 1e6) rather than decimal literals (e.g. 100000), for better code readability.

#### <ins>Proof Of Concept</ins>


```solidity
16: uint256 private constant MAX = 10000;
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L16

```solidity
66: require(tokenId <= 10000, "BadID");
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L66

```solidity
26: uint public maxGasPrice = 1000000000000; // 1000 gwei
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L26





### <a href="#Summary">[NC&#x2011;6]</a><a name="NC&#x2011;6"> Avoid Floating Pragmas: The Version Should Be Locked

Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

#### <ins>Proof Of Concept</ins>

```solidity
Found usage of floating pragmas ^0.8.0 of Solidity in [BondNFT.sol]
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L2

```solidity
Found usage of floating pragmas ^0.8.0 of Solidity in [GovNFT.sol]
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L2

```solidity
Found usage of floating pragmas ^0.8.0 of Solidity in [Lock.sol]
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Lock.sol#L2

```solidity
Found usage of floating pragmas ^0.8.0 of Solidity in [PairsContract.sol]
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/PairsContract.sol#L2

```solidity
Found usage of floating pragmas ^0.8.0 of Solidity in [Position.sol]
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L2

```solidity
Found usage of floating pragmas ^0.8.0 of Solidity in [Referrals.sol]
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Referrals.sol#L2

```solidity
Found usage of floating pragmas ^0.8.0 of Solidity in [StableToken.sol]
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/StableToken.sol#L2

```solidity
Found usage of floating pragmas ^0.8.0 of Solidity in [StableVault.sol]
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/StableVault.sol#L2

```solidity
Found usage of floating pragmas ^0.8.0 of Solidity in [Trading.sol]
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L2

```solidity
Found usage of floating pragmas ^0.8.0 of Solidity in [TradingExtension.sol]
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L2

```solidity
Found usage of floating pragmas ^0.8.9 of Solidity in [IBondNFT.sol]
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/interfaces/IBondNFT.sol#L2

```solidity
Found usage of floating pragmas ^0.8.0 of Solidity in [IGovNFT.sol]
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/interfaces/IGovNFT.sol#L3

```solidity
Found usage of floating pragmas ^0.8.0 of Solidity in [ILayerZeroEndpoint.sol]
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/interfaces/ILayerZeroEndpoint.sol#L3

```solidity
Found usage of floating pragmas ^0.8.0 of Solidity in [ILayerZeroReceiver.sol]
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/interfaces/ILayerZeroReceiver.sol#L3

```solidity
Found usage of floating pragmas ^0.8.0 of Solidity in [ILayerZeroUserApplicationConfig.sol]
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/interfaces/ILayerZeroUserApplicationConfig.sol#L3

```solidity
Found usage of floating pragmas ^0.8.0 of Solidity in [IPairsContract.sol]
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/interfaces/IPairsContract.sol#L3

```solidity
Found usage of floating pragmas ^0.8.0 of Solidity in [IPosition.sol]
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/interfaces/IPosition.sol#L3

```solidity
Found usage of floating pragmas ^0.8.0 of Solidity in [IPosition.sol]
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/interfaces/IPosition.sol#L3

```solidity
Found usage of floating pragmas ^0.8.0 of Solidity in [IReferrals.sol]
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/interfaces/IReferrals.sol#L3

```solidity
Found usage of floating pragmas ^0.8.0 of Solidity in [IStableVault.sol]
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/interfaces/IStableVault.sol#L3

```solidity
Found usage of floating pragmas ^0.8.0 of Solidity in [MetaContext.sol]
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/utils/MetaContext.sol#L2

```solidity
Found usage of floating pragmas ^0.8.0 of Solidity in [TradingLibrary.sol]
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/utils/TradingLibrary.sol#L2






### <a href="#Summary">[NC&#x2011;7]</a><a name="NC&#x2011;7"> Use of Block.Timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.
References: SWC ID: 116

#### <ins>Proof Of Concept</ins>



```solidity
110: require(epoch[bond.asset] == block.timestamp/DAY, "Bad epoch");
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L110

```solidity
329: require(epoch[bond.asset] == block.timestamp/DAY, "Bad epoch");
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L329


```solidity
332: require(block.timestamp > bond.mintTime + 300, "Recent update");
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L332

```solidity
491: require(block.timestamp >= limitDelay[_id]);
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L491

```solidity
887: require(_proxy.proxy == _msgSender() && _proxy.time >= block.timestamp, "Proxy not approved");
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L887

```solidity
109: require(block.timestamp >= _priceData.timestamp, "FutSig");
110: require(block.timestamp <= _priceData.timestamp + _validSignatureTimer, "ExpSig");
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/utils/TradingLibrary.sol#L109-L110



#### <ins>Recommended Mitigation Steps</ins>
Block timestamps should not be used for entropy or generating random numbers—i.e., they should not be the deciding factor (either directly or through some derivation) for winning a game or changing an important state.

Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, or enforcing expiry dates. It is sometimes recommended to use block.number and an average block time to estimate times; with a 10 second block time, 1 week equates to approximately, 60480 blocks. Thus, specifying a block number at which to change a contract state can be more secure, as miners are unable to easily manipulate the block number.



### <a href="#Summary">[NC&#x2011;8]</a><a name="NC&#x2011;8"> Non-usage of specific imports

The current form of relative path import is not recommended for use because it can unpredictably pollute the namespace.
Instead, the Solidity docs recommend specifying imported symbols explicitly.
https://docs.soliditylang.org/en/v0.8.15/layout-of-source-files.html#importing-other-source-files

A good example:
```solidity
import {OwnableUpgradeable} from "openzeppelin-contracts-upgradeable/contracts/access/OwnableUpgradeable.sol";
import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
import {SafeCastLib} from "solmate/utils/SafeCastLib.sol";
import {ERC20} from "solmate/tokens/ERC20.sol";
import {IProducer} from "src/interfaces/IProducer.sol";
import {GlobalState, UserState} from "src/Common.sol";
```

#### <ins>Proof Of Concept</ins>


```solidity
6: import "./interfaces/ILayerZeroEndpoint.sol";
7: import "./interfaces/ILayerZeroReceiver.sol";
8: import "./utils/MetaContext.sol";
9: import "./interfaces/IGovNFT.sol";
10: import "./utils/ExcessivelySafeCall.sol";

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L6-L10

```solidity
7: import "./interfaces/IBondNFT.sol";
8: import "./interfaces/IGovNFT.sol";

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Lock.sol#L7-L8

```solidity
5: import "./interfaces/IPairsContract.sol";
6: import "./interfaces/IPosition.sol";

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/PairsContract.sol#L5-L6

```solidity
6: import "./utils/MetaContext.sol";
7: import "./interfaces/IPosition.sol";

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L6-L7

```solidity
5: import "./interfaces/IReferrals.sol";

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Referrals.sol#L5

```solidity
5: import "./utils/MetaContext.sol";

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/StableToken.sol#L5

```solidity
6: import "./utils/MetaContext.sol";
7: import "./interfaces/IStableVault.sol";

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/StableVault.sol#L6-L7

```solidity
4: import "./utils/MetaContext.sol";
5: import "./interfaces/ITrading.sol";
7: import "./interfaces/IPairsContract.sol";
8: import "./interfaces/IReferrals.sol";
9: import "./interfaces/IPosition.sol";
10: import "./interfaces/IGovNFT.sol";
11: import "./interfaces/IStableVault.sol";
12: import "./utils/TradingLibrary.sol";

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L4-L5

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L7-L12


```solidity
5: import "./interfaces/IPairsContract.sol";
6: import "./utils/TradingLibrary.sol";
7: import "./interfaces/IReferrals.sol";
8: import "./interfaces/IPosition.sol";

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L5-L8

```solidity
5: import "./ILayerZeroUserApplicationConfig.sol";

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/interfaces/ILayerZeroEndpoint.sol#L5

```solidity
5: import "../interfaces/IPosition.sol";

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/utils/TradingLibrary.sol#L5




#### <ins>Recommended Mitigation Steps</ins>

Use specific imports syntax per solidity docs recommendation.



### <a href="#Summary">[NC&#x2011;9]</a><a name="NC&#x2011;9"> Use `bytes.concat()`

Solidity version 0.8.4 introduces `bytes.concat()` (vs `abi.encodePacked(<bytes>,<bytes>)`)

#### <ins>Proof Of Concept</ins>


```solidity
145: bytes memory adapterParams = abi.encodePacked(version, _gas)

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L145




#### <ins>Recommended Mitigation Steps</ins>

Use `bytes.concat()` and upgrade to at least Solidity version 0.8.4 if required. 




### <a href="#Summary">[NC&#x2011;10]</a><a name="NC&#x2011;10"> Use `delete` to Clear Variables

`delete a` assigns the initial value for the type to `a`. i.e. for integers it is equivalent to `a = 0`, but it can also be used on arrays, where it assigns a dynamic array of length zero or a static array of the same length with all elements reset. For structs, it assigns a struct with all members reset. Similarly, it can also be used to set an address to zero address. It has no effect on whole mappings though (as the keys of mappings may be arbitrary and are generally unknown). However, individual keys and what they map to can be deleted: If `a` is a mapping, then `delete a[x]` will delete the value stored at `x`.

The `delete` key better conveys the intention and is also more idiomatic. Consider replacing assignments of zero with `delete` statements.

#### <ins>Proof Of Concept</ins>

```solidity
201: userDebt[_user][_tigAsset] = 0;

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/BondNFT.sol#L201

```solidity
162: _idToOi[_asset][_tigAsset].longOi = 0;

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/PairsContract.sol#L162

```solidity
182: _idToOi[_asset][_tigAsset].shortOi = 0;

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/PairsContract.sol#L182

```solidity
173: _trade.orderType = 0;

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Position.sol#L173

```solidity
730: _fees.botFees = 0;

```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/Trading.sol#L730





### <a href="#Summary">[NC&#x2011;11]</a><a name="NC&#x2011;11"> Use Underscores for Number Literals

i.e. Use 10_000 instead of 10000 and 1_000_000_000_000 instead of 1000000000000 for readability.

#### <ins>Proof Of Concept</ins>


```solidity
16: uint256 private constant MAX = 10000;
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L16

```solidity
66: require(tokenId <= 10000, "BadID");
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/GovNFT.sol#L66

```solidity
26: uint public maxGasPrice = 1000000000000; // 1000 gwei
```

https://github.com/code-423n4/2022-12-tigris/tree/main/contracts/TradingExtension.sol#L26






