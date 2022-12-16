State variable in "StableVault.sol" named "allowed" shadows existing function defined in the Interface "IStateVault.sol"

```solidity
// state variable
mapping(address => bool) public allowed;
```

```solidity
// function in the interface
function allowed(address) external view returns (bool);
```