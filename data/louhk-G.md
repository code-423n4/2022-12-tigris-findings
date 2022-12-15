The vulnerability found in ```GovNFT.sol```

The contract is using vulnerable function - _mint()


The vulnerable code between line#50 to line#57 (https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L50-L57)
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

---

The potential vulnerabilities can be found in below contract between line 48 to line 55

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFTBridged.sol#L48to#L55
```
    function _bridgeMint(address to, uint256 tokenId) public {
        require(msg.sender == address(this) || _msgSender() == owner(), "NotBridge");
        require(tokenId <= 10000 && tokenId != 0, "BadID");
        for (uint i=0; i<assetsLength(); i++) {
            userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];
        }
        super._mint(to, tokenId);
    }

```

In Line#49, the require() function based on owner() and the Line#50, the require() function is for checking tokenId value <= 10000 and not equal to zero.
In Line#49, the require() function didn't check the tokenId value. The attacker may send a transaction with a large amount value. Since Line#51 to Line#53 is a for loop function, it may cause a big amount of gas fee or other problems.

The attacker may call the function like this:
```
 _bridgeMint(attacker, 1000000);
```

Solution:
For Line#49
```
require(msg.sender == address(this) || _msgSender() == owner() && tokenId >= 1 && tokenId <= 10000, "NotBridge");
```

For Line#50
Ensure tokenId is  greater than or equal to 1 and less than or equal to 10000
```
require(tokenId >= 1 && tokenId <= 10000 && tokenId != 0, "BadID");
```