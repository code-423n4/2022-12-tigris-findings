
## Missing event emission and lack of parameters validations in setters
### Summary
It's important to track everything off-chain, mostly for critical state parameters modifications.

Also there is a huge lack of default values validation for address or uint values that are later apply to assets

### Impact
Don't know for example when something is paused, on modify a fee, address, etc
### Github permalink
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L357-L370
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L46-L48
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L113-L116
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L235-L243
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L307-L313
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L897-L979
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/TradingExtension.sol#L125-L146
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/TradingExtension.sol#L222-L276
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/utils/MetaContext.sol#L9-L11
### Mitigation
Add the event emission and check default values not be used (in some cases)

## Deprecated Library
### Summary
```
    // EIP-2612 is Final as of 2022-11-01. This file is deprecated.
    import "./ERC20Permit.sol";
```
### Github Permalink
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/StableToken.sol#L4
### Recommendation
Update to ERC20Permit as it is no longer a draft



## Emitted amount can be bigger than expected
### Impact 
There are ERC20 tokens with transfer at fees. For checking if the transferred amount is the same as expected, code already compares balanceOf before and balanceOf after transfer. People can get confused in cases where real value doesn't match, also applications like subgraphs that uses this value won't work as expected.

### Github Permalinks
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L185-L187
        IERC20(tigAsset).transfer(manager, amount);
        emit ClaimFees(tigAsset, amount, _claimer, _id);
    
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L202-L204
        IERC20(_tigAsset).transfer(manager, amount);
        emit ClaimDebt(_tigAsset, amount, _user);

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L216-L227
        IERC20(_tigAsset).transferFrom(_msgSender(), address(this), _amount);
        emit Distribution(_tigAsset, _amount);


Case 2: Returned amount won't be the same in this cases, but these return values are not used within the code.
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Lock.sol#L34-L41
    ```
    function claim(
        uint256 _id
    ) public returns (address) {
        claimGovFees();
        (uint _amount, address _tigAsset) = bondNFT.claim(_id, msg.sender);
        IERC20(_tigAsset).transfer(msg.sender, _amount);
        return _tigAsset; //@audit can be a wrong amount
    }
    ```

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/StableVault.sol#L68-L71
    ```
        function withdraw(address _token, uint256 _amount) external returns (uint256 _output) {
        IERC20Mintable(stable).burnFrom(_msgSender(), _amount);//@audit can be 0
        _output = _amount/10**(18-IERC20Mintable(_token).decimals());//@audit DECIMALS UWU + / small amount fcked
        IERC20(_token).transfer(
            _msgSender(),
            _output
        );//@audit returned value won't be expected one in some cases, depending where it is used, this can do damage
    }
    ```


### Mitigation
Consider implementing a system like:
```
        uint256 balanceBefore = _token.balanceOf(address(this));
        _token.safeTransferFrom(_from, address(this), _amount);
        uint256 balanceAfter = _token.balanceOf(address(this));

        // check / control flow when (balanceAfter - balanceBefore != _amount);
```
### Recommendation
Consider comparing before and after balance to get the actual transferred amount.


## `_safeMint()` should be used rather than `_mint()` wherever possible
### Impact 
In `GovNFT.sol` and `BondNFT.sol`, eventually it is called ERC721 `_mint()`. Calling `_mint()` this way does not ensure that the receiver of the NFT is able to accept them, making possible to lose them. 

`_safeMint()` should be used with as it checks to see if a user can properly accept an NFT and reverts otherwise.

There is no check of the address provided by the mint NFT that it implements `ERC721Receiver`. 

#### Details 
`_mint()` is discouraged in favor of `_safeMint()` which ensures that the recipient is either an EOA or implements `IERC721Receiver`. 

Both open OpenZeppelin and solmate have versions of this function so that NFTs aren’t lost if they’re minted to contracts that cannot transfer them back out.

#### References
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271

### Github Permalinks
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L50
    function _mint(address to, uint256 tokenId) internal override {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L56
        super._mint(to, tokenId);

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L70
        super._mint(to, tokenId);

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L106
            _mint(_msgSender(), counter);

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L111
        _mint(_msgSender(), counter);

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L83
            _mint(_owner, _bond);

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L306
    function _mint(

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L313
        _mint(to, bond.id);


### Mitigation
Use `_safeMint()` as suggested by OpenZeppelin or include the check before minting.




## Single-step process for critical ownership transfer/renounce is risky
### Impact
Given that `TradingExtension`, `PairsContract`, `Referrals`, `Lock`, `BondNFT`, `MetaContext` are derived from `Ownable`, the ownership management of this contract defaults to `Ownable` ’s `transferOwnership()` and `renounceOwnership()` methods which are not overridden here. 

Such critical address transfer/renouncing in one-step is very risky because it is irrecoverable from any mistakes

Scenario: If an incorrect address, e.g. for which the private key is not known, is used accidentally then it prevents the use of all the `onlyOwner()` functions forever, which includes the changing of various critical addresses and parameters. This use of incorrect address may not even be immediately apparent given that these functions are probably not used immediately. 

When noticed, due to a failing `onlyOwner()` function call, it will force the redeployment of these contracts and require appropriate changes and notifications for switching from the old to new address. This will diminish trust in the protocol and incur a significant reputational damage.

### Github Permalinks
- `Ownable`
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/TradingExtension.sol#L10
contract TradingExtension is Ownable{

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/PairsContract.sol#L8
contract PairsContract is Ownable, IPairsContract {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Referrals.sol#L7
contract Referrals is Ownable, IReferrals {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Lock.sol#L10
contract Lock is Ownable{

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/BondNFT.sol#L8
contract BondNFT is ERC721Enumerable, Ownable {

https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/utils/MetaContext.sol#L6
contract MetaContext is Ownable {


### Proof of Concept
See similar High Risk severity finding from Trail-of-Bits Audit of Hermez.
https://github.com/trailofbits/publications/blob/master/reviews/hermez.pdf
See similar Medium Risk severity finding from Trail-of-Bits Audit of Uniswap V3:
https://github.com/Uniswap/v3-core/blob/main/audits/tob/audit.pdf

### Recommended steps
Consider using OZ Ownable2Step library:
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable2Step.sol

Or do it manually. Recommend overriding the inherited methods to null functions and use separate functions for a two-step address change:
1. Approve a new address as a `pendingOwner`
2. A transaction from the `pendingOwner` address claims the pending ownership change.

This mitigates risk because if an incorrect address is used in step (1) then it can be fixed by re-approving the correct address. Only after a correct address is used in step (1) can step (2) happen and complete the address/ownership change.

Also, consider adding a time-delay for such sensitive actions. And at a minimum, use a multisig owner address and not an EOA.





## Missing checks for address(0x0) when assigning values to `address` state or `immutable` variables 
### Summary
Zero address should be checked for state variables, immutable variables. A zero address can lead into problems.
### Github Permalinks
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/GovNFT.sol#L38
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Lock.sol#L25-L26
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/Trading.sol#L149-L151
https://github.com/code-423n4/2022-12-tigris/blob/0cb05a462e78c4470662e9d9a4f9ab587f266bb5/contracts/TradingExtension.sol#L36-L38
### Mitigation
Check zero address before assigning or using it


