**contracts/Trading.sol**
- L8/29/37 - IReferrals is imported, but never used, so it should be removed. The same happens with some functions inside interfaces that are not used, like: isPaused(), mocifyShortOi()

- L81/86/87/88/89/90 - Created some bugs that were never used, therefore they should be removed.


**contracts/PairsContract.sol**
- L6 - IPosition is imported, but is never used, so it should be removed.


**contracts/utils/TradingLibrary.sol**
- L40/42/44/46/48/64/66 - A division is made by an input, therefore it should be previously validated that they are != 0, this is necessary, since otherwise it is reversed without knowing the reason.


**contracts/Lock.sol**
- L4 - The hardhat console is imported, something that should not be there since it is import useful for testing, it should be removed


**contracts/StableVault.sol**
- L5 - ReentrancyGuard is imported, but is never used, so it should be removed.


**contracts/GovNFT.sol**
- L154 - It allows executing the transaction if msg.value == messageFee, this seems little logical to me since if one sends 100, the function should not be able to be executed, since it would only be gas expense.
It makes more sense to do this validation: msg.value > messageFee


**contracts/BondNFT.sol**
- L252 - Instead of doing: return bond.expireEpoch <= epoch[bond.asset] ? true: false; it is more optimal to do: return bond.expireEpoch <= epoch[bond.asset];

- L109 - When we validate with a require and we could revert, it is important to throw an error message, otherwise the user would not know why he reverted.
