## Use safeTransferFrom instead of transferFrom

[StableVault.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L46)

Issue:
The listed contract is using the unsafe transferFrom function. In case of failure, these functions return false but the output of these method is not validated

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

Recommendation:
Use safeTransferFrom function which will revert in case of failure

## Role segregation is missing

[StableToken.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableToken.sol#L13)

Impact:
It was observed that Minter role is also allowed to Burn tokens. It is always better to have roles segregated. 

```
function burnFrom(
        address account,
        uint256 amount
    ) 
        public 
        virtual 
        onlyMinter() 
    {
        _burn(account, amount);
    }
```

Recommendation:
A separate role can be created for Burn so that Minter will not be allowed to burn tokens