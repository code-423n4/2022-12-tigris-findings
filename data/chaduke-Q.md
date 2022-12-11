QA1. https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L583
WE need to check that ``_timestamp`` is in the future:
``` 
require(_timestamp > block.timestamp, "_timestamp must be in the future.");
```

QA2. https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L583
We need to check that ``_proxy`` is not zero address, otherwise, money will be lost to the zero address forever. 

QA3. https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L859
Need to check whether _delay == 0 since blockDelayPassed[_id] might not exist.