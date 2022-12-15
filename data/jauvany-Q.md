Vulnerability Number 1

Most of the contracts use an unlocked pragma (e.g., pragma solidity ^0.8.0) which is not fixed to a specific Solidity version. Locking the pragma helps ensure that contracts do not accidentally get deployed using a different compiler version with which they have been tested the most.

Proof of Concept
Referenced code:
Please use grep -R pragma . to find the unlocked pragma statements.

Recommended Mitigation Steps

Lock pragmas to a specific Solidity version. Consider the compiler bugs in the following lists and ensure the contracts are not affected by them. It is also recommended to use the latest version of Solidity when deploying contracts (see [Solidity docs](https://docs.soliditylang.org/en/v0.8.6/)).

Solidity compiler bugs:
[Solidity repo - known bugs](https://github.com/ethereum/solidity/blob/develop/docs/bugs.json).
[Solidity repo - bugs by version](https://github.com/ethereum/solidity/blob/develop/docs/bugs_by_version.json).



Vulnerability number 2

Using memory array parameters (e.g. uint[] memory) as function parameters can be tricky in Solidity, because an attack is possible with a very large array which will overlap with other parts of the memory. See proof of concept below.


Proof of Concept

The following functions use a [] memory parameter.

/contracts/Lock.sol: function sendNFTs( uint[] memory _ids ) external onlyOwner() {
/contracts/Referrals.sol: function initRefs(address[] memory _codeOwners, bytes32[] memory _ownedCodes, address[] memory _referredA, bytes32[] memory _referredTo) external onlyOwner {

Tools Used
Editor

Recommended Mitigation Steps
Add checks on the size of the array parameters to make sure they are not absurdly long.

