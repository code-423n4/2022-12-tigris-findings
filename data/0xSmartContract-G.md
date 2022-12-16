### Gas Optimizations List
| Number | Optimization Details | Context |
|:--:|:-------| :-----:|
| [G-01] | Packing and storing state variables | 1 |
| [G-02] |Using `delete` instead of setting  mappings and structs to 0 saves gas | 5 |
| [G-03] | Do not calculate constants variables | 1 |
| [G-04] | Remove import 'hardhat/console.sol’  | 1 |
| [G-05] | require() or revert() statements that check input arguments should be at the top of the function | 3|
| [G-06] | Use ``assembly`` to write _address storage values_  | 12 |
| [G-07] | Setting the _constructor_ to `payable`  | 8 |
| [G-08] | Functions guaranteed to revert_ when callled by normal users can be marked `payable`  | 69 |
| [G-09] |Empty blocks should be removed or emit something  | 1 |
| [G-10] | Optimize names to save gas| All contracts |
| [G-11] | `x += y (x -= y)` costs more gas than `x = x + y (x = x - y)` for state variables|42|
| [G-12] | Comparison operators  |20 |
| [G-13] |Use ``double require`` instead of using ``&&``  |12  |
| [G-14] | Sort Solidity operations using short-circuit mode |22 |
| [G-15] | Use a more recent version of solidity |All contracts  |
| [G-16] | The solady Library's Ownable contract is significantly gas-optimized, which can be used |  |
| [G-17] | Use `Solmate SafeTransferLib ` contracts |  |
| [G-18] | Use Solmate Reentrancy Guard  |  |

Total 18 issues

### [G-01] Packing and storing state variables

For the `gas` state variable in the `GovNFT.sol` contract, the `gas limit` is 30_000_000 in the Polygon network where the platform is, and 287_000_000 in the Arbitrum network.

Even if there are increases, it is expected that this amount will not exceed the uint64 value. This expectation is valid in `counter` and `maxBridge`, and storing state variables by packing ensures less gas consumption, resulting in two slots less usage.


```solidity

contracts\GovNFT.sol:
  15:     uint256 private counter = 1;	        	// slot 0
  16:     uint256 private constant MAX = 10000;
  17:     uint256 public gas = 150000;	        	// slot 1
  18:     string public baseURI;			// slot 2
  19:     uint256 public maxBridge = 20;		// slot 3
  20:     ILayerZeroEndpoint public endpoint;		// slot 4
```

**Recommendation Code:**

```diff
contracts\GovNFT.sol:
- 15:     uint256 private counter = 1;		
  16:     uint256 private constant MAX = 10_000;
+ 15:     uint64 private counter = 1;		        // slot 0
  17:     uint64 public gas = 150_000;		        // slot 0
- 18:     string public baseURI;			
  19:     uint64 public maxBridge = 20;     		// slot 0
  20:     ILayerZeroEndpoint public endpoint;		// slot 1
+ 18:     string public baseURI;			// slot 2
```



### [G-02] Using `delete` instead of setting  mappings and structs to 0 saves gas

```solidity
5 results - 4 files

contracts\BondNFT.sol:
  201:         userDebt[_user][_tigAsset] = 0;

contracts\PairsContract.sol:
  162:                 _idToOi[_asset][_tigAsset].longOi = 0;
  182:                 _idToOi[_asset][_tigAsset].shortOi = 0;

contracts\Position.sol:
  173:         _trade.orderType = 0;

contracts\Trading.sol:
  730:             _fees.botFees = 0;
```

**Recommendation Code:**

```diff
contracts\BondNFT.sol:
- 201:         userDebt[_user][_tigAsset] = 0;
+ 201:         delete userDebt[_user][_tigAsset];

```

### [G-03] Do not calculate constants variables

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.

```solidity
1 result - 1 file

contracts\BondNFT.sol:

  10:     uint constant private DAY = 24 * 60 * 60;

```
**Recommendation Code:**
```diff
contracts\BondNFT.sol:

- 10:     uint constant private DAY = 24 * 60 * 60;
+ 10:     uint constant private DAY = 86_400;   // 24 * 60 * 60

```

### [G-04] Remove import 'hardhat/console.sol’

It's used to print the values of variables while running tests to help debug and see what's happening inside your contracts But since it's a development tool, it serves no purpose on mainnet. 

```solidity
1 result - 1 file

contracts\Lock.sol:
  4: import "hardhat/console.sol";
```

