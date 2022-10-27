[The Fuel Toolchain](#the-fuel-toolchain)
=========================================

La toolchain Fuel est constituée de plusieurs composants.

[Forc (`forc`)](#forc-forc)
---------------------------

Le "Fuel Orchestrator" [Forc](https://github.com/FuelLabs/sway/tree/master/forc) est notre équivalent du [Cargo](https://doc.rust-lang.org/cargo/) de Rust. C'est le principal point d'entrée pour créer, construire, tester et déployer des projets Sway.

[Sway Language Server (`forc-lsp`)](#sway-language-server-forc-lsp)
-------------------------------------------------------------------

Le serveur de langage Sway `forc-lsp` est fourni pour exposer des fonctionnalités aux IDEs. [Instructions d'installation](./installation.html).

Actuellement, seul [Visual Studio Code est supporté par un plugin](https://marketplace.visualstudio.com/items?itemName=FuelLabs.sway-vscode-plugin). Le support de Vim est à venir, bien que [la coloration syntaxique soit fournie](https://github.com/FuelLabs/sway.vim).

> **Note** : Il n'est pas nécessaire de lancer manuellement `forc-lsp` (le plugin le lancera automatiquement), cependant `forc` et `forc-lsp` doivent être dans votre `$PATH`. Pour vérifier si `forc` est dans votre `$PATH`, tapez `forc --help` dans votre terminal.

[Sway Formatter (`forc-fmt`)](#sway-formatter-forc-fmt)
-------------------------------------------------------

Un formateur canonique est fourni avec `forc-fmt`. [Instructions d'installation](./installation.html). Il peut être lancé manuellement avec

    forc fmt
    

Le [plugin Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=FuelLabs.sway-vscode-plugin) formatera automatiquement les fichiers Sway avec `forc-fmt` lors de la sauvegarde.

[Fuel Core (`fuel-core`)](#fuel-core-fuel-core)
-----------------------------------------------

Une implémentation du protocole Fuel, [Fuel Core](https://github.com/FuelLabs/fuel-core), est fournie avec la _Sway toolchain_ pour former la _Fuel toolchain_. [Le SDK Rust](https://github.com/FuelLabs/fuels-rs) démarrera et arrêtera automatiquement une instance du nœud pendant les tests, il n'est donc pas nécessaire d'exécuter manuellement un nœud à moins d'utiliser Forc directement sans le SDK.