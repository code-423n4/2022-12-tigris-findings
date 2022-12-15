## Summary
### Low Risk Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
|[L-01]|  `claim`  and `deposit` allows re-entrancy from hookable tokens| 2 |
|[L-02]| Front running attacks by the owner | 1 |
|[L-03]| No check if `OnERC721Received` is implemented| 1 |
|[L-04]| Draft Openzeppelin Dependencies| 1 |
|[L-05]| There is a risk that the `setFees` variable is accidentally initialized to 0 and platform loses money| 1 |
|[L-06]| Use ```safeTransferOwnership``` instead of ```transferOwnership``` function | 6 |
|[L-07]| Owner can renounce Ownership  | 6 |
|[L-08]| Missing Event for critical parameters init and change| 4 |
|[L-09]| A single point of failure | 6 |
|[L-10]| Loss of precision due to rounding| 1 |

Total 10 issues
### Non-Critical Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
| [N-01]|Insufficient coverage|1|
| [N-02] |Critical Address Changes Should Use Two-step Procedure |13|
| [N-03] |Initial value check is missing in Set Functions| 16 |
| [N-04] |Use a single file for all system-wide constants | 8 |
| [N-05] |NatSpec comments should be increased in contracts | All Conracts|
| [N-06] |`Function writing` that does not comply with the `Solidity Style Guide`| All Contracts |
| [N-07] |Add a timelock to critical functions| 2 |
| [N-08] |Use a more recent version of Solidity | All Contracts |
| [N-09] |Solidity compiler optimizations can be problematic|  |
| [N-10] |For modern and more readable code; update import usages | 57  |
| [N-11] |Include return parameters in NatSpec comments| All Contracts |
| [N-12] |Long lines are not suitable for the ‘Solidity Style Guide’ | 14 |
| [N-13] |Avoid _shadowing_ `inherited state variables`| 1 |
| [N-14] |Constant values such as a call to DAY should used to immutable rather than constant| 37 |
| [N-15] |Need Fuzzing test | 35 |
| [N-16] |Remove : import 'hardhat/console.sol';| 1 |
| [N-17] |Compliance with Solidity Style rules in Constant expressions| 2 |
| [N-18] |Use `uint256` instead `uint`| 309 |
| [N-19] |_Lock pragmas_ to specific compiler version| 16 |
| [N-20] |Use underscores for number literals| 9 |
| [N-21] |Use of bytes.concat() instead of abi.encodePacked()| 1 |
| [N-22] |Revert messages are too short or not| 8 |

Total 22 issues

### Suggestions
| Number | Suggestion Details |
|:--:|:-------|
| [S-01] |Make the Test Context with Solidity |
| [S-02] |Project Upgrade and Stop Scenario should be |
| [S-03] |Generate perfect code headers every time |

Total 3 suggestions

### [L-01] `claim`  and `deposit` allows re-entrancy from hookable tokens

Some token standards, such as ERC777, allow a callback to the source of the funds before the balances are updated in transferFrom(). This callback could be used to re-enter the protocol while already holding the minted tranche tokens and at a point where the system accounting reflects a receipt of funds that has not yet occurred.

Even if an attacker cannot interact with this function due to any restrictions, they may still interact with the rest of the protocol, so care should be taken with any externall calls that may be affected.

```js
contracts/GovNFT.sol:
  273      * @param _tigAsset reward token address
  274:     */
  275:     function claim(address _tigAsset) external {
  276:         address _msgsender = _msgSender();
  277:         uint256 amount = pending(_msgsender, _tigAsset);
  278:         userPaid[_msgsender][_tigAsset] += amount;
  279:         IERC20(_tigAsset).transfer(_msgsender, amount);
  280:     }

contracts/StableVault.sol:
  43      */
  44:     function deposit(address _token, uint256 _amount) public {
  45:         require(allowed[_token], "Token not listed");
  46:         IERC20(_token).transferFrom(_msgSender(), address(this), _amount);
  47:         IERC20Mintable(stable).mintFor(
  48:             _msgSender(),
  49:             _amount*(10**(18-IERC20Mintable(_token).decimals()))
  50:         );
  51:     }

```

### Recommendation;
Add Re-Entrancy Guard to  the function
### [L-02] Front running attacks by the owner 


Project has one  possible attack vectors by the `onlyOwner`:

` dao.Fees` ,  `burnsFees` , `referralFees` ,  `botFees` variable; 
It determines the fees rate
The default deposit fees equal zero.
Can be updated by `onlyOwner` with function `Fees`
 

When a user use feed, expecting to have zero fee , the owner can frontrun the `fee` function and increase fees to  10000 bps , If the  size is big enough, that may be a significant amount of money.


```js
contracts/Trading.sol:
  951       */
  952:     function setFees(bool _open, uint _daoFees, uint _burnFees, uint _referralFees, uint _botFees, uint _percent) external onlyOwner {
  953:         unchecked {
  954:             require(_daoFees >= _botFees+_referralFees*2);
  955:             if (_open) {
  956:                 openFees.daoFees = _daoFees;
  957:                 openFees.burnFees = _burnFees;
  958:                 openFees.referralFees = _referralFees;
  959:                 openFees.botFees = _botFees;
  960:             } else {
  961:                 closeFees.daoFees = _daoFees;
  962:                 closeFees.burnFees = _burnFees;
  963:                 closeFees.referralFees = _referralFees;
  964:                 closeFees.botFees = _botFees;                
  965:             }
  966:             require(_percent <= DIVISION_CONSTANT);
  967:             vaultFundingPercent = _percent;
  968:         }
  969:     }

```


### Recommended Mitigation Steps

Use a timelock to avoid instant changes of the parameters.

## [L-03] No check if `OnERC721Received` is implemented

**Context:**
```solidity

contracts/BondNFT.sol:
  305  
  306:     function _mint(
  307:         address to,
  308:         Bond memory bond
  309:     ) internal {
  310:         unchecked {
  311:             bondPaid[bond.id][bond.asset] = accRewardsPerShare[bond.asset][epoch[bond.asset]] * bond.shares / 1e18;
  312:         }
  313:         _mint(to, bond.id);
  314:     }

```
**Description:**
When minting a NFT, the function does not check if a receiving contract implements onERC721Received().
Check if OnERC721Received is implemented.
If this is intended, ```safemint``` should be used instead of ```mint```


**Recommendation:**
Consider using ```_safeMint``` instead of ```_mint```

### [L-04] Draft Openzeppelin Dependencies

The `StableToken.sol` contracts utilised `draft-ERC20Permit.sol`, an OpenZeppelin contract. This contract is still a draft and are not considered ready for mainnet use. OpenZeppelin contracts may be considered draft contracts if they have not received adequate security auditing or are liable to change with future development.


