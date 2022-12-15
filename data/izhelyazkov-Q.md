Owner can renounce ownership.
The owner can perform some privileged activities.
Since he inherits from Openzeppelin's implementation of Ownable, 
he can call `renounceOwnership` at any time to renounce his owner rights, 
leaving all of the `onlyOwner` functions in the following contracts uncallable:
`contracts/BondNFT.sol`
`contracts/Lock.sol`
`contracts/NFTSale.sol`
`contracts/PairsContract.sol`
`contracts/Referrals.sol`
`contracts/TradingExtension.sol`