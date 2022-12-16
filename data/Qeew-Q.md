
Issues: Unbounded iteration over all indexes

Impact: If the array becomes too large, the transactions associated with this function may fail due to exceeding the block gas limit. This would prevent the desired functionality from being executed, resulting in a denial of service and potentially breaking core functionality.

Proof of concept: 
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Lock.sol#L113
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L67


Tools
VSCODE

Mitigation

Keep the Array size small
