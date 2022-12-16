Remove unused imports:
StableVault.sol: ReentrancyGuard.sol
Lock.sol: hardhat/console.sol
PairsContract.sol: IPosition.sol

BondNFT.sol:
struct Bond uses one additional slot,
if possible change it so the bool is included in a slot with one of the address types.

Lock.sol:
low likelihood of stuck funds if manager not set in BondsNFT.sol 
but allowedAssets are already added to Lock.sol.
If someone were to call Lock.lock() no Bond will be minted and funds will be stuck.
Make sure allowedAssets are added after manager is set in BondNFT.sol