```solidity

contracts/StableToken.sol:
  1: // SPDX-License-Identifier: MIT
  2: pragma solidity ^0.8.0;
  3: 
  4: import "@openzeppelin/contracts/token/ERC20/extensions/draft-ERC20Permit.sol";

```
### [L-05] There is a risk that the `setFees` variable is accidentally initialized to 0 and platform loses money


### Impact

There is a risk that the `fees` variables are accidentally initialized to 0


In addition, it is a strong belief that it will not be 0, as it is an issue that will affect the platform revenues.

Although the value initialized with 0 by mistake or forgetting can be changed later by onlyOwner, in the first place it can be exploited by users and cause huge amount  usage

`require == 0` should not be made because of the possibility of the platform defining the `0` rate.

However, checking with a bool against 0 must be added by mistake;

```solidity

contracts/Trading.sol:
  951       */
  952:     function setFees(bool _open, uint _daoFees, uint _burnFees, uint _referralFees, uint _botFees, uint _percent) external onlyOwner {
  953:         unchecked {
  954:             require(_daoFees >= _botFees+_referralFees*2);
  955:             if (_open) {
  956:                 openFees.daoFees = _daoFees;
  957:                 openFees.burnFees = _burnFees;
  958:                 openFees.referralFees = _referralFees;
  959:                 openFees.botFees = _botFees;
  960:             } else {
  961:                 closeFees.daoFees = _daoFees;
  962:                 closeFees.burnFees = _burnFees;
  963:                 closeFees.referralFees = _referralFees;
  964:                 closeFees.botFees = _botFees;                
  965:             }
  966:             require(_percent <= DIVISION_CONSTANT);
  967:             vaultFundingPercent = _percent;
  968:         }
  969:     }
```


### [L-06] Use ```safeTransferOwnership``` instead of ```transferOwnership``` function

**Context:**
```solidity
6 results - 6 files

contracts/BondNFT.sol:
  6: import "@openzeppelin/contracts/access/Ownable.sol";
  8: contract BondNFT is ERC721Enumerable, Ownable {

contracts/Lock.sol:
   5: import "@openzeppelin/contracts/access/Ownable.sol";
  10: contract Lock is Ownable{

contracts/PairsContract.sol:
  4: import "@openzeppelin/contracts/access/Ownable.sol";
  8: contract PairsContract is Ownable, IPairsContract {

contracts/Referrals.sol:
  4: import "@openzeppelin/contracts/access/Ownable.sol";
  7: contract Referrals is Ownable, IReferrals {

contracts/TradingExtension.sol:
   4: import "@openzeppelin/contracts/access/Ownable.sol";
  10: contract TradingExtension is Ownable{


contracts/utils/MetaContext.sol:
  4: import "@openzeppelin/contracts/access/Ownable.sol";
  6: contract MetaContext is Ownable {

```

**Description:**
```transferOwnership``` function is used to change Ownership from ```Ownable.sol```.

Use a 2 structure transferOwnership which is safer. 
```safeTransferOwnership```,  use it is more secure due to 2-stage ownership transfer.

**Recommendation:**
Use ``Ownable2Step.sol``
[Ownable2Step.sol](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable2Step.sol)


### [L-07] Owner can renounce Ownership

**Context:**
```solidity
6 results - 6 files

contracts/BondNFT.sol:
  6: import "@openzeppelin/contracts/access/Ownable.sol";
  8: contract BondNFT is ERC721Enumerable, Ownable {

contracts/Lock.sol:
   5: import "@openzeppelin/contracts/access/Ownable.sol";
  10: contract Lock is Ownable{

contracts/PairsContract.sol:
  4: import "@openzeppelin/contracts/access/Ownable.sol";
  8: contract PairsContract is Ownable, IPairsContract {

contracts/Referrals.sol:
  4: import "@openzeppelin/contracts/access/Ownable.sol";
  7: contract Referrals is Ownable, IReferrals {

contracts/TradingExtension.sol:
   4: import "@openzeppelin/contracts/access/Ownable.sol";
  10: contract TradingExtension is Ownable{


contracts/utils/MetaContext.sol:
  4: import "@openzeppelin/contracts/access/Ownable.sol";
  6: contract MetaContext is Ownable {

```

**Description:**
Typically, the contract’s owner is the account that deploys the contract. As a result, the owner is able to perform certain privileged activities.

The Openzeppelin’s Ownable used in this project contract implements renounceOwnership. This can represent a certain risk if the ownership is renounced for any other reason than by design. Renouncing ownership will leave the contract without an owner, thereby removing any functionality that is only available to the owner.

`onlyOwner` functions;
```js
45 results - 11 files

contracts/BondNFT.sol:
  349:     function addAsset(address _asset) external onlyOwner {
  357:     function setAllowedAsset(address _asset, bool _bool) external onlyOwner {
  362:     function setBaseURI(string calldata _newBaseURI) external onlyOwner {
  368:     ) public onlyOwner() {

contracts/GovNFT.sol:
   46:     function setBaseURI(string calldata _newBaseURI) external onlyOwner {
  104:     function mintMany(uint _amount) external onlyOwner {
  110:     function mint() external onlyOwner {
  114:     function setTrustedAddress(uint16 _chainId, address _contract, bool _bool) external onlyOwner {
  236:     function setGas(uint _gas) external onlyOwner {
  240:     function setEndpoint(ILayerZeroEndpoint _endpoint) external onlyOwner {
  300:     function addAsset(address _asset) external onlyOwner {
  307:     function setAllowedAsset(address _asset, bool _bool) external onlyOwner {
  311:     function setMaxBridge(uint256 _max) external onlyOwner {

contracts/Lock.sol:
  130:     ) external onlyOwner() {
  140:     ) external onlyOwner() {

contracts/PairsContract.sol:
   33:     function setAssetChainlinkFeed(uint256 _asset, address _feed) external onlyOwner {
   48:     function addAsset(uint256 _asset, string memory _name, address _chainlinkFeed, uint256 _minLeverage, uint256 _maxLeverage, uint256 _feeMultiplier, uint256 _baseFundingRate) external onlyOwner {
   73:     function updateAssetLeverage(uint256 _asset, uint256 _minLeverage, uint256 _maxLeverage) external onlyOwner {
   92:     function setAssetBaseFundingRate(uint256 _asset, uint256 _baseFundingRate) external onlyOwner {
  104:     function updateAssetFeeMultiplier(uint256 _asset, uint256 _feeMultiplier) external onlyOwner {
  115:     function pauseAsset(uint256 _asset, bool _isPaused) external onlyOwner {
  125:     function setMaxBaseFundingRate(uint256 _maxBaseFundingRate) external onlyOwner {
  129:     function setProtocol(address _protocol) external onlyOwner {
  139:     function setMaxOi(uint256 _asset, address _tigAsset, uint256 _maxOi) external onlyOwner {

contracts/Position.sol:
   85:     function setBaseURI(string memory _newBaseURI) external onlyOwner {
  310:     function setMinter(address _minter, bool _bool) external onlyOwner {

contracts/Referrals.sol:
  53:     function setProtocol(address _protocol) external onlyOwner {
  65:     ) external onlyOwner {

contracts/StableToken.sol:
  43:         onlyOwner()

contracts/StableVault.sol:
  78:     function listToken(address _token) external onlyOwner {
  89:     function delistToken(address _token) external onlyOwner {

contracts/Trading.sol:
  902:         onlyOwner
  917:         onlyOwner
  930:         onlyOwner
  939:     function setLimitOrderPriceRange(uint _range) external onlyOwner {
  952:     function setFees(bool _open, uint _daoFees, uint _burnFees, uint _referralFees, uint _botFees, uint _percent) external onlyOwner {
  977:     ) external onlyOwner() {

contracts/TradingExtension.sol:
  144:     function setMaxGasPrice(uint _maxGasPrice) external onlyOwner {
  226:         onlyOwner
  231:     function setChainlinkEnabled(bool _bool) external onlyOwner {
  240:     function setNode(address _node, bool _bool) external onlyOwner {
  254:         onlyOwner
  269:         onlyOwner
  274:     function setPaused(bool _paused) external onlyOwner {

contracts/utils/MetaContext.sol:
  9:     function setTrustedForwarder(address _forwarder, bool _bool) external onlyOwner {


```

