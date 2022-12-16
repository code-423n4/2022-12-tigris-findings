## `require()`/`revert()` statements should have descriptive reason strings
```solidity
contracts/Trading.sol:491:            require(block.timestamp >= limitDelay[_id]);
contracts/Trading.sol:954:            require(_daoFees >= _botFees+_referralFees*2);
contracts/Trading.sol:966:            require(_percent <= DIVISION_CONSTANT);
contracts/Referrals.sol:66:        require(!isInit);
contracts/BondNFT.sol:109:        require(bond.pending == 0);
```