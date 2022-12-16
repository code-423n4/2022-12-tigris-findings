##Gas Optimization Report Tigris

##[G-01]
Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate
#Trading.sol
line 130
mapping(uint => Delay) public blockDelayPassed; // id => Delay
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L130
line 132
mapping(uint => uint) public limitDelay; // id => block.timestamp
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L132
If both fields are accessed in the same function, can save gas per access due to not having to recalculate the key and calculation's associated stack operations.
blockDelayPassed and limitDelay are both being used in the same functions mostly consider making them a struct instead 

##[G-02]
Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate
#TradingExtension.sol
line 18
mapping(address => uint) public minPositionSize;
URL: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L18
line 19
mapping(address => bool) public allowedMargin;
URL: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L19
If both fields are accessed in the same function, can save gas per access due to not having to recalculate the key and calculation's associated stack operations.
allowedMargin and minPositionSize are both being used in the same functions mostly consider making them a struct instead 

##[G-03]
Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate
#GovNFT.sol
line 22
mapping(uint16 => mapping(address => bool)) public isTrustedAddress;
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L22
line 23
mapping(uint16 => mapping(bytes => mapping(uint64 => bytes32))) public failedMessages;
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L23
If both fields are accessed in the same function, can save gas per access due to not having to recalculate the key and calculation's associated stack operations.
isTrustedAddress and failedMessages are both being used in the same functions mostly consider making them a struct instead. 

##[G-04]
Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate
#Lock.sol
line 18
mapping(address => bool) public allowedAssets;
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Lock.sol#L18
line 19
mapping(address => uint) public totalLocked;
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Lock.sol#L19
If both fields are accessed in the same function, can save gas per access due to not having to recalculate the key and calculation's associated stack operations.
allowedAssets and totalLocked are both being used in the same functions mostly consider making them a struct instead.

##[G-05]
Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate
#BondNFT.sol
line 37
mapping(address => uint) public totalShares;
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L37
line 38
mapping(address => mapping(address => uint)) public userDebt; // user => tigAsset => amount
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L38
If both fields are accessed in the same function, can save gas per access due to not having to recalculate the key and calculation's associated stack operations.
allowedAssets and totalLocked are both being used in the same functions mostly consider making them a struct instead.

##[G-06]
Unsinged integers lower than 32 bytes are more costly because solidity automatically assigns 32 bytes minimum to unsigned integers. 
e.g.  uint8 v, on line 73 of Trading.sol 
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L73
remediation: use uint256 and then downcast.


