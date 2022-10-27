
[Installation](#installation)
=============================

La _Sway toolchain_ est suffisante pour compiler les smart contracts Sway. Sinon, notez que si vous voulez exécuter des smart contracts Sway (par exemple pour des tests), un nœud complet Fuel Core est nécessaire, que vous trouverez avec la _Sway toolchain_ en tant que _Fuel toolchain_.

[Installation à partir de fichier binaires pré-compilés](#installing-from-pre-compiled-binaries)
-------------------------------------------------------------------------------

Des fichier binaires pré-compilés pour Linux et macOS sont disponibles pour la Sway toolchain. Windows natif n'est actuellement pas pris en charge ([suivre le ticket dédié au support Windows](https://github.com/FuelLabs/sway/issues/1526)). Le sous-système Windows pour Linux devrait fonctionner mais n'est pas officiellement supporté.

[`fuelup`](https://github.com/FuelLabs/fuelup) est l'équivalent de `rustup` de Rust pour la Fuel toolchain. Il permet de télécharger facilement des versions binaires de la Fuel toolchain.

Commencez par installer `fuelup` avec :

    curl --proto '=https' --tlsv1.2 -sSf \
        https://fuellabs.github.io/fuelup/fuelup-init.sh | sh
    

`fuelup-init` vous demandera la permission d'ajouter `~/.fuelup/bin` à votre PATH. Sinon, vous pouvez aussi passer `--no-modify-path` pour que `fuelup-init` ne modifie pas votre PATH :

    curl --proto '=https' --tlsv1.2 -sSf \
        https://fuellabs.github.io/fuelup/fuelup-init.sh | sh -s -- --no-modify-path
    

Une fois `fuelup` installé, `fuelup-init` exécute automatiquement la commande suivante

    fuelup toolchain install latest
    

pour installer la dernière version de la Fuel toolchain.

Vous pouvez exécuter la même commande plus tard pour mettre à jour la toolchain.

[Installation à partir des sources](#installing-from-source)
-------------------------------------------------

### [Dépendances](#dependances)

La toolchain Rust est un prérequis pour installer et utiliser Sway. Les instructions spécifiques à la plateforme pour installer `rustup` peuvent être trouvées [ici](https://www.rust-lang.org/tools/install). Ensuite, installez la toolchain Rust avec :

    # Installez la dernière toolchain Rust stable.
    rustup install stable
    

L'installation de `fuel-core` peut nécessiter l'installation de dépendances système supplémentaires. Voir [ici](https://github.com/FuelLabs/fuel-core#building) pour les instructions.

La toolchain Sway est construite et testée avec la version `stable` de la toolchain Rust ([https://github.com/rust-lang/rust/releases/latest](https://github.com/rust-lang/rust/releases/latest)). Il n'y a aucune garantie qu'elle fonctionnera avec la toolchain Rust `nightly`, ou avec des versions `stable` antérieures, donc assurez-vous que vous utilisez `stable` avec :

    # Mettre à jour la toolchain Rust installée ; peut être utilisé indépendamment.
    rustup update
    # Définir la toolchain Rust stable par défaut ; peut être utilisé indépendamment.
    rustup default stable
    

### [Installation à partir de Cargo](#installing-from-cargo)

La toolchain Sway et le nœud complet Fuel Core peuvent être installés à partir des sources avec Cargo :

    cargo install forc fuel-core
    

#### [Mise à jour de `forc` depuis Cargo](#updating-forc-from-cargo)

Vous pouvez mettre à jour la toolchain depuis les sources avec Cargo avec :

    cargo install forc fuel-core
    

#### [Installation des plugins `forc` depuis Cargo](#installing-forc-plugins-from-cargo)

L'écosystème Fuel possède quelques plugins qui peuvent être facilement installés via Cargo.

> **Note** : `forc` détecte tout ce qui se trouve dans votre `$PATH` préfixé avec `forc-` comme un plugin. Utilisez `forc plugins` pour voir ce que vous avez actuellement installé.

    # Formateur Sway
    cargo install forc-fmt
    
    # Explorateur de blocs
    cargo install forc-explore
    
    # Serveur de langue Sway
    cargo install forc-lsp
    

### [Construire à partir des sources](#building-from-source)

Plutôt que d'installer à partir de `cargo`, la toolchain Sway peut être construite à partir d'un checkout local des sources en suivant les instructions à [https://github.com/FuelLabs/sway](https://github.com/FuelLabs/sway). L'implémentation complète du nœud Fuel Core peut être construite à partir des sources en suivant les instructions de [https://github.com/FuelLabs/fuel-core](https://github.com/FuelLabs/fuel-core).

[Activer la complétion de tabulation pour Bash, Fish, Zsh ou PowerShell](#enable-tab-completion-for-bash-fish-zsh-or-powershell)
------------------------------------------------------------------------------------------------------------------

`forc` supporte la génération de scripts de complétion pour Bash, Fish, Zsh, et PowerShell. Voir `forc completions --help` pour plus de détails, mais l'essentiel est aussi simple que d'utiliser un des éléments suivants :

    # Bash
    forc completions --shell=bash > ~/.local/share/bash-completion/completions/forc
    
    # Bash (macOS/Homebrew)
    forc completions --shell=bash > $(brew --prefix)/etc/bash_completion.d/forc.bash-completion
    
    # Poisson
    mkdir -p ~/.config/fish/completions
    forc completions --shell=fish > ~/.config/fish/completions/forc.fish
    
    # Zsh
    forc completions --shell=zsh > ~/.zfunc/_forc
    
    # PowerShell v5.0+
    complétions forc --shell=powershell >> $PROFILE.CurrentUserCurrentHost
    # ou
    forc completions --shell=powershell | Out-String | Invoke-Expression
    

Une fois que les complétions ont été générées et correctement installées, fermez et rouvrez votre terminal pour que les nouvelles complétions prennent effet.