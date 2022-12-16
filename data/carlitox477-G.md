# BondNFT#createLock can cache epoch\[_asset\]
This storage variable is accessed twice, so it can be cached
```diff
    // BondNFT.createLock
    unchecked {
+       uint256 epoch_asset = epoch[_asset];
        uint shares = _amount * _period / 365; 
-       uint expireEpoch = epoch[_asset] + _period;
+       uint expireEpoch = epoch_asset + _period;
        id = ++totalBonds;
        totalShares[_asset] += shares;
        Bond memory _bond = Bond(
            id,             // id
            address(0),     // owner
            _asset,         // tigAsset token
            _amount,        // tigAsset amount
-           epoch[_asset],  // mint epoch
+           epoch_asset,  // mint epoch
            block.timestamp,// mint timestamp
            expireEpoch,    // expire epoch
            0,              // pending
            shares,         // linearly scaling share of rewards
            _period,        // lock period
            false           // is expired boolean
        );
        _idToBond[id] = _bond;
        _mint(_owner, _bond); // @audit-issue can this overflow for being in uchecked block?
    }
``` 

# BondNFT#extendLock can cache epoch\[bond.asset\] and avoid double calculation of bond.amount + _amount and bond.period + _period in order to save gas
```diff
    function extendLock(
        uint _id,
        address _asset,
        uint _amount,
        uint _period,
        address _sender
    ) external onlyManager() {
        Bond memory bond = idToBond(_id);
        Bond storage _bond = _idToBond[_id];
        require(bond.owner == _sender, "!owner");
        require(!bond.expired, "Expired");
        require(bond.asset == _asset, "!BondAsset");
        require(bond.pending == 0);
        uint256 assetEpoch = epoch[bond.asset];
-       require(epoch[bond.asset] == block.timestamp/DAY, "Bad epoch");
+       require(assetEpoch == block.timestamp/DAY, "Bad epoch");
-       require(bond.period+_period <= 365, "MAX PERIOD");
+       uint256 newBondPeriod = bond.period + _period;
+       require(newBondPeriod <= 365, "MAX PERIOD");
        unchecked {
+           uint256 newBondAmount = bond.amount + _amount;
-           uint shares = (bond.amount + _amount) * (bond.period + _period) / 365;
+           uint shares = newBondAmount * newBondPeriod / 365;
-            uint expireEpoch = block.timestamp/DAY + bond.period + _period;
+           uint expireEpoch = block.timestamp/DAY + newBondPeriod;
            totalShares[bond.asset] += shares-bond.shares;
            _bond.shares = shares;
-           _bond.amount += _amount;
+           _bond.amount = newBondAmount;
            _bond.expireEpoch = expireEpoch;
-           _bond.period += _period;
+           _bond.period = newBondPeriod;
            _bond.mintTime = block.timestamp;
-           _bond.mintEpoch = epoch[bond.asset];
+           _bond.mintEpoch = assetEpoch;
-           bondPaid[_id][bond.asset] = accRewardsPerShare[bond.asset][epoch[bond.asset]] * _bond.shares / 1e18;
+           bondPaid[_id][bond.asset] = accRewardsPerShare[bond.asset][assetEpoch] * _bond.shares / 1e18;
        }
        emit ExtendLock(_period, _amount, _sender,  _id);
    }
```

# BondNFT#claim math operation can be simplified
Given than ```bondPaid[_id][bond.asset])``` is first substracted and then added (for the minus operator), and common factor ```bond.shares / 1e18``` we can simplify pendingDelta operation to: ```uint _pendingDelta = bond.shares * ( accRewardsPerShare[bond.asset][epoch[bond.asset]] - accRewardsPerShare[bond.asset][bond.expireEpoch-1] ) / 1e18;```


