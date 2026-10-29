# Modularization 3: Consumer Jar

The consumer no longer contains `BubbleSort` or `MySortingUtil`. It compiles and runs against the jar built in `22-modularization-2-service-jar`.

`MySortingUtilConsumer` calls the util API:

```java
MySortingUtil util = new MySortingUtil();
List<String> sorted = util.sort(List.of("Ranga", "Ravi", "Sathish", "Adam", "Eve"));
logger.info(sorted.toString());
```

`DirectConsumer` still imports `com.in28minutes.sorting.algorithm.BubbleSort`. A plain jar hides nothing: every public class in the jar is visible, so both consumers compile.

## Compile and Run

Build the service jar first, then from `23-modularization-3-consumer-jar`:

```bash
javac -d bin -cp ../22-modularization-2-service-jar/sorting-service.jar \
    src/com/in28minutes/consumer/*.java
java -cp bin:../22-modularization-2-service-jar/sorting-service.jar \
    com.in28minutes.consumer.MySortingUtilConsumer
```

On Windows the classpath separator is `;` instead of `:`.

Steps 4 and 5 replace the jar-on-the-classpath setup with Java modules, where `module-info.java` decides which packages another module can see.
