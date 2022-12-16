# Gas Optimizations

## Summary

|               | Issue         | Instances     |
| :-------------: |:-------------|:-------------:|
| 1     | State variables only set in the constructor should be declared `immutable`  | 7 |
| 2     | Variables inside struct should be packed to save gas  | 1  |
| 3     | Multiple address/IDs mappings can be combined into a single mapping of an address/id to a struct | 10 |
| 4     | Remove unecessary `owner` field in `Bond` struct to save gas | 1 |
| 5     | Misorganized if statement in `claim` function cost more gas  | 1 |
| 6     | `assetsLength()` should not be called at each loop iteration | 4 |
| 7     | `storage` variable should be cached into `memory` variables instead of re-reading them  |  2 |
| 8     | Use `unchecked` blocks to save gas  |  1 |
| 9     | `x += y/x -= y` costs more gas than `x = x + y/x = x - y` for state variables  |  30 |
| 10    | `require()` strings longer than 32 bytes cost extra gas  |  4 |
| 11    | Splitting `require()` statements that uses && saves gas  |  2 |
| 12    | `++i/i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as in the case when used in for & while loops |  16  |


## Findings

### 1- State variables only set in the constructor should be declared `immutable` :

The state variables only set in the constructor should be declared `immutable`, this avoids a Gsset (20000 gas) in the constructor, and replaces each Gwarmacces (100 gas) with a PUSH32 (3 gas).

There are 7 instances of this issue:

File: contracts/Trading.sol [Line 121-123](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L121-L123)
```
IPairsContract private pairsContract;
IPosition private position;
IGovNFT private gov;
```

File: contracts/TradingExtension.sol [Line 13](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L13)
```
address public trading;
```

File: contracts/TradingExtension.sol [Line 22-24](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L22-L24)
```
IPairsContract private pairsContract;
IReferrals private referrals;
IPosition private position;
```

### 2- Variables inside `struct` should be packed to save gas :

As the solidity EVM works with 32 bytes, variables less than 32 bytes should be packed inside a struct so that they can be stored in the same slot, each slot saved can avoid an extra Gsset (20000 gas) for the first setting of the struct. Subsequent reads as well as writes have smaller gas savings.

There is 1 instance of this issue:

File: contracts/BondNFT.sol [Line 12-24](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L12-L24)

```
struct Bond {
    uint256 id;
    address owner;
    address asset;
    uint256 amount;
    uint256 mintEpoch;
    uint256 mintTime;
    uint256 expireEpoch;
    uint256 pending;
    uint256 shares;
    uint256 period;
    bool expired;
}
```

As the `mintEpoch`, `mintTime`, `expireEpoch` and `period` parameters values can't really overflow 2^64 their values should be packed together and the struct should be modified as follow to save gas :

```
struct Bond {
    uint256 id;
    address owner;
    address asset;
    uint256 amount;    
    uint256 pending;
    uint256 shares;
    uint64 mintEpoch;
    uint64 mintTime;
    uint64 expireEpoch;
    uint64 period;
    bool expired;
}
```

### 3- Multiple address/IDs mappings can be combined into a single mapping of an address/id to a struct :

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

There are 10 instances of this issue :

```
File: contracts/BondNFT.sol

26      mapping(address => uint256) public epoch;
32      mapping(address => bool) public allowedAsset;
33      mapping(address => uint256) private assetsIndex;
35      mapping(address => mapping(uint256 => uint256)) private accRewardsPerShare; // tigAsset => epoch => accRewardsPerShare
37      mapping(address => uint256) public totalShares;
```

These mappings could be refactored into the following struct and mapping for example :

```
struct Asset {
    uint256 epoch;
    uint256 assetsIndex;
    uint256 totalShares;
    bool allowedAsset;
    mapping(uint256 => uint256) accRewardsPerShare;
}
    
mapping(address => Asset) private _assets;
```

```
File: contracts/GovNFT.sol

