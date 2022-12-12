# GovNFT.sol
## vulnerable function - _mint()


line#50 to line#57 (https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L50-L57)
```
    function _mint(address to, uint256 tokenId) internal override {
        require(counter <= MAX, "Exceeds supply");
        counter += 1;
        for (uint i=0; i<assetsLength(); i++) {
            userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];
        }
        super._mint(to, tokenId);
    }
```

It seems that the smart contract does not check the ```tokenId``` in _mint function. If the attacker insert the invalid token ID, the function will keep process to mint a new token. Attack may also create fake token to perform other attack.

### Solution 
It is suggested to implement ```require``` to check the validation of tokenId