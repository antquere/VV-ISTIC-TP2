# TCC *vs* LCC

Explain under which circumstances *Tight Class Cohesion* (TCC) and *Loose Class Cohesion* (LCC) metrics produce the same value for a given Java class. Build an example of such as class and include the code below or find one example in an open-source project from Github and include the link to the class below. Could LCC be lower than TCC for any given class? Explain.

A refresher on TCC and LCC is available in the [course notes](https://oscarlvp.github.io/vandv-classes/#cohesion-graph).

## Answer

Les circonstances dans lesquelles TCC et LCC produisent la même valeur peuvent se produire lorsque toutes les méthodes de la classe interagissent avec les mêmes sous-ensembles de champs de classe. En d'autres termes, si les méthodes utilisent les mêmes variables membres de la classe, alors TCC et LCC peuvent être égaux.

    public class ComplexExampleClass {
    private int counter;
    private String text;
    private double[] values;

    public void incrementCounter() {
        counter++;
    }

    public void decrementCounter() {
        counter--;
    }

    public void setText(String newText) {
        text = newText;
    }

    public void calculateAverage() {
        // Utilise values pour effectuer des calculs
        double sum = 0;
        for (double value : values) {
            sum += value;
        }
        double average = sum / values.length;
        System.out.println("Average: " + average);
    }

    public void unusedMethod() {
        // Ne fait référence à aucune des variables membres
        // Ne contribue pas à la cohésion entre les méthodes
        }
    }

Dans le code ci-dessus, nous avons ajouté une variable 'values' de type tableau double. La méthode 'calculateAverage' utilise cette variable pour effectuer des calculs, tandis que la méthode 'unusedMethod' ne fait référence à aucune des variables membres de la classe. Cela crée une situation où TCC et LCC produiront la même valeur, car toutes les méthodes interagissent avec le même sous-ensemble de champs de classe, qui comprend : counter, text et values, bien que unusedMethod ne contribue pas à la cohésion en interagissant avec ces variables.

Pour la dernière question, il est possible que la LCC soit inférieure à la TCC pour une classe donnée. Cela peut se produire lorsque certaines méthodes de la classe n'interagissent pas du tout avec les variables membres de la classe. La TCC mesure la cohésion en fonction des interactions entre les méthodes et les variables membres, tandis que la LCC prend en compte toutes les méthodes, qu'elles interagissent ou non avec les variables membres. Ainsi, si certaines méthodes ne contribuent pas à la cohésion en interagissant avec les variables membres, la LCC peut être inférieure à la TCC.

