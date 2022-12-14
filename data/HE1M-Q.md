## No. 1
It is possible to apply grieving attack during creating referral code. Whenever a user intends to create referral code by calling `createReferralCode`, the attacker applies front-run attack and calls this function with the same parameter. So, the user's transaction will be reverted and can not create a referral code. 
```
function createReferralCode(bytes32 _hash) external {
        require(_referral[_hash] == address(0), "Referral code already exists");
        _referral[_hash] = _msgSender();
        emit ReferralCreated(_msgSender(), _hash);
    }
```
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Referrals.sol#L20

It is better to add a unique data to the hash as well (like address of the caller or number of created referral code so far):
```
function createReferralCode(bytes32 _hash) external {

        uint256 newHash = uint256((_hash << 20*8)) + uint256(uint160(msg.sender));

        require(_referral[newHash] == address(0), "Referral code already exists");
        _referral[newHash] = _msgSender();
        emit ReferralCreated(_msgSender(), newHash);
    }
```

## No. 2
During handling the open fees, the `_tigAsset` is distributed to `gov`. But, it is not approved before to be consumed by `gov`. So, the first user's transaction to  initiate a market order, will fail.
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L749

During handling the close fees, the approve max is applied every time before distributing to `gov`. Actually, this is redundant to approve max every time.
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L807

So, the following check is better to be added before distributing to `gov` in both functions `_handleOpenFees` and `_handleCloseFees`:
```
if(IStable(_tigAsset).allowance(address(this), address(gov)) < _daoFeesPaid){
            IStable(_tigAsset).approve(address(gov), type(uint).max);
        }
```

## No. 3
Token id 0 is not handled during bridge minting.
```
function _bridgeMint(address to, uint256 tokenId) public {
        require(msg.sender == address(this) || _msgSender() == owner(), "NotBridge");
        require(tokenId <= 10000, "BadID");
        for (uint i=0; i<assetsLength(); i++) {
            userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];
        }
        super._mint(to, tokenId);
    }
```
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L64

While in the older version, token id 0 was excluded.
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
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFTBridged.sol#L48