# BondNFT#claim can avoid multiple bondPaid\[_id\]\[bond.asset\] access
```diff
    // BondNFT.claim
    unchecked {
+       uint256 _bondPaid = bondPaid[_id][bond.asset]
        if (bond.expired) {            
-           uint _pendingDelta = (bond.shares * accRewardsPerShare[bond.asset][epoch[bond.asset]] / 1e18 - bondPaid[_id][bond.asset]) - (bond.shares * accRewardsPerShare[bond.asset][bond.expireEpoch-1] / 1e18 - bondPaid[_id][bond.asset]);
+           uint _pendingDelta = (bond.shares * accRewardsPerShare[bond.asset][epoch[bond.asset]] / 1e18 - _bondPaid) - (bond.shares * accRewardsPerShare[bond.asset][bond.expireEpoch-1] / 1e18 - _bondPaid);

            if (totalShares[bond.asset] > 0) {
                accRewardsPerShare[bond.asset][epoch[bond.asset]] += _pendingDelta*1e18/totalShares[bond.asset];
            }
        }
-       bondPaid[_id][bond.asset] += amount;
+       bondPaid[_id][bond.asset] = _bondPaid + amount;
    }
```

# BondNFT#distribute can cache epoch\[_tigAsset\] and move it calculation outside the for loop
Giving that we know that there will be ```aEpoch - epoch[_tigAsset]``` iterations, we know that at the end of the loop ```epoch[_tigAsset] += (aEpoch - epoch[_tigAsset])```. Also ```epoch[_tigAsset]``` can be cached, this means

```diff
    // inside BondNFT.distribute
+   uint256 tigEpoch = epoch[_tigAsset]
-   if (aEpoch > epoch[_tigAsset]) {
+   if (aEpoch > tigEpoch) {
-       for (uint i=epoch[_tigAsset]; i<aEpoch; i++) {
        for (uint i = tigEpoch; i<aEpoch; i++) {
-           epoch[_tigAsset] += 1; 
            accRewardsPerShare[_tigAsset][i+1] = accRewardsPerShare[_tigAsset][i];
        }
+       epoch[_tigAsset] = aEpoch - tigEpoch; 
    }
```

# BondNFT#distribute can use storage pointer to save gas
Given that ```accRewardsPerShare[_tigAsset]``` is used multiple times inside the for loop, we can use a storage pointer to save gas

```diff
    // inside BondNFT.distribute
    unchecked {
        uint aEpoch = block.timestamp / DAY;
+       mapping (uint256=>uint256) storage _accRewardsPerShare = accRewardsPerShare[_tigAsset];
        if (aEpoch > epoch[_tigAsset]) {
            for (uint i=epoch[_tigAsset]; i<aEpoch; i++) {
                epoch[_tigAsset] += 1; 
-               accRewardsPerShare[_tigAsset][i+1] = accRewardsPerShare[_tigAsset][i];
+               _accRewardsPerShare[i+1] = _accRewardsPerShare[i];
            }
        }
-       accRewardsPerShare[_tigAsset][aEpoch] += _amount * 1e18 / totalShares[_tigAsset];
+       _accRewardsPerShare[aEpoch] += _amount * 1e18 / totalShares[_tigAsset];
    }
```
# BondNFT#idToBond storage variable epoch\[bond.asset\] is accessed twice
The second access can be avoided:
```diff
    function idToBond(uint256 _id) public view returns (Bond memory bond) {
        bond = _idToBond[_id];
        bond.owner = ownerOf(_id);
+       uint256 assetEpoch = epoch[bond.asset];
-       bond.expired = bond.expireEpoch <= epoch[bond.asset] ? true : false;
        bond.expired = bond.expireEpoch <= assetEpoch ? true : false;
        unchecked {
-           uint _accRewardsPerShare = accRewardsPerShare[bond.asset][bond.expired ? bond.expireEpoch-1 : epoch[bond.asset]];
            uint _accRewardsPerShare = accRewardsPerShare[bond.asset][bond.expired ? bond.expireEpoch-1 : assetEpoch];
            bond.pending = bond.shares * _accRewardsPerShare / 1e18 - bondPaid[_id][bond.asset];
        }
    }
```

# Refferals#createReferralCode can avoid one _msgSender() call
```diff
function createReferralCode(bytes32 _hash) external {
    require(_referral[_hash] == address(0), "Referral code already exists");
+   address msgSender =  _msgSender();
-   _referral[_hash] = _msgSender();
+   _referral[_hash] = msgSender;
-   emit ReferralCreated(_msgSender(), _hash);
+   emit ReferralCreated(msgSender, _hash);
    }
```

