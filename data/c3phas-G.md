## FINDINGS
NB: Some functions have been truncated where neccessary to just show affected parts of the code
Throught the report some places might be denoted with audit tags to show the actual place affected.

## Using immutable on variables that are only set in the constructor and never after (2.1k  gas per var)
Use immutable if you want to assign a permanent value at construction. Use constants if you already know the permanent value. Both get directly embedded in bytecode, saving SLOAD.
Variables only set in the constructor and never edited afterwards should be marked as immutable, as it would avoid the expensive storage-writing operation in the constructor (around 20 000 gas per variable) and replace the expensive storage-reading operations (around 2100 gas per reading) to a less expensive value reading (3 gas)

*Total instaces: 7* 
`gas savings 7 * 2.1k = 14.7k gas`

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Trading.sol#L121-L123
```solidity
File:/contracts/Trading.sol
121:    IPairsContract private pairsContract;
122:    IPosition private position;
123:    IGovNFT private gov;
```

```diff
diff --git a/contracts/Trading.sol b/contracts/Trading.sol
index 5a8ec2f..68c9d76 100644
--- a/contracts/Trading.sol
+++ b/contracts/Trading.sol
@@ -118,9 +118,9 @@ contract Trading is MetaContext, ITrading {
     uint public maxWinPercent;
     uint public vaultFundingPercent;

-    IPairsContract private pairsContract;
-    IPosition private position;
-    IGovNFT private gov;
+    IPairsContract private immutable pairsContract;
+    IPosition private immutable position;
+    IGovNFT private immutable gov;
```

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/TradingExtension.sol#L13
```solidity
File:/contracts/TradingExtension.sol
13:    address public trading;
```

```diff
diff --git a/contracts/TradingExtension.sol b/contracts/TradingExtension.sol
index 0b0d72a..3dc2b19 100644
--- a/contracts/TradingExtension.sol
+++ b/contracts/TradingExtension.sol
@@ -10,7 +10,7 @@ import "./interfaces/IPosition.sol";
 contract TradingExtension is Ownable{
     uint constant private DIVISION_CONSTANT = 1e10; // 100%

-    address public trading;
+    address public immutable trading;
```

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/TradingExtension.sol#L22-L24
```solidity
File: /contracts/TradingExtension.sol
22:    IPairsContract private pairsContract;
23:    IReferrals private referrals;
24:    IPosition private position;
```

```diff
diff --git a/contracts/TradingExtension.sol b/contracts/TradingExtension.sol
index 0b0d72a..4d544ba 100644
--- a/contracts/TradingExtension.sol
+++ b/contracts/TradingExtension.sol
@@ -19,9 +19,9 @@ contract TradingExtension is Ownable{
     mapping(address => bool) public allowedMargin;
     bool public paused;

-    IPairsContract private pairsContract;
-    IReferrals private referrals;
-    IPosition private position;
+    IPairsContract private immutable pairsContract;
+    IReferrals private immutable referrals;
+    IPosition private immutable position;

```

## Tighly pack storage variables/optimize the order of variable declaration(Gas savings : `1 * 2k = 2k gas`)

Here, the storage variables can be tightly packed from 9 SLOTS to 8 SLOTS

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/TradingExtension.sol#L13-L18
```solidity
File: /contracts/TradingExtension.sol

//@audit: Move bool public chainlinkEnabled next to address public trading
13:    address public trading;
14:    uint256 public validSignatureTimer;
15:    bool public chainlinkEnabled;

17:    mapping(address => bool) private isNode;
18:    mapping(address => uint) public minPositionSize;
```

```diff
diff --git a/contracts/TradingExtension.sol b/contracts/TradingExtension.sol
index 0b0d72a..2a46c29 100644
--- a/contracts/TradingExtension.sol
+++ b/contracts/TradingExtension.sol
@@ -11,8 +11,8 @@ contract TradingExtension is Ownable{
     uint constant private DIVISION_CONSTANT = 1e10; // 100%

     address public trading;
-    uint256 public validSignatureTimer;
     bool public chainlinkEnabled;
+    uint256 public validSignatureTimer;

     mapping(address => bool) private isNode;
     mapping(address => uint) public minPositionSize;
```

## Pack structs by putting data types that can be packed together next to each other.(Total SLOTS saved: 6 ) `~ 6 * 2k = 12K gas saved`

As the solidity EVM works with 32 bytes, variables less than 32 bytes should be packed inside a struct so that they can be stored in the same slot, this saves gas when writing to storage ~20000 gas

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/utils/TradingLibrary.sol#L12-L19
### Save 1 SLOT
```solidity
File: /contracts/utils/TradingLibrary.sol

//@audit: Pack bool isClosed next to address provider
12: struct PriceData {
13:    address provider;
14:    uint256 asset;
15:    uint256 price;
16:    uint256 spread;
17:    uint256 timestamp;
18:    bool isClosed;
19: }
```

```diff
diff --git a/contracts/utils/TradingLibrary.sol b/contracts/utils/TradingLibrary.sol
index c6f2995..c01558f 100644
--- a/contracts/utils/TradingLibrary.sol
+++ b/contracts/utils/TradingLibrary.sol
@@ -10,12 +10,12 @@ interface IPrice {
 }

 struct PriceData {
+    bool isClosed;
     address provider;
     uint256 asset;
     uint256 price;
     uint256 spread;
-    uint256 timestamp;
-    bool isClosed;
+    uint256 timestamp;
 }
```


https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L12-L24
### Save 1 SLOT
```solidity
File: /contracts/BondNFT.sol

//@audit: Move bool expired next to address owner as they can be packed together
12:    struct Bond {
13:        uint id;
14:        address owner;
15:        address asset;
16:        uint amount;
17:        uint mintEpoch;
18:        uint mintTime;
19:        uint expireEpoch;
20:        uint pending;
21:        uint shares;
22:        uint period;
23:        bool expired;
24:    }
```

```diff
diff --git a/contracts/BondNFT.sol b/contracts/BondNFT.sol
index 33a6e76..b35ec19 100644
--- a/contracts/BondNFT.sol
+++ b/contracts/BondNFT.sol
@@ -10,9 +10,10 @@ contract BondNFT is ERC721Enumerable, Ownable {
     uint constant private DAY = 24 * 60 * 60;

     struct Bond {
-        uint id;
+        bool expired;
         address owner;
         address asset;
+        uint id;
         uint amount;
         uint mintEpoch;
         uint mintTime;
@@ -20,7 +21,7 @@ contract BondNFT is ERC721Enumerable, Ownable {
         uint pending;
         uint shares;
         uint period;
-        bool expired;
     }
```

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/IPosition.sol#L7-L20
### Save 1 SLOT
```solidity
File:/contracts/interfaces/IPosition.sol
7:    struct Trade {
8:         uint margin;
9:         uint leverage;
10:        uint asset;
11:        bool direction;
12:        uint price;
13:        uint tpPrice;
14:        uint slPrice;
15:        uint orderType;
16:        address trader;
17:        uint id;
18:        address tigAsset;
19:        int accInterest;
20:    }
```

