# No getter!

With the help of JavaParser implement a program that obtains the private fields of public classes that have no public getter in a Java project. 

A field has a public getter if, in the same class, there is a public method that simply returns the value of the field and whose name is `get<name-of-the-field>`.

For example, in the following class:

```Java

class Person {
    private int age;
    private String name;
    
    public String getName() { return name; }

    public boolean isAdult() {
        return age > 17;
    }
}
```

`name` has a public getter, while `age` doesn't.

The program should take as input the path to the source code of the project. It should produce a report in the format of your choice (TXT, CSV, Markdown, HTML, etc.) that lists for each detected field: its name, the name of the declaring class and the package of the declaring class.

Include in this repository the code of your application. Remove all unnecessary files like compiled binaries. See the [instructions](../sujet.md) for suggestions on the projects to use.

*Disclaimer* In a real project not all fields need to be accessed with a public getter.

## Answer

Ce code utilise JavaParser pour analyser le code source du projet, parcourir les classes publiques, et identifier les champs privés sans getter public correspondant. Les détails de chaque champ non conforme sont ensuite inclus dans un rapport généré au format spécifié.

Code of the application :

    import com.github.javaparser.JavaParser;
    import com.github.javaparser.ast.CompilationUnit;
    import com.github.javaparser.ast.body.ClassOrInterfaceDeclaration;
    import com.github.javaparser.ast.body.FieldDeclaration;
    import com.github.javaparser.ast.body.MethodDeclaration;
    import com.github.javaparser.ast.visitor.VoidVisitor;
    import com.github.javaparser.ast.visitor.VoidVisitorAdapter;
    
    import java.io.File;
    import java.io.FileNotFoundException;
    
    public class FieldGetterAnalyzer {

    public static void main(String[] args) {
        // Prenez le chemin du code source du projet en tant qu'argument
        if (args.length == 0) {
            System.err.println("Veuillez fournir le chemin du code source du projet.");
            System.exit(1);
        }

        String sourcePath = args[0];
        analyzeProject(sourcePath);
    }

    private static void analyzeProject(String sourcePath) {
        try {
            // Parsez le code source avec JavaParser
            CompilationUnit compilationUnit = JavaParser.parse(new File(sourcePath));

            // Implémentez un visiteur pour parcourir les classes
            VoidVisitor<Void> classVisitor = new ClassVisitor();
            classVisitor.visit(compilationUnit, null);
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        }
    }

    private static class ClassVisitor extends VoidVisitorAdapter<Void> {
        @Override
        public void visit(ClassOrInterfaceDeclaration n, Void arg) {
            // Vérifiez si la classe est publique
            if (n.isPublic()) {
                // Parcourez les champs de la classe
                for (FieldDeclaration field : n.getFields()) {
                    // Vérifiez si le champ est privé
                    if (field.isPrivate()) {
                        // Vérifiez si la classe a un getter public correspondant
                        if (!hasPublicGetter(n, field)) {
                            // Générez le rapport (vous pouvez adapter le format de sortie selon vos besoins)
                            System.out.println("Champ sans getter public :");
                            System.out.println("Nom du champ : " + field.getVariable(0).getNameAsString());
                            System.out.println("Nom de la classe : " + n.getNameAsString());
                            System.out.println("Package : " + n.findCompilationUnit().get().getPackageDeclaration().map(p -> p.getNameAsString()).orElse("N/A"));
                            System.out.println();
                        }
                    }
                }
            }
            super.visit(n, arg);
        }

        private boolean hasPublicGetter(ClassOrInterfaceDeclaration n, FieldDeclaration field) {
            // Vérifiez si la classe a un getter public correspondant
            String fieldName = field.getVariable(0).getNameAsString();
            String getterName = "get" + fieldName.substring(0, 1).toUpperCase() + fieldName.substring(1);

            return n.getMethodsByName(getterName).stream()
                    .anyMatch(method -> method.isPublic() && method.isMethodDeclaration() &&
                            method.getType().isReferenceType() &&
                            method.getType().asReferenceType().getTypeDeclaration().isPresent() &&
                            method.getType().asReferenceType().getTypeDeclaration().get().isMethod("get" + fieldName));
            }
        }
    }



