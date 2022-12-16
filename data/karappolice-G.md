Gas Finding 1:

In  the following line, https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L221

I  see the epoch of asset is incremented by 1 at a time which is not gas efficient. Can replace with appropriate logic like epoch[_tigAsset]+ = aEpoch-epoch[_tigAsset]


Gas finding 2:

Avoid executing same function assetsLength()  multiple times. Note that Gas7 and Gas8 in automated findings doesn't point to this. They discuss about initializing and counter.

https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L53
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L67
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L95