##[G-07]
i++ should be unchecked{i++} when it is not possible for them to overflow, as is the case when used in for-loop and while-loops
In Solidity 0.8+, there’s a default overflow check on unsigned integers. 
It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.
e.g.  for (uint i=0; i < assets.length; i++) {
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Lock.sol#L113
remediation: use unchecked{i++}

##[G-08]
i++ should be unchecked{i++} when it is not possible for them to overflow, as is the case when used in for-loop and while-loops
In Solidity 0.8+, there’s a default overflow check on unsigned integers. 
It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.
e.g.  for (uint i=0; i<_ids.length; i++) {
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L296
remediation: use unchecked{i++}

##[G-09]
i++ should be unchecked{i++} when it is not possible for them to overflow, as is the case when used in for-loop and while-loops
In Solidity 0.8+, there’s a default overflow check on unsigned integers. 
It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.
e.g.  for (uint i=0; i<_ids.length; i++) {
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L304
remediation: use unchecked{i++}

##[G-10]
i++ should be unchecked{i++} when it is not possible for them to overflow, as is the case when used in for-loop and while-loops
In Solidity 0.8+, there’s a default overflow check on unsigned integers. 
It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.
e.g.  for (uint i=0; i<_codeOwnersL; i++) {
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Referrals.sol#L70
remediation: use unchecked{i++}

##[G-11]
i++ should be unchecked{i++} when it is not possible for them to overflow, as is the case when used in for-loop and while-loops
In Solidity 0.8+, there’s a default overflow check on unsigned integers. 
It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.
e.g.  for (uint i=0; i<_referredAL; i++) {
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Referrals.sol#L73
remediation: use unchecked{i++}

##[G-12]
i++ should be unchecked{i++} when it is not possible for them to overflow, as is the case when used in for-loop and while-loops
In Solidity 0.8+, there’s a default overflow check on unsigned integers. 
It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.
e.g.  for (uint i=0; i<assetsLength(); i++) {
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L53
remediation: use unchecked{i++}

##[G-13]
i++ should be unchecked{i++} when it is not possible for them to overflow, as is the case when used in for-loop and while-loops
In Solidity 0.8+, there’s a default overflow check on unsigned integers. 
It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.
e.g.  for (uint i=0; i<assetsLength(); i++) {
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L67
remediation: use unchecked{i++}

##[G-14]
i++ should be unchecked{i++} when it is not possible for them to overflow, as is the case when used in for-loop and while-loops
In Solidity 0.8+, there’s a default overflow check on unsigned integers. 
It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.
e.g.  for (uint i=0; i<assetsLength(); i++) {
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L78
remediation: use unchecked{i++}

##[G-15]
i++ should be unchecked{i++} when it is not possible for them to overflow, as is the case when used in for-loop and while-loops
In Solidity 0.8+, there’s a default overflow check on unsigned integers. 
It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.
e.g.  for (uint i=0; i<assetsLength(); i++) {
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L95
remediation: use unchecked{i++}

##[G-16]
i++ should be unchecked{i++} when it is not possible for them to overflow, as is the case when used in for-loop and while-loops
In Solidity 0.8+, there’s a default overflow check on unsigned integers. 
It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.
e.g.  for (uint i=0; i<_amount; i++) {
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L105
remediation: use unchecked{i++}

##[G-17]
i++ should be unchecked{i++} when it is not possible for them to overflow, as is the case when used in for-loop and while-loops
In Solidity 0.8+, there’s a default overflow check on unsigned integers. 
It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.
e.g.  for (uint i=0; i<tokenId.length; i++) {
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L131
remediation: use unchecked{i++}

##[G-18]
i++ should be unchecked{i++} when it is not possible for them to overflow, as is the case when used in for-loop and while-loops
In Solidity 0.8+, there’s a default overflow check on unsigned integers. 
It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.
e.g.  for (uint i=0; i<tokenId.length; i++) {
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L200
remediation: use unchecked{i++}

##[G-19]
i++ should be unchecked{i++} when it is not possible for them to overflow, as is the case when used in for-loop and while-loops
In Solidity 0.8+, there’s a default overflow check on unsigned integers. 
It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.
e.g. for (uint i=0; i<_ids.length; i++) {
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L246
remediation: use unchecked{i++}

##[G-20]
i++ should be unchecked{i++} when it is not possible for them to overflow, as is the case when used in for-loop and while-loops
In Solidity 0.8+, there’s a default overflow check on unsigned integers. 
It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.
e.g. for (uint i=0; i<_ids.length; i++) {
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L252
remediation: use unchecked{i++}

##[G-21]
i++ should be unchecked{i++} when it is not possible for them to overflow, as is the case when used in for-loop and while-loops
In Solidity 0.8+, there’s a default overflow check on unsigned integers. 
It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.
e.g. for (uint i=0; i<_ids.length; i++) {
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L258
remediation: use unchecked{i++}

##[G-21]
i++ should be unchecked{i++} when it is not possible for them to overflow, as is the case when used in for-loop and while-loops
In Solidity 0.8+, there’s a default overflow check on unsigned integers. 
It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.
e.g. for (uint i=0; i<_ids.length; i++) {
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L325
remediation: use unchecked{i++}

##[G-22]
i++ should be unchecked{i++} when it is not possible for them to overflow, as is the case when used in for-loop and while-loops
In Solidity 0.8+, there’s a default overflow check on unsigned integers. 
It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.
e.g. for (uint i=0; i < assets.length; i++) {
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Lock.sol#L113
remediation: use unchecked{i++}

##[G-23]
i++ should be unchecked{i++} when it is not possible for them to overflow, as is the case when used in for-loop and while-loops
In Solidity 0.8+, there’s a default overflow check on unsigned integers. 
It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.
e.g. for (uint i=epoch[_tigAsset]; i<aEpoch; i++) {
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L220
remediation: use unchecked{i++}

##[G-24]
i++ should be unchecked{i++} when it is not possible for them to overflow, as is the case when used in for-loop and while-loops
In Solidity 0.8+, there’s a default overflow check on unsigned integers. 
It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.
e.g. for (uint i=0; i<_ids.length; i++) {
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L284
remediation: use unchecked{i++}

##[G-25]
i++ should be unchecked{i++} when it is not possible for them to overflow, as is the case when used in for-loop and while-loops
In Solidity 0.8+, there’s a default overflow check on unsigned integers. 
It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.
e.g. for (uint i=0; i<_ids.length; i++) {
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L292
remediation: use unchecked{i++}

##[G-26]
i++ should be unchecked{i++} when it is not possible for them to overflow, as is the case when used in for-loop and while-loops
In Solidity 0.8+, there’s a default overflow check on unsigned integers. 
It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.
e.g. for (uint i=0; i<_ids.length; i++) {
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L300
remediation: use unchecked{i++}

##[G-27]
i++ should be unchecked{i++} when it is not possible for them to overflow, as is the case when used in for-loop and while-loops
In Solidity 0.8+, there’s a default overflow check on unsigned integers. 
It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.
e.g. for (uint i=0; i<_ids.length; i++) {
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L342
remediation: use unchecked{i++}
