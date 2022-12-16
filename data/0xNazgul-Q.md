## [NAZ-L1] Value Range Validity for Setters
**Severity** Low
**Context**: [`Trading.sol#L898`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L898), [`Trading.sol#L926`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L926), [`Trading.sol#L939`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L939), [`TradingExtension.sol#L222`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L222), [`TradingExtension.sol#L264`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L264), [`PairsContract.sol#L125`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L125), [`GovNFT.sol#L236`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L236), [`GovNFT.sol#L311`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L311)

**Description**:
These functions doesn't have any checks to ensure that the variables being set is within some kind of value range.

**Recommendation**:
Each variable input parameter updated should have it's own value range checks to ensure their validity.


## [NAZ-L2] Missing Equivalence Checks in Setters
**Severity**: Low
**Context**: [`Trading.sol#L898`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L898), [`Trading.sol#L912`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L912), [`Trading.sol#L926`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L926), [`Trading.sol#L939`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L939), [`Trading.sol#L952`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L952), [`Trading.sol#L975`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L975), [`TradingExtension.sol#L144`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L144), [`TradingExtension.sol#L222`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L222), [`TradingExtension.sol#L231`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L231), [`TradingExtension.sol#L240`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L240), [`TradingExtension.sol#L249`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L249), [`TradingExtension.sol#L264`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L264), [`TradingExtension.sol#L274`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L274), [`Position.sol#L85`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L85), [`Position.sol#L310`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L310), [`PairsContract.sol#L33`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L33), [`PairsContract.sol#L73`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L73), [`PairsContract.sol#L92`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L92), [`PairsContract.sol#L104`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L104), [`PairsContract.sol#L115`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L115), [`PairsContract.sol#L125`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L125), [`PairsContract.sol#L129`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L129), [`PairsContract.sol#L139`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L139), [`Referrals.sol#L53`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol#L53), [`GovNFT.sol#L46`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L46), [`GovNFT.sol#L114`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L114), [`GovNFT.sol#L236`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L236), [`GovNFT.sol#L240`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L240), [`GovNFT.sol#L307`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L307), [`GovNFT.sol#L311`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L311), [`StableToken.sol#L38`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableToken.sol#L38), [`Lock.sol#L127`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L127), [`BondNFT.sol#L357`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L357), [`BondNFT.sol#L362`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L362), [`BondNFT.sol#L366`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L366), [`MetaContext.sol#L9`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/MetaContext.sol#L9)

**Description**:
Setter functions are missing checks to validate if the new value being set is the same as the current value already set in the contract. Such checks will showcase mismatches between on-chain and off-chain states.

**Recommendation**:
This may hinder detecting discrepancies between on-chain and off-chain states leading to flawed assumptions of on-chain state and protocol behavior.


## [NAZ-L3] Missing Zero-address Validation
**Severity**: Low
**Context**: [`Trading.sol#L912`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L912), [`Trading.sol#L977`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L977), [`TradingExtension.sol#L240`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L240), [`TradingExtension.sol#L249`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L249), [`TradingExtension.sol#L264`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L264), [`Position.sol#L310`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L310), [`PairsContract.sol#L33`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L33), [`PairsContract.sol#L73`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L73), [`PairsContract.sol#L129`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L129), [`PairsContract.sol#L139`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L139), [`Referrals.sol#L53`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol#L53), [`Referrals.sol#L60`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol#L60), [`GovNFT.sol#L114`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L114), [`GovNFT.sol#L300`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L300), [`GovNFT.sol#L307`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L307), [`StableToken.sol#L38`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableToken.sol#L38), [`StableVault.sol#L78`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L78), [`StableVault.sol#L89`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L89), [`Lock.sol#L127`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L127), [`BondNFT.sol#L349`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L349), [`BondNFT.sol#L357`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L357), [`BondNFT.sol#L366`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L366), [`MetaContext.sol#L9`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/MetaContext.sol#L9)

**Description**:
Lack of zero-address validation on address parameters may lead to transaction reverts, waste gas, require resubmission of transactions and may even force contract redeployments in certain cases within the protocol.

**Recommendation**:
Consider adding explicit zero-address validation on input parameters of address type.


## [NAZ-L4] Lack of Event Emission For Critical Functions
**Severity**: Low
**Context**: [`Trading.sol#L898`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L898), [`Trading.sol#L912`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L912), [`Trading.sol#L926`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L926), [`Trading.sol#L939`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L939), [`Trading.sol#L952`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L952), [`Trading.sol#L975`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L975), [`TradingExtension.sol#L144`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L144), [`TradingExtension.sol#L222`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L222), [`TradingExtension.sol#L231`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L231), [`TradingExtension.sol#L240`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L240), [`TradingExtension.sol#L249`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L249), [`TradingExtension.sol#L264`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L264), [`TradingExtension.sol#L274`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L274), [`Position.sol#L85`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L85), [`Position.sol#L310`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L310), [`PairsContract.sol#L33`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L33), [`PairsContract.sol#L73`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L73), [`PairsContract.sol#L92`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L92), [`PairsContract.sol#L104`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L104), [`PairsContract.sol#L115`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L115), [`PairsContract.sol#L125`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L125), [`PairsContract.sol#L129`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L129), [`PairsContract.sol#L139`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L139), [`Referrals.sol#L53`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol#L53), [`Referrals.sol#L60`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol#L60), [`GovNFT.sol#L46`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L46), [`GovNFT.sol#L114`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L114), [`GovNFT.sol#L236`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L236), [`GovNFT.sol#L240`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L240), [`GovNFT.sol#L300`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L300), [`GovNFT.sol#L307`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L307), [`GovNFT.sol#L311`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L311), [`StableToken.sol#L38`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableToken.sol#L38), [`StableVault.sol#L78`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L78), [`StableVault.sol#L89`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L89), [`Lock.sol#L127`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L127), [`BondNFT.sol#L349`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L349), [`BondNFT.sol#L357`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L357), [`BondNFT.sol#L362`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L362), [`BondNFT.sol#L366`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L366), [`MetaContext.sol#L9`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/MetaContext.sol#L9)

**Description**:
Several functions update critical parameters that are missing event emission. These should be performed to ensure tracking of changes of such critical parameters.

**Recommendation**:
Consider adding events to functions that change critical parameters.


## [NAZ-N1] Unused Import
**Severity**: Informational
**Context**: [`StableVault.sol#L5`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L5)

**Description**:
No need to have the import if it is not going to be used.

**Recommendation**:
Consider using the import or just removing it.


## [NAZ-N2] Array length mismatch
**Severity**: Informational
**Context**: [`Referrals.sol#L60`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol#L60)

**Description**:
These fail to perform input validation on arrays to verify the lengths match. A mismatch could lead to an exception or undefined behavior.

**Recommendation**:
Perform input validation on the arrays to verify that the lengths match.


## [NAZ-N3] Code Contains Empty Blocks
**Severity**: Informational
**Context**: [`StableToken.sol#L11`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableToken.sol#L11)

**Description**:
It's best practice that when there is an empty block, to add a comment in the block explaining why it's empty.

**Recommendation**:
Consider adding `/* Comment on why */` to the empty blocks.


## [NAZ-N4] Function && Variable Naming Convention
**Severity** Informational
**Context**: [`Trading.sol#L96`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L96), [`Trading.sol#L121-L124`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L121-L124), [`TradingExtension.sol#L22-L24`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L22-L24), [`TradingExtension.sol#L61`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L61), [`TradingExtension.sol#L88`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L88), [`TradingExtension.sol#L122`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L112), [`TradingExtension.sol#L190`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L190), [`PairsContract.sol#L14`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L14), [`Referrals.sol#L9`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol#L9), [`GovNFT.sol#L15`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L15), [`GovNFT.sol#L64`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L64), [`Lock.sol#L12-L13`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L12-L13), [`BondNFT.sol#L27`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L27)

**Description**:
The linked variables do not conform to the standard naming convention of Solidity whereby functions and variable names(local and state) utilize the `mixedCase` format unless variables are declared as `constant` in which case they utilize the `UPPER_CASE_WITH_UNDERSCORES` format. Private variables and functions should lead with an `_underscore`.

**Recommendation**:
Consider naming conventions utilized by the linked statements are adjusted to reflect the correct type of declaration according to the [Solidity style guide](https://docs.soliditylang.org/en/latest/style-guide.html). 


## [NAZ-N5] Code Structure Deviates From Best-Practice
**Severity**: Informational
**Context**: [`TradingExtension.sol#L61`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L61), [`TradingExtension.sol#L88`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L88), [`PairsContract.sol#L21`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L21), [`Referrals.sol#L53`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol#L53), [`GovNFT.sol#L264-L269`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L264-L269), [`StableToken.sol#L51`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableToken.sol#L51)

**Description**:
The best-practice layout for a contract should follow the following order: state variables, events, modifiers, constructor and functions. Function ordering helps readers identify which functions they can call and find constructor and fallback functions easier.  Functions should be grouped according to their visibility and ordered as: constructor, receive function (if exists), fallback function (if exists), external, public, internal, private. Functions should then further be ordered with view functions coming after the non-view labeled ones.

**Recommendation**:
Consider adopting recommended best-practice for code structure and layout.


## [NAZ-N6] Use Underscores for Number Literals
**Severity**: Informational
**Context**: [`TradingExtension.sol#L26`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L26), [`Position.sol#L120`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L120), [`GovNFT.sol#L16-L17`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L16-L17), [`GovNFT.sol#L66`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L66)

**Description**:
There are multiple occasions where certain numbers have been hardcoded, either in variables or in the code itself. Large numbers can become hard to read.

**Recommendation**:
Consider using underscores for number literals to improve its readability.


## [NAZ-N7] Spelling Errors
**Severity**: Informational
**Context**: [`Trading.sol#L635 (stablevault => stableVault)`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L635), [`Trading.sol#L637 (stablevault => stableVault)`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L637), [`Trading.sol#L662 (stablevault => stableVault)`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L662), [`Trading.sol#L871 (stablevault => stableVault)`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L871), [`Trading.sol#L876 (stablevault => stableVault)`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L876), [`Position.sol#L97 (stablevault => stableVault)`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L97), [`PairsContract.sol#L151 (interesr => interest)`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L151), [`PairsContract.sol#L171 (interesr => interest)`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L171), [`GovNFT.sol#L119 (crosschain => cross-chain)`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L119), [`Lock.sol#L135 (retreive => retrieve)`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L135)

**Description**:
Spelling errors in comments can cause confusion to both users and developers.

**Recommendation**:
Consider checking all misspellings to ensure they are corrected.


## [NAZ-N8] Missing or Incomplete NatSpec
**Severity**: Informational
**Context**: [`All Contracts`](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts)

**Description**:
Some functions are missing @notice/@dev NatSpec comments for the function, @param for all/some of their parameters and @return for return values. Given that NatSpec is an important part of code documentation, this affects code comprehension, auditability and usability.

**Recommendation**:
Consider adding in full NatSpec comments for all functions to have complete code documentation for future use.


## [NAZ-N9] Floating Pragma
**Severity**: Informational
**Context**: [`All Contracts`](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts)

**Description**:
Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

**Recommendation**: 
Consider locking the pragma version.


## [NAZ-N10] Older Version Pragma
**Severity**: Informational
**Context**: [`All Contracts`](https://github.com/code-423n4/2022-12-tigris/tree/main/contracts)

**Description**:
Using very old versions of Solidity prevents benefits of bug fixes and newer security checks. Using the latest versions might make contracts susceptible to undiscovered compiler bugs. 

**Recommendation**:
Consider using the most recent version.
