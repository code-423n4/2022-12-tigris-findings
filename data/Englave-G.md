#### Functions that can be declared external
“public” functions never called by the contract should be declared “external” to save gas.
Solidity immediately copies array arguments to memory in public functions, while external functions can read directly from the calldata. Memory allocation is expensive, whereas reading from calldata is cheap.
**Path:** ./contracts/Lock.sol : lock(), extendLock(), release(); 
./contracts/BondNFT.sol : isExpired(), pending(), totalAssets(), getAssets(), balanceIds(), setManager();
./contracts/GovNFT.sol : crossChain(), nonblockingLzReceive(), retryMessage();
./contracts/PairsContract.sol : idToAsset(), idToOi();
./contracts/Position.sol : isMinter(), openPositions(), openPositionsIndexes(), assetOpenPositions(), assetOpenPositionsIndexes(), limitOrders(), limitOrderIndexes();
**Recommendation:** Use external instead of public