**Recommendation:**
We recommend to either reimplement the function to disable it or to clearly specify if it is part of the contract design.



### [L-08] Missing Event for critical parameters init and change

**Context:**
```js

contracts/BondNFT.sol:
  39  
  40:     constructor(
  41:         string memory _setBaseURI,
  42:         string memory _name,
  43:         string memory _symbol
  44:     ) ERC721(_name, _symbol) {
  45:         baseURI = _setBaseURI;
  46:     }

contracts/Lock.sol:
  20  
  21:     constructor(
  22:         address _bondNFTAddress,
  23:         address _govNFT
  24:     ) {
  25:         bondNFT = IBondNFT(_bondNFTAddress);
  26:         govNFT = IGovNFT(_govNFT);
  27:     }

contracts/Position.sol:
  75  
  76:     constructor(string memory _setBaseURI, string memory _name, string memory _symbol) ERC721(_name, _symbol) {
  77:         baseURI = _setBaseURI;
  78:         _tokenIds.increment();
  79:     }

contracts/Trading.sol:
  142  
  143:     constructor(
  144:         address _position,
  145:         address _gov,
  146:         address _pairsContract
  147:     )
  148:     {
  149:         position = IPosition(_position);
  150:         gov = IGovNFT(_gov);
  151:         pairsContract = IPairsContract(_pairsContract);
  152:     }

contracts/StableVault.sol:
  34  
  35:     constructor(address _stable) {
  36:         stable = _stable;
  37:     }
  38  

```

**Description:**
Events help non-contract tools to track changes, and events prevent users from being surprised by changes

**Recommendation:**
Add Event-Emit

### [L-09]  A single point of failure 

```solidity
6 results - 6 files

contracts/BondNFT.sol:
  6: import "@openzeppelin/contracts/access/Ownable.sol";
  8: contract BondNFT is ERC721Enumerable, Ownable {

contracts/Lock.sol:
   5: import "@openzeppelin/contracts/access/Ownable.sol";
  10: contract Lock is Ownable{

contracts/PairsContract.sol:
  4: import "@openzeppelin/contracts/access/Ownable.sol";
  8: contract PairsContract is Ownable, IPairsContract {

contracts/Referrals.sol:
  4: import "@openzeppelin/contracts/access/Ownable.sol";
  7: contract Referrals is Ownable, IReferrals {

contracts/TradingExtension.sol:
   4: import "@openzeppelin/contracts/access/Ownable.sol";
  10: contract TradingExtension is Ownable{


contracts/utils/MetaContext.sol:
  4: import "@openzeppelin/contracts/access/Ownable.sol";
  6: contract MetaContext is Ownable {

```

## Impact

The `owner` role has a single point of failure and `onlyOwner` can use critical a few functions.

`owner` role in the project:
Owner is not behind a multisig and changes are not behind a timelock.

Even if protocol admins/developers are not malicious there is still a chance for Owner keys to be stolen. In such a case, the attacker can cause serious damage to the project due to important functions. In such a case, users who have invested in project will suffer high financial losses.


`onlyOwner` functions;
```js
45 results - 11 files

contracts/BondNFT.sol:
  349:     function addAsset(address _asset) external onlyOwner {
  357:     function setAllowedAsset(address _asset, bool _bool) external onlyOwner {
  362:     function setBaseURI(string calldata _newBaseURI) external onlyOwner {
  368:     ) public onlyOwner() {

contracts/GovNFT.sol:
   46:     function setBaseURI(string calldata _newBaseURI) external onlyOwner {
  104:     function mintMany(uint _amount) external onlyOwner {
  110:     function mint() external onlyOwner {
  114:     function setTrustedAddress(uint16 _chainId, address _contract, bool _bool) external onlyOwner {
  236:     function setGas(uint _gas) external onlyOwner {
  240:     function setEndpoint(ILayerZeroEndpoint _endpoint) external onlyOwner {
  300:     function addAsset(address _asset) external onlyOwner {
  307:     function setAllowedAsset(address _asset, bool _bool) external onlyOwner {
  311:     function setMaxBridge(uint256 _max) external onlyOwner {

contracts/Lock.sol:
  130:     ) external onlyOwner() {
  140:     ) external onlyOwner() {

contracts/PairsContract.sol:
   33:     function setAssetChainlinkFeed(uint256 _asset, address _feed) external onlyOwner {
   48:     function addAsset(uint256 _asset, string memory _name, address _chainlinkFeed, uint256 _minLeverage, uint256 _maxLeverage, uint256 _feeMultiplier, uint256 _baseFundingRate) external onlyOwner {
   73:     function updateAssetLeverage(uint256 _asset, uint256 _minLeverage, uint256 _maxLeverage) external onlyOwner {
   92:     function setAssetBaseFundingRate(uint256 _asset, uint256 _baseFundingRate) external onlyOwner {
  104:     function updateAssetFeeMultiplier(uint256 _asset, uint256 _feeMultiplier) external onlyOwner {
  115:     function pauseAsset(uint256 _asset, bool _isPaused) external onlyOwner {
  125:     function setMaxBaseFundingRate(uint256 _maxBaseFundingRate) external onlyOwner {
  129:     function setProtocol(address _protocol) external onlyOwner {
  139:     function setMaxOi(uint256 _asset, address _tigAsset, uint256 _maxOi) external onlyOwner {

contracts/Position.sol:
   85:     function setBaseURI(string memory _newBaseURI) external onlyOwner {
  310:     function setMinter(address _minter, bool _bool) external onlyOwner {

contracts/Referrals.sol:
  53:     function setProtocol(address _protocol) external onlyOwner {
  65:     ) external onlyOwner {

contracts/StableToken.sol:
  43:         onlyOwner()

contracts/StableVault.sol:
  78:     function listToken(address _token) external onlyOwner {
  89:     function delistToken(address _token) external onlyOwner {

contracts/Trading.sol:
  902:         onlyOwner
  917:         onlyOwner
  930:         onlyOwner
  939:     function setLimitOrderPriceRange(uint _range) external onlyOwner {
  952:     function setFees(bool _open, uint _daoFees, uint _burnFees, uint _referralFees, uint _botFees, uint _percent) external onlyOwner {
  977:     ) external onlyOwner() {

contracts/TradingExtension.sol:
  144:     function setMaxGasPrice(uint _maxGasPrice) external onlyOwner {
  226:         onlyOwner
  231:     function setChainlinkEnabled(bool _bool) external onlyOwner {
  240:     function setNode(address _node, bool _bool) external onlyOwner {
  254:         onlyOwner
  269:         onlyOwner
  274:     function setPaused(bool _paused) external onlyOwner {

contracts/utils/MetaContext.sol:
  9:     function setTrustedForwarder(address _forwarder, bool _bool) external onlyOwner {


```

