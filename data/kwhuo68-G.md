G-1:
Can have struct packing in order to use less gas. For example, in Bond struct https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L12-L24 owner and asset can be moved to the end so that one less slot is used (each address is 20 bytes, and bool is 1 byte). Can pack it further by making mintTime a uint32 or uint64 (which is reasonable since it is tracking a block.timestamp). 

Recommendation: Pack structs a bit tighter as desired, look for opportunities where addresses, booleans, and non-uint256 can be rearranged to use less slots.  

G-2: 
Can combine multiple mappings into a simple mapping with a struct. For example, in Position, there are various mappings of uint256 => mapping of address => uint256, for example longOi: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L37 and the same for shortOi and lastUpdate. All of these can be combined into a single mapping with a new Struct that tracks longOi, shortOi, lastUpdate. The fields are accessed in the same main functions trades() and updateFunding() so there are further gas savings there. 

Recommendation: Check cases where there are multiple mappings and see if any can be consolidated using structs. 

G-3:
For functions with modifiers such as onlyOwner, onlyManager, onlyProtocol, etc. the function reverts when non-privileged users call the function. Marking such functions as payable leads to lower gas since the compiler does not check whether payment was provided. 

Examples in BondNFT:
-addAsset() is onlyOwner: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L349 
-setAllowedAsset() is onlyOwner: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L357 
-setBaseURI is onlyOwner: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L362  

Also many other examples in GovNFT, PairsContract, StableVault, Trading, TradingExtension, etc. 

Recommendation: Mark privileged functions as payable where desired. 

G-4:
Can use a = a + b instead of a += b for state variables. 

This occurs in many contracts, for example, in BondNFT:
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L68 

Recommendation: Swap out += for = and +. 
