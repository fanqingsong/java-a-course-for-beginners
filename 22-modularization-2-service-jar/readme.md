# Modularization 2: Service Jar

This directory is only the sorting service from step 1:

- `com.in28minutes.sorting.algorithm.BubbleSort`
- `com.in28minutes.sorting.util.MySortingUtil`

`MySortingUtil.sort` delegates to `BubbleSort`. There is no `main`. The next step compiles this code into a jar and puts that jar on the consumer's classpath.

## Build the Jar

From `22-modularization-2-service-jar`:

```bash
javac -d bin src/com/in28minutes/sorting/algorithm/*.java \
          src/com/in28minutes/sorting/util/*.java
jar --create --file sorting-service.jar -C bin .
```

`23-modularization-3-consumer-jar` compiles against `sorting-service.jar`.
