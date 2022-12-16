# QA Report for Tigris Trade contest
## Overview
During the audit, 2 low and 7 non-critical issues were found.

№ | Title | Risk Rating  | Instance Count
--- | --- | --- | ---
L-1 | Possible DoS | Low | 3
L-2 | NatSpec is misleading | Low | 1
NC-1 | Order of Functions | Non-Critical | 10+
NC-2 | Order of Layout | Non-Critical | 5
NC-3 | Scientific notation may be used | Non-Critical | 3
NC-4 | Typos | Non-Critical | 4
NC-5 | Unused named return variables | Non-Critical | 7
NC-6 | Maximum line length exceeded | Non-Critical | 57
NC-7 | Missing NatSpec | Non-Critical | ~100

## Low Risk Findings(2)
### L-1. Possible DoS
##### Description
If a user has too many tokens, three functions can run out of gas.

##### Instances
- function userTrades(address _user) from Position.sol: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L296
- function balanceIds(address _user) from GovNFT.sol: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L325
- function balanceIds(address _user) from BondNFT.sol: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L342

##### Recommendation
Consider adding the parameters ```uint _from, uint _to``` to limit loops.
#
### L-2. NatSpec is misleading
##### Description
The order of parameters (```_minLeverage``` and ```_maxLeverage```) in NatSpec does not match the order in the function.

##### Instances
```
...
* @param _maxLeverage maximimum allowed leverage
* @param _maxLeverage minimum allowed leverage
...
function addAsset(uint256 _asset, string memory _name, address _chainlinkFeed, uint256 _minLeverage, uint256 _maxLeverage, uint256 _feeMultiplier, uint256 _baseFundingRate) external onlyOwner {
```

##### Recommendation
Change to:
 @param _maxLeverage maximimum allowed leverage =>  @param **_minLeverage** **minimum** allowed leverage
 @param _maxLeverage minimum allowed leverage =>  @param _maxLeverage **maximum** allowed leverage

#

## Non-Critical Risk Findings(7)
### NC-1. Order of Functions
##### Description
According to [Style Guide](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-functions), ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier.  
Functions should be grouped according to their visibility and ordered:
1) constructor
2) receive function (if exists)
3) fallback function (if exists)
4) external
5) public
6) internal
7) private
##### Instances
constructor should be placed before functions:
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L76-L79

external functions should be placed before public:
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L70-L123
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L53-L97
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L47-L53
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L122-L143
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L85-
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L104-L116

internal functions should be placed after public:
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L81-L83
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L42-L44
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L50-L57

##### Recommendation
Reorder functions where possible.
#
### NC-2. Order of Layout
##### Description
According to [Order of Layout](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-layout), inside each contract, library or interface, use the following order:
1) Type declarations
2) State variables
3) Events
4) Modifiers
5) Functions
##### Instances
```pragma``` can not go after ```import```:
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ITrading.sol#L5

modifiers should be placed before functions:
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableToken.sol#L48-L54

state variables should be placed before functions:
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L15-L38
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L263-L269

events should be placed before modifiers and functions:
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L194-L201

#
### NC-3. Scientific notation may be used
##### Description
For readability and to avoid misprints, it is better to use scientific notation.
##### Instances
- [```uint public maxGasPrice = 1000000000000; // 1000 gwei```](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L26)
- [```uint256 private constant MAX = 10000;```](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L16) 
- [```uint256 public gas = 150000;```](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L17) 

##### Recommendation
For example, replace ```10000``` with ```10e4```.
#
### NC-4. Typos
##### Instances
- [```* @param _maxLeverage maximimum allowed leverage```](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L44) => ```maximum```
- [```* @param _onOpen true if adding to open interesr```](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L151) => ```interest```
- [```* @param _onOpen true if adding to open interesr```](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L171) => ```interest```
- [```* @notice Owner can retreive Gov NFTs```](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L135) => ```retrieve```

#
### NC-5. Unused named return variables
##### Description
Both named return variable(s) and return statement are used.
##### Instances
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L809
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L151
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L321
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L226
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L333
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableToken.sol#L59
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/MetaContext.sol#L25

##### Recommendation
To improve clarity use only named return variables.  
For example, change:
```
function functionName() returns (uint id) {
    return x;
```
to
```
function functionName() returns (uint id) {
    id = x;
```
#
### NC-6. Maximum line length exceeded
##### Description
According to [Style Guide](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#maximum-line-length), maximum suggested line length is 120 characters.  
10 contracts have to long lines:
- Trading.sol
- TradingExtension.sol
- TradingLibrary.sol
- Position.sol
- PairsContract.sol
- GovNFT.sol
- StableVault.sol
- BondNFT.sol
- ILayerZeroEndpoint.sol
- IPosition.sol

##### Instances
Trading.sol:
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L178
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L285
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L434
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L496
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L520
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L543
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L612
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L616
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L624
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L643
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L647
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L675
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L877
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L952

TradingExtension.sol:
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L67
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L71 
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L73

TradingLibrary.sol:
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L36
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L40
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L42
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L44
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L46
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L62
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L64
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L66

Position.sol:
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L48
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L55
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L62 
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L69
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L99
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L102
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L104
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L107
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L109
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L153
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L187
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L212
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L233
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L264
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L269
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L270

PairsContract.sol:
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L48
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L157
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L177

GovNFT.sol:
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L175
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L183
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#

StableVault.sol:
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L53

BondNFT.sol:
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L178 
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L240

ILayerZeroEndpoint.sol:
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ILayerZeroEndpoint.sol#L12
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ILayerZeroEndpoint.sol#L14
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ILayerZeroEndpoint.sol#L15
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ILayerZeroEndpoint.sol#L24
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ILayerZeroEndpoint.sol#L41
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ILayerZeroEndpoint.sol#L78

IPosition.sol:
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPosition.sol#L52

##### Recommendation
Make the lines shorter.
#
### NC-7. Missing NatSpec
##### Description
NatSpec is missing for functions in 16 contracts.
##### Instances
- TradingExtension.sol
- Position.sol
- PairsContract.sol
- Referrals.sol
- GovNFT.sol
- StableToken.sol 
- StableVault.sol
- BondNFT.sol
- MetaContext.sol
- IBondNFT.sol
- IGovNFT.sol
- IPairsContract.sol
- IPosition.sol
- IReferrals.sol
- IStableVault.sol
- ITrading.sol

##### Recommendation
Add NatSpec for all functions.