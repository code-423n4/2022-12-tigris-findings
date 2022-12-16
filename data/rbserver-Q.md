## [L-01] NO UPPER LIMIT ON NUMBER OF REWARD ASSET TOKENS IN `GovNFT` CONTRACT
When calling the following `GovNFT._mint`, `GovNFT._bridgeMint`, `GovNFT._burn`, and `GovNFT._transfer` functions, the added reward asset tokens are iterated over. After many reward asset tokens are added, `assetsLength()` can become quite large, and the gas cost for calling these functions can be hugely increased. When the number of reward asset tokens becomes large enough, it is possible that the block gas limit is eventually reached, which causes these function calls to revert, and the users can no longer use these functionalities for the Governance NFTs. To prevent the potential DOS issue, please consider enforcing a maximum number of reward asset tokens that can be added in the `GovNFT` contract.

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L50-L57
```solidity
    function _mint(address to, uint256 tokenId) internal override {
        ...
        for (uint i=0; i<assetsLength(); i++) {
            userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];
        }
        ...
    }
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L64-L71
```solidity
    function _bridgeMint(address to, uint256 tokenId) public {
        ...
        for (uint i=0; i<assetsLength(); i++) {
            userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];
        }
        ...
    }
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L76-L84
```solidity
    function _burn(uint256 tokenId) internal override {
        ...
        for (uint i=0; i<assetsLength(); i++) {
            userDebt[owner][assets[i]] += accRewardsPerNFT[assets[i]];
            userDebt[owner][assets[i]] -= userPaid[owner][assets[i]]/balanceOf(owner);
            userPaid[owner][assets[i]] -= userPaid[owner][assets[i]]/balanceOf(owner);            
        }
        ...
    }
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L89-L102
```solidity
    function _transfer(
        address from,
        address to,
        uint256 tokenId
    ) internal override {
        ...
        for (uint i=0; i<assetsLength(); i++) {
            userDebt[from][assets[i]] += accRewardsPerNFT[assets[i]];
            userDebt[from][assets[i]] -= userPaid[from][assets[i]]/balanceOf(from);
            userPaid[from][assets[i]] -= userPaid[from][assets[i]]/balanceOf(from);
            userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];
        }
        ...
    }
```

## [L-02] `super._safeMint` FUNCTION CAN BE CALLED INSTEAD OF `super._mint` FUNCTION FOR MINTING GOVERNANCE NFTS
Calling the following `GovNFT._mint` and `GovNFT._bridgeMint` functions will mint the Governance NFT for `tokenId` to the `to` address. Both functions call the `super._mint` function. If the `to` address corresponds to a contract, calling the `super._mint` function does not check if the receiving contract supports the ERC721 protocol; if not supported, the minted NFT can be locked and cannot be retrieved. To make sure that the receiving contract supports the ERC721 protocol, please consider calling the `super._safeMint` function instead of the `super._mint` function in the `GovNFT._mint` and `GovNFT._bridgeMint` functions.

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L50-L57
```solidity
    function _mint(address to, uint256 tokenId) internal override {
        ...
        super._mint(to, tokenId);
    }
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L64-L71
```solidity
    function _bridgeMint(address to, uint256 tokenId) public {
        ...
        super._mint(to, tokenId);
    }
```

## [L-03] `super._safeTransfer` FUNCTION CAN BE CALLED INSTEAD OF `super._transfer` FUNCTION FOR TRANSFERRING GOVERNANCE NFTS
When the following `GovNFT._transfer` function is called, the `super._transfer` function is called to transfer the Governance NFT for `tokenId` from the `from` address to the `to` address. When the `to` address corresponds to a contract, calling the `super._transfer` function does not check if the receiving contract supports the ERC721 protocol; if not supported, the transferred NFT can be locked and cannot be retrieved. To ensure that the receiving contract supports the ERC721 protocol, please consider calling the `super._safeTransfer` function instead of the `super._transfer` function in the `GovNFT._transfer` function.

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L89-L102
```solidity
    function _transfer(
        address from,
        address to,
        uint256 tokenId
    ) internal override {
        ...
        super._transfer(from, to, tokenId);
    }
```

## [L-04] USDT IS NOT SUPPORTED FOR CALLING `Trading._handleDeposit` FUNCTION ON ETHEREUM MAINNET
As shown by https://etherscan.io/token/0xdac17f958d2ee523a2206206994597c13d831ec7#code#L205, USDT on the Ethereum mainnet does not allow approving a new amount when the existing approved amount is not zero. On the Ethereum mainnet, when calling the following `Trading._handleDeposit` function with USDT being the margin asset, executing `IStableVault(_stableVault).deposit(_marginAsset, _margin/_marginDecMultiplier)` will not use all of the approved `type(uint).max` amount so executing `IERC20(_marginAsset).approve(_stableVault, type(uint).max)` can revert when calling this function for the second time. In the future, if the protocol wants to support USDT as a margin asset on the Ethereum mainnet, `IERC20(_marginAsset).approve(_stableVault, 0);` can be added before the line of `IERC20(_marginAsset).approve(_stableVault, type(uint).max);` in the `Trading._handleDeposit` function.

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L643-L659
```solidity
    function _handleDeposit(address _tigAsset, address _marginAsset, uint256 _margin, address _stableVault, ERC20PermitData calldata _permitData, address _trader) internal {
        IStable tigAsset = IStable(_tigAsset);
        if (_tigAsset != _marginAsset) {
            ...
            IERC20(_marginAsset).transferFrom(_trader, address(this), _margin/_marginDecMultiplier);
            IERC20(_marginAsset).approve(_stableVault, type(uint).max);
            IStableVault(_stableVault).deposit(_marginAsset, _margin/_marginDecMultiplier);
            ...
        } else {
            ...
        }        
    }
```




