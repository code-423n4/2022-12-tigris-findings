## SINGLE-STEP PROCESS FOR CRITICAL OWNERSHIP TRANSFER
In the Tigris protocol the ownership is handled via the Openzeppelin Ownable function. However, this implements a single-step ownership change. The impact is that, if an incorrect address (for example and address for which the private key is not known) is used accidentally, then it blocks several functionalities of the protocol. You could see [See similar High Risk severity finding](https://github.com/trailofbits/publications/blob/master/reviews/hermez.pdf) from Trail-of-Bits Audit of Hermez and similar [Medium Risk severity finding](https://github.com/Uniswap/uniswap-v3-core/blob/main/audits/tob/audit.pdf) from Trail-of-Bits Audit of Uniswap V3.

### Recommendation
It is recommended to implement a 2-step function in order to `transferOwnership` to a new owner. The first function will approve a new address as the `pendingOwner`. The second function, which is only callable by the `pendingOwner` will claim the pending Owner and will transfer the full ownership to the `pendingOwner`. Recently, Openzeppelin has created a new contract, [Ownable2Step.sol](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable2Step.sol), that implements a 2-step ownership transfer.

## Limit on the maximum number of bytes to copy to memory may force the `lzReceive` function reverting
In the [lzReceive](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L168) function a call is made to the `nonblockingLzReceiver` function by using the [`ExcessivelySafeCall` library](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L175) to handle the unsafe call. However, the third argument (`_maxCopy`), which represents the maximum number of bytes of returndata to copy to memory, is 150. This could lead to the call reverting if many NFTs are to be bridged to a different chain.

## Incompatibility with fee-on transfer tokens
There are ERC20 tokens that charge fee for every `transfer()` or `transferFrom()`.
In the current implementation, when the `transfer()` or `transferFrom` is used, no check is made to ensure that the amount transferred is the initial amount requested. This could lead to some issues if the token that is whitelisted in the `StableVault` is a fee-on transfer token.

### Recommendation
Consider comparing before and after balance to get the actual transferred amount.
