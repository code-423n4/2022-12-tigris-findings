## Input Validation Missing on _baseFundingRate

Contract:
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L62

Issue:
It was observed that `addAsset` function is missing a necessary max cap check for _baseFundingRate. This could allow to set `_baseFundingRate` which is far higher than `maxBaseFundingRate`

```
function addAsset(uint256 _asset, string memory _name, address _chainlinkFeed, uint256 _minLeverage, uint256 _maxLeverage, uint256 _feeMultiplier, uint256 _baseFundingRate) external onlyOwner {
...
_idToAsset[_asset].baseFundingRate = _baseFundingRate;
...
}    

```

Recommendation:
Add below check in 

```
function addAsset(uint256 _asset, string memory _name, address _chainlinkFeed, uint256 _minLeverage, uint256 _maxLeverage, uint256 _feeMultiplier, uint256 _baseFundingRate) external onlyOwner {
require(_baseFundingRate<= maxBaseFundingRate, "Incorrect funding rate");
...
}
```

## Input Validation missing while setting _referred - Self referral allowed

Contract:
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol#L74

Issue:
It was observed that while initializing referrals using `initRefs` function, it is not checked whether self referral is being made. In below case it is not checked whether user is referring themselves

```
for (uint i=0; i<_referredAL; i++) {
            _referred[_referredA[i]] = _referredTo[i];
        }
```

Recommendation:
Kindly add below checks:

```
function initRefs(
        address[] memory _codeOwners,
        bytes32[] memory _ownedCodes,
        address[] memory _referredA,
        bytes32[] memory _referredTo
    ) external onlyOwner {
        require(!isInit);
        isInit = true;
        uint _codeOwnersL = _codeOwners.length;
        uint _referredAL = _referredA.length;
        for (uint i=0; i<_codeOwnersL; i++) {
            _referral[_ownedCodes[i]] = _codeOwners[i];
        }
        for (uint i=0; i<_referredAL; i++) {
if (_referredA[i] == _referral[_referredTo[i]]) {
            continue;
        }
_referred[_referredA[i]] = _referredTo[i];
        }
    }
```