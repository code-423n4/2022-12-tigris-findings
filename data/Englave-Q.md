#### 1. Floating Pragma
Locking the pragma helps ensure that contracts avoid accidentally getting deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.
**Path:** all files

#### 2. StableVault deposits are limited to 18 decimals
During `deposit` and `withdraw` to/from `StableVault` contract, it mints/burns the same amount of stable tokens with respect to decimals. The current implementation supports only tokens with <= 18 decimals. It’s not possible to list a token with a bigger amount of decimals because it would cause overflow exceptions during stable token decimals calculation: `18-IERC20Mintable(_token).decimals()`.
**Path:** ./contracts/StableVault.sol : deposit()

#### 3. Debug imports presence
Some contracts use `hardhat/console.sol` debug import, which should be added to the production code.
**Path:** ./contracts/Lock.sol