**Recommendation:**
Use only for tests

### [G-05] require() or revert() statements that check input arguments should be at the top of the function

Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting a Gcoldsload (2100 gas*) in a function that may ultimately revert in the unhappy case.

```solidity
3 results - 2 files

contracts/BondNFT.sol:
   97     function extendLock(
  106         require(bond.owner == _sender, "!owner");
  107         require(!bond.expired, "Expired");
  108         require(bond.asset == _asset, "!BondAsset");
  109:        require(bond.pending == 0);
  110         require(epoch[bond.asset] == block.timestamp/DAY, "Bad epoch");
  111:        require(bond.period+_period <= 365, "MAX PERIOD");


contracts/GovNFT.sol:
  64     function _bridgeMint(address to, uint256 tokenId) public {
  65         require(msg.sender == address(this) || _msgSender() == owner(), "NotBridge");
  66:        require(tokenId <= 10000, "BadID");

```

**Recommendation Code:**
```diff
contracts/BondNFT.sol:
    97     function extendLock(
+ 109:        require(bond.pending == 0);
+ 111:        require(bond.period+_period <= 365, "MAX PERIOD");
  106         require(bond.owner == _sender, "!owner");
  107         require(!bond.expired, "Expired");
  108         require(bond.asset == _asset, "!BondAsset");
- 109:        require(bond.pending == 0);
  110         require(epoch[bond.asset] == block.timestamp/DAY, "Bad epoch");
- 111:        require(bond.period+_period <= 365, "MAX PERIOD");

```

### [G-06] Use ``assembly`` to write _address storage values_ 

```solidity
12 results - 7 files

contracts\PairsContract.sol:
  129     function setProtocol(address _protocol) external onlyOwner {
  130:         protocol = _protocol;
  131     }  

contracts\Referrals.sol:
  53     function setProtocol(address _protocol) external onlyOwner {
  54:         protocol = _protocol;
  55     }

contracts\BondNFT.sol:
  366     function setManager(
  367         address _manager
  368     ) public onlyOwner() {
  369:         manager = _manager;
  370     }

contracts\GovNFT.sol:
  32:     constructor(
  33         address _endpoint,
  34         string memory _setBaseURI,
  35         string memory _name,
  36         string memory _symbol
  37     ) ERC721(_name, _symbol) {
  38:        endpoint = ILayerZeroEndpoint(_endpoint);

contracts\StableVault.sol:
  35     constructor(address _stable) {
  36:          stable = _stable;
  37      }

contracts\Trading.sol:
  143     constructor(
  144         address _position,
  145         address _gov,
  146         address _pairsContract
  147     )
  148     {
  149:        position = IPosition(_position);
  150:         gov = IGovNFT(_gov);
  151:         pairsContract = IPairsContract(_pairsContract);
  152     }

contracts\TradingExtension.sol:
  28:     constructor(
  29         address _trading,
  30         address _pairsContract,
  31         address _ref,
  32         address _position
  33     )
  34     {
  35:         trading = _trading;
  36:         pairsContract = IPairsContract(_pairsContract);
  37:         referrals = IReferrals(_ref);
  38:         position = IPosition(_position);
  39     }
```

**Recommendation Code:**
```solidity
contracts\PairsContract.sol:

    function setProtocol(address _protocol) external onlyOwner {
        assembly {
            sstore(protocol.slot, _protocol)
         }  
     }
```

### [G-07] Setting the _constructor_ to `payable`

You can cut out 10 opcodes in the creation-time EVM bytecode if you declare a constructor payable. Making the constructor payable eliminates the need for an initial check of ```msg.value == 0``` and saves ```13 gas``` on deployment with no security risks.

**Context:**
```solidity
8 results 8 files

contracts\BondNFT.sol:
  40:     constructor(
  41         string memory _setBaseURI,
  42         string memory _name,
  43         string memory _symbol
  44     ) ERC721(_name, _symbol) {

contracts\GovNFT.sol:
  32:     constructor(
  33         address _endpoint,
  34         string memory _setBaseURI,
  35         string memory _name,
  36         string memory _symbol
  37     ) ERC721(_name, _symbol) {

contracts\Lock.sol:
  21:     constructor(
  22         address _bondNFTAddress,
  23         address _govNFT
  24     ) {

contracts\Position.sol:
  76:     constructor(string memory _setBaseURI, string memory _name, string memory _symbol) ERC721(_name, _symbol) {

contracts\StableToken.sol:
  11:     constructor(string memory name_, string memory symbol_) ERC20Permit(name_) ERC20(name_, symbol_) {}

contracts\StableVault.sol:
  35:     constructor(address _stable) {

contracts\Trading.sol:
  143:     constructor(
  144         address _position,
  145         address _gov,
  146         address _pairsContract
  147     )
  148     {

contracts\TradingExtension.sol:
  28:     constructor(
  29         address _trading,
  30         address _pairsContract,
  31         address _ref,
  32         address _position
  33     )
  34     {
```

