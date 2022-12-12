QA1. https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L583
WE need to check that ``_timestamp`` is in the future:
``` 
require(_timestamp > block.timestamp, "_timestamp must be in the future.");
```

QA2. https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L583
We need to check that ``_proxy`` is not zero address, otherwise, money will be lost to the zero address forever. 

QA3. https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L859
Need to check whether _delay == 0 since blockDelayPassed[_id] might not exist.

QA4: https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Referrals.sol#L32-L41
The function ``setReferred`` fails to revert in failures cases, the function simply returns. We need to revise it as follows:
```
function setReferred(address _referredTrader, bytes32 _hash) external onlyProtocol {
        if (_referred[_referredTrader] != bytes32(0)) {
            revert("Error: referred cannot be set");
        }
        if (_referredTrader == _referral[_hash]) {
            revert("Error: referred cannot be set");
        }
        _referred[_referredTrader] = _hash;
        emit Referred(_referredTrader, _hash);
    }
```
QA5: It is advised to lock all contracts to the most recent version 0.8.17.

QA6: https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Referrals.sol#L60-L76
It is important to check the following two constraints
```
require(_codeOwners.length == _ownedCodes.length), "array lengths not equal.");

require(_referredA.length == _referredTo.length), "array lengths not equal.");

```

QA7. https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Referrals.sol#L32
Function ``setReferred`` fails to check if ``_hash`` is a valid one or not.
```
   require(_referral(_hash) != 0, "Invalid _hash");
```

QA8: https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Referrals.sol#L60-L76
The ``initRefs`` function fails to check the consistency of _codeOwners (no duplicate), and the validity of referrals (each _referredTo is a valid one). The correct implementation is as follows: 

```
function initRefs(
        address[] memory _codeOwners,
        bytes32[] memory _ownedCodes,
        address[] memory _referredA,
        bytes32[] memory _referredTo
    ) external onlyOwner {
        require(!isInit);
        require(_codeOwners.length == _ownedCodes.length), "array lengths not equal."); // @aduit
        require(_referredA.length == _referredTo.length), "array lengths not equal."); // @audit


        isInit = true;
        uint _codeOwnersL = _codeOwners.length;
        uint _referredAL = _referredA.length;
        for (uint i=0; i<_codeOwnersL; i++) {
             require(_referrral[_ownedCodes[i]] == 0, "referral already set");  // @audit
            _referral[_ownedCodes[i]] = _codeOwners[i];
        }
        for (uint i=0; i<_referredAL; i++) {
             require(_referred[_referredTo[i]] != 0, "Invalid referral"); // @audit
             require(_referral[_referredTo[i]] != [_referredA[i], "cannot refer yourself"); // @audit
            _referred[_referredA[i]] = _referredTo[i];
        }
    }
```

QA9: https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/TradingExtension.sol#L190-L202
Fail to revert when the arguments are invalid, the correct implementation is:
```
function _setReferral(
        bytes32 _referral,
        address _trader
    ) external onlyProtocol {
        
        if (_referral != bytes32(0) && 
             referrals.getReferral(_referral) != address(0) &&
              referrals.getReferred(_trader) == bytes32(0)) {
                    referrals.setReferred(_trader, _referral);
        }
 
        Revert("failing to set referred.");   
    }

```

