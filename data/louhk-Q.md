# Faucet.sol
### Tx Origin Attacks
In line#19 the contract written as
```
require(msg.sender == tx.origin, "Is Contract");
```

Attacker may perform phishing attack through the below function
```
function TxOriginAttacker() public {
  owner = msg.sender;
}
```

It is suggested changing it to
```
require(msg.sender == owner);
``` 

## Solutions
Never use tx.origin to check for authorisation of ownership, instead use msg.sender

### Reference
https://medium.com/coinmonks/solidity-tx-origin-attacks-58211ad95514
https://hackernoon.com/hacking-solidity-contracts-using-txorigin-for-authorization-are-vulnerable-to-phishing