**Recommendation:**
Set the constructor to ```payable```

### [G-08]  Functions guaranteed to revert_ when callled by normal users can be marked `payable` 

If a function modifier or require such as onlyOwner-admin is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2), DUP1(3), ISZERO(3), PUSH2(3), JUMPI(10), PUSH1(3), DUP1(3), REVERT(0), JUMPDEST(1), POP(2) which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

```solidity
69 results - 11 files

contracts/BondNFT.sol:
   57      function createLock(
   62:     ) external onlyManager() returns(uint id) {

   97      function extendLock(
  103:     ) external onlyManager() {

  137      function release(
  140:     ) external onlyManager() returns(uint amount, uint lockAmount, address asset, address _owner) {

  168      function claim(
  171:     ) public onlyManager() returns(uint amount, address tigAsset) {

  196      function claimDebt(
  199:     ) public onlyManager() returns(uint amount) {

  349:     function addAsset(address _asset) external onlyOwner {
  357:     function setAllowedAsset(address _asset, bool _bool) external onlyOwner {
  362:     function setBaseURI(string calldata _newBaseURI) external onlyOwner {

  366      function setManager(
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
  127      function editAsset(
  130:     ) external onlyOwner() {

  138      function sendNFTs(
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
  154:     function modifyLongOi(uint256 _asset, address _tigAsset, bool _onOpen, uint256 _amount) external onlyProtocol {
  174:     function modifyShortOi(uint256 _asset, address _tigAsset, bool _onOpen, uint256 _amount) external onlyProtocol {

contracts/Position.sol:
   85:     function setBaseURI(string memory _newBaseURI) external onlyOwner {
   99:     function updateFunding(uint256 _asset, address _tigAsset, uint256 _longOi, uint256 _shortOi, uint256 _baseFundingRate, uint _vaultFundingPercent) external onlyMinter {
  
  131      function mint(
  133:     ) external onlyMinter {
  
  168:     function executeLimitOrder(uint256 _id, uint256 _price, uint256 _newMargin) external onlyMinter {
  197:     function modifyMargin(uint256 _id, uint256 _newMargin, uint256 _newLeverage) external onlyMinter {
  209:     function addToPosition(uint256 _id, uint256 _newMargin, uint256 _newPrice) external onlyMinter {
  220:     function setAccInterest(uint256 _id) external onlyMinter {
  230:     function reducePosition(uint256 _id, uint256 _percent) external onlyMinter {
  242:     function modifyTp(uint _id, uint _tpPrice) external onlyMinter {
  252:     function modifySl(uint _id, uint _slPrice) external onlyMinter {
  260:     function burn(uint _id) external onlyMinter {
  310:     function setMinter(address _minter, bool _bool) external onlyOwner {

contracts/Referrals.sol:
  32:     function setReferred(address _referredTrader, bytes32 _hash) external onlyProtocol {
  53:     function setProtocol(address _protocol) external onlyOwner {

  60      function initRefs(
  65:     ) external onlyOwner {

contracts/StableToken.sol:
  13      function burnFrom(
  19:         onlyMinter()

  24      function mintFor(
  30:         onlyMinter()

  38      function setMinter(
  43:         onlyOwner()

contracts/StableVault.sol:
  78:     function listToken(address _token) external onlyOwner {
  89:     function delistToken(address _token) external onlyOwner {

contracts/Trading.sol:
  898     function setBlockDelay(
  902:         onlyOwner

  912     function setAllowedVault(
  917:         onlyOwner

  926     function setMaxWinPercent(
  930:         onlyOwner

  939:     function setLimitOrderPriceRange(uint _range) external onlyOwner {
  952:     function setFees(bool _open, uint _daoFees, uint _burnFees, uint _referralFees, uint _botFees, uint _percent) external onlyOwner {
  
  975      function setTradingExtension(
  977:     ) external onlyOwner() {

contracts/TradingExtension.sol:
   61      function _closePosition(
   65:     ) external onlyProtocol returns (IPosition.Trade memory _trade, uint256 _positionSize, int256 _payout) {
  
  126      function modifyShortOi(
  131:     ) public onlyProtocol {

  135      function modifyLongOi(
  140:     ) public onlyProtocol {

  144:     function setMaxGasPrice(uint _maxGasPrice) external onlyOwner {

  190      function _setReferral(
  193:     ) external onlyProtocol {

  222      function setValidSignatureTimer(
  226:         onlyOwner

  231:     function setChainlinkEnabled(bool _bool) external onlyOwner {
  240:     function setNode(address _node, bool _bool) external onlyOwner {
  
  249      function setAllowedMargin(
  254:         onlyOwner

  264      function setMinPositionSize(
  269:         onlyOwner

  274:     function setPaused(bool _paused) external onlyOwner {

contracts/utils/MetaContext.sol:
  9:     function setTrustedForwarder(address _forwarder, bool _bool) external onlyOwner {
```

