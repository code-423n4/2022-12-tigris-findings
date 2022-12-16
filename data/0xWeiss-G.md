### CHEAPER SUM

### FINDING
In line: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L152
it is cheaper to update the varible like this:
amount = amount + _claimAmount;   instead of    amount += _claimAmount;


### FOR LOOP OPTIMIZARION

### FINDING
In line: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L220
It is cheaper to update the ++i this way than i++  
Also, if it is preferred, it can also be added unchecked