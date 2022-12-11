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