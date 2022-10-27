[Bibliothèque standard](#standard-library)
=====================================

Comme pour Rust, Sway dispose de sa propre bibliothèque standard.

La bibliothèque standard de Sway est la base du logiciel portable Sway, un ensemble d'abstractions minimales partagées pour l'ensemble de l'écosystème Sway. Elle offre des types de base, comme `Résult<T, E>` et `Option<T>`, des opérations définies par la bibliothèque sur les primitives du langage, la gestion native des actifs, les opérations contextuelles de la blockchain, le contrôle d'accès, la gestion du stockage et la prise en charge des types d'autres VM, entre autres choses.

L'ensemble de la bibliothèque standard de Sway est un projet Forc appelé `std`, et est disponible directement ici : [https://github.com/FuelLabs/sway/tree/master/sway-lib-std](https://github.com/FuelLabs/sway/tree/master/sway-lib-std) (naviguez vers la version appropriée si la dernière version `master` n'est pas compatible).

[Utilisation de la bibliothèque standard](#using-the-standard-library)
---------------------------------------------------------

La bibliothèque standard est rendue implicitement disponible pour tous les projets Forc créés à l'aide de [`forc new`](../forc/commands/forc_new.html). En d'autres termes, il n'est pas nécessaire de spécifier manuellement `std` comme une dépendance explicite. Forc utilisera automatiquement la version de `std` qui correspond à sa version.

L'importation d'éléments de la bibliothèque standard peut être faite en utilisant le mot-clé `use`, tout comme l'importation d'éléments de n'importe quel projet Sway. Par exemple

    use std::storage::StorageMap ;
    

Ceci importe le type `StorageMap` dans l'espace de nom courant.

[Prélude à la bibliothèque standard](#standard-library-prelude)
-----------------------------------------------------

Sway est livré avec une grande variété de choses dans sa bibliothèque standard. Cependant, si vous deviez importer manuellement chaque chose que vous utilisez, ce serait très verbeux. Mais importer beaucoup de choses qu'un programme n'utilise jamais n'est pas bon non plus. Un équilibre doit être trouvé.

Le prélude est la liste des choses que Sway importe automatiquement dans chaque programme Sway. Elle est aussi réduite que possible et se concentre sur les éléments qui sont utilisés dans presque tous les programmes Sway.

La version actuelle du prelude se trouve dans [`std::prelude`](https://github.com/FuelLabs/sway/blob/master/sway-lib-std/src/prelude.sw), et réexporte les éléments suivants :

* [`std::address::Address`](https://github.com/FuelLabs/sway/blob/master/sway-lib-std/src/address.sw), une enveloppe autour du type `b256` représentant une adresse de portefeuille.
* [`std::contract_id::ContractId`](https://github.com/FuelLabs/sway/blob/master/sway-lib-std/src/contract_id.sw), une enveloppe autour du type `b256` représentant l'ID d'un contrat.
* [`std::identity::Identity`](https://github.com/FuelLabs/sway/blob/master/sway-lib-std/src/identity.sw), un enum avec deux variantes possibles : `Address : Address` et `ContractId : ContractId`.
* [`std::vec::Vec`](https://github.com/FuelLabs/sway/blob/master/sway-lib-std/src/vec.sw), un vecteur croissant, alloué au tas.
* [`std::option::Option`](https://github.com/FuelLabs/sway/blob/master/sway-lib-std/src/option.sw), un enum qui exprime la présence ou l'absence d'une valeur.
* [`std::result::Resultat`](https://github.com/FuelLabs/sway/blob/master/sway-lib-std/src/result.sw), un enum pour les fonctions qui peuvent réussir ou échouer.
* [`std::assert::assert`](https://github.com/FuelLabs/sway/blob/master/sway-lib-std/src/assert.sw), une fonction qui renvoie la VM si la condition qui lui est fournie est `false`.
* [`std::revert::require`](https://github.com/FuelLabs/sway/blob/master/sway-lib-std/src/revert.sw), une fonction qui renverse la VM et enregistre une valeur donnée si la condition qui lui est fournie est `false`.
* [`std::revert::revert`](https://github.com/FuelLabs/sway/blob/master/sway-lib-std/src/revert.sw), une fonction qui renverse la VM.