265     mapping(address => bool) private _allowedAsset;
266     mapping(address => uint) private assetsIndex;
269     mapping(address => uint256) private accRewardsPerNFT;
```

These mappings could be refactored into the following struct and mapping for example :

```
struct Asset {
    uint256 assetsIndex;
    uint256 accRewardsPerNFT;
    bool allowedAsset;
}
    
mapping(address => Asset) private _assets;
```

```
File: contracts/StableVault.sol

29      mapping(address => bool) public allowed;
30      mapping(address => uint) private tokenIndex;
```

These mappings could be refactored into the following struct and mapping for example :

```
struct Token {
    uint256 tokenIndex;
    bool allowed;
}
    
mapping(address => Token) private _tokens;
```


### 4- Remove unecessary `owner` field in `Bond` struct to save gas :

The `owner` address stored in the `Bond` struct is unecessary as each bond is minted as an NFT, so with the bond id you can easily get the owner by calling `ownerOf(bond.id)`, and the fact that the `bond.owner` is set to `address(0)` in `createLock` function and never get used later as all the functions call `idToBond` to get bond info which uses `ownerOf(_id)` method.

As you can see the `idToBond` function uses directly `ownerOf(_id)` method :

File: contracts/BondNFT.sol [Line 237](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L237)
```
function idToBond(uint256 _id) public view returns (Bond memory bond) {
    bond = _idToBond[_id];
    /**
        Use of ownerOf(_id) directly 
     */
    bond.owner = ownerOf(_id);
    bond.expired = bond.expireEpoch <= epoch[bond.asset] ? true : false;
    unchecked {
        uint _accRewardsPerShare = accRewardsPerShare[bond.asset][bond.expired ? bond.expireEpoch-1 : epoch[bond.asset]];
        bond.pending = bond.shares * _accRewardsPerShare / 1e18 - bondPaid[_id][bond.asset];
    }
}
```

So i recommend to remove the owner field from `Bond` struct to save gas when calling for example `createLock` function (because there is less value to store).


### 5- Misorganized if statement in `claim` function cost more gas :

The issue occurs in the code below :

File: contracts/BondNFT.sol [Line 168-187](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L168-L187)
```
function claim(
    uint _id,
    address _claimer
) public onlyManager() returns(uint amount, address tigAsset) {
    Bond memory bond = idToBond(_id);
    require(_claimer == bond.owner, "!owner");
    amount = bond.pending;
    tigAsset = bond.asset;
    unchecked {
        if (bond.expired) {
            /** @audit
                Unecessary calculation of _pendingDelta if totalShares[bond.asset] == 0
            */
            uint _pendingDelta = (bond.shares * accRewardsPerShare[bond.asset][epoch[bond.asset]] / 1e18 - bondPaid[_id][bond.asset]) - (bond.shares * accRewardsPerShare[bond.asset][bond.expireEpoch-1] / 1e18 - bondPaid[_id][bond.asset]);
            if (totalShares[bond.asset] > 0) {
                accRewardsPerShare[bond.asset][epoch[bond.asset]] += _pendingDelta*1e18/totalShares[bond.asset];
            }
        }
        bondPaid[_id][bond.asset] += amount;
    }
    IERC20(tigAsset).transfer(manager, amount);
    emit ClaimFees(tigAsset, amount, _claimer, _id);
}
```

As you can see from the code above if we have `bond.expired == true` then the function calculate the `_pendingDelta` but this calculation could be unecessary if we also have `totalShares[bond.asset] == 0`, and because the `_pendingDelta` calculation requires multiple reads from storage it will cost additional gas for nothing.

So i recommend to modify the if checks to avoid this issue, the new code should look like this :

```
function claim(
    uint _id,
    address _claimer
) public onlyManager() returns(uint amount, address tigAsset) {
    ...
    unchecked {
        /** @audit
            Check both bond.expired and totalShares[bond.asset]>0 at the same time
            This avoids unecessary calculation of _pendingDelta if totalShares[bond.asset] == 0
        */
        if (bond.expired && totalShares[bond.asset] > 0) {
            uint _pendingDelta = (bond.shares * accRewardsPerShare[bond.asset][epoch[bond.asset]] / 1e18 - bondPaid[_id][bond.asset]) - (bond.shares * accRewardsPerShare[bond.asset][bond.expireEpoch-1] / 1e18 - bondPaid[_id][bond.asset]);
            accRewardsPerShare[bond.asset][epoch[bond.asset]] += _pendingDelta*1e18/totalShares[bond.asset];
        }
        bondPaid[_id][bond.asset] += amount;
    }
    ...
}
```


### 6- `assetsLength()` should not be called at each loop iteration :

The `assetsLength()` function reads directly from storage to get `assets.length`, so using this function to get the length in a for loop means that in every iteration there will be a read from storage which will cost a lot of gas.

There are 4 instances of this issue :

```
File: contracts/GovNFT.sol

