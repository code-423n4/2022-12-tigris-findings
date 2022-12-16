### [L01] Protocol wont work with all Chains.
**Description:**
Some chains such as Aurora (chain id is 1,313,161,554) have chain id's that are larger than uint16 and aren't compatible with tigris. 

**Location:**
[GovNFT.sol#L114](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L114) 
[GovNFT.sol#L124-L129](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L124-L129) 
[GovNFT.sol#L168-L173](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L168-L173) 
[GovNFT.sol#L183](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L183) 
[GovNFT.sol#L189](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L189) 
[GovNFT.sol#L206](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L206) 
[GovNFT.sol#L219-L225](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L219-L225) 

**Recommendation:**
If you wish to be able to be compatible with all chains change all the chainId variables from uint16 to uint256.

### [L02] Adding certain tokens can DoS claimGovFees 
**Description:**
If either a non standard token (such as USDT which will revert due to the transfer in GovNFT.claim) or an invalid/address(0) token is accidently added then the claimGovFees function will be revert whenever it is called. If this occurs there is  currently no way to remove the token causing the issues from the assets array. 

**Location:**
[GovNFT.sol#L275-L280](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L275-L280)
[BondNFT.sol#L30](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L30) 
[BondNFT.sol#L207-L223](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L207-L223) 
[Lock.sol#L110-L120](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Lock.sol#L110-L120) 

**Recommendation:**
Either add a way to remove items from the BondNFT.assets array or thoroughly vet/test all assets before adding them to the array.

### [L03] Unlocked Pragma
**Description:**
Non Library/Interface contracts should be deployed with the pragma locked to the version that they were tested against in development. 

**Location:**
All contracts.

**Recommendation:**
Lock the pragma to 0.8.0.