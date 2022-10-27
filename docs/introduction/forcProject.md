[Un projet Forc](#a-forc-project)
=================================

Pour initialiser un nouveau projet avec Forc, utilisez `forc new` :

    forc new mon-projet-carburant
    

Voici le projet que Forc a initialisé :

    $ cd mon-projet-carburant
    $ tree .
    ├─── Forc.toml
    └── src
        └── main.sw
    

`Forc.toml` est le _fichier de manifeste_ (similaire à `Cargo.toml` pour Cargo ou `package.json` pour Node), et définit les métadonnées du projet telles que le nom du projet et les dépendances.

Pour plus d'informations sur la gestion des dépendances, voir : [ici](../forc/dependencies.html).

    [projet]
    auteurs = ["Utilisateur"]
    entrée = "main.sw"
    license = "Apache-2.0
    name = "my-fuel-project"
    
    [dépendances]
    

Voici le contenu du seul fichier Sway du projet, et le point d'entrée principal, `src/main.sw` :

    contrat ;
    
    abi MonContrat {
        fn test_function() -> bool ;
    }
    
    impl MonContrat pour Contrat {
        fn test_function() -> bool {
            true
        }
    }
    

Le projet est un _contrat_, l'un des quatre types de projet différents. Pour plus d'informations sur les différents types de projets, voir [ici](../sway-program-types/index.html).

Nous compilons maintenant notre projet avec `forc build`, en passant le flag `--print-finalized-asm` pour voir l'assemblage généré :

    $ forc build --print-finalized-asm
    ...
    .programme :
    ji i4
    noop
    DATA_SECTION_OFFSET [0..32]
    DATA_SECTION_OFFSET[32..64]
    lw $ds $is 1
    add $$ds $$ds $is
    lw $r0 $fp i73 ; charger le sélecteur de fonction d'entrée
    lw $r1 data_0 ; charge le sélecteur de fn pour la comparaison
    eq $r2 $r0 $r1 ; comparaison de sélecteurs de fonctions
    jnzi $r2 i12 ; saut à la fonction sélectionnée
    movi $$tmp i123 ; code spécial pour le sélecteur non adapté
    rvrt $$tmp ; retour en arrière si aucun sélecteur ne correspond
    ret $one
    .données :
    data_0 .word 559005003
    
      Contrat compilé "my-fuel-project".
      La taille du bytecode est de 60 octets.