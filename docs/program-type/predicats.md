[Prédicats](#prédicats)
=========================

Du point de vue de Sway, les prédicats sont des programmes qui renvoient une valeur booléenne et qui représentent la propriété d'une certaine ressource lors de leur exécution à true. Ils n'ont pas accès au stockage des contrats. Voici un prédicat trivial, qui s'évalue toujours à true :

    prédicat ;
    
    // Tous les prédicats nécessitent une fonction main qui renvoie une valeur booléenne.
    fn main() -> bool {
        true
    }
    

[Prédicats de débogage](#debugging-predicates)
---------------------------------------------

Parce qu'ils n'ont pas d'effets secondaires (ils sont _purs_), les prédicats ne peuvent pas créer de reçus. Par conséquent, ils ne peuvent pas avoir de logging ou créer un backtrace de la pile. Cela signifie qu'il n'y a pas de moyen naïf de les déboguer à part l'utilisation d'un débogueur à pas simple (qui est un [work-in-progress](https://github.com/FuelLabs/fuel-debugger/pull/1)).

Comme solution de contournement, le prédicat peut être écrit, testé et débogué d'abord en tant que `script`, et ensuite transformé en `predicate`.