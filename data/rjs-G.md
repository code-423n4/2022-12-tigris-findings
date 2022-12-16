# Introduction

The Tigris Trade code-base already makes use of many well-known gas optimization techniques. Consequently it was a challenge to improve upon this baseline, but I do believe there remain a number of areas of improvement.

# Summary

| ID   | Finding                                                               | Instances |
| ---- | --------------------------------------------------------------------- | --------- |
| G-01 | Use immutable variables whenever possible                             | 2         |
| G-02 | Cache access of mappings and arrays / use storage pointer             | 15        |
| G-03 | Use storage pointer instead of memory for sparely accessed structures | 2         |
| G-04 | Use named returns wherever possible                                   | 4         |
| G-05     | Ternary operator is redundant for complementary boolean outcomes                                                                      |  2         |

# Gas Optimization Findings

## \[G-01\] Use immutable variables whenever possible

Deployment gas savings across all instances: 🔴
Transaction gas savings across all instances: 🟢 (est. ~50,000)

The following variables are set once in the constructor, and can therefore be declared as immutable:

Before:
```solidity
contracts/Trading.sol
122:     IPosition private position;
123:     IGovNFT private gov;
```

After:
```solidity
contracts/Trading.sol
122:     IPosition immutable private position;
123:     IGovNFT immutable private gov;
```

Note that `immutable` **increases** deployment gas in this scenario from 5,234,995 to 5,356,998 (🔴 122,003 gas).

**However** there are substantial gas savings on average user transactions, ranging between 2000 tto 4600 gas, and therefore I nevertheless strongly recommend proceeding with this optimization:
```javascript
·-------------------------------------------------|---------------------------|-----------------|---------------------------|-----------------|
|              Solc version: 0.8.17               ·  BEFORE                                     ·  AFTER                                      · AVG SAVINGS
······················|···························|·············|·············|·················|·············|·············|·················|
|  Contract           ·  Method                   ·  Min        ·  Max        ·  Avg            ·  Min        ·  Max        ·  Avg            ·
······················|···························|·············|·············|·················|·············|·············|··················
|  Trading            ·  addMargin                ·     127839  ·     196857  ·         172769  ·     125557  ·     194575  ·         170640  │ 🟢 2129
······················|···························|·············|·············|·················|·············|·············|··················
|  Trading            ·  addToPosition            ·     366908  ·     367393  ·         367151  ·     362290  ·     362775  ·         362533  │ 🟢 4618
······················|···························|·············|·············|·················|·············|·············|··················
|  Trading            ·  cancelLimitOrder         ·          -  ·          -  ·         153308  ·          -  ·          -  ·         151483  │ 🟢 1825
······················|···························|·············|·············|·················|·············|·············|··················
|  Trading            ·  executeLimitOrder        ·     517949  ·     518013  ·         517981  ·     513531  ·     513595  ·         513563  │ 🟢 4418
······················|···························|·············|·············|·················|·············|·············|··················
|  Trading            ·  initiateCloseOrder       ·     233288  ·     437550  ·         350228  ·     231296  ·     432805  ·         346322  │ 🟢 3906
······················|···························|·············|·············|·················|·············|·············|··················
|  Trading            ·  initiateLimitOrder       ·     453824  ·     596830  ·         585287  ·     451549  ·     594555  ·         583012  │ 🟢 2275
······················|···························|·············|·············|·················|·············|·············|··················
|  Trading            ·  initiateMarketOrder      ·     706170  ·     882365  ·         828297  ·     701577  ·     877772  ·         823704  │ 🟢 4593
······················|···························|·············|·············|·················|·············|·············|··················
|  Trading            ·  limitClose               ·     371569  ·     374171  ·         372871  ·     367914  ·     370516  ·         369216  │ 🟢 3655
······················|···························|·············|·············|·················|·············|·············|··················
|  Trading            ·  liquidatePosition        ·     321189  ·     321406  ·         321298  ·     316831  ·     317529  ·         317180  │ 🟢 4118
······················|···························|·············|·············|·················|·············|·············|··················
|  Trading            ·  removeMargin             ·     209350  ·     212524  ·         210937  ·     207071  ·     210701  ·         208886  │ 🟢 2051
······················|···························|·············|·············|·················|·············|·············|··················
```

---

## \[G-02\] Cache access of mappings and arrays / use storage pointer