**Recommendation:**
Functions guaranteed to revert when called by normal users can be marked payable  (for only ```onlyOwner, onlyManager, onlyMinter or onlyProtocol``` functions)

### [G-09] Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}}). Empty receive()/fallback() payable functions that are not used, can be removed to save deployment gas.

```solidity
1 result - 1 file

contracts/StableToken.sol:
  11:     constructor(string memory name_, string memory symbol_) ERC20Permit(name_) ERC20(name_, symbol_) {}
```

### [G-10] Optimize names to save gas
Contracts most called functions could simply save gas by function ordering via ```Method ID```. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because ```22 gas``` are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions. 

**Context:** 
All Contracts


**Recommendation:** 
Find a lower ```method ID``` name for the most called functions for example Call() vs. Call1() is cheaper by ```22 gas```
For example, the function IDs in the ``` BondNFT.sol ``` contract will be the most used; A lower method ID may be given.

**Proof of Consept:**
https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

BondNFT.sol function names can be named and sorted according to METHOD ID

```js
Sighash   |   Function Signature
========================
54012786  =>  createLock(address,uint256,uint256,address)
ad8a2082  =>  extendLock(uint256,address,uint256,uint256,address)
8124fea6  =>  release(uint256,address)
ddd5e1b2  =>  claim(uint256,address)
13b6c60f  =>  claimDebt(address,address)
fb932108  =>  distribute(address,uint256)
912f93fb  =>  idToBond(uint256)
d9548e53  =>  isExpired(uint256)
af6e6238  =>  pending(uint256)
01e1d114  =>  totalAssets()
67e4ac2c  =>  getAssets()
743976a0  =>  _baseURI()
d6651c70  =>  safeTransferMany(address,uint256[])
3206e3e7  =>  safeTransferFromMany(address,address,uint256[])
99bb8134  =>  approveMany(address,uint256[])
527f1948  =>  _mint(address,Bond)
9b1f9e74  =>  _burn(uint256)
30e0789e  =>  _transfer(address,address,uint256)
adc82e01  =>  balanceIds(address)
298410e5  =>  addAsset(address)
d9a3aa3c  =>  setAllowedAsset(address,bool)
55f804b3  =>  setBaseURI(string)
d0ebdbe7  =>  setManager(address)
```


### [G-11] `x += y (x -= y)` costs more gas than `x = x + y (x = x - y)` for state variables

