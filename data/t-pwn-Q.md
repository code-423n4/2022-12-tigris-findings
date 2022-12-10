Lack of zero address check at setBaseURI() functionality

File: https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L46-L48

```
    function setBaseURI(string calldata _newBaseURI) external onlyOwner {
        baseURI = _newBaseURI;
    }
```