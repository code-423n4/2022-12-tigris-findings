##Gas Optimization Report

##[G-01]
Multiple mapping id/addresses/bool can be changed to struct data type

#Trading.sol
line 130
mapping(uint => Delay) public blockDelayPassed; // id => Delay
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L130

line 132
mapping(uint => uint) public limitDelay; // id => block.timestamp
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L132

line 134
mapping(address => bool) public allowedVault;
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L134

line 141
mapping(address => Proxy) public proxyApprovals;
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L141

##[G-02]
Multiple mapping id/addresses/bool can be changed to struct data type
#TradingExtension.sol
line 17
mapping(address => bool) private isNode;
URL: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L17

line 18
mapping(address => uint) public minPositionSize;
URL: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L18

line 19
mapping(address => bool) public allowedMargin;
URL: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L19

##[G-03]
Multiple mapping id/addresses/bool can be changed to struct data type
#TradingLibrary.sol
line 98
mapping(address => bool) storage _isNode
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/utils/TradingLibrary.sol#L98

##[G-04]
Multiple mapping id/addresses/bool can be changed to struct data type
Position.sol
line 18 
mapping(uint => mapping(address => uint)) public vaultFundingPercent;
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L18

line 20 
mapping(address => bool) private _isMinter; // Trading contract should be minter
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L20

line 21 
mapping(uint256 => Trade) private _trades; // NFT id to Trade
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L21

line 24 
mapping(uint256 => uint256) private _openPositionsIndexes;
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L24

line 26 
mapping(uint256 => uint256[]) private _assetOpenPositions;
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L26

line 27 
mapping(uint256 => mapping(uint256 => uint256)) private _assetOpenPositionsIndexes;
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L27

line 29 
mapping(uint256 => uint256[]) private _limitOrders; // List of limit order nft ids per asset
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L29

line 30 
mapping(uint256 => mapping(uint256 => uint256)) private _limitOrderIndexes; // Keeps track of asset -> id -> array index
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L30

line 33 
mapping(uint256 => mapping(address => int256)) public fundingDeltaPerSec;
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L33

line 34 
mapping(uint256 => mapping(address => mapping(bool => int256))) private accInterestPerOi;
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L34

line 35 
mapping(uint256 => mapping(address => uint256)) private lastUpdate;
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L35

line 36 
mapping(uint256 => int256) private initId;
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L36

line 37 
mapping(uint256 => mapping(address => uint256)) private longOi;
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L37

line 38 
mapping(uint256 => mapping(address => uint256)) private shortOi;
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L38

##[G-05]
Multiple mapping id/addresses/bool can be changed to struct data type
#PairsContract.sol
line 12
mapping(uint256 => bool) public allowedAsset;
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/PairsContract.sol#L12

line 16
mapping(uint256 => Asset) private _idToAsset;
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/PairsContract.sol#L16

line 21
mapping(uint256 => mapping(address => OpenInterest)) private _idToOi;
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/PairsContract.sol#L21

##[G-06]
Multiple mapping id/addresses/bool can be changed to struct data type
#Referrals.sol
line 13
mapping(bytes32 => address) private _referral;
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Referrals.sol#L13

line 14
mapping(address => bytes32) private _referred;
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Referrals.sol#L14

##[G-07]
Multiple mapping id/addresses/bool can be changed to struct data type
#GovNFT.sol
line 22
mapping(uint16 => mapping(address => bool)) public isTrustedAddress;
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L22

line 23
mapping(uint16 => mapping(bytes => mapping(uint64 => bytes32))) public failedMessages;
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L23

line 265
mapping(address => bool) private _allowedAsset;
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L265

line 266
mapping(address => uint) private assetsIndex;
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L266

line 267
mapping(address => mapping(address => uint256)) private userPaid;
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L267

line 268
mapping(address => mapping(address => uint256)) private userDebt;
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L268

line 269
mapping(address => uint256) private accRewardsPerNFT;
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L269

##[G-08]
Multiple mapping id/addresses/bool can be changed to struct data type
#StableToken.sol
line 9
mapping(address => bool) public isMinter;
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/StableToken.sol#L9

