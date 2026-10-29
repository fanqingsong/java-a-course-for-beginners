# Modularization 5: Consumer Module

The consumer is its own module. It cannot see a package unless its `module-info.java` requires the providing module and that module exports the package.

```java
module com.in28minutes.consumer {
    requires com.in28minutes.service.provider;
    requires transitive java.logging;
}
```

- `requires com.in28minutes.service.provider` reads the service module from step 4.
- `requires java.logging` is needed because the consumer uses `java.util.logging.Logger`. `transitive` also exposes `java.logging` to any module that requires this consumer.

`MySortingUtilConsumer` uses the exported util package. `DirectConsumer` uses `BubbleSort`. That compiles only while the service module still exports `com.in28minutes.sorting.algorithm`.

## Compile and Run

Build `sorting-service.jar` in step 4 first. From `25-modularization-5-consumer-module`:

```bash
javac -d bin \
    --module-path ../24-modularization-4-service-module/sorting-service.jar \
    src/module-info.java \
    src/com/in28minutes/consumer/*.java

java --module-path bin:../24-modularization-4-service-module/sorting-service.jar \
    --module com.in28minutes.consumer/com.in28minutes.consumer.MySortingUtilConsumer
```

`--module-path` replaces `-cp` for modules. The launch name is `module/main-class`.
