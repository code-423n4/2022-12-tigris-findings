# Report


## Gas Optimizations


| |Issue|Instances|
|-|:-|:-:|
| [GAS-1](#GAS-1) | Packing uint8 and bool together to save Gas | 1 |
| [GAS-2](#GAS-2) | Functions can be changed to External if they are not getting called internally | 5 |

[GAS-1] Packing uint8 and bool together to save Gas
*Instances (1)*:
```solidity
File: contracts/interfaces/ITrading.sol

24:         uint8 v;
25:         bytes32 r;
26:         bytes32 s;
27:         bool usePermit;

```

[GAS-2] Functions can be changed to External if they are not getting called internally
*Instances (5)*:
```solidity
File: contracts/Position.sol

40:         function isMinter(address _address) public view returns (bool) { return _isMinter[_address]; }

```
```solidity
File: contracts/GovNFT.sol

315:        function assetsLength() public view returns (uint256) {

```
```solidity
File: contracts/BondNFT.sol

262:         ) public view returns (uint256) {

266:        function totalAssets() public view returns (uint256) {

274:        function getAssets() public view returns (address[] memory) {

```
