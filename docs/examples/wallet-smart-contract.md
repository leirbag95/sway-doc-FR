
[Wallet Smart Contract](#wallet-smart-contract)
===============================================

[Déclaration ABI](#abi-declaration)
-----------------------------------

    library wallet_abi ;
    
    abi Wallet {
        #[stockage(read, write)]
        fn receive_funds() ;
        
        #[storage(read, write)]
        fn send_funds(amount_to_send : u64, recipient_address : Address) ;
    }
    

[ABI Implementation](#abi-implementation)
-----------------------------------------

    contrat ;
    
    utiliser std::{
        chaîne::auth::{
            AuthError,
            msg_sender,
        },
        constantes::BASE_ASSET_ID,
        contexte::{
            call_frames::msg_asset_id,
            msg_amount,
        },
        token::transfer_to_address,
    } ;
    
    utilise wallet_abi::Wallet ;
    const OWNER_ADDRESS = ~Address::from(0x8900c5bec4ca97d4febf9ceb4754a60d782abbf3cd815836c1872116f203f861);
    
    stockage {
        balance : u64 = 0,
    }
    
    impl Portefeuille pour Contrat {
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
  