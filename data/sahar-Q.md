1. Title:  Missing checks for address(0)
2. Details: If _asset input is set to zero (Intentional or accidental) all next lines are useless, and it just consumes gas. So zero address checking before the following calculations can save a lot of gas
3. GitHub Permalinks: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L65

4. Mitigation risk:  add [ require( _asset !=0, “zero address”)]  to createLock function body.

______________________________________________________________________
1. Title:  Setting an EOA address instead of the trusted contract address.
2. Details:  The purpose of “setTrustedAddress” function is to introduce trusted contracts to the project, so a solution had to be added to prevent introducing EOA addresses instead of contracts.

POC:  The manager knowingly or mistakenly introduces Bob's address (EOA) as the trusted contract address. So bob can make any kind of call or state change or withdrawal as of a trusted contract.

3. GitHub Permalinks: 
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L114

4. Mitigation risk:   Adding the following line to the top of the function somewhat avoids this error. 
  Assembly {
       if( (extcodesize(_contract) == 0) revert()}
}
5. Difficulty+Impact:  Medium(likelihood:  low, impact: low) 
______________________________________________________________________

1. Title:   Bad assignment to gas parameter.
2. Details:  Assigning very small or zero values to  “gas” parameter can interfere with the proper functioning of the “crossChain” function.
3. GitHub Permalinks: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L236

4. Mitigation risk:  set a parameter as “Minimum gas” and adding below code to “setGas”  function can fix this problem:
Require (_gas > MIN_GAS)

5. Difficulty+Impact:    low (likelihood:  low, impact:  low) 

