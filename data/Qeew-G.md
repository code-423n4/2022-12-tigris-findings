1. Don't use floating Pragma

In the contracts, floating pragmas should not be used. Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly

Proof of concept

All contract affected

https://swcregistry.io/docs/SWC-103

Mitigation: Lock the pragma version: delete pragma solidity ^0.8.0  and replace with pragma solidity 0.8.0

2. Update to solc-0.8.10+

Solidity 0.8.10 has a useful change with reduced gas costs of external calls which expect a return value: https://blog.soliditylang.org/2021/11/09/solidity-0.8.10-release-announcement/

Mitigation: The current uses pragma solidity ^0.8.0  should be upgraded to benefit Gas costs

3. Using double require instead of && operations

using double require instead of operator && can save more gas

Proof of concept

https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L497
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L499
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L501


Mitigation steps

example:

Using &&:

function check(uint amount)public view{
require(amount == 0 && x < 1 );
}

// With Gas cost of 21630

Using double require:

require(amount == 0 );
require( amount < 1);
}
}
// With Gas of 21622



4. Set function to external instead of public

The following functions could be set external to save gas and improve code quality. External call cost is less expensive than of public functions.

Proof of Concept

https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Lock.sol#L65
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L315



5. Cache Array Length

Cache Array Length to save Gas

Proof of Concept

Lock.sol:113: for (uint i=0; i < assets.length; i++) {
GovNFT.sol:131:  for (uint i=0; i<tokenId.length; i++) {
GovNFT.sol:246: for (uint i=0; i<_ids.length; i++) {
Position.sol:296: for (uint i=0; i<_ids.length; i++) {


Mitigation: Store the array's length in a variable before using it in the for-loop


6. Remove = 0, every unassigned variable has default value 0

Proof of concept

Lock.sol:113: for (uint i=0; i < assets.length; i++) {
GovNFT.sol:131: for (uint i=0; i<tokenId.length; i++) {
GovNFT.sol:246: for (uint i=0; i<_ids.length; i++) {
Position.sol:296: for (uint i=0; i<_ids.length; i++) {

Mitigation: remove =0 to save Gas



7. Use calldata in function  instead of memory to save Gas

Proof of concept

https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/TradingExtension.sol#L65
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/TradingExtension.sol#L95
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/TradingExtension.sol#L213
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Lock.sol#L139
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Lock.sol#L111
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L294

