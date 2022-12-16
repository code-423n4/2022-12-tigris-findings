# QA Report

## Summary

|               | Issue         | Risk     | Instances     |
| :-------------: |:-------------|:-------------:|:-------------:|
| 1      | owner can allow assets that are not yet added in `GovNFT`  | Low | 1 |
| 2      | Risk of array length mismatch in `initRefs` function | Low | 1 |
| 3      | `maxbaseFundingRate` is not bounded | Low | 1 |
| 4      | bond owner is set to `address(0)` when `createLock` is called  | Low | 1 |
| 5      | Error in `withdraw` function Natspec  | Low | 1 |
| 6      | Using deprecated Chainlink function `latestAnswer` could result in wrong prices | Low | 1 |
| 7      | Use `call()` instead of `transfer()` | Low | 1 |
| 8      | Immutable state variables lack zero address checks | Low | 3 |
| 9      | Duplicated `require()` checks should be refactored to a modifier  | NC |  6 |
| 10  | contracts/libraries imported but not used  | NC | 2 |
| 11     | Use solidity time-based units | NC | 1 |
| 12     | Use scientific notation | NC | 4 |



## Findings

### 1- owner can allow assets that are not yet added in `GovNFT` :

The `setAllowedAsset` function inside the GovNFT contract give the owner the ability to allow an asset meaning to put `_allowedAsset[_asset] = true` without the asset being already added to the `assets` array.

#### Risk : Low

#### Proof of Concept

Issue occurs in the instance below :

File: contracts/GovNFT.sol [Line 307-309](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L307-L309)
```
/** @audit
    owner can allow asset even if they are not added yet
*/
function setAllowedAsset(address _asset, bool _bool) external onlyOwner {
    _allowedAsset[_asset] = _bool;
}
```

As you can see the owner can allow any asset without having to add it with the The `addAsset` function, so the asset will be allowed but won't be in the `asset` array, this can cause many problems if functions from other contracts access directely the `allowedAsset` view function which will return true even if the asset is not in the `asset` array

#### Mitigation

To avoid this issue i recommend to add check statement for verifying that the asset is already added before allowing it, the function should be modified as follow :
``` 
function setAllowedAsset(address _asset, bool _bool) external onlyOwner {
    /** @audit
        add aasset added check
    */
    require(assets[assetsIndex[_asset]] == _asset, "Not added");
    _allowedAsset[_asset] = _bool;
}
```

### 2- Risk of array length mismatch in `initRefs` function :

The `initRefs` function inside the Referrals contract is used to set an array of `referral` and `referred`, but the function doesn't check if the length of the arrays are the smae meaning that : `_ownedCodes` have the same length as `_codeOwners` and `_referredA` have the same length as `_referredTo`, so if the owner by accident sends arrays with different numbers of elements (length) the `initRefs` function will still set those value but in a wrong way and the owner will not be acknowledged.

Let's say for example the owner put by accident the _referredA.length == 10 and _referredTo.length == 9, the function will still store the values but when it arrives at _referred[9] it will set it as the default bytes32 value because _referredTo[9] doesn't exists.

#### Risk : Low

#### Proof of Concept

Issue occurs in the instance below :

