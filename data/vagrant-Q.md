Remove unused imports:
StableVault.sol: ReentrancyGuard.sol
Lock.sol: hardhat/console.sol
PairsContract.sol: IPosition.sol

BondNFT.sol:
struct Bond uses one additional slot,
if possible change it so the bool is included in a slot with one of the address types.

IPosition.sol:
struct Trade and MintTrade can be gas optimized by packing the bool with an address into one slot.

