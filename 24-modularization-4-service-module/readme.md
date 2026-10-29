# Modularization 4: Service Module

The service from step 2 is now a Java module. `src/module-info.java` names the module and lists the packages other modules may use:

```java
module com.in28minutes.service.provider {
    exports com.in28minutes.sorting.util;
    exports com.in28minutes.sorting.algorithm;
}
```

`exports` is the difference from a jar. A public class in a package that is not exported is invisible to other modules, even though it is `public`.

Both packages are exported here, so a consumer can still construct `BubbleSort` directly. Removing `exports com.in28minutes.sorting.algorithm` keeps `MySortingUtil` available and hides the algorithm. `DirectConsumer` in step 5 would then fail to compile, and `MySortingUtilConsumer` would not.

The classes are the same as step 2: `BubbleSort` and `MySortingUtil`.

## Compile the Module

From `24-modularization-4-service-module`:

```bash
javac -d bin src/module-info.java \
    src/com/in28minutes/sorting/algorithm/*.java \
    src/com/in28minutes/sorting/util/*.java
jar --create --file sorting-service.jar -C bin .
```

The compiled `module-info.class` must be at the root of the jar. `25-modularization-5-consumer-module` requires this module by name.