File: contracts/Referrals.sol [Line 60-76](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol#L60-L76)
```
function initRefs(
    address[] memory _codeOwners,
    bytes32[] memory _ownedCodes,
    address[] memory _referredA,
    bytes32[] memory _referredTo
) external onlyOwner {
    require(!isInit);
    /** @audit
        missing checks for :
        _codeOwners.length == _ownedCodes.length and _referredA.length == _referredTo.length
    */
    isInit = true;
    uint _codeOwnersL = _codeOwners.length;
    uint _referredAL = _referredA.length;
    for (uint i=0; i<_codeOwnersL; i++) {
        _referral[_ownedCodes[i]] = _codeOwners[i];
    }
    for (uint i=0; i<_referredAL; i++) {
        _referred[_referredA[i]] = _referredTo[i];
    }
}
```

#### Mitigation

To avoid this issue i recommend to add check statement of the arrays lengths, the function should be modified as follow :
```
function initRefs(
    address[] memory _codeOwners,
    bytes32[] memory _ownedCodes,
    address[] memory _referredA,
    bytes32[] memory _referredTo
) external onlyOwner {
    require(!isInit);
    /** @audit
        add array lengths checks
    */
    require(_codeOwners.length == _ownedCodes.length, "length mismatch");
    require(_referredA.length == _referredTo.length, "length mismatch");
    isInit = true;
    uint _codeOwnersL = _codeOwners.length;
    uint _referredAL = _referredA.length;
    for (uint i=0; i<_codeOwnersL; i++) {
        _referral[_ownedCodes[i]] = _codeOwners[i];
    }
    for (uint i=0; i<_referredAL; i++) {
        _referred[_referredA[i]] = _referredTo[i];
    }
}
```

### 3- `maxbaseFundingRate` is not bounded :

The `maxbaseFundingRate` variable is used to prevent the owner from setting a large value for `baseFundingRate` variable when calling the `setAssetBaseFundingRate` function, this illustrated with this check :

File: contracts/PairsContract.sol [Line 95](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L95)
```
require(_baseFundingRate <= maxBaseFundingRate, "baseFundingRate too high");
```

But the value of `maxbaseFundingRate` itself is variable and doesn't have a max value, in fact the owner can call the `setMaxBaseFundingRate` function and sets `maxBaseFundingRate = type(uint256).max`, which will also cause the `baseFundingRate` variable to not have a max value.

So the way this system is designed is not really working unless the owner is a governace DAO (must go through voting to set a new maxbaseFundingRate) 

#### Risk : Low

#### Proof of Concept

Issue occurs in the instance below :

File: contracts/PairsContract.sol [Line 125-127](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L125-L127)
```
/** @audit
    owner can set maxBaseFundingRate to any value
*/
function setMaxBaseFundingRate(uint256 _maxBaseFundingRate) external onlyOwner {
    maxBaseFundingRate = _maxBaseFundingRate;
}
```

As you can see the owner can set any value for `maxbaseFundingRate` variables even `type(uint256).max`

#### Mitigation

To avoid this issue i recommend to add a constant maximum value `MAX_RATE` for `maxbaseFundingRate` which everyone can see this still allows the owner to change the `maxbaseFundingRate` value but it will now be bounded, the `setMaxBaseFundingRate` function should be modified as follow :
``` 
/** @audit
    add a max value for maxBaseFundingRate
*/
uint256 private constant MAX_RATE = 1e10;
function setMaxBaseFundingRate(uint256 _maxBaseFundingRate) external onlyOwner {
    require(_maxBaseFundingRate <= MAX_RATE, "too high");
    maxBaseFundingRate = _maxBaseFundingRate;
}
```

### 4- bond owner is set to `address(0)` when `createLock` is called :

When the `createLock` is called it should creates a new bond for the given owner address but inside the `Bond` struct the owner address is set to `address(0)` instead of the real owner address `_owner`, in the current implementation of the `BondNFT` contract this hasn't a big impact as the method `ownerOf(bond.id)` is used every time to get the owner but for futur developoment it must be taken into consideration.

#### Risk : Low

#### Proof of Concept

The issue occurs in the `createLock` function below :

File: contracts/BondNFT.sol [Line 57-86](hhttps://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L57-L86)
```
function createLock(
    address _asset,
    uint _amount,
    uint _period,
    address _owner
) external onlyManager() returns(uint id) {
    require(allowedAsset[_asset], "!Asset");
    unchecked {
        uint shares = _amount * _period / 365;
        uint expireEpoch = epoch[_asset] + _period;
        id = ++totalBonds;
        totalShares[_asset] += shares;
        Bond memory _bond = Bond(
            id,             // id
            /** @audit
                owner value is set to address(0) instead of _owner
            */
            address(0),     // owner
            _asset,         // tigAsset token
            _amount,        // tigAsset amount
            epoch[_asset],  // mint epoch
            block.timestamp,// mint timestamp
            expireEpoch,    // expire epoch
            0,              // pending
            shares,         // linearly scaling share of rewards
            _period,        // lock period
            false           // is expired boolean
        );
        _idToBond[id] = _bond;
        _mint(_owner, _bond);
    }
    emit Lock(_asset, _amount, _period, _owner, id);
}
```

#### Mitigation

To remove this issue there two options :

* Set the correct owner in the `Bond` struct bby replacing `address(0)` by `_owner`.

* Remove completly the owner field from the `Bond` struct as the owner address can be obtained from the minted NFT owner by using method `ownerOf`.

### 5- Error in `withdraw` function Natspec :

In the `withdraw` function inside the StableVault contract the Natspec of the function says that the parameter `_amount` refers to the amount of `_token` received, but when you read the function you notice that it refers in reality to the amount of `tigAsset` swapped for `_token`.

#### Risk : Low

#### Proof of Concept

Issue occurs in the instance below :

File: contracts/StableVault.sol [Line 60-72](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L60-L72)
```
/**
* @notice swap tigAsset to _token
* @param _token address of the token to receive
* @param _amount amount of _token
*/
function withdraw(address _token, uint256 _amount) external returns (uint256 _output) {
    /* @audit
       _amount refers to tigAsset not to _token
    */
    IERC20Mintable(stable).burnFrom(_msgSender(), _amount);
    _output = _amount/10**(18-IERC20Mintable(_token).decimals());
    IERC20(_token).transfer(
        _msgSender(),
        _output
    );
}
```

#### Mitigation

If this is a simple typo it should be corrected as it can be misleading for both devs and users, but if the `_amount` argument really mented to be the amount of `_token` that should be withdrawn then the function should be reviewed to correct the math and transfer the right amount.

### 6- Using deprecated Chainlink function `latestAnswer` could result in wrong prices  :

In the TradingLibrary library the function `verifyPrice` is used to verify the trade price and if the variables `_chainlinkEnabled` is set to true the function will use the Chainlink price feed to get the latest price for the given asset, but the function `verifyPrice` uses a deprecated Chainlink function `latestAnswer` to get the price of the token as it's mentionned [here](https://docs.chain.link/docs/data-feeds/price-feeds/api-reference/#latestanswer), this function does not throw an error if no answer has been reached but returns 0 and it can give a stale price for a given token which would impact on the trading logic.

#### Risk : Low

#### Proof of Concept
Issue occurs in the `verifyPrice` function:

File: contracts/utils/TradingLibrary.sol [Line 91-122](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L91-L122)
```
function verifyPrice(
    uint256 _validSignatureTimer,
    uint256 _asset,
    bool _chainlinkEnabled,
    address _chainlinkFeed,
    PriceData calldata _priceData,
    bytes calldata _signature,
    mapping(address => bool) storage _isNode
)
    external view
{
    ...

    if (_chainlinkEnabled && _chainlinkFeed != address(0)) {
        int256 assetChainlinkPriceInt = IPrice(_chainlinkFeed).latestAnswer();
        if (assetChainlinkPriceInt != 0) {
            uint256 assetChainlinkPrice = uint256(assetChainlinkPriceInt) * 10**(18 - IPrice(_chainlinkFeed).decimals());
            require(
                _priceData.price < assetChainlinkPrice+assetChainlinkPrice*2/100 &&
                _priceData.price > assetChainlinkPrice-assetChainlinkPrice*2/100, "!chainlinkPrice"
            );
        }
    }
}
```

Even though the issue of `latestAnswer` not reverting on non-response is handled with the check `if (assetChainlinkPriceInt != 0)`, the problem of stale price still remaigns.

#### Mitigation

To avoid the risk of getting stale price from Chainlink oracle i recommend to use the `latestRoundData` function with it's required checks.

The function `verifyPrice` should be modified as follow :

```
function verifyPrice(
    uint256 _validSignatureTimer,
    uint256 _asset,
    bool _chainlinkEnabled,
    address _chainlinkFeed,
    PriceData calldata _priceData,
    bytes calldata _signature,
    mapping(address => bool) storage _isNode
)
    external view
{
    ...

    if (_chainlinkEnabled && _chainlinkFeed != address(0)) {
        (
            uint80 roundID,
            uint256 assetPrice,
            ,
            uint256 timestamp,
            uint80 answeredInRound
        ) = IPrice(_chainlinkFeed).latestRoundData();

        require(assetPrice > 0, "Invalid feed price");
        require(answeredInRound >= roundID, "Stale price");
        require(timestamp != 0, "Round not complete");

        uint256 assetChainlinkPrice = uint256(assetPrice) * 10**(18 - IPrice(_chainlinkFeed).decimals());
        require(
            _priceData.price < assetChainlinkPrice+assetChainlinkPrice*2/100 &&
            _priceData.price > assetChainlinkPrice-assetChainlinkPrice*2/100, "!chainlinkPrice"
        );
        
    }
}
```

### 7- Use `call()` instead of `transfer()` :

When transferring ETH, `call()` should be used instead of `transfer()`.

The `transfer()` function only allows the recipient to use 2300 gas. If the recipient uses more than that, transfers will fail. In the future gas costs might change increasing the likelihood of that happening.

Keep in mind that `call()` introduces the risk of reentrancy, but as the code follows the CEI pattern it should be fine.

#### Risk : Low

#### Proof of Concept
Instances include:

File: contracts/Trading.sol [Line 588](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L588)
```
payable(_proxy).transfer(msg.value);
```

#### Mitigation
Replace `transfer()` calls with `call()`, keep in mind to check whether the call was successful by validating the return value.


### 8- Immutable state variables lack zero address checks  :

Constructors should check the values written in an immutable state variables(address) is not the address(0)

#### Risk : Low

#### Proof of Concept
Instances include:

File: contracts/Lock.sol [Line 25-26](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L25-L26)
```
bondNFT = IBondNFT(_bondNFTAddress);
govNFT = IGovNFT(_govNFT);
```

File: contracts/StableVault.sol [Line 36](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L36)
```
stable = _stable;
```

#### Mitigation
Add non-zero address checks in the constructors for the instances aforementioned.


### 9- Duplicated `require()` checks should be refactored to a modifier :

`require()` checks statement used multiple times inside a contract should be refactored to a modifier for better readability.

#### Risk : NON CRITICAL

#### Proof of Concept

There are 6 instances of this issue in `PairsContract.sol`:

```
File: contracts/PairsContract.sol

35      require(_name.length > 0, "!Asset");
75      require(_name.length > 0, "!Asset");
94      require(_name.length > 0, "!Asset");
106     require(_name.length > 0, "!Asset");
117     require(_name.length > 0, "!Asset");
141     require(_name.length > 0, "!Asset");
```

Those checks should be replaced by a `Exists` modifier as follow :

```
modifier Exists(uint256 _asset){
    bytes memory _name  = bytes(_idToAsset[_asset].name);
    require(_name.length > 0, "!Asset");
    _;
}
```

### 10- contracts/libraries imported but not used :

The contracts, interfaces or libraries imported in a contract and are never used should be removed.

#### Risk : NON CRITICAL

#### Proof of Concept
Instances include:

File: contracts/Lock.sol [Line 4](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L4)

```
import "hardhat/console.sol";
```

File: contracts/StableVault.sol [Line 5](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L5)

```
import "@openzeppelin/contracts/security/ReentrancyGuard.sol";
```

#### Mitigation
Remove the imported contracts/libraries if they are not intended to be used.

### 11- Use solidity time-based units :

Solidity contains time-based units (seconds, minutes, hours, days and weeks) which should be used when declaring time based variables/constants instead of using literal numbers, this is done for better readability and to avoid mistakes.

#### Risk : NON CRITICAL

#### Proof of Concept
Instances include:

File: contracts/BondNFT.sol [Line 10](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L10)

```
uint constant private DAY = 24 * 60 * 60;
```

#### Mitigation

Use time units when declaring time-based variables.

### 12- Use scientific notation :

When using multiples of 10 you shouldn't use decimal literals or exponentiation (e.g. 1000000, 10**18) but use scientific notation for better readability.

#### Risk : NON CRITICAL

#### Proof of Concept
Instances include:

File: contracts/GovNFT.sol [Line 16](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L16)

```
uint256 private constant MAX = 10000;
```

File: contracts/GovNFT.sol [Line 17](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L17)

```
uint256 public gas = 150000;
```

File: contracts/GovNFT.sol [Line 66](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L66)

```
require(tokenId <= 10000, "BadID");
```


File: contracts/TradingExtension.sol [Line 26](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L26)

```
uint public maxGasPrice = 1000000000000;
```

#### Mitigation

Use scientific notation for the instances aforementioned.
