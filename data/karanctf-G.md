## Multiple address mappings can be combined into a single mapping of an `address` to a `struct`, where appropriate
check mannulay and only use of addresses is used as a key

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.
```solidity
contracts/GovNFT.sol:267:    mapping(address => mapping(address => uint256)) private userPaid;
contracts/GovNFT.sol:268:    mapping(address => mapping(address => uint256)) private userDebt;
contracts/BondNFT.sol:38:    mapping(address => mapping(address => uint)) public userDebt; // user => tigAsset => amount
```


## Use `abi.encodePacked` for gas optimization
Changing the abi.encode function to abi.encodePacked  can save gas since the abi.encode function pads extra null bytes at the end of the call data, which is unnecessary. Also, in general, abi.encodePacked is more gas-efficient.
```solidity
contracts/GovNFT.sol:141:        bytes memory payload = abi.encode(_to, tokenId);
contracts/utils/TradingLibrary.sol:103:            keccak256(abi.encode(_priceData))
```

