##Gas Optimization Test Plan

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
userDebt and totalShares are both being used in the same functions mostly consider making them a struct instead.