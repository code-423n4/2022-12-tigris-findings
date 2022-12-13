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
