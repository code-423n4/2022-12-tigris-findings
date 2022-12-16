# Lines of code
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L588

# Vulnerability details
transfer() function possible gas reprice in future

## Impact
2300 gas subsidy might lead to breaking the contract when opcode gas reprice occurs and stop it from processing basic functionality.

## Proof of Concept
https://docs.soliditylang.org/en/v0.8.17/security-considerations.html#sending-and-receiving-ether - Solidity's documentation
