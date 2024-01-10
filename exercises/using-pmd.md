# Using PMD

Pick a Java project from Github (see the [instructions](../sujet.md) for suggestions). Run PMD on its source code using any ruleset. Describe below an issue found by PMD that you think should be solved (true positive) and include below the changes you would add to the source code. Describe below an issue found by PMD that is not worth solving (false positive). Explain why you would not solve this issue.

You can use the default [rule base](https://github.com/pmd/pmd/blob/master/pmd-java/src/main/resources/rulesets/java/quickstart.xml) available on the source repository of PMD.

## Answer

Tests sur projet de gestion de bibliothèque (GitHub : openlibrary) :

Vraie Positive :
PMD a détecté une vraie positive liée à une règle de complexité cyclomatique élevée dans la méthode de recherche des livres. La méthode contient plusieurs blocs "if" imbriqués, augmentant la complexité du code et le rendant difficile à comprendre.

Changements proposés :
Pour résoudre cette vraie positive, nous pourrions réorganiser la logique de la méthode de recherche en introduisant des méthodes auxiliaires pour gérer les différents cas de recherche (par titre, par auteur, par catégorie, etc.). Cela simplifierait chaque bloc "if" et rendrait le code plus lisible.

Fausse Positive :
PMD signale une fausse positive concernant une variable non utilisée dans une classe de gestion d'utilisateurs. La variable en question est déclarée mais n'est pas utilisée directement dans cette classe, car elle est utilisée dans une classe héritée spécifique à un cas d'utilisation rare.

Explication :
Dans ce cas, la variable non utilisée dans la classe parente peut être une nécessité pour des cas particuliers, même si elle n'est pas utilisée directement dans cette classe spécifique. La fausse positive peut être ignorée ici, car la variable a une utilité dans un contexte plus large du système.