```diff
diff --git a/contracts/interfaces/IPosition.sol b/contracts/interfaces/IPosition.sol
index 46c5830..1cb6823 100644
--- a/contracts/interfaces/IPosition.sol
+++ b/contracts/interfaces/IPosition.sol
@@ -5,17 +5,17 @@ pragma solidity ^0.8.0;
 interface IPosition {

     struct Trade {
+        bool direction;
+        address trader;
+        address tigAsset;
         uint margin;
         uint leverage;
-        uint asset;
-        bool direction;
+        uint asset;
         uint price;
         uint tpPrice;
         uint slPrice;
-        uint orderType;
-        address trader;
+        uint orderType;
         uint id;
-        address tigAsset;
         int accInterest;
     }
```

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/IPosition.sol#L22-L33
### Save 1 SLOT
```solidity
File: /contracts/interfaces/IPosition.sol
22:    struct MintTrade {
23:        address account;
24:        uint256 margin;
25:        uint256 leverage;
26:        uint256 asset;
27:        bool direction;
28:        uint256 price;
29:        uint256 tp;
30:        uint256 sl;
31:        uint256 orderType;
32:        address tigAsset;
33:    }
```

```diff
diff --git a/contracts/interfaces/IPosition.sol b/contracts/interfaces/IPosition.sol
index 46c5830..b76786b 100644
--- a/contracts/interfaces/IPosition.sol
+++ b/contracts/interfaces/IPosition.sol
@@ -20,11 +20,11 @@ interface IPosition {
     }

     struct MintTrade {
+        bool direction;
         address account;
         uint256 margin;
         uint256 leverage;
         uint256 asset;
-        bool direction;
         uint256 price;
         uint256 tp;
         uint256 sl;
```

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/ITrading.sol#L9-L19
### Save 1 SLOT
```solidity
File: /contracts/interfaces/ITrading.sol
9:    struct TradeInfo {
10:        uint256 margin;
11:        address marginAsset;
12:        address stableVault;
13:        uint256 leverage;
14:        uint256 asset;
15:        bool direction;
16:        uint256 tpPrice;
17:        uint256 slPrice;
18:        bytes32 referral;
19:    }
```

```diff
diff --git a/contracts/interfaces/ITrading.sol b/contracts/interfaces/ITrading.sol
index 43ac6d2..0b9e074 100644
--- a/contracts/interfaces/ITrading.sol
+++ b/contracts/interfaces/ITrading.sol
@@ -7,12 +7,12 @@ pragma solidity ^0.8.0;
 interface ITrading {

     struct TradeInfo {
-        uint256 margin;
+        bool direction;
         address marginAsset;
         address stableVault;
+        uint256 margin;
         uint256 leverage;
-        uint256 asset;
-        bool direction;
+        uint256 asset;
         uint256 tpPrice;
         uint256 slPrice;
         bytes32 referral;
```

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/interfaces/ITrading.sol#L21-L28
### Save 1 SLOT
```solidity
File:/contracts/interfaces/ITrading.sol
21:    struct ERC20PermitData {
22:        uint256 deadline;
23:        uint256 amount;
24:        uint8 v;
25:        bytes32 r;
26:        bytes32 s;
27:        bool usePermit;
28:    }
```

```diff
diff --git a/contracts/interfaces/ITrading.sol b/contracts/interfaces/ITrading.sol
index 43ac6d2..b889333 100644
--- a/contracts/interfaces/ITrading.sol
+++ b/contracts/interfaces/ITrading.sol
@@ -22,9 +22,9 @@ interface ITrading {
         uint256 deadline;
         uint256 amount;
         uint8 v;
-        bytes32 r;
-        bytes32 s;
         bool usePermit;
+        bytes32 r;
+        bytes32 s;
     }
```

## The result of a function call should be cached rather than re-calling the function
Consider caching the following:

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L50-L57
### GovNFT.sol.\_mint(): assetsLength() should be cached - don't call it inside the for loop(too expensive)
```solidity
File: /contracts/GovNFT.sol
50:    function _mint(address to, uint256 tokenId) internal override {
51:        require(counter <= MAX, "Exceeds supply");
52:        counter += 1;
53:        for (uint i=0; i<assetsLength(); i++) {
54:            userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];
55:        }
56:        super._mint(to, tokenId);
57:    }
```

```diff
diff --git a/contracts/GovNFT.sol b/contracts/GovNFT.sol
index 05439ef..6bc5671 100644
--- a/contracts/GovNFT.sol
+++ b/contracts/GovNFT.sol
@@ -50,7 +50,8 @@ contract GovNFT is ERC721Enumerable, ILayerZeroReceiver, MetaContext, IGovNFT {
     function _mint(address to, uint256 tokenId) internal override {
         require(counter <= MAX, "Exceeds supply");
         counter += 1;
-        for (uint i=0; i<assetsLength(); i++) {
+        uint256 _assetLength = assetsLength();
+        for (uint i=0; i<_assetLength; i++) {
             userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];
         }
         super._mint(to, tokenId);q
```

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L67-L69
### GovNFT.sol.\_bridgeMint(): assetsLength() should not called inside a loop
```solidity
File:/contracts/GovNFT.sol
67:        for (uint i=0; i<assetsLength(); i++) {
68:            userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];
69:        }
```

```diff
diff --git a/contracts/GovNFT.sol b/contracts/GovNFT.sol
index 05439ef..f682c9a 100644
--- a/contracts/GovNFT.sol
+++ b/contracts/GovNFT.sol
@@ -64,7 +64,8 @@ contract GovNFT is ERC721Enumerable, ILayerZeroReceiver, MetaContext, IGovNFT {
     function _bridgeMint(address to, uint256 tokenId) public {
         require(msg.sender == address(this) || _msgSender() == owner(), "NotBridge");
         require(tokenId <= 10000, "BadID");
-        for (uint i=0; i<assetsLength(); i++) {
+        uint256 _assetLength = assetsLength();
+        for (uint i=0; i<_assetLength; i++) {
             userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];
         }
```

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L78
### GovNFT.sol.\_burn(): assetsLength() should not called inside a loop
```solidity
File: /contracts/GovNFT.sol
78:        for (uint i=0; i<assetsLength(); i++) {
```

