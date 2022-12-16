# Tigris Trade Gas Optimization Findings
## Summary
The Gas savings are calculated using the `npx hardhat test` command.  
The same tests were used that are provided in the contest repository.

| Issue      | Title | File | Instances | Estimated Gas Savings (Deployments) | Estimated Gas Savings (Method calls) |
| ----------- | ----------- | ----------- | ----------- | ----------- | ----------- |
| G-00 | Simplify `_pendingDelta` calculation | BondNFT.sol | 1 | 27838 | 478 |

## [G-00] Simplify `_pendingDelta` calculation
The code in `BondNFT.claim` ([https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L168-L187](https://github.com/code-423n4/2022-12-tigris/blob/496e1974ee3838be8759e7b4096dbee1b8795593/contracts/BondNFT.sol#L168-L187)) to calculate `_pendingDelta` can be simplified like this:  

```solidity
             if (bond.expired) {
-                uint _pendingDelta = (bond.shares * accRewardsPerShare[bond.asset][epoch[bond.asset]] / 1e18 - bondPaid[_id][bond.asset]) - (bond.shares * accRewardsPerShare[bond.asset][bond.expireEpoch-1] / 1e18 - bondPaid[_id][bond.asset]);
+                uint _pendingDelta = ((bond.shares * accRewardsPerShare[bond.asset][epoch[bond.asset]]) - 
+                (bond.shares * accRewardsPerShare[bond.asset][bond.expireEpoch-1]))  / 1e18;
                 if (totalShares[bond.asset] > 0) {
                     accRewardsPerShare[bond.asset][epoch[bond.asset]] += _pendingDelta*1e18/totalShares[bond.asset];
                 }
```


Deployment Gas saved: **27838**  
Method call Gas saved: **478**  