53      for (uint i=0; i<assetsLength(); i++)
67      for (uint i=0; i<assetsLength(); i++)
78      for (uint i=0; i<assetsLength(); i++)
95      for (uint i=0; i<assetsLength(); i++)
```

I recommend to call the `assetsLength()` function before the for loop and store its return values in a memory variable this will save a lot of gas, for example :

```
uint256 len = assetsLength();

for (uint i=0; i<len; i++){
    ...
}
```

### 7- `storage` variable should be cached into `memory` variables instead of re-reading them :

The instances below point to the second+ access of a state variable within a function, the caching of a state variable replaces each Gwarmaccess (100 gas) with a much cheaper stack read, thus saves **100gas** for each instance.

There are 2 instances of this issue :

File: contracts/GovNFT.sol [Line 80-81](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L80-L81)

```
userDebt[owner][assets[i]] -= userPaid[owner][assets[i]]/balanceOf(owner);
userPaid[owner][assets[i]] -= userPaid[owner][assets[i]]/balanceOf(owner);     
```

The above operations should be modified as follow :
```
uint256 _amount = userPaid[owner][assets[i]]/balanceOf(owner);
userDebt[owner][assets[i]] -= _amount;
userPaid[owner][assets[i]] -= _amount;     
```

File: contracts/GovNFT.sol [Line 97-98](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L97-L98)

```
userDebt[from][assets[i]] -= userPaid[from][assets[i]]/balanceOf(from);
userPaid[from][assets[i]] -= userPaid[from][assets[i]]/balanceOf(from);     
```

The above operations should be modified as follow :
```
uint256 _amount = userPaid[from][assets[i]]/balanceOf(from);
userDebt[from][assets[i]] -= _amount;
userPaid[from][assets[i]] -= _amount;      
```

### 8- Use `unchecked` blocks to save gas :

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block.

There is 1 instance of this issue:

File: contracts/GovNFT.sol [Line 52](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L52)
```
counter += 1;  
```

The above operation cannot overflow due to the check [require(counter <= MAX, "Exceeds supply");](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L51) It should be marked as `unchecked`. 


### 9- `x += y/x -= y` costs more gas than `x = x + y/x = x - y` for state variables :

Using the addition operator instead of plus-equals saves **113 gas** as explained [here](https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8)

There are 30 instances of this issue:

```
File: contracts/BondNFT.sol

68      totalShares[_asset] += shares;
115     totalShares[bond.asset] += shares-bond.shares;
117     _bond.amount += _amount;
119     _bond.period += _period;
150     totalShares[bond.asset] -= bond.shares;
180     accRewardsPerShare[bond.asset][epoch[bond.asset]] += _pendingDelta*1e18/totalShares[bond.asset];
183     bondPaid[_id][bond.asset] += amount;
221     epoch[_tigAsset] += 1;
225     accRewardsPerShare[_tigAsset][aEpoch] += _amount * 1e18 / totalShares[_tigAsset];
333     userDebt[from][bond.asset] += bond.pending;
334     bondPaid[_id][bond.asset] += bond.pending;