```diff
diff --git a/contracts/GovNFT.sol b/contracts/GovNFT.sol
index 05439ef..e46e23d 100644
--- a/contracts/GovNFT.sol
+++ b/contracts/GovNFT.sol
@@ -75,7 +75,8 @@ contract GovNFT is ERC721Enumerable, ILayerZeroReceiver, MetaContext, IGovNFT {
     */
     function _burn(uint256 tokenId) internal override {
         address owner = ownerOf(tokenId);
-        for (uint i=0; i<assetsLength(); i++) {
+        uint256 _assetLength = assetsLength();
+        for (uint i=0; i<_assetLength; i++) {
```

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L89-L102
### GovNFT.sol.\_transfer(): balanceOf(from) should be cached
```solidity
File:/contracts/GovNFT.sol
89:    function _transfer(

97:            userDebt[from][assets[i]] -= userPaid[from][assets[i]]/balanceOf(from); //@audit: called here
98:            userPaid[from][assets[i]] -= userPaid[from][assets[i]]/balanceOf(from); //@audit: called here

102:    }
```

```diff
diff --git a/contracts/GovNFT.sol b/contracts/GovNFT.sol
index 05439ef..0349f58 100644
--- a/contracts/GovNFT.sol
+++ b/contracts/GovNFT.sol
@@ -92,10 +92,11 @@ contract GovNFT is ERC721Enumerable, ILayerZeroReceiver, MetaContext, IGovNFT {
         uint256 tokenId
     ) internal override {
         require(ownerOf(tokenId) == from, "!Owner");
+        uint256 _balanceOf = balanceOf(from);
         for (uint i=0; i<assetsLength(); i++) {
             userDebt[from][assets[i]] += accRewardsPerNFT[assets[i]];
-            userDebt[from][assets[i]] -= userPaid[from][assets[i]]/balanceOf(from);
-            userPaid[from][assets[i]] -= userPaid[from][assets[i]]/balanceOf(from);
+            userDebt[from][assets[i]] -= userPaid[from][assets[i]]/_balanceOf;
+            userPaid[from][assets[i]] -= userPaid[from][assets[i]]/_balanceOf;
             userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];
         }
         super._transfer(from, to, tokenId);
```

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L104-L108
### GovNFT.sol.mintMany():\_msgSender() should be cached
```solidity
File: /contracts/GovNFT.sol
104:    function mintMany(uint _amount) external onlyOwner {
105:        for (uint i=0; i<_amount; i++) {
106:            _mint(_msgSender(), counter);
107:        }
108:    }
```

```diff
diff --git a/contracts/GovNFT.sol b/contracts/GovNFT.sol
index 05439ef..5ba9c14 100644
--- a/contracts/GovNFT.sol
+++ b/contracts/GovNFT.sol
@@ -102,8 +102,9 @@ contract GovNFT is ERC721Enumerable, ILayerZeroReceiver, MetaContext, IGovNFT {
     }

     function mintMany(uint _amount) external onlyOwner {
+        address _sender = _msgSender();
         for (uint i=0; i<_amount; i++) {
-            _mint(_msgSender(), counter);
+            _mint(_sender, counter);
         }
     }
```

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L131-L135
### GovNFT.sol: \_msgSender() should be cached outside the loop
```solidity
File: /contracts/GovNFT.sol
131:        for (uint i=0; i<tokenId.length; i++) {
132:            require(_msgSender() == ownerOf(tokenId[i]), "Not the owner");
133:            // burn NFT
134:            _burn(tokenId[i]);
135:        }
```

```diff
diff --git a/contracts/GovNFT.sol b/contracts/GovNFT.sol
index 05439ef..7ed3389 100644
--- a/contracts/GovNFT.sol
+++ b/contracts/GovNFT.sol
@@ -128,8 +128,9 @@ contract GovNFT is ERC721Enumerable, ILayerZeroReceiver, MetaContext, IGovNFT {
         uint256[] memory tokenId
     ) public payable {
         require(tokenId.length > 0, "Not bridging");
+        address _sender = _msgSender();
         for (uint i=0; i<tokenId.length; i++) {
-            require(_msgSender() == ownerOf(tokenId[i]), "Not the owner");
+            require(_sender == ownerOf(tokenId[i]), "Not the owner");
             // burn NFT
             _burn(tokenId[i]);
         }
```

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L287-L294
### GovNFT.sol.distribute(): totalSupply() should be cached
```solidity
File: /contracts/GovNFT.sol
287:    function distribute(address _tigAsset, uint _amount) external {
288:        if (assets.length == 0 || assets[assetsIndex[_tigAsset]] == address(0) || totalSupply() == 0 || !_allowedAsset[_tigAsset]) return;
289:        try IERC20(_tigAsset).transferFrom(_msgSender(), address(this), _amount) {
290:            accRewardsPerNFT[_tigAsset] += _amount/totalSupply();
291:        } catch {
292:            return;
293:        }
294:    }
```

```diff
@@ -285,9 +286,10 @@ contract GovNFT is ERC721Enumerable, ILayerZeroReceiver, MetaContext, IGovNFT {
     * @param _amount amount to be distributed
     */
     function distribute(address _tigAsset, uint _amount) external {
-        if (assets.length == 0 || assets[assetsIndex[_tigAsset]] == address(0) || totalSupply() == 0 || !_allowedAsset[_tigAsset]) return;
+        uint256 _totalSupply = totalSupply();
+        if (assets.length == 0 || assets[assetsIndex[_tigAsset]] == address(0) || _totalSupply == 0 || !_allowedAsset[_tigAsset]) return;
         try IERC20(_tigAsset).transferFrom(_msgSender(), address(this), _amount) {
-            accRewardsPerNFT[_tigAsset] += _amount/totalSupply();
+            accRewardsPerNFT[_tigAsset] += _amount/_totalSupply;
         } catch {
             return;
         }
```

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/StableVault.sol#L44-L51
### StableVault.sol.deposit(): \_msgSender() should be cached
```solidity
File:/contracts/StableVault.sol
44:    function deposit(address _token, uint256 _amount) public {
45:        require(allowed[_token], "Token not listed");
46:        IERC20(_token).transferFrom(_msgSender(), address(this), _amount);
47:        IERC20Mintable(stable).mintFor(
48:            _msgSender(),
49:            _amount*(10**(18-IERC20Mintable(_token).decimals()))
50:        );
51:    }
```