##[G-09]
Multiple mapping id/addresses/bool can be changed to struct data type
#StableVault.sol
line 29
mapping(address => bool) public allowed;
URL: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L29

line 30
mapping(address => uint) private tokenIndex;
URL: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L30

##[G-10]
Multiple mapping id/addresses/bool can be changed to struct data type
#Lock.sol
line 18
mapping(address => bool) public allowedAssets;
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Lock.sol#L18

line 19
mapping(address => uint) public totalLocked;
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Lock.sol#L19

##[G-11]
Multiple mapping id/addresses/bool can be changed to struct data type
#BondNFT.sol
line 26
mapping(address => uint256) public epoch;
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L26

line 32
mapping(address => bool) public allowedAsset;
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L32

line 33
mapping(address => uint) private assetsIndex;
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L33

line 34
mapping(uint256 => mapping(address => uint256)) private bondPaid;
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L34

line 35
mapping(address => mapping(uint256 => uint256)) private accRewardsPerShare; // tigAsset => epoch => accRewardsPerShare
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L35

line 36
mapping(uint => Bond) private _idToBond;
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L36

line 37
mapping(address => uint) public totalShares;
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L37

line 38
mapping(address => mapping(address => uint)) public userDebt; // user => tigAsset => amount
URL: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L38

Gas costs: INTERNAL ERROR in module Gas costs: e.inputs[varIndex] is undefined Line: not available
URL: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol

Gas Cost:
Gas requirement of function TradingExtension._closePosition is infinite: 
If the gas requirement of a function is higher than the block gas limit, it cannot be executed. 
Please avoid loops in your functions or actions that modify large areas of storage (this includes clearing or copying arrays in storage)
Line: 61
URL: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol

Gas Cost: 
Gas requirement of function TradingExtension._limitClose is infinite: 
If the gas requirement of a function is higher than the block gas limit, it cannot be executed. 
Please avoid loops in your functions or actions that modify large areas of storage (this includes clearing or copying arrays in storage)
Line: 88
URL: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol

Gas Cost:
Gas requirement of function TradingExtension._limitClose is infinite: 
If the gas requirement of a function is higher than the block gas limit, it cannot be executed. 
Please avoid loops in your functions or actions that modify large areas of storage (this includes clearing or copying arrays in storage)
Line: 88
URL: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol

Gas costs: 
Gas requirement of function TradingExtension.modifyShortOi is infinite: 
If the gas requirement of a function is higher than the block gas limit, it cannot be executed. 
Please avoid loops in your functions or actions that modify large areas of storage (this includes clearing or copying arrays in storage)
Line: 126 
URL: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol

Gas costs: 
Gas requirement of function TradingExtension.modifyLongOi is infinite: 
If the gas requirement of a function is higher than the block gas limit, it cannot be executed. 
Please avoid loops in your functions or actions that modify large areas of storage (this includes clearing or copying arrays in storage)
Line: 135 
URL: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol

Gas costs: 
Gas requirement of function TradingExtension.getRef is infinite: 
If the gas requirement of a function is higher than the block gas limit, it cannot be executed. 
Please avoid loops in your functions or actions that modify large areas of storage (this includes clearing or copying arrays in storage)
Line: 148 
URL: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol

Gas costs: 
Gas requirement of function TradingExtension.getVerifiedPrice is infinite: 
If the gas requirement of a function is higher than the block gas limit, it cannot be executed. 
Please avoid loops in your functions or actions that modify large areas of storage (this includes clearing or copying arrays in storage)
Line: 163 
URL: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol

Gas costs: 
Gas requirement of function TradingExtension._setReferral is infinite: 
If the gas requirement of a function is higher than the block gas limit, it cannot be executed. 
Please avoid loops in your functions or actions that modify large areas of storage (this includes clearing or copying arrays in storage)
Line: 190 
URL: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol

Gas costs: 
Gas requirement of function TradingExtension.validateTrade is infinite: 
If the gas requirement of a function is higher than the block gas limit, it cannot be executed. 
Please avoid loops in your functions or actions that modify large areas of storage (this includes clearing or copying arrays in storage)
Line: 211 
URL: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol

Gas costs: 
Gas requirement of function TradingLibrary.pnl is infinite: 
If the gas requirement of a function is higher than the block gas limit, it cannot be executed. 
Please avoid loops in your functions or actions that modify large areas of storage (this includes clearing or copying arrays in storage) 
Line: 36
URL: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol

Gas costs: 
Gas requirement of function TradingLibrary.liqPrice is infinite: 
If the gas requirement of a function is higher than the block gas limit, it cannot be executed. 
Please avoid loops in your functions or actions that modify large areas of storage (this includes clearing or copying arrays in storage) 
Line: 62
URL: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol

Gas costs: 
Gas requirement of function TradingLibrary.getLiqPrice is infinite: 
If the gas requirement of a function is higher than the block gas limit, it cannot be executed. 
Please avoid loops in your functions or actions that modify large areas of storage (this includes clearing or copying arrays in storage) 
Line: 76
URL: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol

Gas costs: 
Gas requirement of function Referrals.createReferralCode is infinite: 
If the gas requirement of a function is higher than the block gas limit, it cannot be executed. 
Please avoid loops in your functions or actions that modify large areas of storage (this includes clearing or copying arrays in storage) 
Line: 20
URL: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol

Gas costs: 
Gas requirement of function Referrals.setReferred is infinite: 
If the gas requirement of a function is higher than the block gas limit, it cannot be executed. 
Please avoid loops in your functions or actions that modify large areas of storage (this includes clearing or copying arrays in storage) 
Line: 32
URL: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol

Gas costs: 
Gas requirement of function Referrals.initRefs is infinite: 
If the gas requirement of a function is higher than the block gas limit, it cannot be executed. 
Please avoid loops in your functions or actions that modify large areas of storage (this includes clearing or copying arrays in storage) 
Line: 60
URL: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol

Gas costs: 
Gas requirement of function BondNFT.baseURI is infinite: 
If the gas requirement of a function is higher than the block gas limit, it cannot be executed. 
Please avoid loops in your functions or actions that modify large areas of storage (this includes clearing or copying arrays in storage) 
Line: 28
URL: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol

Gas costs: 
Gas requirement of function BondNFT.createLock is infinite: 
If the gas requirement of a function is higher than the block gas limit, it cannot be executed. 
Please avoid loops in your functions or actions that modify large areas of storage (this includes clearing or copying arrays in storage) 
Line: 57
URL: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol

Gas costs: 
Gas requirement of function BondNFT.extendLock is infinite: 
If the gas requirement of a function is higher than the block gas limit, it cannot be executed. 
Please avoid loops in your functions or actions that modify large areas of storage (this includes clearing or copying arrays in storage) 
Line: 97
URL: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol

Gas costs: 
Gas requirement of function BondNFT.claim is infinite: 
If the gas requirement of a function is higher than the block gas limit, it cannot be executed. 
Please avoid loops in your functions or actions that modify large areas of storage (this includes clearing or copying arrays in storage) 
Line: 168
URL: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol

Gas costs: 
Gas requirement of function BondNFT.claimDebt is infinite: 
If the gas requirement of a function is higher than the block gas limit, it cannot be executed. 
Please avoid loops in your functions or actions that modify large areas of storage (this includes clearing or copying arrays in storage) 
Line: 196
URL: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol

Gas costs: 
Gas requirement of function BondNFT.distribute is infinite: 
If the gas requirement of a function is higher than the block gas limit, it cannot be executed. 
Please avoid loops in your functions or actions that modify large areas of storage (this includes clearing or copying arrays in storage) 
Line: 211
URL: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol

Gas costs: 
Gas requirement of function BondNFT.idToBond is infinite: 
If the gas requirement of a function is higher than the block gas limit, it cannot be executed. 
Please avoid loops in your functions or actions that modify large areas of storage (this includes clearing or copying arrays in storage) 
Line: 235
URL: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol

Gas costs: 
Gas requirement of function BondNFT.pending is infinite: 
If the gas requirement of a function is higher than the block gas limit, it cannot be executed. 
Please avoid loops in your functions or actions that modify large areas of storage (this includes clearing or copying arrays in storage) 
Line: 260
URL: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol

Gas costs: 
Gas requirement of function BondNFT.getAssets is infinite: 
If the gas requirement of a function is higher than the block gas limit, it cannot be executed. 
Please avoid loops in your functions or actions that modify large areas of storage (this includes clearing or copying arrays in storage) 
Line: 274
URL: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol

Gas costs: 
Gas requirement of function BondNFT.safeTransferMany is infinite: 
If the gas requirement of a function is higher than the block gas limit, it cannot be executed. 
Please avoid loops in your functions or actions that modify large areas of storage (this includes clearing or copying arrays in storage) 
Line: 282
URL: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol

Gas costs: 
Gas costs: Gas requirement of function BondNFT.safeTransferFromMany is infinite: 
If the gas requirement of a function is higher than the block gas limit, it cannot be executed. 
Please avoid loops in your functions or actions that modify large areas of storage (this includes clearing or copying arrays in storage) 
Line: 290
URL: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol

Gas costs: 
Gas requirement of function BondNFT.approveMany is infinite: 
If the gas requirement of a function is higher than the block gas limit, it cannot be executed. 
Please avoid loops in your functions or actions that modify large areas of storage (this includes clearing or copying arrays in storage) 
Line: 298
URL: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol

Gas costs: 
Gas requirement of function BondNFT.balanceIds is infinite: 
If the gas requirement of a function is higher than the block gas limit, it cannot be executed. 
Please avoid loops in your functions or actions that modify large areas of storage (this includes clearing or copying arrays in storage) 
Line: 339
URL: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol

Gas costs: 
Gas requirement of function BondNFT.addAsset is infinite: 
If the gas requirement of a function is higher than the block gas limit, it cannot be executed. 
Please avoid loops in your functions or actions that modify large areas of storage (this includes clearing or copying arrays in storage) 
Line: 349
URL: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol

Gas costs: 
Gas requirement of function BondNFT.setBaseURI is infinite: 
If the gas requirement of a function is higher than the block gas limit, it cannot be executed. 
Please avoid loops in your functions or actions that modify large areas of storage (this includes clearing or copying arrays in storage) 
Line: 362
URL: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol

For loop over dynamic array: 
Loops that do not have a fixed number of iterations, for example, loops that depend on storage values, have to be used carefully. 
Due to the block gas limit, transactions can only consume a certain amount of gas. 
The number of iterations in a loop can grow beyond the block gas limit which can cause the complete contract to be stalled at a certain point. 
Additionally, using unbounded loops incurs in a lot of avoidable gas costs. 
Carefully test how many items at maximum you can pass to such functions to make it successful.
Line: 284
URL: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol

For loop over dynamic array: 
Loops that do not have a fixed number of iterations, for example, loops that depend on storage values, have to be used carefully. 
Due to the block gas limit, transactions can only consume a certain amount of gas. 
The number of iterations in a loop can grow beyond the block gas limit which can cause the complete contract to be stalled at a certain point. 
Additionally, using unbounded loops incurs in a lot of avoidable gas costs. 
Carefully test how many items at maximum you can pass to such functions to make it successful.
Line: 292
URL: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol

For loop over dynamic array: 
Loops that do not have a fixed number of iterations, for example, loops that depend on storage values, have to be used carefully. 
Due to the block gas limit, transactions can only consume a certain amount of gas. 
The number of iterations in a loop can grow beyond the block gas limit which can cause the complete contract to be stalled at a certain point. 
Additionally, using unbounded loops incurs in a lot of avoidable gas costs. 
Carefully test how many items at maximum you can pass to such functions to make it successful.
Line: 300
URL: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol

For loop over dynamic array: 
Loops that do not have a fixed number of iterations, for example, loops that depend on storage values, have to be used carefully. 
Due to the block gas limit, transactions can only consume a certain amount of gas. 
The number of iterations in a loop can grow beyond the block gas limit which can cause the complete contract to be stalled at a certain point. 
Additionally, using unbounded loops incurs in a lot of avoidable gas costs. 
Carefully test how many items at maximum you can pass to such functions to make it successful.
Line: 342
URL: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol