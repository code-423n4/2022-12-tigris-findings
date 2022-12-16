### [G-01] ```abi.encode()``` is less efficient than ```abi.encodePacked()```


#### Impact
Consider using ```abi.encodePacked()``` instead for efficieny.


#### Findings:
```
contracts/GovNFT.sol:L141        bytes memory payload = abi.encode(_to, tokenId);

contracts/utils/TradingLibrary.sol:L103            keccak256(abi.encode(_priceData))

```

### [G-02] Empty blocks should be removed or emit something


#### Impact
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.


#### Findings:
```
contracts/StableToken.sol:L11    constructor(string memory name_, string memory symbol_) ERC20Permit(name_) ERC20(name_, symbol_) {}

```

### [G-03] Use a more recent version of solidity.


#### Impact
Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining 
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads 
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings 
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value.


#### Findings:
```
contracts/Lock.sol:L2      pragma solidity ^0.8.0;

contracts/BondNFT.sol:L2      pragma solidity ^0.8.0;

contracts/StableToken.sol:L2      pragma solidity ^0.8.0;

contracts/StableVault.sol:L2      pragma solidity ^0.8.0;

contracts/Position.sol:L2      pragma solidity ^0.8.0;

contracts/TradingExtension.sol:L2      pragma solidity ^0.8.0;

contracts/Referrals.sol:L2      pragma solidity ^0.8.0;

contracts/PairsContract.sol:L2      pragma solidity ^0.8.0;

contracts/GovNFT.sol:L2      pragma solidity ^0.8.0;

contracts/Trading.sol:L2      pragma solidity ^0.8.0;

contracts/utils/MetaContext.sol:L2      pragma solidity ^0.8.0;

contracts/utils/TradingLibrary.sol:L2      pragma solidity ^0.8.0;

contracts/interfaces/IStableVault.sol:L3      pragma solidity ^0.8.0;

contracts/interfaces/IPosition.sol:L3      pragma solidity ^0.8.0;

contracts/interfaces/ILayerZeroUserApplicationConfig.sol:L3      pragma solidity ^0.8.0;

contracts/interfaces/IReferrals.sol:L3      pragma solidity ^0.8.0;

contracts/interfaces/ILayerZeroEndpoint.sol:L3      pragma solidity ^0.8.0;

contracts/interfaces/ITrading.sol:L5      pragma solidity ^0.8.0;

contracts/interfaces/IBondNFT.sol:L2      pragma solidity ^0.8.9;

contracts/interfaces/ILayerZeroReceiver.sol:L3      pragma solidity ^0.8.0;

contracts/interfaces/IPairsContract.sol:L3      pragma solidity ^0.8.0;

contracts/interfaces/IGovNFT.sol:L3      pragma solidity ^0.8.0;

```

### [G-04] Functions guaranteed to revert when called by normal users can be declared as payable.


#### Impact
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.


#### Findings:
```
contracts/BondNFT.sol:L349    function addAsset(address _asset) external onlyOwner {

contracts/BondNFT.sol:L357    function setAllowedAsset(address _asset, bool _bool) external onlyOwner {

contracts/BondNFT.sol:L362    function setBaseURI(string calldata _newBaseURI) external onlyOwner {

contracts/StableVault.sol:L78    function listToken(address _token) external onlyOwner {

contracts/StableVault.sol:L89    function delistToken(address _token) external onlyOwner {

contracts/Position.sol:L85    function setBaseURI(string memory _newBaseURI) external onlyOwner {

contracts/Position.sol:L99    function updateFunding(uint256 _asset, address _tigAsset, uint256 _longOi, uint256 _shortOi, uint256 _baseFundingRate, uint _vaultFundingPercent) external onlyMinter {

contracts/Position.sol:L168    function executeLimitOrder(uint256 _id, uint256 _price, uint256 _newMargin) external onlyMinter {

contracts/Position.sol:L197    function modifyMargin(uint256 _id, uint256 _newMargin, uint256 _newLeverage) external onlyMinter {

contracts/Position.sol:L209    function addToPosition(uint256 _id, uint256 _newMargin, uint256 _newPrice) external onlyMinter {

contracts/Position.sol:L220    function setAccInterest(uint256 _id) external onlyMinter {

contracts/Position.sol:L230    function reducePosition(uint256 _id, uint256 _percent) external onlyMinter {

contracts/Position.sol:L242    function modifyTp(uint _id, uint _tpPrice) external onlyMinter {

contracts/Position.sol:L252    function modifySl(uint _id, uint _slPrice) external onlyMinter {

contracts/Position.sol:L260    function burn(uint _id) external onlyMinter {

contracts/Position.sol:L310    function setMinter(address _minter, bool _bool) external onlyOwner {

contracts/TradingExtension.sol:L144    function setMaxGasPrice(uint _maxGasPrice) external onlyOwner {

contracts/TradingExtension.sol:L231    function setChainlinkEnabled(bool _bool) external onlyOwner {

contracts/TradingExtension.sol:L240    function setNode(address _node, bool _bool) external onlyOwner {

contracts/TradingExtension.sol:L274    function setPaused(bool _paused) external onlyOwner {

contracts/Referrals.sol:L32    function setReferred(address _referredTrader, bytes32 _hash) external onlyProtocol {

contracts/Referrals.sol:L53    function setProtocol(address _protocol) external onlyOwner {

contracts/PairsContract.sol:L33    function setAssetChainlinkFeed(uint256 _asset, address _feed) external onlyOwner {

contracts/PairsContract.sol:L48    function addAsset(uint256 _asset, string memory _name, address _chainlinkFeed, uint256 _minLeverage, uint256 _maxLeverage, uint256 _feeMultiplier, uint256 _baseFundingRate) external onlyOwner {

contracts/PairsContract.sol:L73    function updateAssetLeverage(uint256 _asset, uint256 _minLeverage, uint256 _maxLeverage) external onlyOwner {

contracts/PairsContract.sol:L92    function setAssetBaseFundingRate(uint256 _asset, uint256 _baseFundingRate) external onlyOwner {

contracts/PairsContract.sol:L104    function updateAssetFeeMultiplier(uint256 _asset, uint256 _feeMultiplier) external onlyOwner {

contracts/PairsContract.sol:L115    function pauseAsset(uint256 _asset, bool _isPaused) external onlyOwner {

contracts/PairsContract.sol:L125    function setMaxBaseFundingRate(uint256 _maxBaseFundingRate) external onlyOwner {

contracts/PairsContract.sol:L129    function setProtocol(address _protocol) external onlyOwner {

contracts/PairsContract.sol:L139    function setMaxOi(uint256 _asset, address _tigAsset, uint256 _maxOi) external onlyOwner {

contracts/PairsContract.sol:L154    function modifyLongOi(uint256 _asset, address _tigAsset, bool _onOpen, uint256 _amount) external onlyProtocol {

contracts/PairsContract.sol:L174    function modifyShortOi(uint256 _asset, address _tigAsset, bool _onOpen, uint256 _amount) external onlyProtocol {

contracts/GovNFT.sol:L46    function setBaseURI(string calldata _newBaseURI) external onlyOwner {

contracts/GovNFT.sol:L104    function mintMany(uint _amount) external onlyOwner {

contracts/GovNFT.sol:L110    function mint() external onlyOwner {

contracts/GovNFT.sol:L114    function setTrustedAddress(uint16 _chainId, address _contract, bool _bool) external onlyOwner {

contracts/GovNFT.sol:L236    function setGas(uint _gas) external onlyOwner {

contracts/GovNFT.sol:L240    function setEndpoint(ILayerZeroEndpoint _endpoint) external onlyOwner {

contracts/GovNFT.sol:L300    function addAsset(address _asset) external onlyOwner {

contracts/GovNFT.sol:L307    function setAllowedAsset(address _asset, bool _bool) external onlyOwner {

contracts/GovNFT.sol:L311    function setMaxBridge(uint256 _max) external onlyOwner {

contracts/Trading.sol:L939    function setLimitOrderPriceRange(uint _range) external onlyOwner {

contracts/Trading.sol:L952    function setFees(bool _open, uint _daoFees, uint _burnFees, uint _referralFees, uint _botFees, uint _percent) external onlyOwner {

contracts/utils/MetaContext.sol:L9    function setTrustedForwarder(address _forwarder, bool _bool) external onlyOwner {
```

### [G-05] ```X += Y``` costs more gas than ```X = X + Y``` for state variables.


#### Impact
Consider changing ```X += Y``` to ```X = X + Y``` to save gas.


#### Findings:
```
contracts/Lock.sol:L73        totalLocked[_asset] += _amount;

contracts/Lock.sol:L103        totalLocked[asset] -= lockAmount;

contracts/BondNFT.sol:L68            totalShares[_asset] += shares;

contracts/BondNFT.sol:L115            totalShares[bond.asset] += shares-bond.shares;

contracts/BondNFT.sol:L117            _bond.amount += _amount;

contracts/BondNFT.sol:L119            _bond.period += _period;

contracts/BondNFT.sol:L150            totalShares[bond.asset] -= bond.shares;

contracts/BondNFT.sol:L152            amount += _claimAmount;

contracts/BondNFT.sol:L180                    accRewardsPerShare[bond.asset][epoch[bond.asset]] += _pendingDelta*1e18/totalShares[bond.asset];

contracts/BondNFT.sol:L183            bondPaid[_id][bond.asset] += amount;

contracts/BondNFT.sol:L221                    epoch[_tigAsset] += 1;

contracts/BondNFT.sol:L225            accRewardsPerShare[_tigAsset][aEpoch] += _amount * 1e18 / totalShares[_tigAsset];

contracts/BondNFT.sol:L333            userDebt[from][bond.asset] += bond.pending;

contracts/BondNFT.sol:L334            bondPaid[_id][bond.asset] += bond.pending;

contracts/Position.sol:L62        _trade.accInterest += (int256(_trade.margin*_trade.leverage/1e18)*(accInterestPerOi[_trade.asset][_trade.tigAsset][_trade.direction]+_pendingFunding)/1e18)-initId[_id];

contracts/Position.sol:L102                accInterestPerOi[_asset][_tigAsset][true] += ((int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(longOi[_asset][_tigAsset]))*int256(1e10-vaultFundingPercent[_asset][_tigAsset])/1e10;

contracts/Position.sol:L104            accInterestPerOi[_asset][_tigAsset][false] -= (int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(shortOi[_asset][_tigAsset]);

contracts/Position.sol:L107            accInterestPerOi[_asset][_tigAsset][true] -= (int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(longOi[_asset][_tigAsset]);

contracts/Position.sol:L109                accInterestPerOi[_asset][_tigAsset][false] += ((int256(block.timestamp-lastUpdate[_asset][_tigAsset])*fundingDeltaPerSec[_asset][_tigAsset])*1e18/int256(shortOi[_asset][_tigAsset]))*int256(1e10-vaultFundingPercent[_asset][_tigAsset])/1e10;

contracts/Position.sol:L231        _trades[_id].accInterest -= _trades[_id].accInterest*int256(_percent)/int256(DIVISION_CONSTANT);

contracts/Position.sol:L232        _trades[_id].margin -= _trades[_id].margin*_percent/DIVISION_CONSTANT;

contracts/TradingExtension.sol:L185            _price += _price * _spread / DIVISION_CONSTANT;

contracts/TradingExtension.sol:L187            _price -= _price * _spread / DIVISION_CONSTANT;

contracts/PairsContract.sol:L156            _idToOi[_asset][_tigAsset].longOi += _amount;

contracts/PairsContract.sol:L160            _idToOi[_asset][_tigAsset].longOi -= _amount;

contracts/PairsContract.sol:L176            _idToOi[_asset][_tigAsset].shortOi += _amount;

contracts/PairsContract.sol:L180            _idToOi[_asset][_tigAsset].shortOi -= _amount;

contracts/GovNFT.sol:L52        counter += 1;

contracts/GovNFT.sol:L54            userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];

contracts/GovNFT.sol:L68            userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];

contracts/GovNFT.sol:L79            userDebt[owner][assets[i]] += accRewardsPerNFT[assets[i]];

contracts/GovNFT.sol:L80            userDebt[owner][assets[i]] -= userPaid[owner][assets[i]]/balanceOf(owner);

contracts/GovNFT.sol:L81            userPaid[owner][assets[i]] -= userPaid[owner][assets[i]]/balanceOf(owner);            

contracts/GovNFT.sol:L96            userDebt[from][assets[i]] += accRewardsPerNFT[assets[i]];

contracts/GovNFT.sol:L97            userDebt[from][assets[i]] -= userPaid[from][assets[i]]/balanceOf(from);

contracts/GovNFT.sol:L98            userPaid[from][assets[i]] -= userPaid[from][assets[i]]/balanceOf(from);

contracts/GovNFT.sol:L99            userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];

contracts/GovNFT.sol:L278        userPaid[_msgsender][_tigAsset] += amount;

contracts/GovNFT.sol:L290            accRewardsPerNFT[_tigAsset] += _amount/totalSupply();

contracts/Trading.sol:L293            _addMargin -= _fee;

contracts/Trading.sol:L509                trade.price += trade.price * _spread / DIVISION_CONSTANT;

contracts/Trading.sol:L511                trade.price -= trade.price * _spread / DIVISION_CONSTANT;

```

### [G-06] Revert message greater than 32 bytes


#### Impact
Keep revert message lower than or equal to 32 bytes to save gas.


#### Findings:
```
contracts/GovNFT.sol:L153        require(

contracts/GovNFT.sol:L185        require(msg.sender == address(this), "NonblockingLzApp: caller must be app");

contracts/GovNFT.sol:L209        require(payloadHash != bytes32(0), "NonblockingLzApp: no stored message");

contracts/GovNFT.sol:L210        require(keccak256(_payload) == payloadHash, "NonblockingLzApp: invalid payload");

```

### [G-07] Splitting ```require()``` statements that use && saves gas.


#### Impact
Consider splitting the ```require()``` statements to save gas.


#### Findings:
```
contracts/PairsContract.sol:L52        require(_maxLeverage >= _minLeverage && _minLeverage > 0, "Wrong leverage values");

contracts/Trading.sol:L887            require(_proxy.proxy == _msgSender() && _proxy.time >= block.timestamp, "Proxy not approved");

```

### [G-08] ```++i```/```i++``` should be ```unchecked{++i}```/```unchecked{i++}``` when it is not possible for them to overflow, as is the case when used in for- and while-loops.


#### Impact
The ```unchecked``` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop.


#### Findings:
```
contracts/Lock.sol:L113        for (uint i=0; i < assets.length; i++) {

contracts/Position.sol:L296        for (uint i=0; i<_ids.length; i++) {

contracts/Position.sol:L304        for (uint i=0; i<_ids.length; i++) {

contracts/Referrals.sol:L70        for (uint i=0; i<_codeOwnersL; i++) {

contracts/Referrals.sol:L73        for (uint i=0; i<_referredAL; i++) {

contracts/GovNFT.sol:L53        for (uint i=0; i<assetsLength(); i++) {

contracts/GovNFT.sol:L67        for (uint i=0; i<assetsLength(); i++) {

contracts/GovNFT.sol:L78        for (uint i=0; i<assetsLength(); i++) {

contracts/GovNFT.sol:L95        for (uint i=0; i<assetsLength(); i++) {

contracts/GovNFT.sol:L105        for (uint i=0; i<_amount; i++) {

contracts/GovNFT.sol:L131        for (uint i=0; i<tokenId.length; i++) {

contracts/GovNFT.sol:L200        for (uint i=0; i<tokenId.length; i++) {

contracts/GovNFT.sol:L246        for (uint i=0; i<_ids.length; i++) {

contracts/GovNFT.sol:L252        for (uint i=0; i<_ids.length; i++) {

contracts/GovNFT.sol:L258        for (uint i=0; i<_ids.length; i++) {

contracts/GovNFT.sol:L325        for (uint i=0; i<_ids.length; i++) {

```

### [G-09] Using Storage Instead of Memory for Structs/Arrays Saves Gas.

#### Impact
When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read.

Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct

#### Findings:
```
contracts/Lock.sol:111:        address[] memory assets = bondNFT.getAssets();
contracts/Lock.sol:139:        uint[] memory _ids
contracts/BondNFT.sol:340:        uint[] memory _ids = new uint[](balanceOf(_user));
contracts/Position.sol:295:        uint[] memory _ids = new uint[](balanceOf(_user));
contracts/Position.sol:303:        uint[] memory _ids = new uint[](_to-_from);
contracts/Referrals.sol:61:        address[] memory _codeOwners,
contracts/Referrals.sol:62:        bytes32[] memory _ownedCodes,
contracts/Referrals.sol:63:        address[] memory _referredA,
contracts/Referrals.sol:64:        bytes32[] memory _referredTo
contracts/GovNFT.sol:128:        uint256[] memory tokenId
contracts/GovNFT.sol:324:        uint[] memory _ids = new uint[](balanceOf(_user));

```