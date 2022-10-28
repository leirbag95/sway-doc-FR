
[Compteur](#compteur)
===================

Ce qui suit est un exemple simple d'un contrat qui implémente un compteur. Les méthodes ABI `initialize_counter()` et `increment_counter()` retournent toutes deux la valeur actuellement définie.

    forc template --template-name counter mon_compteur_projet


```
contrat ;

abi TestContract {
    #[stockage(write)]
    fn initialize_counter(value : u64) -> u64 ;

    #[stockage(lecture, écriture)]
    fn increment_counter(amount : u64) -> u64 ;
}

stockage {
    compteur : u64 = 0,
}

impl TestContract pour Contrat {
    #[stockage(write)]
    fn initialize_counter(value : u64) -> u64 {
        stockage.compteur = valeur ;
        valeur
    }

    #[stockage(read, write)]
    fn increment_counter(amount : u64) -> u64 {
        let increment = storage.counter + amount ;
        storage.counter = incremented ;
        incrémenté
    }
}
```
    