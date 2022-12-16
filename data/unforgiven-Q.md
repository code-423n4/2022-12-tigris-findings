#### [[1]]
safeTransferMany() in GoveNFT/BondNFT contracts  are not using safe transferring, onERC721() function of receiver address doesn't get called because code calls _transfer()
```
    function safeTransferMany(address _to, uint[] calldata _ids) external {
        for (uint i=0; i<_ids.length; i++) {
            _transfer(_msgSender(), _to, _ids[i]);
        }
    }
```
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L282-L288
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L245-L249

#### [[2]]
wrong condition for checking that if tigAsset is not in assets list. condition ` assets[assetsIndex[_tigAsset]] == address(0)` will always be `false` (if `assets[0]` has value) because `assetsIndex[nonAddedAsset]` would be 0 and `assets[0]` would have some value if `assets.leng() >0`. the correct condition is  `assets[assetsIndex[_tigAsset]] != _tigAsset`
```
    function distribute(address _tigAsset, uint _amount) external {
        if (assets.length == 0 || assets[assetsIndex[_tigAsset]] == address(0) || totalSupply() == 0 || !_allowedAsset[_tigAsset]) return;
        try IERC20(_tigAsset).transferFrom(_msgSender(), address(this), _amount) {
            accRewardsPerNFT[_tigAsset] += _amount/totalSupply();
        } catch {
            return;
        }
    }
```
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L287-L294

#### [[3]]
there is no functionality in GovNFT and BondNFT to remove assets and if assets length increase too much(by mistake or intentionaly) then all the logics would fail because of the looping through assets. also the gas would go higher.

#### [[4]]
function `_bridgeMint()` uses hardcoded value 10000 instead of using MAX variable. if for some reason the number of NFT tokens changes and MAX has new value but 10000 didn't get changed then it won't be possible to transfer tokens with id>10000 to other chains (code won't mint them in the dest chain) and transferring them would cause those tokens to be lost.
```
    function _bridgeMint(address to, uint256 tokenId) public {
        require(msg.sender == address(this) || _msgSender() == owner(), "NotBridge");
        require(tokenId <= 10000, "BadID");
        for (uint i=0; i<assetsLength(); i++) {
            userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];
        }
        super._mint(to, tokenId);
    }
```
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L64-L71

#### [[5]]
Function crossChain() in GovNFT should have limit for maximum tokens allowed to be transferred, because of gas limit in the dest chain. if a user transferred a lot of tokens because there was two loop inside each other in handling logic(one loop through NFT token ids and one loop through assets list so the execution is in order #NFTs * #Assets) so transaction can be revert in the destination chain.
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L124-L165

#### [[6]]
setNode() in TradingExtension contract shouldn't allow owner to set 0x0 address as a valid node, specially when some ESCDA libraries return 0x0 address when signature is invalid and if code uses it in bad way (`isNode[signer(message, hash)] == true`) then all the messages with wrong signature would be calculated as valid messages.
```
    /**
     * @dev whitelists a node
     * @param _node node address
     * @param _bool bool
     */
    function setNode(address _node, bool _bool) external onlyOwner {
        isNode[_node] = _bool;
    }
```
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/TradingExtension.sol#L235-L242

