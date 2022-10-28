
[FizzBuzz](#fizzbuzz)
=====================

Cet exemple n'est pas le traditionnel [FizzBuzz](https://en.wikipedia.org/wiki/Fizz_buzz#Programming) ; il s'agit plutôt de la version contrat intelligent ! Un script peut appeler la méthode ABI `fizzbuzz` de ce contrat avec une valeur `u64` et recevoir en retour son caractère fizzbuzzable sous forme d'un `enum`.

Le format des structures personnalisées et des enums tels que `FizzBuzzResult` sera automatiquement inclus dans le JSON ABI afin que le code hors chaîne puisse gérer la forme encodée des données retournées.

    contrat ;
    
    enum FizzBuzzResult {
        Fizz : (),
        Buzz : (),
        FizzBuzz : (),
        Other : u64,
    }
    
    abi FizzBuzz {
        fn fizzbuzz(input : u64) -> FizzBuzzResult ;
    }
    
    impl FizzBuzz pour Contrat {
        fn fizzbuzz(input : u64) -> FizzBuzzResult {
            si entrée % 15 == 0 {
                FizzBuzzResult::FizzBuzz
            } else if input % 3 == 0 {
                FizzBuzzResult::Fizz
            } else if input % 5 == 0 {
                FizzBuzzResult::Buzz
            } else {
                FizzBuzzResult::Autre(entrée)
            }
        }
    }
    