This increases the risk of ```A single point of failure```




## Tools Used
Manuel Code Review


## Recommended Mitigation Steps
Add a time lock to  critical functions. Admin-only functions that change critical parameters should emit events and have timelocks. 

Events allow capturing the changed parameters so that off-chain tools/interfaces can register such changes with timelocks that allow users to evaluate them and consider if they would like to engage/exit based on how they perceive the changes as affecting the trustworthiness of the protocol or profitability of the implemented financial services.

Allow only multi-signature wallets to call the function to reduce the likelihood of an attack.

https://twitter.com/danielvf/status/1572963475101556738?s=20&t=V1kvzfJlsx-D2hfnG0OmuQ

Also detail them in documentation and NatSpec comments


### [L-10] Loss of precision due to rounding

Loss of precision due to rounding to `_price` value, the higher the value of _price, the greater the loss of precision

Add scalars so roundings are negligible


```solidity
_positionSize = _initPositionSize * _currentPrice / _price;

```

```solidity
contracts/utils/TradingLibrary.sol:
  35      */
  36:     function pnl(bool _direction, uint _currentPrice, uint _price, uint _margin, uint _leverage, int256 accInterest) external pure returns (uint256 _positionSize, int256 _payout) {
  37:         unchecked {
  38:             uint _initPositionSize = _margin * _leverage / 1e18;
  39:             if (_direction && _currentPrice >= _price) {
  40:                 _payout = int256(_margin) + int256(_initPositionSize * (1e18 * _currentPrice / _price - 1e18)/1e18) + accInterest;
  41:             } else if (_direction && _currentPrice < _price) {
  42:                 _payout = int256(_margin) - int256(_initPositionSize * (1e18 - 1e18 * _currentPrice / _price)/1e18) + accInterest;
  43:             } else if (!_direction && _currentPrice <= _price) {
  44:                 _payout = int256(_margin) + int256(_initPositionSize * (1e18 - 1e18 * _currentPrice / _price)/1e18) + accInterest;
  45:             } else {
  46:                 _payout = int256(_margin) - int256(_initPositionSize * (1e18 * _currentPrice / _price - 1e18)/1e18) + accInterest;
  47:             }
  48:             _positionSize = _initPositionSize * _currentPrice / _price;
  49:         }
  50:     }


```


### [N-01] Insufficient coverage

**Description:**
The test coverage rate of the project is 97%. Testing all functions is best practice in terms of security criteria.

```js

-----------------------|----------|----------|----------|----------|----------------|
File                   |  % Stmts | % Branch |  % Funcs |  % Lines |Uncovered Lines |
-----------------------|----------|----------|----------|----------|----------------|
 contracts/            |    98.44 |    95.38 |    97.22 |    98.51 |                |
  BondNFT.sol          |      100 |      100 |      100 |      100 |                |
  Forwarder.sol        |      100 |      100 |      100 |      100 |                |
  GovNFT.sol           |    90.28 |    64.52 |    90.63 |     89.8 |... 215,312,336 |
  Lock.sol             |      100 |      100 |      100 |      100 |                |
  PairsContract.sol    |      100 |      100 |      100 |      100 |                |
  Position.sol         |    98.28 |      100 |    96.77 |    99.15 |            324 |
  Referrals.sol        |      100 |      100 |      100 |      100 |                |
  StableToken.sol      |       80 |      100 |    85.71 |    85.71 |             63 |
  StableVault.sol      |      100 |      100 |      100 |      100 |                |
  Trading.sol          |      100 |      100 |      100 |      100 |                |
  TradingExtension.sol |      100 |    98.57 |      100 |      100 |                |
 contracts/utils/      |      100 |    97.06 |      100 |      100 |                |
  MetaContext.sol      |      100 |    83.33 |      100 |      100 |                |
  TradingLibrary.sol   |      100 |      100 |      100 |      100 |                |
-----------------------|----------|----------|----------|----------|----------------|
All files              |     98.5 |    95.49 |    97.34 |    98.57 |                |
-----------------------|----------|----------|----------|----------|----------------|

```
Due to its capacity, test coverage is expected to be 100%.


### [N-02] Critical Address Changes Should Use Two-step Procedure

The critical procedures should be two step process.


```solidity

13 results

contracts/BondNFT.sol:
357:     function setAllowedAsset(address _asset, bool _bool) external onlyOwner {
366:     function setManager(

contracts/GovNFT.sol:
114:     function setTrustedAddress(uint16 _chainId, address _contract, bool _bool) external onlyOwner {
307:     function setAllowedAsset(address _asset, bool _bool) external onlyOwner {

contracts/PairsContract.sol:
129:     function setProtocol(address _protocol) external onlyOwner {
139:     function setMaxOi(uint256 _asset, address _tigAsset, uint256 _maxOi) external onlyOwner {

contracts/Position.sol:
  310:     function setMinter(address _minter, bool _bool) external onlyOwner {

contracts/Referrals.sol:
   32:     function setReferred(address _referredTrader, bytes32 _hash) external onlyProtocol {
   53:     function setProtocol(address _protocol) external onlyOwner {

contracts/StableToken.sol:
  38:     function setMinter(

contracts/TradingExtension.sol:
  240:     function setNode(address _node, bool _bool) external onlyOwner {
  249:     function setAllowedMargin(

contracts/utils/MetaContext.sol:
  9:     function setTrustedForwarder(address _forwarder, bool _bool) external onlyOwner {

```

