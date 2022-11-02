[Bibliothèques](#libraries)
=======================

Les bibliothèques dans Sway sont des fichiers utilisés pour définir de nouveaux comportements communs. L'exemple le plus marquant est la [Sway Standard Library](../introduction/standard_library.html).

[Écriture de bibliothèques](#writing-libraries)
---------------------------------------

Les bibliothèques sont définies à l'aide du mot-clé `library` au début d'un fichier, suivi d'un nom afin qu'elles puissent être importées.

    library my_library ;
    
    // code de la bibliothèque
    

Une bonne bibliothèque de référence à utiliser pour apprendre à concevoir des bibliothèques est la [Sway Standard Library](../introduction/standard_library.html). Par exemple, la bibliothèque standard propose une [implémentation](https://github.com/FuelLabs/sway/blob/master/sway-lib-std/src/option.sw) du `enum Option<T>` qui est un type générique qui représente soit l'existence d'une valeur en utilisant la variante `Some(..)`, soit l'absence d'une valeur en utilisant la variante `None`. Le [fichier Sway implémentant `Option<T>`] (https://github.com/FuelLabs/sway/blob/master/sway-lib-std/src/option.sw) a la structure suivante :

* Le mot-clé `library` suivi du nom de la bibliothèque :

    library option ;
    

* Une déclaration `use` qui importe `revert` d'une autre bibliothèque _en dehors de la bibliothèque standard :

    use ::revert::revert ;
    

* La définition de `enum` qui commence par le mot clé `pub` pour indiquer que cette `Option<T>` est disponible publiquement _en dehors de la bibliothèque `option` :

    pub enum Option<T> {
        // variants
    }
    

* Un bloc `impl` qui implémente certaines méthodes pour `Option<T>` :

    impl<T> Option<T> {
    
        fn is_some(self) -> bool {
            // corps de is_some
        }
    
        // autres méthodes
    }
    

Maintenant que la bibliothèque `option` est entièrement écrite, et parce que `Option<T>` est définie avec le mot-clé `pub`, nous sommes maintenant capables d'importer `Option<T>` en utilisant `use std::option::Option;` depuis n'importe quel projet Sway et d'avoir accès à toutes ses variantes et méthodes. Ceci étant dit, `Option` est automatiquement disponible dans le [prélude de la bibliothèque standard](../introduction/standard_library.html#standard-library-prelude) de sorte que vous n'avez jamais à l'importer manuellement.

Les bibliothèques sont composées d'un seul fichier `Forc.toml` et d'un répertoire `src`, contrairement aux contrats qui contiennent habituellement un répertoire `tests` et un fichier `Cargo.toml` également. Un exemple de fichier `Forc.toml` d'une bibliothèque :

    [projet]
    auteurs = ["Fuel Labs <[email protected]>"]
    entrée = "lib.sw"
    license = "Apache-2.0
    name = "ma_bibliothèque"
    
    [dépendances]
    

qui désigne les auteurs, un fichier d'entrée, le nom par lequel il peut être importé, et les éventuelles dépendances.

Pour les grandes bibliothèques, il est recommandé d'avoir un point d'entrée `lib.sw` qui réexporte toutes les autres sous-bibliothèques. Par exemple, le `lib.sw` de la bibliothèque standard ressemble à :

    library std ;
    
    dep bloc ;
    dep stockage ;
    dep constantes ;
    // ... Autres deps
    

avec d'autres bibliothèques contenues dans le dossier `src`, comme la bibliothèque block (à l'intérieur de `block.sw`) :

    library block ;
    
    // Implémentation de la bibliothèque `block`. 
    

Le mot-clé `dep` dans la bibliothèque principale inclut une dépendance sur une autre bibliothèque, rendant tous ses éléments (comme les fonctions et les structures) accessibles depuis la bibliothèque principale. Le mot-clé `dep` fait simplement de la bibliothèque une dépendance et la rend entièrement accessible dans le contexte actuel.

[Utilisation des bibliothèques](#using-libraries)
-----------------------------------

Les bibliothèques peuvent être importées en utilisant le mot-clé `use` et avec un `::` séparant le nom de la bibliothèque et l'importation.

Voici un exemple d'importation des fonctions `get<T>` et `store<T>` de la bibliothèque `storage`.

    use std::storage::{get, store} ;
    

Les importations joker utilisant `*` sont supportées, mais il est toujours recommandé d'utiliser des importations explicites lorsque cela est possible.

Les bibliothèques _autres que la bibliothèque standard_ doivent être ajoutées comme dépendance dans `Forc.toml`. Cela peut être fait en ajoutant un chemin vers la bibliothèque dans la section `[dependencies]`. Par exemple :

    wallet_lib = { path = "/path/to/wallet_lib" }
    

> **Note** : la bibliothèque standard est implicitement disponible pour tous les projets Forc, c'est-à-dire que vous n'êtes pas obligé de spécifier manuellement `std` comme une dépendance explicite dans `Forc.toml`.

Traduit avec www.DeepL.com/Translator (version gratuite)