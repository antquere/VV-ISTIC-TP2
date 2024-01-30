# Extending PMD

Use XPath to define a new rule for PMD to prevent complex code. The rule should detect the use of three or more nested `if` statements in Java programs so it can detect patterns like the following:

```Java
if (...) {
    ...
    if (...) {
        ...
        if (...) {
            ....
        }
    }

}
```
Notice that the nested `if`s may not be direct children of the outer `if`s. They may be written, for example, inside a `for` loop or any other statement.
Write below the XML definition of your rule.

You can find more information on extending PMD in the following link: https://pmd.github.io/latest/pmd_userdocs_extending_writing_rules_intro.html, as well as help for using `pmd-designer` [here](https://github.com/selabs-ur1/VV-ISTIC-TP2/blob/master/exercises/designer-help.md).

Use your rule with different projects and describe you findings below. See the [instructions](../sujet.md) for suggestions on the projects to use.

## Answer

Définition XML de la nouvelle règle :

    <rule name="AvoidNestedIfStatements"
    language="java"
    message="Avoid using three or more nested if statements"
    class="net.sourceforge.pmd.lang.rule.XPathRule">
    <description>
    This rule detects the use of three or more nested if statements in Java programs.
    </description>
    <priority>3</priority>
    <properties>
    <!-- Specify the maximum allowed nesting level -->
    <property name="maxNestingLevel" value="3"/>
    </properties>
    <example>
    <![CDATA[
        if (...) {
            ...
            if (...) {
                ...
                if (...) {
                    ....
                }
            }
        }
    ]]>
    </example>
    <xpath>
    <![CDATA[
        //IfStatement
            [count(.//IfStatement) >= $maxNestingLevel]
    ]]>
    </xpath>
    </rule>


- L'attribut name est le nom de la règle.
- L'attribut language est défini sur "java" car la règle est destinée aux programmes Java.
- L'attribut message fournit un message bref pour les violations.
- L'attribut class est défini sur "net.sourceforge.pmd.lang.rule.XPathRule" pour une règle XPath.
- L'élément description fournit des informations supplémentaires sur la règle.
- L'élément priority définit la priorité de la règle.
- L'élément properties vous permet de spécifier des propriétés configurables. Dans ce cas, il inclut la propriété maxNestingLevel pour définir le niveau d'imbrication maximal autorisé.
- L'élément example fournit un exemple de code qui violerait la règle.
- L'élément xpath contient l'expression XPath qui définit le motif à rechercher. Dans ce cas, elle compte le nombre de déclarations "IfStatement" imbriquées et signale une violation si le nombre dépasse le niveau d'imbrication maximal spécifié.
- Nombre de Violations : 9
- Commentaire : L'application de la règle sur ce framework a mis en évidence des cas où des déclarations if étaient imbriquées, notamment dans la gestion des routes et des filtres. Ces zones nécessitent une attention particulière pour garantir une maintenance plus aisée du framework.