# GovNFT#_mint: assets\[i\], counter and assetsLength() can be cached in order to save gas. Use storage pointer to save gas
```diff
    function _mint(address to, uint256 tokenId) internal override {
+       uint256 _counter = counter;
-       require(counter <= MAX, "Exceeds supply");
+       require(_counter <= MAX, "Exceeds supply");
-       counter += 1;
+       counter = _counter + 1;
+       uint256 assetLen = assetsLength();
+       address[] storage _assets = assets;
-       for (uint i=0; i<assetsLength(); i++) {
+       for (uint i; i<assetLen; unchecked{++i}) {
+           uint256 asset = _assets[i]
-           userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];
+           userPaid[to][asset] += accRewardsPerNFT[asset];
        }
        super._mint(to, tokenId);
    }
```

# GovNFT#_bridgeMint: assets\[i\] and assetsLength() can be cached in order to save gas. Use storage pointer to save gas
```diff
    function _bridgeMint(address to, uint256 tokenId) public {
        require(msg.sender == address(this) || _msgSender() == owner(), "NotBridge");
        require(tokenId <= 10000, "BadID");
+       address[] storage _assets = assets;
+       uint256 assetLen = assetsLength();    
-       for (uint i=0; i<assetsLength(); i++) {
+       for (uint i; i<assetLen; unchecked{++i}) {
+           address _asset = _assets[i];
-           userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];
+           userPaid[to][_asset] += accRewardsPerNFT[_asset];
        }
        super._mint(to, tokenId);
    }

```


# GovNFT#_burn: multiple variables can be cached in order to save gas. Also storage pointer can be used to save gas
```diff
    function _burn(uint256 tokenId) internal override {
        address owner = ownerOf(tokenId);
+       uint256 assetLen = assetsLength();
+       address[] storage _assets = assets;
-       for (uint i=0; i<assetsLength(); i++) {
+       for (uint i; i<assetLen; unchecked{++i}) {
+           address _asset = _assets[i];
+           uint256 userPaidBalance = userPaid[from][_asset];
+           uint256 userPaidAssetProp = userPaidBalance/balanceOf(from);

-           userDebt[owner][assets[i]] += accRewardsPerNFT[assets[i]];
-           userDebt[owner][assets[i]] -= userPaid[owner][assets[i]]/balanceOf(owner);
+           userDebt[owner][_asset] = userDebt[owner][_asset] + accRewardsPerNFT[_asset] - userPaidAssetProp;

-           userPaid[owner][assets[i]] -= userPaid[owner][assets[i]]/balanceOf(owner);
+           userPaid[owner][_asset] = userPaidBalance - userPaidAssetProp;

        }
        super._burn(tokenId);
    }
```

# GovNFT#_transfer: multiple variables can be cached in order to save gas. Also storage pointer can be used to save gas
```diff
    function _transfer(
        address from,
        address to,
        uint256 tokenId
    ) internal override {
        require(ownerOf(tokenId) == from, "!Owner");
+       uint256 assetLen = assetsLength();
+       address[] storage _assets = assets;
-       for (uint i=0; i<assetsLength(); i++) {
+       for (uint i; i<assetLen; unchecked{++i}) {
+           address _asset = _assets[i];
+           uint256 userPaidBalance = userPaid[from][_asset];
+           uint256 _assetAccRewardsPerNFT = accRewardsPerNFT[_asset];

+           uint256 userPaidAssetProp = userPaidBalance/balanceOf(from);
+           userDebt[from][_asset] = userDebt[from][_asset] + _assetAccRewardsPerNFT - userPaidAssetProp;
-           userDebt[from][assets[i]] += accRewardsPerNFT[assets[i]];
-           userDebt[from][assets[i]] -= userPaid[from][assets[i]]/balanceOf(from);

+           userPaid[from][_asset] = userPaidBalance - userPaidAssetProp + _assetAccRewardsPerNFT;
-           userPaid[from][assets[i]] -= userPaid[from][assets[i]]/balanceOf(from);
-           userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];
        }
        super._transfer(from, to, tokenId);
    }
```




