# 1. [G-1] For loops: increments in for loop can be uncheck to save gas

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block

    File contracts/GovNFT.sol, line 53:     for (uint i=0; i<assetsLength(); i++)
    File contracts/GovNFT.sol, line 67, 78, 95, 105, 131, 200, 246, 252, 258, 325.

    File contracts/Lock.sol, line 113:     for (uint i=0; i < assets.length; i++)

    File contracts/Position.sol, line 209:     for (uint i=0; i<_ids.length; i++)
    File contracts/Position.sol, line 304:     for (uint i=0; i<_ids.length; i++)

    File contracts/Referrals.sol, line 70, 73.     

The code would go from:

    for (uint256 i; i < numIterations; i++) { 
      ...
    }

to:

    for (uint256 i; i < numIterations;) { 
      ...
      unchecked { ++i; }  
    }

# 2. [G-2] Variables: "Constant" expressions are expressions, not constants

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.

If the variable was immutable instead: the calculation would only be done once at deploy time (in the constructor), and then the result would be saved and read directly at runtime rather than being recalculated.

Instances include:

    File contracts/BondNFT.sol, line 10:             uint constant private DAY = 24 * 60 * 60;

Change these expressions from `constant` to `immutable` and implement the calculation in the constructor, or hardcode these values in the constants and add a comment to say how the value was calculated.

# 3. [G-3] Avoid changing storage data.

Changing storage data costs a lot more gas than changing memory or stack variables so you should update the storage variable after all the calculations rather than updating it on every calculation.

Instances include:

    File: contracts/BondNFT.sol, line 53 - 55:             
                for (uint i=0; i<assetsLength(); i++) {
                    userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];
                }

    File: contracts/BondNFT.sol, line 67 - 69:             
                for (uint i=0; i<assetsLength(); i++) {
                    userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];
                }

I suggest:

                mapping(address => uint256) memory _userPaid = userPaid[to];
                mapping(address => uint256) memory _accRewardsPerNFT = accRewardsPerNFT;
                for (uint i=0; i<assetsLength(); i++) {
                    _userPaid[assets[i]] += _accRewardsPerNFT[assets[i]];
                }
                userPaid[to] = _userPaid;

    File: contracts/BondNFT.sol, line 78- 82:            
                        for (uint i=0; i<assetsLength(); i++) {
                            userDebt[owner][assets[i]] += accRewardsPerNFT[assets[i]];
                            userDebt[owner][assets[i]] -= userPaid[owner][assets[i]]/balanceOf(owner);
                            userPaid[owner][assets[i]] -= userPaid[owner][assets[i]]/balanceOf(owner);            
                        }

I suggest:

                        mapping(address => uint256) _userDebt = userDebt[owner];
                        mapping(address => uint256) _userPaid = userPaid[owner];
                        uint256 balanceOfOwner = balanceOf(owner);
                        mapping(address => uint256) memory _accRewardsPerNFT = accRewardsPerNFT;      
                        for (uint i = 0; i < assetsLength(); i++) {
                            _userDebt[assets[i]] += _accRewardsPerNFT[assets[i]];
                            uint256 balance= _userPaid[assets[i]]/balanceOfOwner;
                            _userDebt[assets[i]] -= balance;
                            _userPaid[assets[i]] -= balance;            
                        }
                        userDebt[owner] = _userDebt;
                        userPaid[owner] = _userPaid;