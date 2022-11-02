[Scripts](#scripts)
===================

Un script est un bytecode exécutable sur la chaîne qui s'exécute une fois pour effectuer une tâche. Il ne représente pas la propriété d'une ressource et ne peut pas être appelé par un contrat. Un script peut retourner une seule valeur de n'importe quel type.

Les scripts sont conscients de l'état dans la mesure où, bien qu'ils n'aient pas de stockage persistant (car ils n'existent que pendant la transaction), ils peuvent appeler des contrats et agir en fonction des valeurs et des résultats renvoyés.

Cet exemple de script appelle un contrat :

    script ;
    
    use std::constants::ZERO_B256 ;
    utilise wallet_abi::Wallet ;
    
    fn main() {
        let contract_address = 0x9299da6c73e6dc03eeabcce242bb347de3f5f56cd1c70926d76526d7ed199b8b ;
        let caller = abi(Wallet, contract_address) ;
        let amount_to_send = 200 ;
        let recipient_address = ~Address::from(0x9299da6c73e6dc03eeabcce242bb347de3f5f56cd1c70926d76526d7ed199b8b);
        appelant.send_funds {
            gaz : 10000,
            pièces : 0,
            asset_id : ZERO_B256,
        }(amount_to_send, recipient_address) ;
    }
    

Les scripts, comme les prédicats, s'appuient sur une fonction `main()` comme point d'entrée. Vous pouvez appeler d'autres fonctions définies dans un script à partir de la fonction `main()` ou appeler un autre contrat via un [abi cast](./smart_contracts.html#calling-a-smart-contract-from-a-script).

Un exemple d'utilisation d'un script serait un routeur qui négocie des fonds à travers plusieurs DEX pour obtenir le prix de l'actif d'entrée, ou un script pour réajuster une position de dette collatéralisée via un prêt flash.

[Scripts et SDK](#scripts-et-les-sdks)
---------------------------------------------

Contrairement aux transactions EVM qui peuvent appeler un contrat directement (mais ne peuvent appeler qu'un seul contrat), les transactions Fuel exécutent un script, qui peut appeler zéro ou plusieurs contrats. Les SDK Rust et TypeScript fournissent des fonctions permettant d'appeler des méthodes de contrat comme si elles appelaient directement des contrats. Sous le capot, les SDK enveloppent tous les appels de contrat avec des scripts qui contiennent un code minimal pour simplement effectuer l'appel et transmettre des données de script comme paramètres d'appel.