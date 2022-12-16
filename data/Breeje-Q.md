# Report


## Non Critical Issues


| |Issue|Instances|
|-|:-|:-:|
| [NC-1](#NC-1) | Event is missing indexed field | 22 |

[NC-1] Event is missing indexed field

*Instances (22)*:
```solidity
File: contracts/BondNFT.sol

377:    event Distribution(address _tigAsset, uint256 _amount);
378:    event Lock(address _tigAsset, uint256 _amount, uint256 _period, address _owner, uint256 _id);
379:    event ExtendLock(uint256 _period, uint256 _amount, address _owner, uint256 _id);
380:    event Release(address _tigAsset, uint256 _amount, address _owner, uint256 _id);
381:    event ClaimFees(address _tigAsset, uint256 _amount, address _claimer, uint256 _id);
382:    event ClaimDebt(address _tigAsset, uint256 _amount, address _owner);

```
```solidity
File: contracts/GovNFT.sol

28:   address _from,

```
```solidity
File: contracts/Referrals.sol

85:   event ReferralCreated(address _referrer, bytes32 _hash);
86:   event Referred(address _referredTrader, bytes32 _hash);

```
```solidity
File: contracts/Trading.sol

988:       address _trader,
997:       address _trader,
998:       address _executor
1003:     address _trader,
1004:     address _executor
1014:     address _trader,
1015:     address _executor
1022:     address _trader
1027:     address _trader
1035:     address _trader
1042:     address _trader
1046:     address _tigAsset,
1051:     address _referrer

```