```solidity
27 results - 7 files

contracts/BondNFT.sol:
   68:             totalShares[_asset] += shares;
  115:             totalShares[bond.asset] += shares-bond.shares;
  117:             _bond.amount += _amount;
  119:             _bond.period += _period;
  152:             amount += _claimAmount;
  180:                     accRewardsPerShare[bond.asset][epoch[bond.asset]] += _pendingDelta*1e18/totalShares[bond.asset];
  183:             bondPaid[_id][bond.asset] += amount;
  221:                     epoch[_tigAsset] += 1;
  225:             accRewardsPerShare[_tigAsset][aEpoch] += _amount * 1e18 / totalShares[_tigAsset];
  333:             userDebt[from][bond.asset] += bond.pending;
  334:             bondPaid[_id][bond.asset] += bond.pending;

contracts/GovNFT.sol:
   52:         counter += 1;
   54:             userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];
   68:             userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];
   79:             userDebt[owner][assets[i]] += accRewardsPerNFT[assets[i]];
   96:             userDebt[from][assets[i]] += accRewardsPerNFT[assets[i]];
   99:             userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];
  278:         userPaid[_msgsender][_tigAsset] += amount;
  290:             accRewardsPerNFT[_tigAsset] += _amount/totalSupply();

contracts/Lock.sol:
  73:         totalLocked[_asset] += _amount;

contracts/PairsContract.sol:
  156:             _idToOi[_asset][_tigAsset].longOi += _amount;
  176:             _idToOi[_asset][_tigAsset].shortOi += _amount;

contracts/Position.sol:
   62:         _trade.accInterest += (int256(_trade.margin*_trade.leverage/1e18)*(accInterestPerOi[_trade.asset][_trade.tigAsset][_trade.direction]+_pendingFunding)/1e18)-initId[_id];
  102:                 accInterestPerOi[_asset][_tigAsset][true] += ((int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(longOi[_asset][_tigAsset]))*int256(1e10-vaultFundingPercent[_asset][_tigAsset])/1e10;
  109:                 accInterestPerOi[_asset][_tigAsset][false] += ((int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(shortOi[_asset][_tigAsset]))*int256(1e10-vaultFundingPercent[_asset][_tigAsset])/1e10;

contracts/Trading.sol:
  509:                 trade.price += trade.price * _spread / DIVISION_CONSTANT;

contracts/TradingExtension.sol:
  185:             _price += _price * _spread / DIVISION_CONSTANT;
```

```diff
contracts/BondNFT.sol:#L68:

- totalShares[_asset] += shares;
+ totalShares[_asset] = totalShares[_asset] + shares;
```

`x += y` costs more gas than `x = x  + y` for state variables.


```solidity
15 results - 7 files

contracts/BondNFT.sol:
  150:             totalShares[bond.asset] -= bond.shares;

contracts/GovNFT.sol:
  80:             userDebt[owner][assets[i]] -= userPaid[owner][assets[i]]/balanceOf(owner);
  81:             userPaid[owner][assets[i]] -= userPaid[owner][assets[i]]/balanceOf(owner);            
  97:             userDebt[from][assets[i]] -= userPaid[from][assets[i]]/balanceOf(from);
  98:             userPaid[from][assets[i]] -= userPaid[from][assets[i]]/balanceOf(from);

contracts/Lock.sol:
  103:         totalLocked[asset] -= lockAmount;

contracts/PairsContract.sol:
  160:             _idToOi[_asset][_tigAsset].longOi -= _amount;
  180:             _idToOi[_asset][_tigAsset].shortOi -= _amount;

contracts/Position.sol:
  104:             accInterestPerOi[_asset][_tigAsset][false] -= (int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(shortOi[_asset][_tigAsset]);
  107:             accInterestPerOi[_asset][_tigAsset][true] -= (int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(longOi[_asset][_tigAsset]);
  231:         _trades[_id].accInterest -= _trades[_id].accInterest*int256(_percent)/int256(DIVISION_CONSTANT);
  232:         _trades[_id].margin -= _trades[_id].margin*_percent/DIVISION_CONSTANT;

contracts/Trading.sol:
  293:             _addMargin -= _fee;
  511:                 trade.price -= trade.price * _spread / DIVISION_CONSTANT;

contracts/TradingExtension.sol:
  187:             _price -= _price * _spread / DIVISION_CONSTANT;
```

```diff
contracts/BondNFT.sol:
  150:             totalShares[bond.asset] -= bond.shares;

contracts/BondNFT.sol:#L150

- totalShares[bond.asset] -= bond.shares;
+ totalShares[bond.asset] = totalShares[bond.asset] - bond.shares;
```

`x -= y` costs more gas than `x = x  - y` for state variables.

### [G-12] Comparison operators

In the EVM, there is no opcode for `>=` or `<=`. When using greater than or equal, two operations are performed: `> and =`. Using strict comparison operators hence saves gas

