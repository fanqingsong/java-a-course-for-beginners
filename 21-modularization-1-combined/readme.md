# Modularization 1: One Project

The sorting code and the code that uses it live in the same project. Later steps split them into a jar and then into modules. `BubbleSort.sort` in this sample returns the list unchanged. The point of the example is the package boundary, not the algorithm.

## Service

`com.in28minutes.sorting.algorithm.BubbleSort` is the implementation.

`com.in28minutes.sorting.util.MySortingUtil` is the API a caller is meant to use. It creates a `BubbleSort` and returns the result:

```java
public class MySortingUtil {
    public List<String> sort(List<String> names) {
        BubbleSort bubbleSort = new BubbleSort();
        return bubbleSort.sort(names);
    }
}
```

## Two Consumers

`MySortingUtilConsumer` depends only on the util class:

```java
MySortingUtil util = new MySortingUtil();
List<String> sorted = util.sort(List.of("Ranga", "Ravi", "Sathish", "Adam", "Eve"));
logger.info(sorted.toString());
```

`DirectConsumer` constructs `BubbleSort` itself. It is coupled to the algorithm package. When that package is no longer exported, this class stops compiling. The util consumer does not.

## Run

From `21-modularization-1-combined`:

```bash
javac -d bin src/com/in28minutes/sorting/algorithm/*.java \
          src/com/in28minutes/sorting/util/*.java \
          src/com/in28minutes/consumer/*.java
java -cp bin com.in28minutes.consumer.MySortingUtilConsumer
java -cp bin com.in28minutes.consumer.DirectConsumer
```
