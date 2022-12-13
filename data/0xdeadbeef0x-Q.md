-----
-----


## DOS `StableVault` deposit and withdraws if ERC20 with more than 18 decimals used.

### Description

`withdraw` and `deposit` functions in `StableVault` will revert if ERC20 token with more than 18 decimals is used. 
This is due to an revert when trying to subtract the token decimals from hardcoded `18`.

`deposit`:
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/StableVault.sol#L49
```
    function deposit(address _token, uint256 _amount) public {
        require(allowed[_token], "Token not listed");
        IERC20(_token).transferFrom(_msgSender(), address(this), _amount);
        IERC20Mintable(stable).mintFor(
            _msgSender(),
            _amount*(10**(18-IERC20Mintable(_token).decimals()))
        );
    }
```

### Recommendation:

Add a check that token decimals is under 18 in `listToken`.
`withdraw`:
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/StableVault.sol#L67:
```
    function withdraw(address _token, uint256 _amount) external returns (uint256 _output) {
        IERC20Mintable(stable).burnFrom(_msgSender(), _amount);
        _output = _amount/10**(18-IERC20Mintable(_token).decimals());
        IERC20(_token).transfer(
            _msgSender(),
            _output
        );
    }
```

-----
-----


## Users borrowing against bonds will lose their rewards during borrow period

### Description

According to the sponsor `BondNFT` can be sold, transferred and used against for borrowing.
If it is used as collateral for borrowing, the user will not receive rewards for the full time of lock.

This is due to the creation of debt rewards during `_transfer` of the `BondNFT:
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L334
```
    function _transfer(
        address from,
        address to,
        uint256 _id
    ) internal override {
------
            userDebt[from][bond.asset] += bond.pending;
            bondPaid[_id][bond.asset] += bond.pending;
------
    }
```

### Hardhat POC

Add the following test to `09.Bonds.js`:
https://github.com/code-423n4/2022-12-tigris/blob/main/test/09.Bonds.js#L170
```
    it("no rewards after depositing BondNFT as colleteral", async function () {
      await stabletoken.connect(owner).mintFor(owner.address, ethers.utils.parseEther("3000"));
      await lock.connect(owner).lock(StableToken.address, ethers.utils.parseEther("3000"), 365);

      await network.provider.send("evm_increaseTime", [300]); // Skip 300 seconds
      await network.provider.send("evm_mine");

      await bond.connect(owner).safeTransferMany(user.address, [1]); // deposit BondNFT as colleteral
      
      // Distribute rewards during borrow
      await stabletoken.connect(owner).mintFor(owner.address, ethers.utils.parseEther("1000"));
      await govnft.distribute(stabletoken.address, ethers.utils.parseEther("1000"));
      await lock.connect(owner).claimGovFees();

      await network.provider.send("evm_increaseTime", [8640000]); // Skip 100 days
      await network.provider.send("evm_mine");

      await lock.connect(owner).claimGovFees();
      await bond.connect(user).safeTransferMany(owner.address, [1]);

      await network.provider.send("evm_increaseTime", [300]); // Skip 300 seconds
      await network.provider.send("evm_mine");

      await lock.connect(owner).claimGovFees();
      // User expects to get rewards distrubuted while borrowing but does not get anything
      expect(await bond.pending(1)).to.be.equals("0");
    });
```

To run the POC execute:
```
npx hardhat test --grep "no rewards after depositing BondNFT as colleteral"
```

### Recommendation:

Implemented logic to whitelist escrow accounts would be difficult and could contain more vulnerabilities, I recommend letting users know that they will lose their rewards during borrow period if using the BondNFT as collateral.

This issue is not critical, but users should be aware.


-----
-----