## [L-05] MARGIN ASSET TOKENS WITH MORE THAN 18 DECIMALS ARE NOT SUPPORTED
As shown below, arithmetic operations of the `StableVault.deposit`, `StableVault.withdraw`, `Trading._handleDeposit`, and `Trading._handleWithdraw` functions that subtract the margin asset tokens' decimals will underflow if these decimals are more than 18; in this case, calling these functions will revert. This means that the protocol cannot scale to support margin asset tokens that have more than 18 decimals in the future. To prevent the described issue, please consider updating these functions' arithmetic operations to use divisions that divide `10**18` by `10**n`, where `n` is the corresponding token decimals instead of subtracting such token decimals from 18.

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L44-L51
```solidity
    function deposit(address _token, uint256 _amount) public {
        ...
        IERC20Mintable(stable).mintFor(
            _msgSender(),
            _amount*(10**(18-IERC20Mintable(_token).decimals()))
        );
    }
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L65-L72
```solidity
    function withdraw(address _token, uint256 _amount) external returns (uint256 _output) {
        ...
        _output = _amount/10**(18-IERC20Mintable(_token).decimals());
        ...
    }
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L643-L659
```solidity
    function _handleDeposit(address _tigAsset, address _marginAsset, uint256 _margin, address _stableVault, ERC20PermitData calldata _permitData, address _trader) internal {
        ...
        if (_tigAsset != _marginAsset) {
            ...
            uint _marginDecMultiplier = 10**(18-ExtendedIERC20(_marginAsset).decimals());
            ...
        } else {
            ...
        }        
    }
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L668-L678
```solidity
    function _handleWithdraw(IPosition.Trade memory _trade, address _stableVault, address _outputToken, uint _toMint) internal {
        ...
        if (_outputToken == _trade.tigAsset) {
            ...
        } else {
            ...
            if (IERC20(_outputToken).balanceOf(address(this)) != _balBefore + _toMint/(10**(18-ExtendedIERC20(_outputToken).decimals()))) revert BadWithdraw();
            ...
        }        
    }
```

## [L-06] UNSAFE `decimals()` CALL FOR MARGIN ASSET TOKENS THAT DO NOT IMPLEMENT `decimals()`
The following `StableVault.deposit`, `StableVault.withdraw`, `Trading._handleDeposit`, and `Trading._handleWithdraw` functions call `decimals()` to get the decimals of the corresponding margin asset tokens. However, according to https://eips.ethereum.org/EIPS/eip-20,  `decimals()` is optional so it is possible that some margin asset tokens do not implement `decimals()`. For such tokens, calling these functions will revert. To scale the protocol for supporting margin asset tokens that do not implement `decimals()` in the future, helper functions like BoringCrypto's [`safeDecimals`](https://github.com/boringcrypto/BoringSolidity/blob/ccb743d4c3363ca37491b87c6c9b24b1f5fa25dc/contracts/libraries/BoringERC20.sol#L52-L55) can be used instead of directly calling `decimals()` in these functions.

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L44-L51
```solidity
    function deposit(address _token, uint256 _amount) public {
        ...
        IERC20Mintable(stable).mintFor(
            _msgSender(),
            _amount*(10**(18-IERC20Mintable(_token).decimals()))
        );
    }
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L65-L72
```solidity
    function withdraw(address _token, uint256 _amount) external returns (uint256 _output) {
        ...
        _output = _amount/10**(18-IERC20Mintable(_token).decimals());
        ...
    }
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L643-L659
```solidity
    function _handleDeposit(address _tigAsset, address _marginAsset, uint256 _margin, address _stableVault, ERC20PermitData calldata _permitData, address _trader) internal {
        ...
        if (_tigAsset != _marginAsset) {
            ...
            uint _marginDecMultiplier = 10**(18-ExtendedIERC20(_marginAsset).decimals());
            ...
        } else {
            ...
        }        
    }
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L668-L678
```solidity
    function _handleWithdraw(IPosition.Trade memory _trade, address _stableVault, address _outputToken, uint _toMint) internal {
        ...
        if (_outputToken == _trade.tigAsset) {
            ...
        } else {
            ...
            if (IERC20(_outputToken).balanceOf(address(this)) != _balBefore + _toMint/(10**(18-ExtendedIERC20(_outputToken).decimals()))) revert BadWithdraw();
            ...
        }        
    }
```