File: contracts/GovNFT.sol

54      userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];
68      userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];
79      userDebt[owner][assets[i]] += accRewardsPerNFT[assets[i]];       
80      userDebt[owner][assets[i]] -= userPaid[owner][assets[i]]/balanceOf(owner);
81      userPaid[owner][assets[i]] -= userPaid[owner][assets[i]]/balanceOf(owner);
96      userDebt[from][assets[i]] += accRewardsPerNFT[assets[i]];
97      userDebt[from][assets[i]] -= userPaid[from][assets[i]]/balanceOf(from);
98      userPaid[from][assets[i]] -= userPaid[from][assets[i]]/balanceOf(from);
99      userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];
278     userPaid[_msgsender][_tigAsset] += amount;
290     accRewardsPerNFT[_tigAsset] += _amount/totalSupply();

File: contracts/Lock.sol

73      totalLocked[_asset] += _amount;
103     totalLocked[asset] -= lockAmount;

File: contracts/PairsContract.sol

156     _idToOi[_asset][_tigAsset].longOi += _amount;
160     _idToOi[_asset][_tigAsset].longOi -= _amount;
176     _idToOi[_asset][_tigAsset].shortOi += _amount;
180     _idToOi[_asset][_tigAsset].shortOi -= _amount;

File: contracts/Position.sol

231     _trades[_id].accInterest -= _trades[_id].accInterest*int256(_percent)/int256(DIVISION_CONSTANT);
232     _trades[_id].margin -= _trades[_id].margin*_percent/DIVISION_CONSTANT;
```

### 10- `require()` strings longer than 32 bytes cost extra gas :

Each extra memory word of bytes past the original 32 incurs an MSTORE which costs 3 gas.

There are 4 instances of this issue :

```
File: contracts/GovNFT.sol

153     require(
            msg.value >= messageFee,
            "Must send enough value to cover messageFee"
        );
185     require(msg.sender == address(this), "NonblockingLzApp: caller must be app");
209     require(payloadHash != bytes32(0), "NonblockingLzApp: no stored message");
210     require(keccak256(_payload) == payloadHash, "NonblockingLzApp: invalid payload");
```

### 11- Splitting `require()` statements that uses && saves gas :

There are 2 instances of this issue :

```
File: contracts/PairsContract.sol

52      require(_maxLeverage >= _minLeverage && _minLeverage > 0, "Wrong leverage values");

File: contracts/Trading.sol

887     require(_proxy.proxy == _msgSender() && _proxy.time >= block.timestamp, "Proxy not approved");
```

### 12- `++i/i++` should be `unchecked{++i}/unchecked{i++}` when it is not possible for them to overflow, as in the case when used in for & while loops :

There are 16 instances of this issue:
 
```
File: contracts/GovNFT.sol

53      for (uint i=0; i<assetsLength(); i++)
67      for (uint i=0; i<assetsLength(); i++)
78      for (uint i=0; i<assetsLength(); i++)
95      for (uint i=0; i<assetsLength(); i++)
105     for (uint i=0; i<_amount; i++)
131     for (uint i=0; i<tokenId.length; i++)
200     for (uint i=0; i<tokenId.length; i++)
246     for (uint i=0; i<_ids.length; i++)
252     for (uint i=0; i<_ids.length; i++)
258     for (uint i=0; i<_ids.length; i++)
325     for (uint i=0; i<_ids.length; i++)

File: contracts/Lock.sol

113     for (uint i=0; i < assets.length; i++) 

File: contracts/Position.sol

296     for (uint i=0; i<_ids.length; i++)
304     for (uint i=0; i<_ids.length; i++)

File: contracts/Referrals.sol

70      for (uint i=0; i<_codeOwnersL; i++)
73      for (uint i=0; i<_referredAL; i++)
```