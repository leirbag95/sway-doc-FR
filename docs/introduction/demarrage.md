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

Tout d'abord, [installons la toolchain Sway](./installation.html). Puis, avec `forc` installé, créons un projet de contrat :

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
    

Vous devriez voir quelque chose comme cette sortie :

    Bibliothèque compilée "core".
      Bibliothèque compilée "std".
      Contrat compilé "counter_contract".
      La taille du bytecode est de 224 octets.
    

### [Déployer le contrat](#deployer-le-contrat)

Il est maintenant temps de déployer le contrat et de l'appeler sur un nœud Fuel. Nous allons montrer comment le faire en utilisant `forc` en ligne de commande, mais vous pouvez aussi le faire en utilisant le [Rust SDK](https://fuellabs.github.io/fuels-rs/master/getting-started/contracts.html) ou le [TypeScript SDK](https://fuellabs.github.io/fuels-ts/#deploying-contracts).

### [Faite tourner votre noeud Fuel](#spin-up-a-fuel-node)

Dans un onglet séparé de votre terminal, créez un nœud Fuel local :

    fuel-core run --db-type in-memory
    

Ceci démarre un noeud Fuel avec une base de données volatile qui sera effacée à l'arrêt (bon pour les tests).

### [Déployer `counter_contract` vers votre noeud Fuel local](#deploy-counter_contract-to-your-local-fuel-node)

Pour déployer `counter_contract` sur votre noeud Fuel local, ouvrez un nouvel onglet de terminal et exécutez la commande suivante depuis la racine du répertoire `wallet_contract` :

    forc deploy --unsigned
    

**Note** Vous ne pouvez pas utiliser la même session de terminal qui exécute fuel-core pour exécuter d'autres commandes car cela mettrait fin à votre processus fuel-core.

Ceci devrait produire une sortie dans `stdout` qui ressemble à ceci :

    $ forc deploy --unsigned
      Bibliothèque compilée "core".
      Bibliothèque compilée "std".
      Contrat compilé "counter_contract".
      La taille du bytecode est de 224 octets.
    Contract id: 0xaf94c0a707756caae667ee43ca18bace441b25998c668010192444a19674dc4f
    Journaux :
    TransactionId(HexFormatted(7cef24ea33513733ab78c5daa5328d622d4b38187d0f0d1857b272090d99f96a))
    

Notez l'identifiant du contrat - vous en aurez besoin si vous voulez construire un frontend pour interagir avec ce contrat.

[Test de votre contrat](#testing-your-contract)
-----------------------------------------------

Dans le répertoire `tests`, naviguez jusqu'à `harness.rs.` Ici vous verrez qu'il y a un peu de code passe-partout pour vous aider à commencer à interagir avec et à tester votre contrat.

Au bas du fichier, définissez le corps de `can_get_contract_instance`. Voici à quoi devrait ressembler votre code pour vérifier que la valeur du compteur a bien été incrémentée :

    #[tokio::test]
    async fn can_get_contract_instance() {
        // Incrémente le compteur
        let _result = instance.increment().call().await.unwrap() ;
    
        // Récupère la valeur actuelle du compteur
        let result = instance.counter().call().await.unwrap() ;
        assert !(result.value > 0) ;
    }
    

Exécutez la commande suivante dans le terminal : `forc test`.

Vous verrez quelque chose comme ceci dans votre sortie :

      Bibliothèque compilée "core".
      Bibliothèque compilée "std".
      Contrat compilé "counter_contract".
      La taille du bytecode est de 224 octets.
       Compilation de counter_contract v0.1.0 (<path/to/counter_contract>)
        Terminé test [unoptimized + debuginfo] cible(s) en 4.55s
         Exécution de tests/harness.rs (target/debug/deps/integration_tests-7a2922c770587b45)
    
    Exécution de 1 test
    test can_get_contract_id ... ok
    
    Résultat du test : ok. 1 réussi ; 0 échoué ; 0 ignoré ; 0 mesuré ; 0 filtré ; terminé en 0.09s
    

Félicitations, vous venez de créer et de tester votre premier contrat intelligent Sway 🎉. Vous pouvez maintenant construire un frontend pour interagir avec votre contrat en utilisant le SDK TypeScript. Vous pouvez trouver un guide étape par étape pour construire un front-end pour votre projet [ici](https://fuellabs.github.io/fuels-ts/QUICKSTART.html).