```diff
diff --git a/contracts/StableVault.sol b/contracts/StableVault.sol
index 373698a..d5c5be7 100644
--- a/contracts/StableVault.sol
+++ b/contracts/StableVault.sol
@@ -43,9 +43,10 @@ contract StableVault is MetaContext, IStableVault {
     */
     function deposit(address _token, uint256 _amount) public {
         require(allowed[_token], "Token not listed");
-        IERC20(_token).transferFrom(_msgSender(), address(this), _amount);
+        address _sender = _msgSender();
+        IERC20(_token).transferFrom(_sender, address(this), _amount);
         IERC20Mintable(stable).mintFor(
-            _msgSender(),
+            _sender,
             _amount*(10**(18-IERC20Mintable(_token).decimals()))
         );
     }
```

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/StableVault.sol#L65-L72
### StableVault.sol.withdraw(): \_msgSender() should be cached
```solidity
File: /contracts/StableVault.sol
65:    function withdraw(address _token, uint256 _amount) external returns (uint256 _output) {
66:        IERC20Mintable(stable).burnFrom(_msgSender(), _amount);
67:        _output = _amount/10**(18-IERC20Mintable(_token).decimals());
68:        IERC20(_token).transfer(
69:            _msgSender(),
70:            _output
71:        );
72:    }
```

```diff
diff --git a/contracts/StableVault.sol b/contracts/StableVault.sol
index 373698a..ebc160b 100644
--- a/contracts/StableVault.sol
+++ b/contracts/StableVault.sol
@@ -63,10 +63,11 @@ contract StableVault is MetaContext, IStableVault {
     * @param _amount amount of _token
     */
     function withdraw(address _token, uint256 _amount) external returns (uint256 _output) {
-        IERC20Mintable(stable).burnFrom(_msgSender(), _amount);
+        address _sender = _msgSender();
+        IERC20Mintable(stable).burnFrom(_sender, _amount);
         _output = _amount/10**(18-IERC20Mintable(_token).decimals());
         IERC20(_token).transfer(
-            _msgSender(),
+            _sender,
             _output
         );
     }
```

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Referrals.sol#L20-L24
### Referrals.sol.createReferralCode(): \_msgSender() should be cached
```solidity
File:/contracts/Referrals.sol
20:    function createReferralCode(bytes32 _hash) external {
21:        require(_referral[_hash] == address(0), "Referral code already exists");
22:        _referral[_hash] = _msgSender();
23:        emit ReferralCreated(_msgSender(), _hash);
24:    }
```

```diff
diff --git a/contracts/Referrals.sol b/contracts/Referrals.sol
index 6a4313e..c3ab952 100644
--- a/contracts/Referrals.sol
+++ b/contracts/Referrals.sol
@@ -19,8 +19,9 @@ contract Referrals is Ownable, IReferrals {
     */
     function createReferralCode(bytes32 _hash) external {
         require(_referral[_hash] == address(0), "Referral code already exists");
-        _referral[_hash] = _msgSender();
-        emit ReferralCreated(_msgSender(), _hash);
+        address _sender = _msgSender();
+        _referral[_hash] = _sender;
+        emit ReferralCreated(_sender, _hash);
     }

```

## Multiple accesses of a mapping/array should use a local variable cache

Caching a mapping's value in a local storage or calldata variable when the value is accessed multiple times saves ~42 gas per access due to not having to perform the same offset calculation every time.
**Help the Optimizer by saving a storage variable's reference instead of repeatedly fetching it**

To help the optimizer,declare a storage type variable and use it instead of repeatedly fetching the reference in a map or an array. 
As an example, instead of repeatedly calling ```someMap[someIndex]```, save its reference like this: ```SomeStruct storage someStruct = someMap[someIndex]``` and use it.

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L48-L65
### PairsContract.sol.addAsset(): \_idToAsset[\_asset] should be cached
```solidity
File: /contracts/PairsContract.sol
48:    function addAsset(uint256 _asset, string memory _name, address _chainlinkFeed, uint256 _minLeverage, uint256 _maxLeverage, uint256 _feeMultiplier, uint256 _baseFundingRate) external onlyOwner {
49:        bytes memory _assetName  = bytes(_idToAsset[_asset].name);
50:        require(_assetName.length == 0, "Already exists");
51:        require(bytes(_name).length > 0, "No name");
52:        require(_maxLeverage >= _minLeverage && _minLeverage > 0, "Wrong leverage values");

54:        allowedAsset[_asset] = true;
55:        _idToAsset[_asset].name = _name;

57:        _idToAsset[_asset].chainlinkFeed = _chainlinkFeed;

59:        _idToAsset[_asset].minLeverage = _minLeverage;
60:        _idToAsset[_asset].maxLeverage = _maxLeverage;
61:        _idToAsset[_asset].feeMultiplier = _feeMultiplier;
62:        _idToAsset[_asset].baseFundingRate = _baseFundingRate;

64:        emit AssetAdded(_asset, _name);
65:    }
```