# PairsContract#addAsset can save gas by reducing _idToAsset\[_asset\] assignations
```diff
    function addAsset(uint256 _asset, string memory _name, address _chainlinkFeed, uint256 _minLeverage, uint256 _maxLeverage, uint256 _feeMultiplier, uint256 _baseFundingRate) external onlyOwner {
        bytes memory _assetName  = bytes(_idToAsset[_asset].name);
        require(_assetName.length == 0, "Already exists");
        require(bytes(_name).length > 0, "No name");
        require(_maxLeverage >= _minLeverage && _minLeverage > 0, "Wrong leverage values");

        allowedAsset[_asset] = true;
-       _idToAsset[_asset].name = _name;
-       _idToAsset[_asset].chainlinkFeed = _chainlinkFeed;
-       _idToAsset[_asset].minLeverage = _minLeverage;
-       _idToAsset[_asset].maxLeverage = _maxLeverage;
-       _idToAsset[_asset].feeMultiplier = _feeMultiplier;
-       _idToAsset[_asset].baseFundingRate = _baseFundingRate;
+       _idToAsset[_asset] = Asset(
+           _name,
+           _chainlinkFeed,
+           _minLeverage,
+           _maxLeverage,
+           _feeMultiplier,
+           _baseFundingRate
+           );

        emit AssetAdded(_asset, _name);
    }
```

# PairsContract#modifyLongOi can use storage pointer and caache variables in order to save gas
```diff
    function modifyLongOi(uint256 _asset, address _tigAsset, bool _onOpen, uint256 _amount) external onlyProtocol {
+       OpenInterest storage assetOpenInterest = _idToOi[_asset][_tigAsset];
+       uint256 updated_idToOi;
        if (_onOpen) {
+           updated_idToOi = assetOpenInterest.longOi + _amount;
-           _idToOi[_asset][_tigAsset].longOi += _amount;
+           assetOpenInterest.longOi = updated_idToOi;
-           require(_idToOi[_asset][_tigAsset].longOi <= _idToOi[_asset][_tigAsset].maxOi || _idToOi[_asset][_tigAsset].maxOi == 0, "MaxLongOi");
+           require(updated_idToOi <= assetOpenInterest.maxOi || assetOpenInterest.maxOi == 0, "MaxLongOi");
        }
        else {
+           updated_idToOi = _idToOi[_asset][_tigAsset].longOi - _amount;
-           _idToOi[_asset][_tigAsset].longOi -= _amount;
+           assetOpenInterest.longOi = updated_idToOi;            
-           if (_idToOi[_asset][_tigAsset].longOi < 1e9) {
-               _idToOi[_asset][_tigAsset].longOi = 0;
+           if (updated_idToOi < 1e9) {
+               assetOpenInterest.longOi = 0;
            }
        }
    }
```


# PairsContract#modifyShortOi can use storage pointer and caache variables in order to save gas
```diff
    function modifyShortOi(uint256 _asset, address _tigAsset, bool _onOpen, uint256 _amount) external onlyProtocol {
+       OpenInterest storage assetOpenInterest = _idToOi[_asset][_tigAsset];
+       uint256 updated_idToOi;
        if (_onOpen) {
+           updated_idToOi = _idToOi[_asset][_tigAsset].shortOi + _amount;
-           _idToOi[_asset][_tigAsset].shortOi += _amount;
+           assetOpenInterest.shortOi = updated_idToOi;
-           require(_idToOi[_asset][_tigAsset].shortOi <= _idToOi[_asset][_tigAsset].maxOi || _idToOi[_asset][_tigAsset].maxOi == 0, "MaxShortOi");
+           require(updated_idToOi <= assetOpenInterest.maxOi || assetOpenInterest.maxOi == 0, "MaxShortOi");
            }
        else {
+           updated_idToOi = _idToOi[_asset][_tigAsset].shortOi - _amount;
-           _idToOi[_asset][_tigAsset].shortOi -= _amount;
+           assetOpenInterest.shortOi = updated_idToOi;
-           if (_idToOi[_asset][_tigAsset].shortOi < 1e9) {
-               _idToOi[_asset][_tigAsset].shortOi = 0;
+           if (updated_idToOi < 1e9) {
+               assetOpenInterest.shortOi = 0;
            }
        }
    }
```

# Trading#initiateMarketOrder can avoid calling _checkDelay
Giving that position.getCount() returns the number of position, we know that the last position opened will be always ```position.getCount()```. Therefore, checking if a position that does not exist is opened or not is redundant.

Then ```_checkDelay(position.getCount(), true);``` can be omitted