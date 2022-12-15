# ERC20 permit may allow signature replays

If the implementation of ERC20Permit does not check the chain ID as part of the message signed, then signatures will be valid across forks. The OpenZeppelin implementation does not check chain ID: https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/extensions/ERC20Permit.sol#L49

This issue is present in StableVault.depositWithPermit and Trading._handleDeposit.

Recommendation: ensure the implementation of ERC20 includes the chain ID in the message, for example, it should be added as part of the hash here: https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/extensions/ERC20Permit.sol#L60

