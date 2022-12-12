**contracts/BondNFT.sol**
- L63/106-111/142/145/173/329-332/350/358/373 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.

- L284/292/300/342/350/351 - When the length of an array is used more than once, it is less expensive to create a variable in length memory and use that variable.

- L284/292/300/342 - It is not necessary to create a variable and set a value when we want to set its default value, this is so since when creating the variable it already has that value.

- L220/284/292/300/342 - It is less expensive to do ++i or --i, rather than i++, i-- or i - 1.


**contracts/Lock.sol**
- L113 - When the length of an array is used more than once, it is less expensive to create a variable in length memory and use that variable.


**contracts/StableVault.sol**
- L45/79/90 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.


**contracts/GovNFT.sol**
- L51/65/66/94/130/132/140/153/174/185/194/209/210/241/301 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.

- L53/67/78/95/130/131/200/246/252/258/325 - En vez utilizar el llamado a una funcion (assetsLength) directamente se consulta a la variable en storage, esto genera menos costo de gas. Ademas como esto se utiliza para obtener el length, se deberia crear una variable en memoria y usarla dentro del ciclo for.

- L53/67/78/95/105/131/200/246/252/258/325 - It is not necessary to create a variable and set a value when we want to set its default value, this is so since when creating the variable it already has that value.

- L155/185/209/210 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS

- L53/67/78/95/105/131/200/246/252/258/325 - It is less expensive to do ++i or --i, rather than i++, i-- or i - 1.


**contracts/Referrals.sol**
- L21/66/81 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.

- L70/73 - It is not necessary to create a variable and set a value when we want to set its default value, this is so since when creating the variable it already has that value.


**contracts/PairsContract.sol**
- L35/51/52/75/77/80/94/106/117/141 - It is less expensive to validate that uint != 0 than to validate uint > 0

- L35/50/51/52/75/84/94/95/106/117/141/157/177/190 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.


**contracts/Position.sol**
- L44/47/54/101/108/149/263 - It is less expensive to validate that uint != 0 than to validate uint > 0

- L296/304 - When the length of an array is used more than once, it is less expensive to create a variable in length memory and use that variable.


**contracts/utils/TradingLibrary.sol**
- L105-111/116 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.


**contracts/Trading.sol**
- L622/625 - It is less expensive to validate that uint != 0 than to validate uint > 0

- L491/876/877/887/954/966 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.

- L491/954/966 - When we validate with a require and we could revert, it is important to throw an error message, otherwise the user would not know why he reverted.




