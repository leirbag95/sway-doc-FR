
[Qu'est-ce qu'un contrat intelligent ?](#what-is-a-smart-contract)
======================================================

Un contrat intelligent n'est pas différent d'un script ou d'un prédicat dans la mesure où il s'agit d'un morceau de bytecode qui est déployé sur la blockchain via une [transaction](https://github.com/FuelLabs/fuel-specs/blob/master/specs/protocol/tx_format.md). Les principales caractéristiques d'un contrat intelligent qui le différencient des scripts ou des prédicats sont qu'il est "appelable" et "étatique". En d'autres termes, un contrat intelligent est analogue à une API déployée avec un certain état de base de données. L'interface d'un contrat intelligent, également appelé contrat, doit être strictement définie par une [déclaration ABI](#the-abi-declaration). Voir [ce contrat](../examples/subcurrency.html) pour un exemple.

[Syntaxe d'un contrat intelligent](#syntaxe-d'un-contrat-smart)
---------------------------------------------------------

Comme tout programme Sway, le programme commence par une déclaration du [type de programme](./index.html) dont il s'agit. Un contrat doit également définir ou importer une [déclaration ABI](#the-abi-declaration) et l'implémenter. Il est considéré comme une bonne pratique de définir votre ABI dans une bibliothèque séparée et de l'importer dans votre contrat. Cela permet aux personnes qui appellent votre contrat de simplement importer l'ABI directement et de l'utiliser dans leurs scripts pour appeler votre contrat. Voyons un exemple de déclaration d'ABI dans une bibliothèque :

    library wallet_abi ;
    
    abi Wallet {
        #[stockage(read, write)]
        fn receive_funds() ;
        
        #[storage(read, write)]
        fn send_funds(amount_to_send : u64, recipient_address : Address) ;
    }
    

Concentrons-nous sur la déclaration ABI et inspectons-la ligne par ligne.

### [La déclaration ABI](#the-abi-declaration)

    abi Wallet {
        #[stockage(read, write)]
        fn receive_funds() ;
        
        #[storage(read, write)]
        fn send_funds(amount_to_send : u64, recipient_address : Address) ;
    }
    

* * *

Dans la première ligne, `abi Wallet {`, nous déclarons le nom de cette _Application Binary Interface_, ou ABI. Nous nommons cette ABI `Wallet`. Pour importer cette ABI dans un script à appeler ou dans un contrat à implémenter, vous utiliserez les éléments suivants

    use wallet_abi::Wallet ;
    

* * *

Dans la deuxième ligne,

        #[storage(read, write)]
        fn receive_funds() ;
    

nous déclarons une méthode ABI appelée `receive_funds` qui, lorsqu'elle est appelée, doit recevoir des fonds dans ce portefeuille. Notez que nous définissons simplement une interface ici, il n'y a donc pas de _corps de fonction_ ou d'implémentation de la fonction. Nous devons seulement définir l'interface elle-même. De cette façon, les déclarations ABI sont similaires aux [déclarations de traits](../advanced/traits.html). Cette méthode ABI particulière ne prend pas de paramètres.

* * *

Dans la troisième ligne,

        #[stockage(read, write)]
        fn send_funds(amount_to_send : u64, recipient_address : Address) ;
    

nous déclarons une autre méthode ABI, cette fois-ci appelée `send_funds`. Elle prend deux paramètres : le montant à envoyer, et l'adresse à laquelle envoyer les fonds.

> **Note** : Les méthodes ABI `receive_funds` et `send_funds` nécessitent également l'annotation `#[storage(read, write)]` car leurs implémentations nécessitent la lecture et l'écriture d'une variable de stockage qui garde la trace du solde du portefeuille, comme nous le verrons bientôt. Reportez-vous à [Purity](../blockchain-development/purity.html#Purity) pour plus d'informations sur les annotations de stockage.


[Implémentation d'une ABI pour un contrat intelligent](#implementing-an-abi-for-a-smart-contract)
-------------------------------------------------------------------------------------

Maintenant que nous avons vu comment définir l'interface, voyons comment l'utiliser. Nous allons commencer par implémenter l'ABI ci-dessus pour un contrat spécifique.

L'implémentation d'une ABI pour un contrat est accomplie avec la syntaxe `impl <nom de l'ABI> pour le contrat`. La syntaxe `for Contract` ne peut être utilisée que pour implémenter une ABI pour un contrat ; l'implémentation de méthodes pour une structure doit utiliser la syntaxe `impl Foo`.

    impl Wallet pour Contrat {
        #[stockage(read, write)]
        fn receive_funds() {
            if msg_asset_id() == BASE_ASSET_ID {
                // Si nous avons reçu `BASE_ASSET_ID` alors gardez la trace du solde.
                // Sinon, nous recevons d'autres actifs natifs et ne nous soucions pas de notre solde de jetons.
                // de notre solde de jetons.
                storage.balance += msg_amount() ;
            }
        }
    
        #[stockage(read, write)]
        fn send_funds(amount_to_send : u64, recipient_address : Address) {
            // Note : Le type de retour de `msg_sender()` peut être déduit par le compilateur.
            // compilateur. Il est indiqué ici pour plus de clarté.
            let sender : Resultat<Identity, AuthError> = msg_sender() ;
            match sender.unwrap() {
                Identité::Adresse(addr) => assert(addr == OWNER_ADDRESS),
                _ => revert(0),
            } ;
    
            let current_balance = storage.balance ;
            assert(solde_actuel >= montant_à_envoyer) ;
    
            storage.balance = current_balance - amount_to_send ;
    
            // Note : `transfer_to_address()` n'est pas un appel et donc pas une // interaction.
            // interaction. Néanmoins, ce code est conforme à
            // checks-effects-interactions pour éviter la ré-entrance.
            transfer_to_address(amount_to_send, BASE_ASSET_ID, recipient_address) ;
        }
    }
    

Vous pouvez remarquer une fois de plus les similitudes entre [traits](../advanced/traits.html) et les ABIs. Et, en effet, en prime, vous pouvez spécifier des méthodes en plus de la surface d'interface d'une ABI, tout comme un trait. En implémentant les méthodes dans la surface d'interface, vous obtenez les implémentations de méthodes supplémentaires For Free™.

Notez que l'implémentation ci-dessus de l'ABI suit le modèle [Checks, Effects, Interactions](https://docs.soliditylang.org/en/v0.6.11/security-considerations.html#re-entrancy).

[Appel d'un contrat intelligent depuis un script](#calling-a-smart-contract-from-a-script)
---------------------------------------------------------------------------------

> **Note** : Dans la plupart des cas, l'appel d'un contrat doit être effectué à partir du [SDK Rust](../testing/testing-with-rust.html) ou du [SDK TypeScript](../frontend/typescript_sdk.html) qui offrent une interface utilisateur plus ergonomique pour interagir avec un contrat. Cependant, dans certaines situations, il est nécessaire d'écrire manuellement un script pour appeler un contrat.

Maintenant que nous avons défini notre interface et l'avons implémentée pour notre contrat, nous devons savoir comment _appeler_ réellement notre contrat. Jetons un coup d'œil à un appel de contrat :

    script ;
    
    use std::constants::ZERO_B256 ;
    use wallet_abi::Wallet ;
    
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
    

Le principal nouveau concept est le `abi cast_ : `abi(AbiName, contract_address)`. Cela renvoie un type `ContractCaller` qui peut être utilisé pour appeler des contrats. Les méthodes de l'ABI deviennent les méthodes disponibles sur cet appelant de contrat : `send_funds` et `receive_funds`. On appelle alors directement la méthode ABI du contrat comme s'il s'agissait d'une méthode ordinaire. Vous avez également la possibilité de spécifier les paramètres spéciaux suivants entre accolades, juste avant la liste principale des paramètres :

1.  `gas` : un `u64` qui représente le gaz qui sera transmis au contrat lorsqu'il sera appelé.
2.  `coins` : un `u64` qui représente le nombre de pièces envoyées lors de l'appel.
3.  `asset_id` : un `b256` qui représente l'ID du _type d'actif_ des pièces transférées.

Chaque paramètre spécial est optionnel et prend une valeur par défaut lorsqu'il est ignoré :

1.  La valeur par défaut pour `gas` est le gaz du contexte (c'est à dire le contenu du registre spécial `$cgas`). Référez-vous aux [spécifications FuelVM](https://github.com/FuelLabs/fuel-specs/blob/master/specs/vm/main.md) pour plus d'informations sur le gaz de contexte.
2.  La valeur par défaut de `coins` est 0.
3.  La valeur par défaut de `asset_id` est `ZERO_B256`.