**Context:**
```solidity
20 results - 6 files

contracts/BondNFT.sol:
  111:        require(bond.period+_period <= 365, "MAX PERIOD");

contracts/GovNFT.sol:
  51:         require(counter <= MAX, "Exceeds supply");
  66:         require(tokenId <= 10000, "BadID");
  153         require(
  154:            msg.value >= messageFee,

contracts/Lock.sol:
  66:         require(_period <= maxPeriod, "MAX PERIOD");
  67:         require(_period >= minPeriod, "MIN PERIOD");

contracts/PairsContract.sol:
   52:        require(_maxLeverage >= _minLeverage && _minLeverage > 0, "Wrong leverage values");
   84:        require(_idToAsset[_asset].maxLeverage >= _idToAsset[_asset].minLeverage, "Wrong leverage values");

   95:        require(_baseFundingRate <= maxBaseFundingRate, "baseFundingRate too high");
  157:        require(_idToOi[_asset][_tigAsset].longOi <= _idToOi[_asset][_tigAsset].maxOi || _idToOi[_asset][_tigAsset].maxOi == 0, "MaxLongOi");
  177:        require(_idToOi[_asset][_tigAsset].shortOi <= _idToOi[_asset][_tigAsset].maxOi || _idToOi[_asset][_tigAsset].maxOi == 0, "MaxShortOi");

contracts/Trading.sol:
  436:        if (_payout <= int256(_newMargin*(DIVISION_CONSTANT-liqPercent)/DIVISION_CONSTANT)) revert LiqThreshold();
  966:        require(_percent <= DIVISION_CONSTANT);

  491:        require(block.timestamp >= limitDelay[_id]);
  887:        require(_proxy.proxy == _msgSender() && _proxy.time >= block.timestamp, "Proxy not approved");
  954:        require(_daoFees >= _botFees+_referralFees*2);


contracts/utils/TradingLibrary.sol:
   39:        if (_direction && _currentPrice >= _price) {
  109:        require(block.timestamp >= _priceData.timestamp, "FutSig");

   43:        else if (!_direction && _currentPrice <= _price) {
  110:        require(block.timestamp <= _priceData.timestamp + _validSignatureTimer, "ExpSig");
```

```diff
contracts/BondNFT.sol:
- 111:        require(bond.period+_period <= 365, "MAX PERIOD");
+ 111:        require(bond.period+_period < 366, "MAX PERIOD");

```


**Recommendation:** 
Replace <= with <, and >= with >. Do not forget to increment/decrement the compared variable.


### [G-13] Use ``double require`` instead of using ``&&``

Using double require instead of operator && can save more gas
When having a require statement with 2 or more expressions needed, place the expression that cost less gas first.

**Context:** 
```solidity
12 results - 4 files

contracts/PairsContract.sol:
  52:         require(_maxLeverage >= _minLeverage && _minLeverage > 0, "Wrong leverage values");

contracts/Position.sol:
  47:         if (_trade.direction && longOi[_trade.asset][_trade.tigAsset] > 0) {

contracts/Trading.sol:
  497:             if (trade.direction && trade.orderType == 1) {
  499:             } else if (!trade.direction && trade.orderType == 1) {
  501:             } else if (!trade.direction && trade.orderType == 2) {
  625:                 if (maxWinPercent > 0 && _toMint > _trade.margin*maxWinPercent/DIVISION_CONSTANT) {
  838:             if (_sl < _price && _sl != 0) revert("3"); //BadStopLoss
  887:             require(_proxy.proxy == _msgSender() && _proxy.time >= block.timestamp, "Proxy not approved");

contracts/utils/TradingLibrary.sol:
   39:             if (_direction && _currentPrice >= _price) {
   41:             } else if (_direction && _currentPrice < _price) {
   43:             } else if (!_direction && _currentPrice <= _price) {
  112:         if (_chainlinkEnabled && _chainlinkFeed != address(0)) {
```

**Recommendation Code:**
 ```diff
contracts/PairsContract.sol#L52
- 52:         require(_maxLeverage >= _minLeverage && _minLeverage > 0, "Wrong leverage values");
+ 52:        require(_maxLeverage >= _minLeverage, "Wrong leverage values");
                 require(_minLeverage > 0, "Wrong leverage values");
```

### [G-14] Sort Solidity operations using short-circuit mode

Short-circuiting is a solidity contract development model that uses ```OR/AND``` logic to sequence different cost operations. It puts low gas cost operations in the front and high gas cost operations in the back, so that if the front is low If the cost operation is feasible, you can skip (short-circuit) the subsequent high-cost Ethereum virtual machine operation.

```
//f(x) is a low gas cost operation 
//g(y) is a high gas cost operation 

//Sort operations with different gas costs as follows 
f(x) || g(y) 
f(x) && g(y)
```

