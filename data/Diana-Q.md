## 01 MISSING EVENTS FOR ONLY FUNCTIONS THAT CHANGE CRITICAL PARAMETERS

The afunctions that change critical parameters should emit events. Events allow capturing the changed parameters so that off-chain tools/interfaces can register such changes with timelocks that allow users to evaluate them and consider if they would like to engage/exit based on how they perceive the changes as affecting the trustworthiness of the protocol or profitability of the implemented financial services. The alternative of directly querying on-chain contract state for such changes is not considered practical for most users/usages.

Missing events and timelocks do not promote transparency and if such changes immediately affect users’ perception of fairness or trustworthiness, they could exit the protocol causing a reduction in liquidity which could negatively impact protocol TVL and reputation.

_There are 34 instances of this issue_

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol

```
File: contracts/BondNFT.sol

357: function setAllowedAsset(address _asset, bool _bool) external onlyOwner {
362: function setBaseURI(string calldata _newBaseURI) external onlyOwner {
366: function setManager(
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol

```
File: contracts/GovNFT.sol

46: function setBaseURI(string calldata _newBaseURI) external onlyOwner {
114: function setTrustedAddress(uint16 _chainId, address _contract, bool _bool) external onlyOwner {
236: function setGas(uint _gas) external onlyOwner {
240: function setEndpoint(ILayerZeroEndpoint _endpoint) external onlyOwner {
307: function setAllowedAsset(address _asset, bool _bool) external onlyOwner {
311: function setMaxBridge(uint256 _max) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol

```
File: contracts/PairsContract.sol

33: function setAssetChainlinkFeed(uint256 _asset, address _feed) external onlyOwner {
92: function setAssetBaseFundingRate(uint256 _asset, uint256 _baseFundingRate) external onlyOwner {
125: function setMaxBaseFundingRate(uint256 _maxBaseFundingRate) external onlyOwner {
129: function setProtocol(address _protocol) external onlyOwner {
139: function setMaxOi(uint256 _asset, address _tigAsset, uint256 _maxOi) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol

```
File: contracts/Position.sol

85: function setBaseURI(string memory _newBaseURI) external onlyOwner {
220: function setAccInterest(uint256 _id) external onlyMinter {
310: function setMinter(address _minter, bool _bool) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol

```
File: contracts/Referrals.sol

53: function setProtocol(address _protocol) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableToken.sol

```
File: contracts/StableToken.sol

38: function setMinter(
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol

```
File: contracts/Trading.sol

898: function setBlockDelay(
912: function setAllowedVault(
926: function setMaxWinPercent(
939: function setLimitOrderPriceRange(uint _range) external onlyOwner {
952: function setFees(bool _open, uint _daoFees, uint _burnFees, uint _referralFees, uint _botFees, uint _percent) external onlyOwner {
975: function setTradingExtension(
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol

```
File: contracts/TradingExtension.sol

144: function setMaxGasPrice(uint _maxGasPrice) external onlyOwner {
190: function _setReferral(
222: function setValidSignatureTimer(
231: function setChainlinkEnabled(bool _bool) external onlyOwner {
240: function setNode(address _node, bool _bool) external onlyOwner {
249: function setAllowedMargin(
264: function setMinPositionSize(
274: function setPaused(bool _paused) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/MetaContext.sol

```
File: contracts/utils/MetaContext.sol

9: function setTrustedForwarder(address _forwarder, bool _bool) external onlyOwner {
```

--------

## 02 LARGE MULTIPLES OF TEN SHOULD USE SCIENTIFIC NOTATION

Use (e.g. 1e12) rather than decimal literals (e.g. 1000000000000), for better code readability

_There is 1 instance of this issue_

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol

```
File: contracts/TradingExtension.sol

26: uint public maxGasPrice = 1000000000000; // 1000 gwei
```

----

## 03 RACE CONDITION IN approve()

Add increaseAllowance and decreaseAllowance methods

### Proof of Concept

_There are 5 instances of this issue_

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol

```
File: contracts/BondNFT.sol

301: approve(_to, _ids[i]);
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol

```
File: contracts/GovNFT.sol

259: approve(_to, _ids[i]);
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol

```
File: contracts/Lock.sol

117: IERC20(assets[i]).approve(address(bondNFT), type(uint256).max);
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol

```
File: contracts/Trading.sol

652: IERC20(_marginAsset).approve(_stableVault, type(uint).max);
807: IStable(_tigAsset).approve(address(gov), type(uint).max);
```

--------

## 04 USING BOTH NAMED RETURNS AND A RETURN STATEMENT ISN'T NECESSARY

Removing unused named returns variables can reduce gas usage (MSTOREs/MLOADs) and improve code clarity. To save gas and improve code quality: consider using only one of those.

_There are 6 instances of this issue_

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol

```
File: contracts/GovNFT.sol

219: function estimateFees(
332: function _msgSender() internal view override(Context, MetaContext) returns (address sender) {
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol

```
File: contracts/Position.sol

320: function _msgSender() internal view override(Context, MetaContext) returns (address sender) {
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableToken.sol

```
File: contracts/StableToken.sol

57: function _msgSender() internal view override(Context, MetaContext) returns (address sender) {
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol

```
File: contracts/Trading.sol

762: function _handleCloseFees(
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/MetaContext.sol

```
File: contracts/utils/MetaContext.sol

17: function _msgSender() internal view virtual override returns (address sender) {
```

---------

## 05 NON-LIBRARY/INTERFACE FILES SHOULD USE FIXED COMPILER VERSIONS, NOT FLOATING ONES

In the contracts, floating pragmas should not be used. Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

### Proof of Concept

This issue exists in all the In-scope contracts 

### Recommended Mitigation Steps

Lock the pragma version

-----

