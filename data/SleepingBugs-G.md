
## `<X> += <Y>` costs more gas than `<X> = <X> + <Y>` for state variables
### Summary
`x+=y` costs more gas than x=x+y for state variables

### Details
Same goes for `-=`
### Github Permalinks
- `+=`
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L102
                accInterestPerOi[_asset][_tigAsset][true] += ((int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(longOi[_asset][_tigAsset]))*int256(1e10-vaultFundingPercent[_asset][_tigAsset])/1e10;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L109
                accInterestPerOi[_asset][_tigAsset][false] += ((int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(shortOi[_asset][_tigAsset]))*int256(1e10-vaultFundingPercent[_asset][_tigAsset])/1e10;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/PairsContract.sol#L156
            _idToOi[_asset][_tigAsset].longOi += _amount;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/PairsContract.sol#L176
            _idToOi[_asset][_tigAsset].shortOi += _amount;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L52
        counter += 1;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L54
            userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L68
            userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L79
            userDebt[owner][assets[i]] += accRewardsPerNFT[assets[i]];

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L96
            userDebt[from][assets[i]] += accRewardsPerNFT[assets[i]];

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L99
            userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L278
        userPaid[_msgsender][_tigAsset] += amount;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L290
            accRewardsPerNFT[_tigAsset] += _amount/totalSupply();

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Lock.sol#L73
        totalLocked[_asset] += _amount;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L68
            totalShares[_asset] += shares;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L115
            totalShares[bond.asset] += shares-bond.shares;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L117
            _bond.amount += _amount;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L119
            _bond.period += _period;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L180
                    accRewardsPerShare[bond.asset][epoch[bond.asset]] += _pendingDelta*1e18/totalShares[bond.asset];

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L183
            bondPaid[_id][bond.asset] += amount;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L221
                    epoch[_tigAsset] += 1;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L225
            accRewardsPerShare[_tigAsset][aEpoch] += _amount * 1e18 / totalShares[_tigAsset];

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L333
            userDebt[from][bond.asset] += bond.pending;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L334
            bondPaid[_id][bond.asset] += bond.pending;

- `-=`
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L104
            accInterestPerOi[_asset][_tigAsset][false] -= (int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(shortOi[_asset][_tigAsset]);

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L107
            accInterestPerOi[_asset][_tigAsset][true] -= (int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(longOi[_asset][_tigAsset]);

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L231
        _trades[_id].accInterest -= _trades[_id].accInterest*int256(_percent)/int256(DIVISION_CONSTANT);

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L232
        _trades[_id].margin -= _trades[_id].margin*_percent/DIVISION_CONSTANT;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/PairsContract.sol#L160
            _idToOi[_asset][_tigAsset].longOi -= _amount;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/PairsContract.sol#L180
            _idToOi[_asset][_tigAsset].shortOi -= _amount;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L80
            userDebt[owner][assets[i]] -= userPaid[owner][assets[i]]/balanceOf(owner);

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L81
            userPaid[owner][assets[i]] -= userPaid[owner][assets[i]]/balanceOf(owner);            

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L97
            userDebt[from][assets[i]] -= userPaid[from][assets[i]]/balanceOf(from);

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L98
            userPaid[from][assets[i]] -= userPaid[from][assets[i]]/balanceOf(from);

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Lock.sol#L103
        totalLocked[asset] -= lockAmount;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L150
            totalShares[bond.asset] -= bond.shares;
### Mitigation
Don't use `+=` for state variables as it cost more gas.


## Functions guaranteed to revert when called by normal users can be marked `payable`
### Summary
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function.

Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

### Details
The extra opcodes avoided are:
CALLVALUE (2), DUP1 (3), ISZERO (3), PUSH2 (3), JUMPI (10), PUSH1 (3), DUP1 (3), REVERT(0), JUMPDEST (1), POP (2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

### Github Permalinks

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L902
        onlyOwner

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L917
        onlyOwner

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L930
        onlyOwner

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L939
    function setLimitOrderPriceRange(uint _range) external onlyOwner {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L952
    function setFees(bool _open, uint _daoFees, uint _burnFees, uint _referralFees, uint _botFees, uint _percent) external onlyOwner {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L977
    ) external onlyOwner() {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/TradingExtension.sol#L144
    function setMaxGasPrice(uint _maxGasPrice) external onlyOwner {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/TradingExtension.sol#L226
        onlyOwner

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/TradingExtension.sol#L231
    function setChainlinkEnabled(bool _bool) external onlyOwner {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/TradingExtension.sol#L240
    function setNode(address _node, bool _bool) external onlyOwner {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/TradingExtension.sol#L254
        onlyOwner

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/TradingExtension.sol#L269
        onlyOwner

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/TradingExtension.sol#L274
    function setPaused(bool _paused) external onlyOwner {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L85
    function setBaseURI(string memory _newBaseURI) external onlyOwner {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L310
    function setMinter(address _minter, bool _bool) external onlyOwner {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/PairsContract.sol#L33
    function setAssetChainlinkFeed(uint256 _asset, address _feed) external onlyOwner {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/PairsContract.sol#L48
    function addAsset(uint256 _asset, string memory _name, address _chainlinkFeed, uint256 _minLeverage, uint256 _maxLeverage, uint256 _feeMultiplier, uint256 _baseFundingRate) external onlyOwner {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/PairsContract.sol#L73
    function updateAssetLeverage(uint256 _asset, uint256 _minLeverage, uint256 _maxLeverage) external onlyOwner {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/PairsContract.sol#L92
    function setAssetBaseFundingRate(uint256 _asset, uint256 _baseFundingRate) external onlyOwner {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/PairsContract.sol#L104
    function updateAssetFeeMultiplier(uint256 _asset, uint256 _feeMultiplier) external onlyOwner {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/PairsContract.sol#L115
    function pauseAsset(uint256 _asset, bool _isPaused) external onlyOwner {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/PairsContract.sol#L125
    function setMaxBaseFundingRate(uint256 _maxBaseFundingRate) external onlyOwner {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/PairsContract.sol#L129
    function setProtocol(address _protocol) external onlyOwner {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/PairsContract.sol#L139
    function setMaxOi(uint256 _asset, address _tigAsset, uint256 _maxOi) external onlyOwner {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Referrals.sol#L53
    function setProtocol(address _protocol) external onlyOwner {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Referrals.sol#L65
    ) external onlyOwner {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L46
    function setBaseURI(string calldata _newBaseURI) external onlyOwner {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L104
    function mintMany(uint _amount) external onlyOwner {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L110
    function mint() external onlyOwner {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L114
    function setTrustedAddress(uint16 _chainId, address _contract, bool _bool) external onlyOwner {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L236
    function setGas(uint _gas) external onlyOwner {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L240
    function setEndpoint(ILayerZeroEndpoint _endpoint) external onlyOwner {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L300
    function addAsset(address _asset) external onlyOwner {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L307
    function setAllowedAsset(address _asset, bool _bool) external onlyOwner {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L311
    function setMaxBridge(uint256 _max) external onlyOwner {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/StableToken.sol#L43
        onlyOwner()

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/StableVault.sol#L78
    function listToken(address _token) external onlyOwner {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/StableVault.sol#L89
    function delistToken(address _token) external onlyOwner {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Lock.sol#L130
    ) external onlyOwner() {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Lock.sol#L140
    ) external onlyOwner() {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L349
    function addAsset(address _asset) external onlyOwner {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L357
    function setAllowedAsset(address _asset, bool _bool) external onlyOwner {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L362
    function setBaseURI(string calldata _newBaseURI) external onlyOwner {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L368
    ) public onlyOwner() {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/utils/MetaContext.sol#L9
    function setTrustedForwarder(address _forwarder, bool _bool) external onlyOwner {

### Mitigation
Consider adding payable to save gas

## splitting `require()` statements that use `&&` saves gas
### Summary
Instead of using the && operator in a single require statement to check multiple conditions, consider using multiple require statements with 1 condition per require statement (saving 3 gas per & ):
### Github Permalinks

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L887
            require(_proxy.proxy == _msgSender() && _proxy.time >= block.timestamp, "Proxy not approved");

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/utils/TradingLibrary.sol#L117
                    _priceData.price < assetChainlinkPrice+assetChainlinkPrice*2/100 &&

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/PairsContract.sol#L52
        require(_maxLeverage >= _minLeverage && _minLeverage > 0, "Wrong leverage values");

### Mitigation
Split require statements


## Reduce the size of error messages (Long revert Strings)
### NOTE
1 case not notice for custom errors by Picodes C4 4naly3er
3 noticed but attached as I consider this a different gas optimization (even so similar to custom errors)

My recommendation is to use custom errors since version 0.8.4, however, in case of wanting to keep regular messages, consider reducing length of them

### Summary
Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.
Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

### Github Permalinks
NOTE: This wasn't notice by Picodes C4 4naly3er
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L155

NOTE: These are noticed by Picodes C4 4naly3er
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L185

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L209

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L210

### Mitigation
Consider shortening the revert strings to fit in 32 bytes

## Increments can be `unchecked` in loops
### Summary
Unchecked operations as the `++i` on for loops are cheaper than checked one.

### Details
In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline..

    The code would go from:
```
    for (uint256 i; i < numIterations; i++) {
    // ...
    }
```

    to
```
    for (uint256 i; i < numIterations;) {
      // ...
      unchecked { ++i; }
    }
    The risk of overflow is inexistent for a uint256 here.
```

### Github Permalinks
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L296
        for (uint i=0; i<_ids.length; i++) {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L304
        for (uint i=0; i<_ids.length; i++) {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Referrals.sol#L70
        for (uint i=0; i<_codeOwnersL; i++) {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Referrals.sol#L73
        for (uint i=0; i<_referredAL; i++) {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L53
        for (uint i=0; i<assetsLength(); i++) {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L67
        for (uint i=0; i<assetsLength(); i++) {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L78
        for (uint i=0; i<assetsLength(); i++) {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L95
        for (uint i=0; i<assetsLength(); i++) {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L105
        for (uint i=0; i<_amount; i++) {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L131
        for (uint i=0; i<tokenId.length; i++) {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L200
        for (uint i=0; i<tokenId.length; i++) {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L246
        for (uint i=0; i<_ids.length; i++) {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L252
        for (uint i=0; i<_ids.length; i++) {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L258
        for (uint i=0; i<_ids.length; i++) {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L325
        for (uint i=0; i<_ids.length; i++) {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Lock.sol#L113
        for (uint i=0; i < assets.length; i++) {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L220
                for (uint i=epoch[_tigAsset]; i<aEpoch; i++) {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L284
            for (uint i=0; i<_ids.length; i++) {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L292
            for (uint i=0; i<_ids.length; i++) {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L300
            for (uint i=0; i<_ids.length; i++) {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L342
            for (uint i=0; i<_ids.length; i++) {


### Mitigation
Add unchecked `++i` at the end of all the for loop where it's not expected to overflow and remove them from the for header


## Store using `Struct` over multiple `mappings`
### Summary
All these variables could be combine in a `Struct` in order to reduce the gas cost. 

### Details
As noticed in: 
https://gist.github.com/alexon1234/b101e3ac51bea3cbd9cf06f80eaa5bc2

When multiple `mappings` that access the same `addresses`, `uints`, etc, all of them can be mixed into an `struct` and then that data accessed like:
`mapping(datatype => newStructCreated) newStructMap;`

Also, you have this post where it explains the benefits of using `Structs` over `mappings` 
https://medium.com/@novablitz/storing-structs-is-costing-you-gas-774da988895e

### Github Permalinks

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L130
    mapping(uint => Delay) public blockDelayPassed; // id => Delay

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L132
    mapping(uint => uint) public limitDelay; // id => block.timestamp

- - - - -

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L134
    mapping(address => bool) public allowedVault;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L141
    mapping(address => Proxy) public proxyApprovals;

- - - - -

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/TradingExtension.sol#L17
    mapping(address => bool) private isNode;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/TradingExtension.sol#L18
    mapping(address => uint) public minPositionSize;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/TradingExtension.sol#L19
    mapping(address => bool) public allowedMargin;

- - - - -

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L18
    mapping(uint => mapping(address => uint)) public vaultFundingPercent;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L21
    mapping(uint256 => Trade) private _trades; // NFT id to Trade

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L24
    mapping(uint256 => uint256) private _openPositionsIndexes;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L26
    mapping(uint256 => uint256[]) private _assetOpenPositions;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L27
    mapping(uint256 => mapping(uint256 => uint256)) private _assetOpenPositionsIndexes;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L29
    mapping(uint256 => uint256[]) private _limitOrders; // List of limit order nft ids per asset

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L30
    mapping(uint256 => mapping(uint256 => uint256)) private _limitOrderIndexes; // Keeps track of asset -> id -> array index

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L33
    mapping(uint256 => mapping(address => int256)) public fundingDeltaPerSec;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L34
    mapping(uint256 => mapping(address => mapping(bool => int256))) private accInterestPerOi;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L35
    mapping(uint256 => mapping(address => uint256)) private lastUpdate;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L36
    mapping(uint256 => int256) private initId;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L37
    mapping(uint256 => mapping(address => uint256)) private longOi;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L38
    mapping(uint256 => mapping(address => uint256)) private shortOi;

- - - - -

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/PairsContract.sol#L12
    mapping(uint256 => bool) public allowedAsset;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/PairsContract.sol#L16
    mapping(uint256 => Asset) private _idToAsset;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/PairsContract.sol#L21
    mapping(uint256 => mapping(address => OpenInterest)) private _idToOi;

- - - - -

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L22
    mapping(uint16 => mapping(address => bool)) public isTrustedAddress;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L23
    mapping(uint16 => mapping(bytes => mapping(uint64 => bytes32))) public failedMessages;

- - - - -

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L265
    mapping(address => bool) private _allowedAsset;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L266
    mapping(address => uint) private assetsIndex;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L267
    mapping(address => mapping(address => uint256)) private userPaid;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L268
    mapping(address => mapping(address => uint256)) private userDebt;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L269
    mapping(address => uint256) private accRewardsPerNFT;

- - - - -

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/StableToken.sol#L9
    mapping(address => bool) public isMinter;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/StableVault.sol#L29
    mapping(address => bool) public allowed;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/StableVault.sol#L30
    mapping(address => uint) private tokenIndex;

- - - - -

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Lock.sol#L18
    mapping(address => bool) public allowedAssets;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Lock.sol#L19
    mapping(address => uint) public totalLocked;

- - - - -

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L34
    mapping(uint256 => mapping(address => uint256)) private bondPaid;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L36
    mapping(uint => Bond) private _idToBond;

- - - - -

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L35
    mapping(address => mapping(uint256 => uint256)) private accRewardsPerShare; // tigAsset => epoch => accRewardsPerShare

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L26
    mapping(address => uint256) public epoch;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L32
    mapping(address => bool) public allowedAsset;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L33
    mapping(address => uint) private assetsIndex;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L37
    mapping(address => uint) public totalShares;

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L38
    mapping(address => mapping(address => uint)) public userDebt; // user => tigAsset => amount
der;


- - - 

### Mitigation
Consider mixing different mappings into an struct when able in order to save gas.


## Pack structs tightly
### Summary
Gas efficiency can be achieved by tightly packing the struct. Struct variables are stored in 32 bytes each so you can group smaller types to occupy less storage. 

### Details
You can read more here: https://fravoll.github.io/solidity-patterns/tight_variable_packing.html or in the official documentation: https://docs.soliditylang.org/en/v0.4.21/miscellaneous.html
### Github Permalinks
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/interfaces/IPosition.sol#L7
    struct Trade {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/interfaces/IPosition.sol#L22
    struct MintTrade {

### Mitigation
Order structs to reduce gas usage.


## `abi.encode()` is less gas efficient than `abi.encodePacked()`
### Summary
In general, `abi.encodePacked` is more gas-efficient.

### Details
Changing the abi.encode function to `abi.encodePacked` can save gas since the abi.encode function pads extra null bytes at the end of the call data, which is unnecessary. Also, in general, `abi.encodePacked` is more gas-efficient.
### Github Permalinks


https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/utils/TradingLibrary.sol#L103
            keccak256(abi.encode(_priceData))

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L141
        bytes memory payload = abi.encode(_to, tokenId);


### Mitigation
Consider changing abi.encode to `abi.encodePacked`



## else cases should include revert in order to save gas
### Summary
There are if pathes that when else, doesn't do a thing, however, code finishes and remaining gas doesn't return.
### Github Permalinks
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/utils/TradingLibrary.sol#L112-L121
### Recommendation
Revert in this cases


## Variables should be cached when used several times
### Summary
Variables read more than once improves gas usage when cached into local variable
### Details
In loops or state variables, this is even more gas saving

### Github Permalinks
State variables that can be cached: `_trades` `_limitOrders` `_openPositionsIndexes` `_openPositions_limitOrderIndexes` `_assetOpenPositionsIndexes` `_assetOpenPositions` `_asset` `_id`

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Position.sol#L260-L280

### Mitigation
Cache variables used more than one into a local variable.

## `require()` or `revert()` statements that check input arguments should be at the top of the function
### Summary
Checks that involve constants and input parameters should come before checks that involve state variables.
### Github Permalinks
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/utils/TradingLibrary.sol#L107-L111
### Mitigation
Add requires/reverts before accessing state variable / doing some operations in order to save more gas in case of REVERT

## Retrieve internal variables directly
### Summary
Save `~30 gas` per call to each function

### Details
use `assets.length;` rather than `assetsLength()`
### Github Permalinks
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L53
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L67
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L78
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L94
### Mitigation
Retrieve them directly