```solidity
22 results - 7 files

contracts/BondNFT.sol:
  215:         if (totalShares[_tigAsset] == 0 || !allowedAsset[_tigAsset]) return;
  350:         require(assets.length == 0 || assets[assetsIndex[_asset]] != _asset, "Already added");

contracts/GovNFT.sol:
   65:         require(msg.sender == address(this) || _msgSender() == owner(), "NotBridge");
  288:         if (assets.length == 0 || assets[assetsIndex[_tigAsset]] == address(0) || totalSupply() == 0 || !_allowedAsset[_tigAsset]) return;
  301:         require(assets.length == 0 || assets[assetsIndex[_asset]] != _asset, "Already added");

contracts/PairsContract.sol:
   52:         require(_maxLeverage >= _minLeverage && _minLeverage > 0, "Wrong leverage values");
  157:         require(_idToOi[_asset][_tigAsset].longOi <= _idToOi[_asset][_tigAsset].maxOi || _idToOi[_asset][_tigAsset].maxOi == 0, "MaxLongOi");
  177:         require(_idToOi[_asset][_tigAsset].shortOi <= _idToOi[_asset][_tigAsset].maxOi || _idToOi[_asset][_tigAsset].maxOi == 0, "MaxShortOi");

contracts/Trading.sol:
  241:         if (_percent > DIVISION_CONSTANT || _percent == 0) revert BadClosePercent();
  496:         if (_price > trade.price+trade.price*limitOrderPriceRange/DIVISION_CONSTANT || _price < trade.price-trade.price*limitOrderPriceRange/DIVISION_CONSTANT) revert("6"); //LimitNotMet
  877:         require(_token == IStableVault(_stableVault).stable() || IStableVault(_stableVault).allowed(_token), "Token not approved in vault");
  497:         if (trade.direction && trade.orderType == 1) {
  499:         else if (!trade.direction && trade.orderType == 1) {
  501:         else if (!trade.direction && trade.orderType == 2) {
  625:         if (maxWinPercent > 0 && _toMint > _trade.margin*maxWinPercent/DIVISION_CONSTANT) {
  838:         if (_sl < _price && _sl != 0) revert("3"); //BadStopLoss

contracts/TradingExtension.sol:
  217:         if (_leverage < asset.minLeverage || _leverage > asset.maxLeverage) revert("!lev");

contracts/Position.sol:
  47:          if (_trade.direction && longOi[_trade.asset][_trade.tigAsset] > 0) {

contracts/utils/TradingLibrary.sol:
   39:         if (_direction && _currentPrice >= _price) {
   41:         else if (_direction && _currentPrice < _price) {
   43:         else if (!_direction && _currentPrice <= _price) {
  112:         if (_chainlinkEnabled && _chainlinkFeed != address(0)) {
```

### [G-15] Use a more recent version of solidity

**Context:** 
All Contracts

Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings.

Solidity 0.8.10 has a useful change that reduced gas costs of external calls which expect a return value. 

In 0.8.15 the conditions necessary for inlining are relaxed. Benchmarks show that the change significantly decreases the bytecode size (which impacts the deployment cost) while the effect on the runtime gas usage is smaller.

In 0.8.17 prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call; Simplify the starting offset of zero-length operations to zero. More efficient overflow checks for multiplication.

```solidity
contracts/BondNFT.sol:
  1  // SPDX-License-Identifier: MIT
  2: pragma solidity ^0.8.0;
```

### [G-16] The solady Library's Ownable contract is significantly gas-optimized, which can be used

The project uses the ` onlyOwner ` authorization model I recommend using _Solady's highly gas optimized contract._

https://github.com/Vectorized/solady/blob/main/src/auth/OwnableRoles.sol


### [G-17] Use `Solmate SafeTransferLib ` contracts

**Description:**
Use the gas-optimized Solmate SafeTransferLib contract for Erc20

https://github.com/transmissions11/solmate/blob/main/src/utils/SafeTransferLib.sol

### [G-18] Use Solmate Reentrancy Guard 

Solmate reentrancy guard is high gas optimized

```solidity
1 result - 1 file

contracts/StableVault.sol:
  5: import "@openzeppelin/contracts/security/ReentrancyGuard.sol";

```

https://github.com/transmissions11/solmate/blob/main/src/utils/ReentrancyGuard.sol