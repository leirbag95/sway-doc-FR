
[Sous-devise](#sous-devise)
===========================

L'exemple suivant est un exemple simple de sous-devise qui met en œuvre une fonctionnalité permettant de frapper et d'envoyer un jeton. Il s'agit d'un jeton basé sur le grand livre, c'est-à-dire que le contrat maintient un grand livre des soldes des comptes des utilisateurs.

Comme il s'agit d'un jeton basé sur le grand livre, cet exemple n'utilise pas le [système d'actifs natifs] de Fuel (../blockchain-development/native_assets.html). Il n'est pas recommandé d'utiliser réellement les jetons basés sur le grand livre dans la production ; cet exemple est ici purement à titre d'illustration.

    contrat ;
    
    use std::{chain::auth::{AuthError, msg_sender}, hash::sha256, logging::log, storage::StorageMap} ;
    
    ////////////////////////////////////////
    // Déclarations d'événements
    ////////////////////////////////////////
    // 
    // Les événements permettent aux clients de réagir aux changements dans le contrat.
    // Contrairement à Solidity, les événements sont simplement des structs.
    //
    /// Émis lorsqu'un jeton est envoyé.
    struct Sent {
        from : Adresse,
        à : Adresse,
        amount : u64,
    }
    
    ////////////////////////////////////////
    // Déclarations de méthodes ABI
    ////////////////////////////////////////
    /// ABI pour une sous-monnaie.
    abi Token {
        // Frappe de nouveaux jetons et les envoie à une adresse.
        // Ne peut être appelé que par le créateur du contrat.
        #[stockage(read, write)]
        fn mint(receiver : Address, amount : u64) ;
    
        // Envoie un montant d'un jeton existant.
        // Peut être appelé depuis n'importe quelle adresse.
        #[storage(read, write)]
        fn send(receiver : Address, amount : u64) ;
    }
    
    ////////////////////////////////////////
    // Constantes
    ////////////////////////////////////////
    /// Adresse du créateur du contrat.
    const MINTER = ~Address::from(0x9299da6c73e6dc03eeabcce242bb347de3f5f56cd1c70926d76526d7ed199b8b);
    
    ////////////////////////////////////////
    // Stockage du contrat
    ////////////////////////////////////////
    // Le stockage des contrats persiste à travers les transactions.
    stockage {
        soldes : StorageMap<Address, u64> = StorageMap {},
    }
    
    ////////////////////////////////////////
    // Définitions ABI
    ////////////////////////////////////////
    /// Le contrat implémente l'ABI `Token`.
    impl Token pour Contrat {
        #[stockage(read, write)]
        fn mint(receiver : Adresse, amount : u64) {
            // Note : Le type de retour de `msg_sender()` peut être déduit par le compilateur.
            // compilateur. Il est montré ici pour être explicite.
            let sender : Result<Identity, AuthError> = msg_sender() ;
            let sender : Address = match sender.unwrap() {
                Identité::Adresse(addr) => {
                    assert(addr == MINTER) ;
                    addr
                },
                _ => revert(0),
            } ;
    
            // Augmenter le solde du récepteur
            storage.balances.insert(receiver, storage.balances.get(receiver) + amount)
        }
    
        #[stockage(read, write)]
        fn send(receiver : Address, amount : u64) {
            // Note : Le type de retour de `msg_sender()` peut être déduit par le compilateur.
            // compilateur. Il est montré ici pour être explicite.
            let sender : Result<Identity, AuthError> = msg_sender() ;
            let sender = match sender.unwrap() {
                Identité::Adresse(addr) => addr,
                _ => revert(0),
            } ;
    
            // Réduire le solde de l'expéditeur
            let sender_amount = storage.balances.get(sender) ;
            assert(sender_amount > amount) ;
            storage.balances.insert(sender, sender_amount - amount) ;
    
            // Augmenter le solde du récepteur
            storage.balances.insert(receiver, storage.balances.get(receiver) + amount) ;
    
            log(Envoyé {
                de : expéditeur,
                à : récepteur,
                montant : montant,
            }) ;
        }
    }
    