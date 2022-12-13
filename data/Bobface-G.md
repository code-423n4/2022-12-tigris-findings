# Tigris Trade Gas Report


## Value expensively computed multiple times
A value is expensively computed multiple times, such as reading from storage, arithmetic, internal function calls and jumps, or external calls. Storing the value in a local variable and reusing it will save gas by avoiding to repeatedly run costly instructions.

| File                    | Lines                         | Description |
|---|---|---|
| `contracts/Trading.sol` | 885, 887 | `_msgSender()` |
|`contracts/Trading.sol`| 821, 822 | `idToOi()` |
| `contracts/StableVault.sol` | 46, 48 | `_msgSender()`  |
| `contracts/StableVault.sol` | 66, 69 | `_msgSender()`  |
| `contracts/Referrals.sol` | 22, 23  | `_msgSender()`  |
| `contracts/GovNFT.sol` | 80, 81 | `userPaid[owner][assets[i]]/balanceOf(owner)`  |
| `contracts/GovNFT.sol` | 96, 99 | `accRewardsPerNFT[assets[i]]` |
| `contracts/GovNFT.sol` | 97, 98 | `userPaid[from][assets[i]]/balanceOf(from)` |
| `contracts/GovNFT.sol` | 106  | `_msgSender()` (in loop)|
| `contracts/GovNFT.sol` | 132, 161  | `_msgSender()`  |
| `contracts/GovNFT.sol` | 247  | `_msgSender()` (in loop) |
| `contracts/BondNFT.sol` | 66, 74 | `epoch` |
| `contracts/BondNFT.sol` | 110, 122 | `epoch` |
| `contracts/BondNFT.sol` | 178 | `bondPaid[_id][bond.asset]` |
| `contracts/BondNFT.sol` | 179, 180 | `totalShares[bond.asset` |
| `contracts/BondNFT.sol` | 219, 220 | `epoch[_tigAsset]` |
| `contracts/BondNFT.sol` | 285 | `_msgSender()`  |
|`contracts/Position.sol`| 102 104, 107, 109 | `fundingDeltaPerSec[_asset][_tigAsset]` |
|`contracts/Position.sol`| 102 104, 107, 109 | `lastUpdate[_asset][_tigAsset]` |
|`contracts/Position.sol`| 100, 101, 102, 106, 107 | `longOi[_asset][_tigAsset]` |
|`contracts/Position.sol`| 100, 104, 106, 108, 109 | `shortOi[_asset][_tigAsset]` |
|`contracts/Position.sol`| 102, 109 | `vaultFundingPercent[_asset][_tigAsset]` |

## Missing storage pointers
Some storage locations need to be expensively calculated before being written to, such as mappings or arrays. If the storage location is written to or read from multiple times, saving the location in a storage pointer avoids recalculation.
| File                    | Lines                         | Description |
|---|---|---|
|`contracts/Trading.sol`| 864 - 865 | `blockDelayPassed[_id]` |
|`contracts/Referrals.sol`| 33, 39 | `_referred[_referredTrader]` |
|`contracts/Position.sol`| 102, 104, 107, 109 | `accInterestPerOi[_asset][_tigAsset]` |
|`contracts/Position.sol`| 198 - 199 | `_trades[_id]` |
|`contracts/Position.sol`| 210 - 212 | `_trades[_id]` |
|`contracts/Position.sol`| 231 - 233 | `_trades[_id]` |
|`contracts/PairsContracts.sol`| 49, 55 - 62 | `_idToAsset[_asset]` |
|`contracts/PairsContract.sol`| 74, 78, 81, 84 | `_idToAsset[_asset]` |
|`contracts/PairsContract.sol`| 93, 96 | `_idToAsset[_asset]` |
|`contracts/PairsContract.sol`| 105, 107 | `_idToAsset[_asset]` |
|`contracts/PairsContract.sol`| 154 - 162 | `_idToOi[_asset][_tigAsset]` |
|`contracts/PairsContract.sol`| 176 - 182 | `_idToOi[_asset][_tigAsset]` |
|`contracts/GovNFT.sol`| 79 - 80 | `userDebt[owner][assets[i]]` |
|`contracts/GovNFT.sol`| 80 - 81 | `userPaid[owner][assets[i]]` |
|`contracts/GovNFT.sol`| 96 - 97 | `userDebt[from][assets[i]]` |
|`contracts/GovNFT.sol`| 97 - 99 | `userPaid[to][assets[i]]` |


## Value dropped and recomputed
A value is computed, dropped, and then recalculated. Storing the value or returning it from a function avoid recomputing it.

| File                    | Line                         | Description |
|---|---|---|
| `contracts/Trading.sol` | 875  | `_checkVault()` calls `.stable()` on the vault. This function should return the `stable` address, since `_checkVault()` is called from other functions which then again call `.stable()` a second time (e.g. `initiateLimitOrder()` or `initiateMarketOrder()`). By caching and returning the `stable` address, a second expensive `STATICCALL` can be avoided. |

## Missing unchecked blocks
Arithmetic which is clearly guaranteed to not over- or underflow should be wrapped in an `unchecked` block to exclude compiler-inserted overflow and underflow checks.
| File                    | Line                         | Description |
|---|---|---|
| `contracts/StableVault.sol` | 91, 92 | The first `require` in the function ensures that the subtraction cannot underflow and that the index is in bounds.  |

## Redundant assertions
The same condition is asserted multiple times.
| File                    | Line                         | Description |
|---|---|---|
| `contracts/GovNFT.sol` | 94 | The function later unconditionally calls into `super._transfer()`, which is the OpenZeppelin ERC721 implementation, which then does the same check again.   |

## Storage writes in loop
A storage location is written to in a loop, such as a counter which is incremented in each iteration. Since storage writes are expensive operations, performing the writes as a single operation after the loop is preferred.
| File                    | Line                         | Description |
|---|---|---|
| `contracts/BondNFT.sol` | 221 | `epoch[_tigAsset] += 1` |

## Large struct fully loaded into memory
A large struct is fully loaded into memory even though only a minor subset of its fields are later used. Loading the entire struct is costly, since the EVM only offers reading storage slot-by-slot, resulting in many storage loads. Reading the required fields directly from storage is thus cheaper.
| File                    | Line                         | Description |
|---|---|---|
| `contracts/BondNFT.sol` | 251 | `Bond` |

## Complex arithmetic
Arithmetic which can be simplified to save operations.
| File                    | Line                         | Description |
|---|---|---|
| `contracts/utils/TradingLibrary.sol` | 117, 118 | Doing `assetChainlinkPrice*102/100` and `assetChainlinkPrice*98/100` will save an addition. |