### [Gas Optimization]

## [G-1] State variables and struct can be closely packed to save gas

There are 4 instance of it

1st instance in the Trading.sol in the Fees struct 
there instead of uint we can use uint128 for saving it for 2 slot 
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L98-L103

2nd instance is on
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/IPosition.sol#L7-L20

3rd is on  here bool can be written under the address 
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/IPosition.sol#L7-L20

4th is on here also bool can written under the addres to save 1 slot 
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/interfaces/IPosition.sol#L22-L33


