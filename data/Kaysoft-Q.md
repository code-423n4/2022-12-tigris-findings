#### [NC-1] USE locked solidity pragma version.
- Location: All files
<https://swcregistry.io/docs/SWC-103>

#### [NC-2] use Solidity latest stable version 0.8.17 which makes overflow checks on multiplication more efficient. Prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call.
- Location: All files

#### [NC-3] Emit events for state changing functions 
- [BondNFT.sol#L357](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L357)
- [BondNFT.sol#L349](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L349)
- [BondNFT.sol#L366](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L366)

#### [NC-4] Use NatSpec comments for functions
- [BondNFT.sol#L349](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L349)

#### [L-1] Avoid the use of <address>.transfer because it will fail with Gnosis Safe wallets due to the fact that Gnosis Safe fallback function consume more than the 2300 gas provided by <address>.transfer. use <address>.call instead. 
<https://consensys.net/diligence/blog/2019/09/stop-using-soliditys-transfer-now/>

- [Trading.sol#L588](https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L588)

