### [Low-01] Transfering ETH using transfer() function
transfer() capped gas to 2300, which may be insufficient if receiver contract performing something on ETH receive after Istambul fork,

Recommend to use call with Re-entrancy gaurd or capped gas 
*Instances (1)*
```solidity
File:   contracts/Trading.sol

payable(_proxy).transfer(msg.value);

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L588

```

### [Low-02] Absence of Zero address check, during setting critical state variable

*Instances (1)*
```solidity
File:   contracts/StableVault.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L36
```

### [Low-03] Floating Pragma used and Solidity version is old one

- Instead of Floating, lock pragma version in contract files
- Use a solidity version of at least 0.8.13 to get the ability to use `using for`
 with a list of free functions
- Use a solidity version of at least 0.8.4 to get `bytes.concat()`
 instead of `abi.encodePacked(<bytes>,<bytes>)`
 Use a solidity version of at least 0.8.12 to get `string.concat()`
 instead of `abi.encodePacked(<str>,<str>)`

*Instances (22)*
```solidity
File:   contracts/Trading.sol
File:   contracts/TradingExtension.sol
File:   contracts/Position.sol
File:   contracts/PairsContract.sol
File:   contracts/Referrals.sol
File:   contracts/GovNFT.sol
File:   contracts/StableToken.sol
File:   contracts/StableVault.sol
File:   contracts/Lock.sol
File:   contracts/BondNFT.sol
File:   contracts/utils/MetaContext.sol
File:   contracts/utils/TradingLibrary.sol

and other Interface contracts that are in scope
```

### [NC-01] Unclear Error msg on Revert
*Instances (5)*
```solidity
File:   contracts/Trading.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L327
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L398
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L432
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L464
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L495
```


### [NC-02] Instead of magic number try to use CONSTANT state variables (These are different from public mentioned magic number bugs of contest)
*Instances (8)*
```solidity
File:   contracts/Trading.sol

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L347
```
```solidity
File:   contracts/BondNFT.sol

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L65
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L111
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L113
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L145
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L332
```
```solidity
File:   contracts/GovNFT.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L144
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L175
```

### [NC-03] Missing of Error Message from Revert()
*Instances (3)*
```solidity
File:   contracts/Trading.sol

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L392
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L428
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L966
```

### [NC-04] Instead of expression CONSTANT should be a value
*Instances (1)*
```solidity
File:   contracts/BondNFT.sol

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L10
```

### [NC-05] Instaed of long huge number try to use scientific notations that will increase readability
*Instances (2)*
```solidity
File:   contracts/GovNFT.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L16
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L17
```