```diff
diff --git a/contracts/PairsContract.sol b/contracts/PairsContract.sol
index 61be7b2..fbcd333 100644
--- a/contracts/PairsContract.sol
+++ b/contracts/PairsContract.sol
@@ -46,20 +46,21 @@ contract PairsContract is Ownable, IPairsContract {
      * @param _feeMultiplier percent value that the opening/closing fee is multiplied by in BP
      */
     function addAsset(uint256 _asset, string memory _name, address _chainlinkFeed, uint256 _minLeverage, uint256 _maxLeverage, uint256 _feeMultiplier, uint256 _baseFundingRate) external onlyOwner {
-        bytes memory _assetName  = bytes(_idToAsset[_asset].name);
+        Asset storage _idAsset = _idToAsset[_asset];
+        bytes memory _assetName  = bytes(_idAsset.name);
         require(_assetName.length == 0, "Already exists");
         require(bytes(_name).length > 0, "No name");
         require(_maxLeverage >= _minLeverage && _minLeverage > 0, "Wrong leverage values");

         allowedAsset[_asset] = true;
-        _idToAsset[_asset].name = _name;
+        _idAsset.name = _name;

-        _idToAsset[_asset].chainlinkFeed = _chainlinkFeed;
+        _idAsset.chainlinkFeed = _chainlinkFeed;

-        _idToAsset[_asset].minLeverage = _minLeverage;
-        _idToAsset[_asset].maxLeverage = _maxLeverage;
-        _idToAsset[_asset].feeMultiplier = _feeMultiplier;
-        _idToAsset[_asset].baseFundingRate = _baseFundingRate;
+        _idAsset.minLeverage = _minLeverage;
+        _idAsset.maxLeverage = _maxLeverage;
+        _idAsset.feeMultiplier = _feeMultiplier;
+        _idAsset.baseFundingRate = _baseFundingRate;

         emit AssetAdded(_asset, _name);
     }
```

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L73-L85
### PairsContract.sol.updateAssetLeverage(): \_idToAsset[\_asset] should be cached
```solidity
File: /contracts/PairsContract.sol
73:    function updateAssetLeverage(uint256 _asset, uint256 _minLeverage, uint256 _maxLeverage) external onlyOwner {
74:        bytes memory _name  = bytes(_idToAsset[_asset].name);/@audit: Access
75:        require(_name.length > 0, "!Asset");
76:
77:        if (_maxLeverage > 0) {
78:            _idToAsset[_asset].maxLeverage = _maxLeverage;/@audit: Access
79:        }
80:        if (_minLeverage > 0) {
81:            _idToAsset[_asset].minLeverage = _minLeverage;/@audit: Access
82:        }
        
84:        require(_idToAsset[_asset].maxLeverage >= _idToAsset[_asset].minLeverage, "Wrong leverage values");/@audit: Access
85:    }
```

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L92-L97
### PairsContract.sol.setAssetBaseFundingRate(): \_idToAsset[\_asset] should be cached
```solidity
File:/contracts/PairsContract.sol
92:    function setAssetBaseFundingRate(uint256 _asset, uint256 _baseFundingRate) external onlyOwner {
93:        bytes memory _name  = bytes(_idToAsset[_asset].name);/@audit: Access
94:        require(_name.length > 0, "!Asset");
95:        require(_baseFundingRate <= maxBaseFundingRate, "baseFundingRate too high");
96:        _idToAsset[_asset].baseFundingRate = _baseFundingRate;/@audit: Access
97:    }
```

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L104-L108
### PairsContract.sol.updateAssetFeeMultiplier(): \_idToAsset[\_asset] should be cached
```solidity
File:/contracts/PairsContract.sol
104:    function updateAssetFeeMultiplier(uint256 _asset, uint256 _feeMultiplier) external onlyOwner {
105:        bytes memory _name  = bytes(_idToAsset[_asset].name);/@audit: Access
106:        require(_name.length > 0, "!Asset");
107:        _idToAsset[_asset].feeMultiplier = _feeMultiplier;/@audit: Access
108:    }
```

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L154-L165
### PairsContract.sol.modifyLongOi(): \_idToOi[\_asset]\[\_tigAsset] should be cached
```solidity
File: /contracts/PairsContract.sol
154:    function modifyLongOi(uint256 _asset, address _tigAsset, bool _onOpen, uint256 _amount) external onlyProtocol {
155:        if (_onOpen) {
156:            _idToOi[_asset][_tigAsset].longOi += _amount;
157:            require(_idToOi[_asset][_tigAsset].longOi <= _idToOi[_asset][_tigAsset].maxOi || _idToOi[_asset][_tigAsset].maxOi == 0, "MaxLongOi");
158:        }
159:        else {
160:            _idToOi[_asset][_tigAsset].longOi -= _amount;
161:            if (_idToOi[_asset][_tigAsset].longOi < 1e9) {
162:                _idToOi[_asset][_tigAsset].longOi = 0;
163:            }
164:        }
165:    }
```

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L174-L185
### PairsContract.sol.modifyShortOi(): \_idToOi[\_asset]\[\_tigAsset] should be cached
```solidity
File: /contracts/PairsContract.sol
174:    function modifyShortOi(uint256 _asset, address _tigAsset, bool _onOpen, uint256 _amount) external onlyProtocol {
175:        if (_onOpen) {
176:            _idToOi[_asset][_tigAsset].shortOi += _amount;
177:            require(_idToOi[_asset][_tigAsset].shortOi <= _idToOi[_asset][_tigAsset].maxOi || _idToOi[_asset][_tigAsset].maxOi == 0, "MaxShortOi");
178:            }
179:        else {
180:            _idToOi[_asset][_tigAsset].shortOi -= _amount;
181:            if (_idToOi[_asset][_tigAsset].shortOi < 1e9) {
182:                _idToOi[_asset][_tigAsset].shortOi = 0;
183:            }
184:        }
185:    }
```


## Multiple address mappings can be combined into a single mapping of an address to a struct, where appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/StableVault.sol#L29-L30
```solidity
File: /contracts/StableVault.sol
29:    mapping(address => bool) public allowed;
30:    mapping(address => uint) private tokenIndex;
```

## Using storage instead of memory for structs/arrays saves gas

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L251
```solidity
File: /contracts/BondNFT.sol
251:        Bond memory bond = _idToBond[_id];
```

```diff
diff --git a/contracts/BondNFT.sol b/contracts/BondNFT.sol
index 33a6e76..5f40cf9 100644
--- a/contracts/BondNFT.sol
+++ b/contracts/BondNFT.sol
@@ -248,7 +248,7 @@ contract BondNFT is ERC721Enumerable, Ownable {
      * @return bool true if bond is expired
      */
     function isExpired(uint256 _id) public view returns (bool) {
-        Bond memory bond = _idToBond[_id];
+        Bond storage bond = _idToBond[_id];
         return bond.expireEpoch <= epoch[bond.asset] ? true : false;
     }
```

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L42
```solidity
File: /contracts/Position.sol
42:        Trade memory _trade = _trades[_id];
```

```diff
diff --git a/contracts/Position.sol b/contracts/Position.sol
index afbcbec..04c1a1e 100644
--- a/contracts/Position.sol
+++ b/contracts/Position.sol
@@ -39,7 +39,7 @@ contract Position is ERC721Enumerable, MetaContext, IPosition {

     function isMinter(address _address) public view returns (bool) { return _isMinter[_address]; }
     function trades(uint _id) public view returns (Trade memory) {
-        Trade memory _trade = _trades[_id];
+        Trade storage _trade = _trades[_id];
         _trade.trader = ownerOf(_id);
         if (_trade.orderType > 0) return _trade;
```

https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L700
```solidity
File: /contracts/Trading.sol
700:        Fees memory _fees = openFees;
```

