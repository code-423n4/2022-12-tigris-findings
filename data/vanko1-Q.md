# QA Report

# Use `private constant` in place of `constant private`
To keep the code base consistent and readable, change the  `private constant` for `constant private` as it is widely used as a standart.  
There are x instances of this issue:  
1. https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L11  
2. https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L16
3. https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L10  

# Emit event on critical state changes  
There are no Mint/Burn events for the `StableToken` contract.  
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableToken.sol#L32  
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableToken.sol#L21