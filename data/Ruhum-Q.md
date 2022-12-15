# Report

- [Report](#report)
  - [01: `GovNFT.maxBridge` state varialbe is unused](#01-govnftmaxbridge-state-varialbe-is-unused)
  - [02: `PairsContract.addAsset()` doesn't verify baseFundingRate](#02-pairscontractaddasset-doesnt-verify-basefundingrate)
  - [03: `Lock.claimGovFees()` will revert with tokens that have approve race condition protection](#03-lockclaimgovfees-will-revert-with-tokens-that-have-approve-race-condition-protection)


## 01: `GovNFT.maxBridge` state varialbe is unused

I think the value is supposed to limit the number of nfts that a user can bridge at any given time. But, it's not used anywhere.

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L19

## 02: `PairsContract.addAsset()` doesn't verify baseFundingRate

The parameter is supposed to be smaller than `1e10`. But, that check doesn't happen in `addAsset()` only in `setAssetBaseFundingRate()`:

- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L62
- https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L95

A value higher than `1e10` will cause issues here: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L120

## 03: `Lock.claimGovFees()` will revert with tokens that have approve race condition protection

Some tokens only allow you to approve a new value if the current allowance is set to zero, e.g. [USDT](https://etherscan.io/address/0xdac17f958d2ee523a2206206994597c13d831ec7#code#L199). 

This is not an issue right now on Polygon or Arbitrum. But, stablecoin contracts on those networks are upgradable and migth change. Also, if you decide to deploy the protocol to other networks, you might encounter this problem.