Deployment gas savings across all instances: 🟢 (est. ~50,000)
Transaction gas savings  across all instances: 🟢 (est. ~5,000)

When referencing the same mapping in a loop, a simple optimization is to cache access of mappings and arrays.

Additionally, in the example below, since the mapping needs to be modified in storage, the `target` variable must be marked as `storage`.

### Example

Before
```solidity
contracts/BondNFT.sol
217:         unchecked {
218:             uint aEpoch = block.timestamp / DAY;
219:             if (aEpoch > epoch[_tigAsset]) {
220:                 for (uint i=epoch[_tigAsset]; i<aEpoch; i++) {
221:                     epoch[_tigAsset] += 1;
222:                     accRewardsPerShare[_tigAsset][i+1] = accRewardsPerShare[_tigAsset][i];
223:                 }
224:             }
225:             accRewardsPerShare[_tigAsset][aEpoch] += _amount * 1e18 / totalShares[_tigAsset];
226:         }
```

After:
- Deployment:  4,429,164 -> 4,427,436 (🟢 1,728 gas savings)
- Transaction:  134,337 -> 134,115 (🟢 222 avg. gas savings)
```solidity
contracts/BondNFT.sol
218:             mapping(uint256 => uint256) storage target = accRewardsPerShare[_tigAsset];
219:             uint aEpoch = block.timestamp / DAY;
220:             if (aEpoch > epoch[_tigAsset]) {
221:                 for (uint i=epoch[_tigAsset]; i<aEpoch; i++) {
222:                     epoch[_tigAsset] += 1;
223:                     target[i+1] = target[i];
224:                 }
225:             }
226:             target[aEpoch] += _amount * 1e18 / totalShares[_tigAsset];
227:         }
```

### Other instances

- `\_msgSender()` can be cached:

```solidity
contracts/BondNFT.sol
284:             for (uint i=0; i<_ids.length; i++) {
285:                 _transfer(_msgSender(), _to, _ids[i]);

contracts/GovNFT.sol
105:         for (uint i=0; i<_amount; i++) {
106:             _mint(_msgSender(), counter);
...
131:         for (uint i=0; i<tokenId.length; i++) {
132:             require(_msgSender() == ownerOf(tokenId[i]), "Not the owner");
...
246:         for (uint i=0; i<_ids.length; i++) {
247:             _transfer(_msgSender(), _to, _ids[i]);

contracts/GovNFTBridged.sol
93:         for (uint i=0; i<tokenId.length; i++) {
94:             require(_msgSender() == ownerOf(tokenId[i]), "Not the owner");
...
206:         for (uint i=0; i<_ids.length; i++) {
207:             _transfer(_msgSender(), _to, _ids[i]);
```

- `assets[i]` can be cached:

```solidity
contracts/GovNFT.sol
53:         for (uint i=0; i<assetsLength(); i++) {
54:             userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];
...
67:         for (uint i=0; i<assetsLength(); i++) {
68:             userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];
...
78:         for (uint i=0; i<assetsLength(); i++) {
79:             userDebt[owner][assets[i]] += accRewardsPerNFT[assets[i]];
80:             userDebt[owner][assets[i]] -= userPaid[owner][assets[i]]/balanceOf(owner);
81:             userPaid[owner][assets[i]] -= userPaid[owner][assets[i]]/balanceOf(owner);    
...
95:         for (uint i=0; i<assetsLength(); i++) {
96:             userDebt[from][assets[i]] += accRewardsPerNFT[assets[i]];
97:             userDebt[from][assets[i]] -= userPaid[from][assets[i]]/balanceOf(from);
98:             userPaid[from][assets[i]] -= userPaid[from][assets[i]]/balanceOf(from);
99:             userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];

contracts/GovNFTBridged.sol
51:         for (uint i=0; i<assetsLength(); i++) {
52:             userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];
...
59:         for (uint i=0; i<assetsLength(); i++) {
60:             userDebt[owner][assets[i]] += accRewardsPerNFT[assets[i]];
61:             userDebt[owner][assets[i]] -= userPaid[owner][assets[i]]/balanceOf(owner);
62:             userPaid[owner][assets[i]] -= userPaid[owner][assets[i]]/balanceOf(owner);            
...
73:         for (uint i=0; i<assetsLength(); i++) {
74:             userDebt[from][assets[i]] += accRewardsPerNFT[assets[i]];
75:             userDebt[from][assets[i]] -= userPaid[from][assets[i]]/balanceOf(from);
76:             userPaid[from][assets[i]] -= userPaid[from][assets[i]]/balanceOf(from);
77:             userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];

contracts/Lock.sol
113:         for (uint i=0; i < assets.length; i++) {
114:             uint balanceBefore = IERC20(assets[i]).balanceOf(address(this));
115:             IGovNFT(govNFT).claim(assets[i]);
116:             uint balanceAfter = IERC20(assets[i]).balanceOf(address(this));
117:             IERC20(assets[i]).approve(address(bondNFT), type(uint256).max);
118:             bondNFT.distribute(assets[i], balanceAfter - balanceBefore);
```

