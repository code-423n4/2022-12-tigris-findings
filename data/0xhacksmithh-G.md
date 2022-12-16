### [Gas-01] <x> += <y> costs more gas than <x> = <x> + <y>, same thing for substraction as well 

*Instances (29)*
```solidity
File:   contracts/Trading.sol

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L293
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L509
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L511
```
```solidity
File:   contracts/BondNFT.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L68
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L115
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L117
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L119
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L150
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L152
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L180
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L183
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L221
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L225
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L333
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L334
```
```solidity
File:   contracts/Position.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L62
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L231
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L232
```
```solidity
File:   contracts/PairsContract.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L156
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L160
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L176
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L180
```
```solidity
File:   contracts/GovNFT.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L52
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L54
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L68
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L79-L81
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L278
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L290
```
```solidity
File:   contracts/Lock.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L73
```



### [Gas-02] ```public``` Functions those could be ```external```

*Instances (10)*
```solidity
File:   contracts/BondNFT.sol

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L250
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L260
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L266
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L274
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L366
```
```solidity
File:   contracts/PairsContract.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L22
```
```solidity
File:   contracts/GovNFT.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L183
```
```solidity
File:   contracts/StableToken.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableToken.sol#L13
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableToken.sol#L24
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableToken.sol#L38
```



### [Gas-03] Arithmatic operations should be Unchecked whenever Over/Underflow is not possible

*Instances (16)*
```solidity
File:   contracts/Position.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L296
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L304
```
```solidity
File:   contracts/Referrals.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol#L70
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol#L73
```
```solidity
File:   contracts/GovNFT.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L53
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L67
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L78
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L95
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L105
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L131
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L200
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L246
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L252
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L258
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L325
```
```solidity
File:   contracts/Lock.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L113
```