```diff
diff --git a/contracts/Trading.sol b/contracts/Trading.sol
index 5a8ec2f..5a7dc2a 100644
--- a/contracts/Trading.sol
+++ b/contracts/Trading.sol
@@ -697,7 +697,7 @@ contract Trading is MetaContext, ITrading {
         returns (uint _feePaid)
     {
         IPairsContract.Asset memory asset = pairsContract.idToAsset(_asset);
-        Fees memory _fees = openFees;
+        Fees storage _fees = openFees;
         unchecked {
             _fees.daoFees = _fees.daoFees * asset.feeMultiplier / DIVISION_CONSTANT;
             _fees.burnFees = _fees.burnFees * asset.feeMultiplier / DIVISION_CONSTANT;
```

https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L774
```solidity
File:/contracts/Trading.sol
774:        Fees memory _fees = closeFees;
```

```diff
diff --git a/contracts/Trading.sol b/contracts/Trading.sol
index 5a8ec2f..090d31f 100644
--- a/contracts/Trading.sol
+++ b/contracts/Trading.sol
@@ -771,7 +771,7 @@ contract Trading is MetaContext, ITrading {
         returns (uint payout_)
     {
         IPairsContract.Asset memory asset = pairsContract.idToAsset(_asset);
-        Fees memory _fees = closeFees;
+        Fees storage _fees = closeFees;
```

https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L857-L868
```solidity
File: /contracts/Trading.sol
857:    function _checkDelay(uint _id, bool _type) internal {
858:        unchecked {
859:            Delay memory _delay = blockDelayPassed[_id];
860:            if (_delay.actionType == _type) {
861:                blockDelayPassed[_id].delay = block.number + blockDelay;
862:            } else {
863:                if (block.number < _delay.delay) revert("0"); //Wait
864:                blockDelayPassed[_id].delay = block.number + blockDelay;
865:                blockDelayPassed[_id].actionType = _type;
866:            }
867:        }
868:    }
```

```diff
diff --git a/contracts/Trading.sol b/contracts/Trading.sol
index 5a8ec2f..25ba9b4 100644
--- a/contracts/Trading.sol
+++ b/contracts/Trading.sol
@@ -856,13 +856,13 @@ contract Trading is MetaContext, ITrading {
      */
     function _checkDelay(uint _id, bool _type) internal {
         unchecked {
-            Delay memory _delay = blockDelayPassed[_id];
+            Delay storage _delay = blockDelayPassed[_id];
             if (_delay.actionType == _type) {
-                blockDelayPassed[_id].delay = block.number + blockDelay;
+                _delay.delay = block.number + blockDelay;
             } else {
                 if (block.number < _delay.delay) revert("0"); //Wait
-                blockDelayPassed[_id].delay = block.number + blockDelay;
-                blockDelayPassed[_id].actionType = _type;
+                _delay.delay = block.number + blockDelay;
+                _delay.actionType = _type;
             }
         }
     }
```

**Other instances**
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L886
```solidity
File: /contracts/Trading.sol

859:            Delay memory _delay = blockDelayPassed[_id];

886:            Proxy memory _proxy = proxyApprovals[_trader];
```

### Avoid contract existence checks by using solidity version 0.8.10 or later

Prior to 0.8.10 the compiler inserted extra code, including EXTCODESIZE (100 gas), to check for contract existence for external calls. In more recent solidity versions, the compiler will not insert these checks if the external call has a return value

*Total Instances: 32*
`gas savings: 32 * 100 = 3200 `

https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L182
```solidity
File: /contracts/Trading.sol

//@audit: getVerifiedPrice()
182:        (uint256 _price,) = tradingExtension.getVerifiedPrice(_tradeInfo.asset, _priceData, _signature, _isLong);

//@audit: getCount()
208:            emit PositionOpened(_tradeInfo, 0, _price, position.getCount()-1, _trader, _marginAfterFees);

//@audit: trades()
237:        IPosition.Trade memory _trade = position.trades(_id);

//@audit: getVerifiedPrice()
239:        (uint256 _price,) = tradingExtension.getVerifiedPrice(_trade.asset, _priceData, _signature, 0);

//@audit: trades()
270:        IPosition.Trade memory _trade = position.trades(_id);

//@audit: getVerifiedPrice()
285:            (uint256 _price,) = tradingExtension.getVerifiedPrice(_trade.asset, _priceData, _signature, _trade.direction ? 1 : 2);

324:        address _tigAsset = IStableVault(_tradeInfo.stableVault).stable();

//@audit: getCount()
332:        uint256 _id = position.getCount();

//@audit: trades()
363:        IPosition.Trade memory _trade = position.trades(_id);

//@audit: trades()
391:        IPosition.Trade memory _trade = position.trades(_id);

//@audit: idToAsset()
393:       IPairsContract.Asset memory asset = pairsContract.idToAsset(_trade.asset);

//@audit: trades()
427:        IPosition.Trade memory _trade = position.trades(_id);

//@audit: idToAsset()
429:        IPairsContract.Asset memory asset = pairsContract.idToAsset(_trade.asset);

//@audit: getVerifiedPrice()
433:        (uint _assetPrice,) = tradingExtension.getVerifiedPrice(_trade.asset, _priceData, _signature, 0);

//@audit: trades()
463:        IPosition.Trade memory _trade = position.trades(_id);

//@audit: getVerifiedPrice()
468:            (uint256 _price,) = tradingExtension.getVerifiedPrice(_trade.asset, _priceData, _signature, 0);

//@audit: getVerifiedPrice()
494:            (uint256 _price, uint256 _spread) = tradingExtension.getVerifiedPrice(trade.asset, _priceData, _signature, 0);

//@audit: trades()
539:            IPosition.Trade memory _trade = position.trades(_id);

//@audit: getVerifiedPrice()
542:            (uint256 _price,) = tradingExtension.getVerifiedPrice(_trade.asset, _priceData, _signature, 0);

//@audit: _limitClose()
574:        (uint _limitPrice, address _tigAsset) = tradingExtension._limitClose(_id, _tp, _priceData, _signature);

//@audit: idToAsset()
773:        IPairsContract.Asset memory asset = pairsContract.idToAsset(_asset);

//@audit: getRef()
783:        address _referrer = tradingExtension.getRef(_trader);//referrals.getReferral(referrals.getReferred(_trader));

//@audit: ownerOf()
848:        if (position.ownerOf(_id) != _trader) revert("2"); //NotPositionOwner   
```

https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L146
```solidity
File: /contracts/GovNFT.sol
//@audit: estimateFees()
146:        (uint256 messageFee, ) = endpoint.estimateFees(
```

https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/TradingExtension.sol#L66
```solidity
File: /contracts/TradingExtension.sol

//@audit: trades()
66:        _trade = position.trades(_id);

//@audit: trades()
95:        IPosition.Trade memory _trade = position.trades(_id);

//@audit: idToAsset()
213:            IPairsContract.Asset memory asset = pairsContract.idToAsset(_asset);
```