---

## \[G-03\] Use storage pointer instead of memory for sparely accessed structures

When referencing only a few fields from a complex structure, using a `storage` pointer is more efficient than `memory` because memory causes a number of loads from storage for data that is never used.

### Example

Before:
```solidity
contracts/BondNFT.sol
251:         Bond memory bond = _idToBond[_id];
```

After:
- Deployment:  4,429,164 -> 4,403,231 (🟢 25,933 gas savings)
```solidity
contracts/BondNFT.sol
251:         Bond storage bond = _idToBond[_id];
```

### Other instances
```solidity
contracts/Trading.sol
859:             Delay storage _delay = blockDelayPassed[_id];
```

---

## \[G-04\] Use named returns wherever possible

Named returns not only save deployment gas and improve code clarity, but reduce the number of lines of code, which will result in a cheaper audit next time around.

### Example

Before:
```solidity
contracts/GovNFT.sol
323:     function balanceIds(address _user) external view returns (uint[] memory) {
324:         uint[] memory _ids = new uint[](balanceOf(_user));
325:         for (uint i=0; i<_ids.length; i++) {
326:             _ids[i] = tokenOfOwnerByIndex(_user, i);
327:         }
328:         return _ids;
329:     }
```

After:
- Deployment:  5071743 -> 5069811 (🟢 1,932 gas savings)
- Transaction:  69324 -> 62672 (🟢 6,652 avg. gas savings -- distribute())
```solidity
contracts/GovNFT.sol
323:     function balanceIds(address _user) external view returns (uint[] memory _ids) {
324:         _ids = new uint[](balanceOf(_user));
325:         for (uint i=0; i<_ids.length; i++) {
326:             _ids[i] = tokenOfOwnerByIndex(_user, i);
327:         }
328:     }
```

### Other instances

```solidity
contracts/Position.sol
41:     function trades(uint _id) public view returns (Trade memory) {
42:         Trade memory _trade = _trades[_id];
...
64:     return _trade;

// can become

41:     function trades(uint _id) public view returns (Trade memory _trade) {
42:         _trade = _trades[_id];
```

```solidity
contracts/Position.sol
294:     function userTrades(address _user) external view returns (uint[] memory) {
295:         uint[] memory _ids = new uint[](balanceOf(_user));
...
302:     function openPositionsSelection(uint _from, uint _to) external view returns (uint[] memory) {
303:         uint[] memory _ids = new uint[](_to-_from);
```

### Redundant

`return` is redundant when just return a named return variable:
```solidity
contracts/Trading.sol
762:     function _handleCloseFees(
763:         uint _asset,
764:         uint _payout,
765:         address _tigAsset,
766:         uint _positionSize,
767:         address _trader,
768:         bool _isBot
769:     )
770:         internal
771:         returns (uint payout_)
772:     {
...
809:         return payout_; // <-- can remove
```

---

## \[G-05\] Ternary operator is redundant for complementary boolean outcomes

The ternary operator is used in expressions of the form `<expression> ? <trueExpression> : <falseExpression>`. 

When `trueExpression` and `falseExpression` are `true`/`false` or `false/true`, then the ternary operator is redundant.

### Example

Before:
```solidity
contracts/BondNFT.sol
238:         bond.expired = bond.expireEpoch <= epoch[bond.asset] ? true : false;
```

After:
- Deployment:  4,429,164 -> 4,419,460 (🟢 9,704 gas savings)
- Transaction:  246,499 -> 246,343 (🟢 156 gas savings)
```solidity
contracts/BondNFT.sol
238:         bond.expired = bond.expireEpoch <= epoch[bond.asset];
```

### Other instances

```solidity
contracts/BondNFT.sol
252:         return bond.expireEpoch <= epoch[bond.asset] ? true : false;
```