Recommended Mitigation Steps
Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.


### [N-03] Initial value check is missing in Set Functions

**Context:**


```js

16 results

contracts/BondNFT.sol:
  357:     function setAllowedAsset(address _asset, bool _bool) external onlyOwner {
  366:     function setManager(

contracts/GovNFT.sol:
  307:     function setAllowedAsset(address _asset, bool _bool) external onlyOwner {

contracts/PairsContract.sol:
   33:     function setAssetChainlinkFeed(uint256 _asset, address _feed) external onlyOwner {
  129:     function setProtocol(address _protocol) external onlyOwner {
  139:     function setMaxOi(uint256 _asset, address _tigAsset, uint256 _maxOi) external onlyOwner {

contracts/Position.sol:
  310:     function setMinter(address _minter, bool _bool) external onlyOwner {

contracts/Referrals.sol:
  32:     function setReferred(address _referredTrader, bytes32 _hash) external onlyProtocol {
  53:     function setProtocol(address _protocol) external onlyOwner {

contracts/StableToken.sol:
  38:     function setMinter(

contracts/Trading.sol:
  939:     function setLimitOrderPriceRange(uint _range) external onlyOwner {
  952:     function setFees(bool _open, uint _daoFees, uint _burnFees, uint _referralFees, uint _botFees, uint _percent) external onlyOwner {
  975:     function setTradingExtension(

contracts/TradingExtension.sol:
  249:     function setAllowedMargin(
  264:     function setMinPositionSize(

contracts/utils/MetaContext.sol:
  9:     function setTrustedForwarder(address _forwarder, bool _bool) external onlyOwner {

```



Checking whether the current value and the new value are the same should be added

### [N-04] Use a single file for all system-wide constants

There are many addresses and constants used in the system. It is recommended to put the most used ones in one file (for example constants.sol, use inheritance to access these values)

  This will help with readability and easier maintenance for future changes. 

constants.sol
Use and import this file in contracts that require access to these values. This is just a suggestion, in some use cases this may result in higher gas usage in the distribution

```solidity
8 results

contracts/BondNFT.sol:
  10:     uint constant private DAY = 24 * 60 * 60;

contracts/GovNFT.sol:
  16:     uint256 private constant MAX = 10000;

contracts/Lock.sol:
  12:     uint public constant minPeriod = 7;
  13:     uint public constant maxPeriod = 365;

contracts/Position.sol:
  16:     uint constant public DIVISION_CONSTANT = 1e10; // 100%

contracts/Trading.sol:
  95:     uint constant private DIVISION_CONSTANT = 1e10; // 100%

contracts/TradingExtension.sol:
  11:     uint constant private DIVISION_CONSTANT = 1e10; // 100%

contracts/utils/ExcessivelySafeCall.sol:
  5:     uint256 constant LOW_28_MASK =


```
### [N-05] NatSpec comments should be increased in contracts

**Context:**
All Contracts

**Description:**
It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation.
In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.
https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

**Recommendation:**
NatSpec comments should be increased in contracts

### [N-06] `Function writing` that does not comply with the `Solidity Style Guide`

**Context:**
All Contracts

**Description:**
Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

Functions should be grouped according to their visibility and ordered:

 constructor
receive function (if exists)
fallback function (if exists)
external
public
internal
private
within a grouping, place the view and pure functions last

### [N-07] Add a timelock to critical functions

It is a good practice to give time for users to react and adjust to critical changes. A timelock provides more guarantees and reduces the level of trust required, thus decreasing risk for users. It also indicates that the project is legitimate (less risk of a malicious owner making a sandwich attack on a user).
Consider adding a timelock to:

```solidity

2 results 

contracts/BondNFT.sol:
  361  
  362:     function setBaseURI(string calldata _newBaseURI) external onlyOwner {
  363:         baseURI = _newBaseURI;
  364:     }

  366:     function setManager(
  367:         address _manager
  368:     ) public onlyOwner() {
  369:         manager = _manager;
  370:     }



```

### [N-08] Use a more recent version of Solidity

**Context:**
All contracts (^0.8.0)

**Description:**
For security, it is best practice to use the latest Solidity version.
For the security fix list in the versions;
https://github.com/ethereum/solidity/blob/develop/Changelog.md


**Recommendation:**
Old version of Solidity is used , newer version can be used `(0.8.17)` 

### [N-09] Solidity compiler optimizations can be problematic

```js
hardhat.config.js:
  29  module.exports = {
  30:   solidity: {
  31:     compilers: [
  32:       {
  33:         version: "0.8.17",
  34:         settings: {
  35:           optimizer: {
  36:               enabled: true,
  37:               runs: 1000000
  38            }

```

**Description:**
Protocol has enabled optional compiler optimizations in Solidity.
There have been several optimization bugs with security implications. Moreover, optimizations are actively being developed. Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them. 

Therefore, it is unclear how well they are being tested and exercised.
High-severity security issues due to optimization bugs have occurred in the past. A high-severity bug in the emscripten-generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG. 

Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. More recently, another bug due to the incorrect caching of keccak256 was reported.
A compiler audit of Solidity from November 2018 concluded that the optional optimizations may not be safe.
It is likely that there are latent bugs related to optimization and that new bugs will be introduced due to future optimizations.

Exploit Scenario
A latent or future bug in Solidity compiler optimizations—or in the Emscripten transpilation to solc-js—causes a security vulnerability in the contracts.


**Recommendation:**
Short term, measure the gas savings from optimizations and carefully weigh them against the possibility of an optimization-related bug.
Long term, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.

### [N-10] For modern and more readable code; update import usages

**Context:**