https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L134
```solidity
File: /contracts/Position.sol

//@audit: current()
134:        uint newTokenID = _tokenIds.current();

//@audit: current()
291:        return _tokenIds.current();
```

https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Lock.sol#L38
```solidity
File: /contracts/Lock.sol

//@audit: claim()
38:        (uint _amount, address _tigAsset) = bondNFT.claim(_id, msg.sender);

//@audit: claimDebt()
51:        uint amount = bondNFT.claimDebt(msg.sender, _tigAsset);

//@audit: getAssets()
111:        address[] memory assets = bondNFT.getAssets();
```

## Don't initiliaze struct members with their default values

If a variable is not set/initialized, it is assumed to have the default value (0, false, 0x0 etc depending on the data type). If you explicitly initialize it with its default value, you are just wasting gas.
It costs more gas to initialize variables to zero than to let the default of zero be applied
Declaring uint256 i = 0; means doing an MSTORE of the value 0 Instead you could just declare uint256 i to declare the variable without assigning it’s default value, saving 3 gas per declaration

https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L98-L115
```solidity
File: /contracts/Trading.sol
98:    struct Fees {
99:        uint daoFees;
100:        uint burnFees;
101:        uint referralFees;
102:        uint botFees;
103:    }
104:    Fees public openFees = Fees(
105:       0,
106:        0,
107:        0,
108:        0
109:    );
110:    Fees public closeFees = Fees(
111:        0,
112:        0,
113:        0,
114:        0
115:    );
```

## x += y costs more gas than x = x + y for state variables

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L52
```solidity
File:/contracts/GovNFT.sol
52:        counter += 1;
```

```diff
diff --git a/contracts/GovNFT.sol b/contracts/GovNFT.sol
index 05439ef..f707bf0 100644
--- a/contracts/GovNFT.sol
+++ b/contracts/GovNFT.sol
@@ -49,7 +49,7 @@ contract GovNFT is ERC721Enumerable, ILayerZeroReceiver, MetaContext, IGovNFT {

     function _mint(address to, uint256 tokenId) internal override {
         require(counter <= MAX, "Exceeds supply");
-        counter += 1;
+        counter = counter + 1;
```

## Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html Use a larger size then downcast where needed

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L114
```solidity
File: /contracts/GovNFT.sol

//@audit: uint16 _chainId
114:    function setTrustedAddress(uint16 _chainId, address _contract, bool _bool) external onlyOwner {


//@audit: uint16 _dstChainId
124:    function crossChain(
125:        uint16 _dstChainId,
126:        bytes memory _destination,
127:        address _to,
128:        uint256[] memory tokenId
129:    ) public payable {


//@audit: uint16 _srcChainId,uint64 _nonce
183:    function nonblockingLzReceive(uint16 _srcChainId, bytes calldata _srcAddress, uint64 _nonce, bytes calldata _payload) public {


//@audit: uint16 _srcChainId
189:    function _nonblockingLzReceive(uint16 _srcChainId, bytes memory _srcAddress, uint64, bytes memory _payload) internal {


//@audit: uint16 _srcChainId,uint64 _nonce
206:    function retryMessage(uint16 _srcChainId, bytes calldata _srcAddress, uint64 _nonce, bytes calldata _payload) public {


//@audit: uint16 _dstChainId
219:    function estimateFees(
220:        uint16 _dstChainId,
221:        address _userApplication,
222:        bytes calldata _payload,
223:        bool _payInZRO,
224:        bytes calldata _adapterParams
225:    ) external view returns (uint256 nativeFee, uint256 zroFee) {
```

## Using unchecked blocks to save gas - Increments in for loop can be unchecked  ( save 30-40 gas per loop iteration)
The majority of Solidity for loops increment a uint256 variable that starts at 0. These increment operations never need to be checked for over/underflow because the variable will never reach the max number of uint256 (will run out of gas long before that happens). The default over/underflow check wastes gas in every iteration of virtually every for loop . eg.

**Total instances:16** 
`Gas savings = ~500 gas`
**Affected code**
https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Referrals.sol#L70-L75
```solidity
File: /contracts/Referrals.sol
70:        for (uint i=0; i<_codeOwnersL; i++) {
71:            _referral[_ownedCodes[i]] = _codeOwners[i];
72:        }
73:        for (uint i=0; i<_referredAL; i++) {
74:            _referred[_referredA[i]] = _referredTo[i];
75:        }
```

```diff
diff --git a/contracts/Referrals.sol b/contracts/Referrals.sol
index 6a4313e..de22651 100644
--- a/contracts/Referrals.sol
+++ b/contracts/Referrals.sol
@@ -67,11 +67,17 @@ contract Referrals is Ownable, IReferrals {
         isInit = true;
         uint _codeOwnersL = _codeOwners.length;
         uint _referredAL = _referredA.length;
-        for (uint i=0; i<_codeOwnersL; i++) {
+        for (uint i=0; i<_codeOwnersL; {
             _referral[_ownedCodes[i]] = _codeOwners[i];
+            unchecked {
+                ++i;
+            }
         }
-        for (uint i=0; i<_referredAL; i++) {
+        for (uint i=0; i<_referredAL;) {
             _referred[_referredA[i]] = _referredTo[i];
+            unchecked {
+                ++i;
+            }
         }
     }
```

