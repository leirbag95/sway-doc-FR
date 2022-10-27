[Démarrage](#getting-started)
===================================

Suivez ce guide pour écrire, tester et déployer un contrat intelligent simple dans Sway.

[Glossaire](#glossary)
---------------------

Avant de commencer, il peut être utile de comprendre la terminologie qui sera utilisée tout au long de la documentation et la façon dont elle se rapporte les uns aux autres :

* **Fuel** : la blockchain Fuel.
* **FuelVM** : la machine virtuelle qui alimente Fuel.
**Sway** : le langage spécifique au domaine conçu pour la FuelVM ; il est inspiré de Rust.
**Forc** : le système de construction et le gestionnaire de paquets pour Sway, similaire à Cargo pour Rust.

[Comprendre les types de programmes Sway](#understand-sway-program-types)
---------------------------------------------------------------

Il existe quatre types de programmes Sway :

* `contrat`
* `predicate`
* `script`
* `library` (bibliothèque)

Les contrats, prédicats et scripts peuvent produire des artefacts utilisables sur la blockchain, tandis qu'une bibliothèque est simplement un projet conçu pour la réutilisation du code et n'est pas directement déployable.

Voir [le chapitre sur les types de programmes](../sway-program-types/index.html) pour plus d'informations.

[Votre premier projet Sway](#votre-premier-projet-sway)
---------------------------------------------------

Nous allons construire un contrat de compteur simple avec deux fonctions : une pour incrémenter le compteur, et une pour retourner la valeur du compteur.

Quelques informations qui seront utiles avant de continuer :

* Les principales caractéristiques d'un contrat intelligent qui le différencient des scripts ou des prédicats sont qu'il est appelable et avec état.
* Un script est un bytecode exécutable sur la chaîne qui peut appeler des contrats pour effectuer une tâche. Il ne représente pas la propriété d'une ressource et ne peut pas être appelé par un contrat.

### [Écrire le contrat](#writing-the-contract)

Tout d'abord, [installons la chaîne d'outils Sway](./installation.html). Puis, avec `forc` installé, créons un projet de contrat :

    forc new counter_contract
    

Voici le projet que Forc a initialisé :

    $ cd counter_contract
    $ tree .
    ├─── Cargo.toml
    ├── Forc.toml
    ├─── src
    │ └─── main.sw
    └─── tests
        └─── harness.rs
    

`Forc.toml` est le _fichier de manifeste_ (similaire à `Cargo.toml` pour Cargo ou `package.json` pour Node), et définit les métadonnées du projet telles que le nom du projet et les dépendances.

Nous allons écrire notre code dans le dossier `src/main.sw`.

Changez de répertoire dans votre projet de contrat et supprimez le code passe-partout dans `src/main.sw`. Chaque fichier Sway doit commencer par une déclaration du type de programme qu'il contient ; ici, nous avons déclaré que ce fichier est un contrat.

    contrat ;
    

Ensuite, nous allons définir une valeur de stockage. Dans notre cas, nous avons un seul compteur que nous appellerons `counter` de type 64-bit unsigned integer et l'initialiserons à 0.

    storage {
        compteur : u64 = 0,
    }
    

### [ABI](#abi)

Une ABI définit une interface, et il n'y a pas de corps de fonction dans l'ABI. Un contrat doit soit définir, soit importer une déclaration ABI et l'implémenter. Il est considéré comme une bonne pratique de définir votre ABI dans une bibliothèque séparée et de l'importer dans votre contrat car cela permet aux appelants du contrat d'importer et d'utiliser l'ABI dans des scripts pour appeler votre contrat.

Pour plus de simplicité, nous allons définir l'ABI de manière native dans le contrat.

    abi Counter {
        #[stockage(read, write)]
        fn increment() ;
    
        #[stockage(lecture)]
        fn counter() -> u64 ;
    }
    

### [Aller ligne par ligne](#going-line-by-line)

`#[storage(read, write)]` est une annotation qui indique que cette fonction a la permission de lire et d'écrire une valeur dans le stockage.

`fn increment()` - Nous introduisons la fonctionnalité d'incrémentation et indiquons qu'elle ne doit pas retourner de valeur.

`#[storage(read)]` est une annotation qui indique que cette fonction a la permission de lire des valeurs dans le stockage.

`fn counter() -> u64;` - Nous introduisons la fonctionnalité d'incrémentation du compteur et indiquons la valeur de retour de la fonction.

### [Implement ABI](#implement-abi)

Sous la définition de votre ABI, vous écrirez l'implémentation des fonctions définies dans votre ABI.

    impl Compteur pour Contrat {
        #[stockage(read)]
        fn counter() -> u64 {
          return storage.counter ;
        }
        #[stockage(read, write)]
        fn increment() {
            storage.counter = storage.counter + 1 ;
        }
    }
    

> **Note** `return storage.counter;` est équivalent à `storage.counter`.

### [Ce que nous venons de faire](#what-we-just-did)

Lire et retourner la valeur de la propriété counter du stockage du contrat.

    fn counter() -> u64 {
        retourne storage.counter ;
    }
    

Le corps de la fonction accède à la valeur du compteur dans le stockage, et incrémente la valeur de un. Ensuite, nous retournons la nouvelle valeur mise à jour du compteur.

    fn increment() {
        storage.counter = storage.counter + 1 ;
    }
    

### [Construire le contrat](#build-the-contract)

Construisez `counter_contract` en exécutant la commande suivante dans votre terminal depuis le répertoire `counter_contract` :

    forc build
    

Vous devriez s