```solidity
57 results - 16 files

contracts/BondNFT.sol:
  4: import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Enumerable.sol";
  5: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
  6: import "@openzeppelin/contracts/access/Ownable.sol";

contracts/GovNFT.sol:
   4: import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Enumerable.sol";
   5: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
   6: import "./interfaces/ILayerZeroEndpoint.sol";
   7: import "./interfaces/ILayerZeroReceiver.sol";
   8: import "./utils/MetaContext.sol";
   9: import "./interfaces/IGovNFT.sol";
  10: import "./utils/ExcessivelySafeCall.sol";

contracts/GovNFTBridged.sol:
   4: import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Enumerable.sol";
   5: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
   6: import "./interfaces/ILayerZeroEndpoint.sol";
   7: import "./interfaces/ILayerZeroReceiver.sol";
   8: import "./utils/MetaContext.sol";
   9: import "./interfaces/IGovNFT.sol";
  10: import "./utils/ExcessivelySafeCall.sol";

contracts/Lock.sol:
  4: import "hardhat/console.sol";
  5: import "@openzeppelin/contracts/access/Ownable.sol";
  6: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
  7: import "./interfaces/IBondNFT.sol";
  8: import "./interfaces/IGovNFT.sol";

contracts/NFTSale.sol:
  4: import "@openzeppelin/contracts/access/Ownable.sol";

contracts/PairsContract.sol:
  4: import "@openzeppelin/contracts/access/Ownable.sol";
  5: import "./interfaces/IPairsContract.sol";
  6: import "./interfaces/IPosition.sol";

contracts/Position.sol:
  4: import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Enumerable.sol";
  5: import "@openzeppelin/contracts/utils/Counters.sol";
  6: import "./utils/MetaContext.sol";
  7: import "./interfaces/IPosition.sol";

contracts/Referrals.sol:
  4: import "@openzeppelin/contracts/access/Ownable.sol";
  5: import "./interfaces/IReferrals.sol";

contracts/StableToken.sol:
  4: import "@openzeppelin/contracts/token/ERC20/extensions/draft-ERC20Permit.sol";
  5: import "./utils/MetaContext.sol";

contracts/StableVault.sol:
  4: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
  5: import "@openzeppelin/contracts/security/ReentrancyGuard.sol";
  6: import "./utils/MetaContext.sol";
  7: import "./interfaces/IStableVault.sol";

contracts/Trading.sol:
   4: import "./utils/MetaContext.sol";
   5: import "./interfaces/ITrading.sol";
   6: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
   7: import "./interfaces/IPairsContract.sol";
   8: import "./interfaces/IReferrals.sol";
   9: import "./interfaces/IPosition.sol";
  10: import "./interfaces/IGovNFT.sol";
  11: import "./interfaces/IStableVault.sol";
  12: import "./utils/TradingLibrary.sol";

contracts/TradingExtension.sol:
  4: import "@openzeppelin/contracts/access/Ownable.sol";
  5: import "./interfaces/IPairsContract.sol";
  6: import "./utils/TradingLibrary.sol";
  7: import "./interfaces/IReferrals.sol";
  8: import "./interfaces/IPosition.sol";

contracts/interfaces/ILayerZeroEndpoint.sol:
  5: import "./ILayerZeroUserApplicationConfig.sol";

contracts/interfaces/ITrading.sol:
  3: import "../utils/TradingLibrary.sol";

contracts/utils/MetaContext.sol:
  4: import "@openzeppelin/contracts/access/Ownable.sol";

contracts/utils/TradingLibrary.sol:
  4: import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";
  5: import "../interfaces/IPosition.sol";
```


**Description:**
Solidity code is also cleaner in another way that might not be noticeable: the struct Point. We were importing it previously with global import but not using it. The Point struct `polluted the source code` with an unnecessary object we were not using because we did not need it. 
This was breaking the rule of modularity and modular programming: `only import what you need` Specific imports with curly braces allow us to apply this rule better.

**Recommendation:**
`import {contract1 , contract2} from "filename.sol";`

A good example from the ArtGobblers project;
```js
import {Owned} from "solmate/auth/Owned.sol";
import {ERC721} from "solmate/tokens/ERC721.sol";
import {LibString} from "solmate/utils/LibString.sol";
import {MerkleProofLib} from "solmate/utils/MerkleProofLib.sol";
import {FixedPointMathLib} from "solmate/utils/FixedPointMathLib.sol";
import {ERC1155, ERC1155TokenReceiver} from "solmate/tokens/ERC1155.sol";
import {toWadUnsafe, toDaysWadUnsafe} from "solmate/utils/SignedWadMath.sol";
```

### [N-11] Include return parameters in NatSpec comments

**Context:**
All Contracts

**Description:**
It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation. In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.

https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

**Recommendation:**
Include return parameters in NatSpec comments

_Recommendation  Code Style: (from Uniswap3)_
```js
    /// @notice Adds liquidity for the given recipient/tickLower/tickUpper position
    /// @dev The caller of this method receives a callback in the form of IUniswapV3MintCallback#uniswapV3MintCallback
    /// in which they must pay any token0 or token1 owed for the liquidity. The amount of token0/token1 due depends
    /// on tickLower, tickUpper, the amount of liquidity, and the current price.
    /// @param recipient The address for which the liquidity will be created
    /// @param tickLower The lower tick of the position in which to add liquidity
    /// @param tickUpper The upper tick of the position in which to add liquidity
    /// @param amount The amount of liquidity to mint
    /// @param data Any data that should be passed through to the callback
    /// @return amount0 The amount of token0 that was paid to mint the given amount of liquidity. Matches the value in the callback
    /// @return amount1 The amount of token1 that was paid to mint the given amount of liquidity. Matches the value in the callback
    function mint(
        address recipient,
        int24 tickLower,
        int24 tickUpper,
        uint128 amount,
        bytes calldata data
    ) external returns (uint256 amount0, uint256 amount1);
```

## [N-12] Long lines are not suitable for the ‘Solidity Style Guide’