**Other instances**
https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Position.sol#L296-L298
```solidity
File: /contracts/Position.sol
296:        for (uint i=0; i<_ids.length; i++) {

304:        for (uint i=0; i<_ids.length; i++) {
```

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#53
```solidity
File: /contracts/GovNFT.sol

53:        for (uint i=0; i<assetsLength(); i++) {

67:        for (uint i=0; i<assetsLength(); i++) {

78:        for (uint i=0; i<assetsLength(); i++) {

95:        for (uint i=0; i<assetsLength(); i++) {

105:        for (uint i=0; i<_amount; i++) {

131:        for (uint i=0; i<tokenId.length; i++) {

200:        for (uint i=0; i<tokenId.length; i++) {

246:        for (uint i=0; i<_ids.length; i++) {

252:        for (uint i=0; i<_ids.length; i++) {

258:        for (uint i=0; i<_ids.length; i++) {

325:        for (uint i=0; i<_ids.length; i++) {
```

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Lock.sol#L113
```solidity
File: /contracts/Lock.sol
113:        for (uint i=0; i < assets.length; i++) {
```
[see resource](https://github.com/ethereum/solidity/issues/10695)

## Splitting require() statements that use && saves gas - (saves 8 gas per &&)
Instead of using the && operator in a single require statement to check multiple conditions,using multiple require statements with 1 condition per require statement will save 8 GAS per &&
The gas difference would only be realized if the revert condition is realized(met).

**Total Instances: 3** Gas savings = `3*8= 24 gas`

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L52
```solidity
File:/contracts/PairsContract.sol
52:        require(_maxLeverage >= _minLeverage && _minLeverage > 0, "Wrong leverage values");
```

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/utils/TradingLibrary.sol#L116-L119
```solidity
File: /contracts/utils/TradingLibrary.sol
116:   require(
117:     _priceData.price < assetChainlinkPrice+assetChainlinkPrice*2/100 &&
118:     _priceData.price > assetChainlinkPrice-assetChainlinkPrice*2/100, "!chainlinkPrice"
119:    );
```

https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L887
```solidity
File: /contracts/Trading.sol
887:            require(_proxy.proxy == _msgSender() && _proxy.time >= block.timestamp, "Proxy not approved");
```

## A modifier used only once and not being inherited should be inlined to save gas

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/Referrals.sol#L80-L83
```solidity
File:/contracts/Referrals.sol
80:    modifier onlyProtocol() {
81:        require(_msgSender() == address(protocol), "!Protocol");
82:        _;
83:    }
```

## Use shorter revert strings(less than 32 bytes) 
Every reason string takes at least 32 bytes so make sure your string fits in 32 bytes or it will become more expensive.Each extra chunk of byetes past the original 32 incurs an MSTORE which costs 3 gas

Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.
Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/GovNFT.sol#L153-L156
```solidity
File: /contracts/GovNFT.sol
153:        require(
154:            msg.value >= messageFee,
155:            "Must send enough value to cover messageFee"
156:        );
```

I suggest shortening the revert strings to fit in 32 bytes, or using custom errors.

## Use a more recent version of solidity

Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/TradingExtension.sol#L2
```solidity
File: /contracts/TradingExtension.sol
2:pragma solidity ^0.8.0;
```
All the contracts in scope seems to be using the above version


## The following should have been caught by the automated script but were not

### Cache storage values in memory to minimize SLOADs
The code can be optimized by minimizing the number of SLOADs.

SLOADs are expensive (100 gas after the 1st one) compared to MLOADs/MSTOREs (3 gas each). Storage values read multiple times should instead be cached in memory the first time (costing 1 SLOAD) and then read from this cache to avoid multiple SLOADs.

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/PairsContract.sol#L33-L37
### PairsContract.sol.setAssetChainlinkFeed(): \_idToAsset[\_asset] should be cached
```solidity
File: /contracts/PairsContract.sol
33:    function setAssetChainlinkFeed(uint256 _asset, address _feed) external onlyOwner {
34:        bytes memory _name  = bytes(_idToAsset[_asset].name);
35:        require(_name.length > 0, "!Asset");
36:        _idToAsset[_asset].chainlinkFeed = _feed;
37:    }
```

```diff
diff --git a/contracts/PairsContract.sol b/contracts/PairsContract.sol
index 61be7b2..dc93c18 100644
--- a/contracts/PairsContract.sol
+++ b/contracts/PairsContract.sol
@@ -31,9 +31,10 @@ contract PairsContract is Ownable, IPairsContract {
      * @param _feed contract address of the Chainlink price feed
      */
     function setAssetChainlinkFeed(uint256 _asset, address _feed) external onlyOwner {
-        bytes memory _name  = bytes(_idToAsset[_asset].name);
+        Asset storage _idAsset = _idToAsset[_asset];
+        bytes memory _name  = bytes(_idAsset.name);
         require(_name.length > 0, "!Asset");
-        _idToAsset[_asset].chainlinkFeed = _feed;
+        _idAsset.chainlinkFeed = _feed;
     }
```

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/StableVault.sol#L89-L96
### StableVault.sol.delistToken(): tokens[tokens.length-1] should be cached
```solidity
File: /contracts/StableVault.sol
89:    function delistToken(address _token) external onlyOwner {
90:        require(allowed[_token], "Not added");
91:        tokenIndex[tokens[tokens.length-1]] = tokenIndex[_token];
92:        tokens[tokenIndex[_token]] = tokens[tokens.length-1];
```

```diff
diff --git a/contracts/StableVault.sol b/contracts/StableVault.sol
index 373698a..7b88476 100644
--- a/contracts/StableVault.sol
+++ b/contracts/StableVault.sol
@@ -88,8 +88,9 @@ contract StableVault is MetaContext, IStableVault {
     */
     function delistToken(address _token) external onlyOwner {
         require(allowed[_token], "Not added");
-        tokenIndex[tokens[tokens.length-1]] = tokenIndex[_token];
-        tokens[tokenIndex[_token]] = tokens[tokens.length-1];
+        address _tokens = tokens[tokens.length-1];
+        tokenIndex[_tokens] = tokenIndex[_token];
+        tokens[tokenIndex[_token]] =_tokens;
         delete tokenIndex[_token];
         tokens.pop();
         allowed[_token] = false;
```

https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L57-L86
```solidity
File: /contracts/BondNFT.sol
57:    function createLock(

66:            uint expireEpoch = epoch[_asset] + _period;

74:                epoch[_asset],  // mint epoch
```

```diff
diff --git a/contracts/BondNFT.sol b/contracts/BondNFT.sol
index 33a6e76..787de70 100644
--- a/contracts/BondNFT.sol
+++ b/contracts/BondNFT.sol
@@ -61,9 +61,10 @@ contract BondNFT is ERC721Enumerable, Ownable {
         address _owner
     ) external onlyManager() returns(uint id) {
         require(allowedAsset[_asset], "!Asset");
+        uint256 _epoch = epoch[_asset];
         unchecked {
             uint shares = _amount * _period / 365;
-            uint expireEpoch = epoch[_asset] + _period;
+            uint expireEpoch = _epoch + _period;
             id = ++totalBonds;
             totalShares[_asset] += shares;
             Bond memory _bond = Bond(
@@ -71,7 +72,7 @@ contract BondNFT is ERC721Enumerable, Ownable {
                 address(0),     // owner
                 _asset,         // tigAsset token
                 _amount,        // tigAsset amount
-                epoch[_asset],  // mint epoch
+                _epoch,  // mint epoch
                 block.timestamp,// mint timestamp
                 expireEpoch,    // expire epoch
                 0,              // pendingqq
```

