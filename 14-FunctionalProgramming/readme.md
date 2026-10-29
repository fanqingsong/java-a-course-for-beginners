# Functional Programming

## Functions as Values

A loop says how to walk a list. A stream says what to do with each element. The action is a lambda, passed into `forEach` or `filter`:

```java
List<String> list = List.of("Apple", "Bat", "Cat", "Dog");

list.stream()
    .filter(element -> element.endsWith("at"))
    .forEach(element -> System.out.println(element));
```

The same filter written with a loop:

```java
for (String string : list) {
    if (string.endsWith("at")) {
        System.out.println(string);
    }
}
```

## Stream Operations

A stream pipeline has intermediate operations and one terminal operation. Intermediate operations (`filter`, `map`, `sorted`, `distinct`) build the pipeline. Nothing runs until a terminal operation (`forEach`, `reduce`, `max`, `min`, `collect`).

```java
List<Integer> numbers = List.of(3, 5, 3, 213, 45, 5, 7);

numbers.stream()
    .distinct()
    .sorted()
    .map(e -> e * e)
    .forEach(e -> System.out.println(e));
```

`IntStream.range(1, 11)` is the numbers 1 through 10. `boxed()` turns the primitive stream into `Stream<Integer>` so it can be collected:

```java
IntStream.range(1, 11)
    .map(e -> e * e)
    .boxed()
    .collect(Collectors.toList());
```

Other exercises from `commands.txt`:

```java
List.of("Apple", "Ant", "Bat").stream()
    .map(s -> s.toLowerCase())
    .forEach(p -> System.out.println(p));

List.of("Apple", "Ant", "Bat").stream()
    .map(s -> s.length())
    .forEach(p -> System.out.println(p));
```

## Reduce, max, min

`reduce` folds the stream into one value. The first argument is the starting value:

```java
int sum = numbers.stream()
    .reduce(0, (number1, number2) -> number1 + number2);
```

`max` and `min` need a `Comparator` and return an `Optional`, because the stream might be empty after `filter`:

```java
int maxEven = List.of(23, 45, 67, 34).stream()
    .filter(n -> n % 2 == 0)
    .max((n1, n2) -> Integer.compare(n1, n2))
    .orElse(0);
```

`orElse(0)` supplies a value when no element matched. `isPresent()` and `get()` are the other ways to look inside an `Optional`.

## Behind the Lambda

Each lambda matches a functional interface with one abstract method:

| Lambda used as | Interface | Method |
| --- | --- | --- |
| `filter` | `Predicate<T>` | `boolean test(T t)` |
| `map` | `Function<T, R>` | `R apply(T t)` |
| `forEach` | `Consumer<T>` | `void accept(T t)` |

A class can implement the same interface. These two pipelines do the same work:

```java
List.of(23, 43, 34, 45, 36, 48).stream()
    .filter(n -> n % 2 == 0)
    .map(n -> n * n)
    .forEach(e -> System.out.println(e));

List.of(23, 43, 34, 45, 36, 48).stream()
    .filter(new EvenNumberPredicate())
    .map(new NumberSquareMapper())
    .forEach(new SystemOutConsumer());
```

A lambda can be stored and returned. That is what "functions are first class citizens" means here:

```java
Predicate<? super Integer> evenPredicate = n -> n % 2 == 0;
Predicate<? super Integer> oddPredicate = createOddPredicate();
```

## Method References

When a lambda only calls one existing method, a method reference replaces it.

| Lambda | Method reference |
| --- | --- |
| `s -> s.length()` | `String::length` |
| `s -> MethodReferencesRunner.print(s)` | `MethodReferencesRunner::print` |
| `n -> MethodReferencesRunner.isEven(n)` | `MethodReferencesRunner::isEven` |
| `(n1, n2) -> Integer.compare(n1, n2)` | `Integer::compare` |

```java
Integer max = List.of(23, 45, 67, 34).stream()
    .filter(MethodReferencesRunner::isEven)
    .max(Integer::compare)
    .orElse(0);
```

## Run

From `14-FunctionalProgramming`:

```bash
javac -d bin src/com/in28minutes/functionalprogramming/*.java
java -cp bin com.in28minutes.functionalprogramming.FunctionalProgrammingRunner
java -cp bin com.in28minutes.functionalprogramming.FPNumberRunner
java -cp bin com.in28minutes.functionalprogramming.LambdaBehindTheScreensRunner
java -cp bin com.in28minutes.functionalprogramming.MethodReferencesRunner
```

`commands.txt` is the jshell session for the stream exercises.