**Context:**
[Trading.sol#L178](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L178)
[Trading.sol#L496](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L496)
[Trading.sol#L543](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L543)
[Trading.sol#L624](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L624)
[Position.sol#L48](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L48)
[Position.sol#L62](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L62)
[Position.sol#L153](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L153)
[Position.sol#L269](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L269)
[Position.sol#L270](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L270)
[GovNFT.sol#L175](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L175)
[BondNFT.sol#L178](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L178)
[TradingLibrary.sol#L36](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L36)
[TradingLibrary.sol#L62](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L62)

**Description:**
It is generally recommended that lines in the source code should not exceed 80-120 characters. Today's screens are much larger, so in some cases it makes sense to expand that. The lines above should be split when they reach that length, as the files will most likely be on GitHub and GitHub always uses a scrollbar when the length is more than 164 characters.

(why-is-80-characters-the-standard-limit-for-code-width)[https://softwareengineering.stackexchange.com/questions/148677/why-is-80-characters-the-standard-limit-for-code-width]

**Recommendation:**
Multiline output parameters and return statements should follow the same style recommended for wrapping long lines found in the Maximum Line Length section.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html#introduction

```js
thisFunctionCallIsReallyLong(
    longArgument1,
    longArgument2,
    longArgument3
);
```
## [N-13] Avoid _shadowing_ `inherited state variables`

**Context:**
```solidity
 function _burn(uint256 tokenId) internal override {
        address owner = ownerOf(tokenId);
        for (uint i=0; i<assetsLength(); i++) {
            userDebt[owner][assets[i]] += accRewardsPerNFT[assets[i]];
            userDebt[owner][assets[i]] -= userPaid[owner][assets[i]]/balanceOf(owner);
            userPaid[owner][assets[i]] -= userPaid[owner][assets[i]]/balanceOf(owner);            
        }
        super._burn(tokenId);
    }
```
`owner` is shadowed, 

StateVar `Ownable.owner` and `burn()` function local variable `owner`  are same name

**Recommendation:**
Avoid using variables with the same name, including inherited in the same contract, if used, it must be specified in the NatSpec comments.

### [N-14] Constant values such as a call to DAY should used to immutable rather than constant

There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts.

While it doesn't save any gas because the compiler knows that developers often make this mistake, it's still best to use the right tool for the task at hand.

Constants should be used for literal values written into the code, and immutable variables should be used for expressions, or values calculated in, or passed into the constructor.


```solidity
contracts/BondNFT.sol:
   7  
   8: contract BondNFT is ERC721Enumerable, Ownable {
   9:     
  10:     uint constant private DAY = 24 * 60 * 60;
```

### [N-15] Need Fuzzing test

**Context:**

```solidity
37 results - 5 files

contracts/BondNFT.sol:
   63          require(allowedAsset[_asset], "!Asset");
   64:         unchecked {
   65              uint shares = _amount * _period / 365;

  111          require(bond.period+_period <= 365, "MAX PERIOD");
  112:         unchecked {
  113              uint shares = (bond.amount + _amount) * (bond.period + _period) / 365;

  143          if (_releaser != bond.owner) {
  144:             unchecked {
  145                  require(bond.expireEpoch + 7 < epoch[bond.asset], "Bond owner priority");

  148          amount = bond.amount;
  149:         unchecked {
  150              totalShares[bond.asset] -= bond.shares;

  175          tigAsset = bond.asset;
  176:         unchecked {
  177              if (bond.expired) {

  216          IERC20(_tigAsset).transferFrom(_msgSender(), address(this), _amount);
  217:         unchecked {
  218              uint aEpoch = block.timestamp / DAY;

  238          bond.expired = bond.expireEpoch <= epoch[bond.asset] ? true : false;
  239:         unchecked {
  240              uint _accRewardsPerShare = accRewardsPerShare[bond.asset][bond.expired ? bond.expireEpoch-1 : epoch[bond.asset]];

  282      function safeTransferMany(address _to, uint[] calldata _ids) external {
  283:         unchecked {
  284              for (uint i=0; i<_ids.length; i++) {

  290      function safeTransferFromMany(address _from, address _to, uint[] calldata _ids) external {
  291:         unchecked {
  292              for (uint i=0; i<_ids.length; i++) {

  298      function approveMany(address _to, uint[] calldata _ids) external {
  299:         unchecked {
  300              for (uint i=0; i<_ids.length; i++) {

  309      ) internal {
  310:         unchecked {
  311              bondPaid[bond.id][bond.asset] = accRewardsPerShare[bond.asset][epoch[bond.asset]] * bond.shares / 1e18;

  330          require(!bond.expired, "Expired!");
  331:         unchecked {
  332              require(block.timestamp > bond.mintTime + 300, "Recent update");

  340          uint[] memory _ids = new uint[](balanceOf(_user));
  341:         unchecked {
  342              for (uint i=0; i<_ids.length; i++) {

contracts/Trading.sol:
  195          _checkSl(_tradeInfo.slPrice, _tradeInfo.direction, _price);
  196:         unchecked {
  197              if (_tradeInfo.direction) {

  206          );
  207:         unchecked {
  208              emit PositionOpened(_tradeInfo, 0, _price, position.getCount()-1, _trader, _marginAfterFees);

  283          position.setAccInterest(_id);
  284:         unchecked {
  285              (uint256 _price,) = tradingExtension.getVerifiedPrice(_trade.asset, _priceData, _signature, _trade.direction ? 1 : 2);

  394          _handleDeposit(_trade.tigAsset, _marginAsset, _addMargin, _stableVault, _permitData, _trader);
  395:         unchecked {
  396              uint256 _newMargin = _trade.margin + _addMargin;

  434          (,int256 _payout) = TradingLibrary.pnl(_trade.direction, _assetPrice, _trade.price, _newMargin, _newLeverage, _trade.accInterest);
  435:         unchecked {
  436              if (_payout <= int256(_newMargin*(DIVISION_CONSTANT-liqPercent)/DIVISION_CONSTANT)) revert LiqThreshold();

  486      {
  487:         unchecked {
  488              _checkDelay(_id, true);

  536      {
  537:         unchecked {
  538              tradingExtension._checkGas();

  622          if (_payout > 0) {
  623:             unchecked {
  624                  _toMint = _handleCloseFees(_trade.asset, uint256(_payout)*_percent/DIVISION_CONSTANT, _trade.tigAsset, _positionSize*_percent/DIVISION_CONSTANT, _trade.trader, _isBot);

  700          Fees memory _fees = openFees;
  701:         unchecked {
  702              _fees.daoFees = _fees.daoFees * asset.feeMultiplier / DIVISION_CONSTANT;

  708          if (_referrer != address(0)) {
  709:             unchecked {
  710                  IStable(_tigAsset).mintFor(

  719          if (_isBot) {
  720:             unchecked {
  721                  IStable(_tigAsset).mintFor(

  731          }
  732:         unchecked {
  733              uint _daoFeesPaid = _positionSize * _fees.daoFees / DIVISION_CONSTANT;

  777          uint _referralFeesPaid;
  778:         unchecked {
  779              _daoFeesPaid = (_positionSize*_fees.daoFees/DIVISION_CONSTANT)*asset.feeMultiplier/DIVISION_CONSTANT;

  784          if (_referrer != address(0)) {
  785:             unchecked {
  786                  _referralFeesPaid = (_positionSize*_fees.referralFees/DIVISION_CONSTANT)*asset.feeMultiplier/DIVISION_CONSTANT;

  794          if (_isBot) {
  795:             unchecked {
  796                  _botFeesPaid = (_positionSize*_fees.botFees/DIVISION_CONSTANT)*asset.feeMultiplier/DIVISION_CONSTANT;

  857      function _checkDelay(uint _id, bool _type) internal {
  858:         unchecked {
  859              Delay memory _delay = blockDelayPassed[_id];

  952      function setFees(bool _open, uint _daoFees, uint _burnFees, uint _referralFees, uint _botFees, uint _percent) external onlyOwner {
  953:         unchecked {
  954              require(_daoFees >= _botFees+_referralFees*2);

contracts/TradingExtension.sol:
   68  
   69:         unchecked {
   70              if (_trade.direction) {

  211      function validateTrade(uint _asset, address _tigAsset, uint _margin, uint _leverage) external view {
  212:         unchecked {
  213              IPairsContract.Asset memory asset = pairsContract.idToAsset(_asset);

contracts/utils/TradingLibrary.sol:
  36      function pnl(bool _direction, uint _currentPrice, uint _price, uint _margin, uint _leverage, int256 accInterest) external pure returns (uint256 _positionSize, int256 _payout) {
  37:         unchecked {
  38              uint _initPositionSize = _margin * _leverage / 1e18;

```

**Description:**
In total 5 contracts, 37 unchecked are used, the functions used are critical. For this reason, there must be fuzzing tests in the tests of the project. Not seen in tests.

**Recommendation:**
Use should fuzzing test like Echidna.

As Alberto Cuesta Canada said:
_Fuzzing is not easy, the tools are rough, and the math is hard, but it is worth it. Fuzzing gives me a level of confidence in my smart contracts that I didn’t have before. Relying just on unit testing anymore and poking around in a testnet seems reckless now._

https://medium.com/coinmonks/smart-contract-fuzzing-d9b88e0b0a05


Low Risk and Non-Critical Issues
### [N-16] Remove : import 'hardhat/console.sol';

Context: 
```solidity

1 result - 1 file

contracts/Lock.sol:
  1: // SPDX-License-Identifier: UNLICENSED
  2: pragma solidity ^0.8.0;
  3: 
  4: import "hardhat/console.sol";

```

Description:It's used to print the values of variables while running tests to help debug and see what's happening inside your contracts But since it's a development tool, it serves no purpose on mainnet.
It creates code pollution

Recommendation:Use only for tests


### [N-17] Compliance with Solidity Style rules in Constant expressions

**Context:**

```solidity
2 results - 1 file

contracts/Lock.sol:
  12:     uint public constant minPeriod = 7;
  13:     uint public constant maxPeriod = 365;

```

**Recommendation:**
Variables are declared as constant utilize the UPPER_CASE_WITH_UNDERSCORES format.

### [N-18] Use `uint256` instead `uint`

Project use uint and uint256
 
Number of uses:
uint  = 309 results - 21 files
uint256 = 264 results - 20 files


Some developers prefer to use `uint256` because it is consistent with other uint data types, which also specify their size, and also because making the size of the data explicit reminds the developer and the reader how much data they've got to play with, which may help prevent or detect bugs.

For example if doing ```bytes4(keccak('transfer(address, uint)’))```, you'll get a different method sig ID than ```bytes4(keccak('transfer(address, uint256)’))``` and smart contracts will only understand the latter when comparing method sig IDs


### [N-19] _Lock pragmas_ to specific compiler version

**Description:**
Pragma statements can be allowed to float when a contract is intended for consumption by other developers, as in the case with contracts in a library or EthPM package. Otherwise, the developer would need to manually update the pragma in order to compile locally. 
https://swcregistry.io/docs/SWC-103

**Recommendation:**
Ethereum Smart Contract Best Practices - Lock pragmas to specific compiler version.
[solidity-specific/locking-pragmas](https://consensys.github.io/smart-contract-best-practices/development-recommendations/solidity-specific/locking-pragmas/)

```solidity

16 results - 16 files

contracts/BondNFT.sol:
  2: pragma solidity ^0.8.0;

contracts/Faucet.sol:
  2: pragma solidity ^0.8.0;

contracts/Forwarder.sol:
  2: pragma solidity ^0.8.0;

contracts/GovNFT.sol:
  2: pragma solidity ^0.8.0;

contracts/Lock.sol:
  2: pragma solidity ^0.8.0;

contracts/NFTSale.sol:
  2: pragma solidity ^0.8.0;

contracts/PairsContract.sol:
  2: pragma solidity ^0.8.0;

contracts/Position.sol:
  2: pragma solidity ^0.8.0;

contracts/Referrals.sol:
  2: pragma solidity ^0.8.0;

contracts/StableToken.sol:
  2: pragma solidity ^0.8.0;

contracts/StableVault.sol:
  2: pragma solidity ^0.8.0;

contracts/Timelock.sol:
  2: pragma solidity ^0.8.0;

contracts/Trading.sol:
  2: pragma solidity ^0.8.0;

contracts/TradingExtension.sol:
  2: pragma solidity ^0.8.0;

contracts/utils/MetaContext.sol:
  2: pragma solidity ^0.8.0;

contracts/utils/TradingLibrary.sol:
  2: pragma solidity ^0.8.0;
```

### [N-20] Use underscores for number literals

```solidity
9 results - 3 files

contracts/GovNFT.sol:
  16:     uint256 private constant MAX = 10000;
  17:     uint256 public gas = 150000;
  66:         require(tokenId <= 10000, "BadID");

contracts/Position.sol:
  120:         fundingDeltaPerSec[_asset][_tigAsset] = (_oiDelta*int256(_baseFundingRate)/int256(DIVISION_CONSTANT))/31536000;

contracts/TradingExtension.sol:
  26:     uint public maxGasPrice = 1000000000000; // 1000 gwei


```
**Description:**
 There is   occasions where certain number have been hardcoded, either in variable or in the code itself. Large numbers can become hard to read.

**Recommendation:**
Consider using underscores for number literals to improve its readability.

### [N-21] Use of bytes.concat() instead of abi.encodePacked()

```solidity

1 result - 1 file

contracts/GovNFT.sol:
  145:         bytes memory adapterParams = abi.encodePacked(version, _gas);

```
Rather than using `abi.encodePacked` for appending bytes, since version 0.8.4, bytes.concat() is enabled

Since version 0.8.4 for appending bytes, bytes.concat() can be used instead of abi.encodePacked(,)

### [N-22] Revert messages are too short or not


**Description:**
The correct and clear error description explains to the user why the function reverts, but the error descriptions below in the project are not self-explanatory. These error descriptions are very important in the debug features of DApps like Tenderly. Error definitions should be added to the require block, not exceeding 32 bytes.


```solidity

8 results

contracts/Trading.sol:
  363          IPosition.Trade memory _trade = position.trades(_id);
  364:         if (_trade.orderType == 0) revert();
  365          IStable(_trade.tigAsset).mintFor(_trader, _trade.margin);

  391          IPosition.Trade memory _trade = position.trades(_id);
  392:         if (_trade.orderType != 0) revert(); //IsLimit
  393          IPairsContract.Asset memory asset = pairsContract.idToAsset(_trade.asset);

  427          IPosition.Trade memory _trade = position.trades(_id);
  428:         if (_trade.orderType != 0) revert(); //IsLimit
  429          IPairsContract.Asset memory asset = pairsContract.idToAsset(_trade.asset);

contracts/Trading.sol:
  863:                 if (block.number < _delay.delay) revert("0"); 

contracts/TradingExtension.sol:
  122      function _checkGas() public view {
  123:         if (tx.gasprice > maxGasPrice) revert("1"); 

   48:         if (position.ownerOf(_id) != _trader) revert("2"); 

contracts/Trading.sol:
  835          if (_direction) {
  836:             if (_sl > _price) revert("3");
  837          } else {
  838:             if (_sl < _price && _sl != 0) revert("3");

```


