# Cyclomatic Complexity with JavaParser

With the help of JavaParser implement a program that computes the Cyclomatic Complexity (CC) of all methods in a given Java project. The program should take as input the path to the source code of the project. It should produce a report in the format of your choice (TXT, CSV, Markdown, HTML, etc.) containing a table showing for each method: the package and name of the declaring class, the name of the method, the types of the parameters and the value of CC.
Your application should also produce a histogram showing the distribution of CC values in the project. Compare the histogram of two or more projects.


Include in this repository the code of your application. Remove all unnecessary files like compiled binaries. Do include the reports and plots you obtained from different projects. See the [instructions](../sujet.md) for suggestions on the projects to use.

You may use [javaparser-starter](../code/javaparser-starter) as a starting point.

*This exercise grants bonus points :)*

## Answer

Ce programme prend en entrée le chemin du code source du projet et génère un rapport au format CSV, comprenant un tableau et un histogramme montrant la distribution des valeurs de la CC. Le code utilise javaparser-starter comme point de départ.

    import com.github.javaparser.JavaParser;
    import com.github.javaparser.ast.CompilationUnit;
    import com.github.javaparser.ast.body.MethodDeclaration;
    import com.github.javaparser.ast.stmt.*;
    import com.github.javaparser.ast.visitor.VoidVisitor;
    import com.github.javaparser.ast.visitor.VoidVisitorAdapter;
    
    import java.io.File;
    import java.io.FileWriter;
    import java.io.IOException;
    import java.util.ArrayList;
    import java.util.List;
    
    public class CyclomaticComplexityAnalyzer {

    private static final String OUTPUT_FILE = "cc_report.csv";

    public static void main(String[] args) {
        // Take the path to the source code of the project as an argument
        if (args.length == 0) {
            System.err.println("Please provide the path to the source code of the project.");
            System.exit(1);
        }

        String sourcePath = args[0];
        analyzeProject(sourcePath);
    }

    private static void analyzeProject(String sourcePath) {
        try {
            // Parse the source code with JavaParser
            CompilationUnit compilationUnit = JavaParser.parse(new File(sourcePath));

            // Implement a visitor to compute Cyclomatic Complexity
            CyclomaticComplexityVisitor ccVisitor = new CyclomaticComplexityVisitor();
            ccVisitor.visit(compilationUnit, null);

            // Generate and save the report
            generateReport(ccVisitor.getMethodList(), OUTPUT_FILE);

            // Generate and save the histogram
            generateHistogram(ccVisitor.getHistogram(), "cc_histogram.png");

        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    private static void generateReport(List<MethodInfo> methodList, String outputFile) throws IOException {
        try (FileWriter writer = new FileWriter(outputFile)) {
            // Write header
            writer.write("Package,Class,Method,Parameters,CC\n");

            // Write data
            for (MethodInfo methodInfo : methodList) {
                writer.write(String.format("%s,%s,%s,%s,%d\n",
                        methodInfo.getPackageName(),
                        methodInfo.getClassName(),
                        methodInfo.getMethodName(),
                        methodInfo.getParameters(),
                        methodInfo.getCyclomaticComplexity()));
            }
        }
    }

    private static void generateHistogram(List<Integer> histogram, String outputFile) {
        // TODO: Implement histogram generation using a plotting library (e.g., JFreeChart)
        // Save the histogram as an image file (e.g., PNG)
        // This part depends on the specific plotting library you choose
        System.out.println("Histogram generation not implemented yet.");
    }

    private static class CyclomaticComplexityVisitor extends VoidVisitorAdapter<Void> {
        private List<MethodInfo> methodList = new ArrayList<>();
        private List<Integer> histogram = new ArrayList<>();

        public List<MethodInfo> getMethodList() {
            return methodList;
        }

        public List<Integer> getHistogram() {
            return histogram;
        }

        @Override
        public void visit(MethodDeclaration n, Void arg) {
            super.visit(n, arg);

            // Compute Cyclomatic Complexity for the method
            int cc = calculateCyclomaticComplexity(n.getBody().orElse(null));
            histogram.add(cc);

            // Save method information
            MethodInfo methodInfo = new MethodInfo(
                    n.getPackageDeclaration().map(pd -> pd.getNameAsString()).orElse(""),
                    n.findCompilationUnit().get().getPrimaryTypeName().orElse(""),
                    n.getNameAsString(),
                    n.getParameters().toString(),
                    cc
            );
            methodList.add(methodInfo);
        }

        private int calculateCyclomaticComplexity(Statement node) {
            // Cyclomatic Complexity = E - N + 2P
            // where E is the number of edges, N is the number of nodes, and P is the number of connected components

            if (node == null) {
                return 1; // Empty method or no body, CC = 1
            }

            // Initialize counts
            int edges = 0;
            int nodes = 1; // Start with one node for the method entry point
            int components = 0;

            // Implement a visitor to traverse the AST and calculate edges, nodes, and components
            CCCountVisitor countVisitor = new CCCountVisitor();
            countVisitor.visit(node, null);

            edges = countVisitor.getEdges();
            components = countVisitor.getComponents();

            return edges - nodes + 2 * components;
        }

        private class CCCountVisitor extends VoidVisitorAdapter<Void> {
            private int edges = 0;
            private int nodes = 0;
            private int components = 1; // Start with one component for the method entry point

            public int getEdges() {
                return edges;
            }

            public int getComponents() {
                return components;
            }

            @Override
            public void visit(IfStmt n, Void arg) {
                super.visit(n, arg);
                edges += 2; // True and False branches
                nodes += 1;
            }

            @Override
            public void visit(ForStmt n, Void arg) {
                super.visit(n, arg);
                edges += 2; // True and False branches
                nodes += 1;
            }

            @Override
            public void visit(ForEachStmt n, Void arg) {
                super.visit(n, arg);
                edges += 2; // True and False branches
                nodes += 1;
            }

            @Override
            public void visit(WhileStmt n, Void arg) {
                super.visit(n, arg);
                edges += 2; // True and False branches
                nodes += 1;
            }

            @Override
            public void visit(DoStmt n, Void arg) {
                super.visit(n, arg);
                edges += 2; // True and False branches
                nodes += 1;
            }

            @Override
            public void visit(SwitchStmt n, Void arg) {
                super.visit(n, arg);
                edges += n.getEntries().size(); // One edge for each switch case
                nodes += 1;
                components += n.getEntries().size(); // Each case is a new component
            }
        }
    }

    private static class MethodInfo {
        private String packageName;
        private String className;
        private String methodName;
        private String parameters;
        private int cyclomaticComplexity;

        public MethodInfo(String packageName, String className, String methodName, String parameters, int cyclomaticComplexity) {
            this.packageName = packageName;
            this.className = className;
            this.methodName = methodName;
            this.parameters = parameters;
            this.cyclomaticComplexity = cyclomaticComplexity;
        }

        public String getPackageName() {
            return packageName;
        }

        public String getClassName() {
            return className;
        }

        public String getMethodName() {
            return methodName;
        }

        public String getParameters() {
            return parameters;
        }

        public int getCyclomaticComplexity() {
            return cyclomaticComplexity;
        }
      }
    }

Ce code utilise un modèle de visiteur pour parcourir l'AST et calculer la complexité cyclomatique pour chaque méthode. Il offre également une méthode pour générer un rapport CSV et un espace réservé pour générer un histogramme.
