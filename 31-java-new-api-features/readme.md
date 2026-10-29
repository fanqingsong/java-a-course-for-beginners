# Java New API Features

Each package under `src/com/in28minutes/api` is one feature. Several of them need a recent JDK: sealed classes and pattern matching in `switch` need 21, sequenced collections need 21, and stream gatherers need 24. Virtual threads need 21.

## `List.copyOf`

`List.copyOf` returns an unmodifiable copy. Changing the original list does not change the copy. Calling `add` on the copy throws `UnsupportedOperationException`.

```java
List<String> names = new ArrayList<>();
names.add("Ranga");
names.add("Ravi");
names.add("John");

List<String> copyOfNames = List.copyOf(names);
copyOfNames.add("ShouldNotbeAllowed");
```

## Read and Write a Whole File as a String

`Files.readString` and `Files.writeString` avoid the line-by-line API from the files chapter. Paths are relative to the working directory, so run this from `31-java-new-api-features`.

```java
Path path = Paths.get("./resources/sample.txt");
String fileContent = Files.readString(path);
String newFileContent = fileContent.replace("Line", "Lines");
Files.writeString(Paths.get("./resources/sample-new.txt"), newFileContent);
```

## `Predicate.not`

`Predicate.not` negates a predicate, including a method reference:

```java
numbers.stream()
    .filter(Predicate.not(PredicateNotRunner::isEven))
    .forEach(System.out::println);
```

## String Methods

```java
" ".isBlank();                                          // true
" L R ".strip();                                        // "L R"
" L R ".stripLeading();
" L R ".stripTrailing();
"Line1\nLine2\nLine3\nLine4".lines();                   // Stream<String>
"UPPER".transform(s -> s.substring(2));                 // "PER"
"My name is %s. My age is %d.".formatted("Ranga", 25);
```

`isBlank()` on a `null` reference throws `NullPointerException`. The message names the variable that was null (`str` or `sample.str`).

## Local-Variable Type Inference

`var` asks the compiler to use the type of the initializer. The variable still has a fixed type. `var` needs an initializer, and it is allowed in a `for` loop variable.

```java
var names1 = List.of("Ranga", "Ravi");
final var names = List.of(names1, List.of("John", "Adam"));

for (var name : names1)
    System.out.println(name);
```

`var` is also a legal variable name (`var var = ""`), which makes that line hard to read.

## Switch Expressions

A switch expression produces a value. Each arm is a single expression, or a block that ends with `yield`. There is no fall-through.

```java
String dayOfWeek = switch (day) {
    case 0 -> {
        System.out.println("Do Some complex logic here");
        yield "Sunday";
    }
    case 1 -> "Monday";
    case 2 -> "Tuesday";
    case 3 -> "Wednesday";
    default -> throw new IllegalArgumentException("Invalid Option" + day);
};
```

An enum switch can group labels with a comma. If every constant is covered, `default` is optional:

```java
String str = switch (dayOfWeek) {
    case MONDAY -> "First Day Of the work week!";
    case TUESDAY, WEDNESDAY, THURSDAY -> "Middle Of the Work Week";
    case FRIDAY -> "Last Day of the work week!";
    case SATURDAY, SUNDAY -> "Weekend";
};
```

`SwitchRecordsRunner` switches on a sealed type and deconstructs the record in the pattern:

```java
String response = switch (customerMessage) {
    case Message(String text) -> text;
    case Feedback(int rating, String description) -> description;
};
```

## Text Blocks

A text block starts with `"""` and a line break. Incidental indentation is removed. `formatted` fills `%s` placeholders:

```java
String textBlock = """
        Line 1: %s
        Line 2: %s
        Line 3
        Line 4
        """.formatted("Some Value", "Some Other Value");
```

## Records

A record declares its state once. The compiler generates the constructor, accessors, `equals`, `hashCode`, and `toString`.

A compact constructor runs before the fields are assigned. An accessor can be overridden:

```java
record Person(String name, String email, String phoneNumber) {
    Person {
        if (name == null)
            throw new IllegalArgumentException("name is null");
    }

    public String name() {
        System.out.println("Do Something");
        return name;
    }
}
```

Two records with the same component values are equal.

## Sealed Classes and Interfaces

A `sealed` type lists every permitted subtype. Each permitted subtype is `final`, `sealed`, or `non-sealed`.

- `final` — no further subclass
- `sealed` — its own `permits` list
- `non-sealed` — open for any subclass

```java
sealed class Vehicle permits Car, Truck, Bike {}

final class Car extends Vehicle {}
non-sealed class Truck extends Vehicle {}
sealed class Bike extends Vehicle permits SuperBike {}
final class SuperBike extends Bike {}
```

`SealedInterfacesRunner` is the same rule for an interface: `Flyable` permits `Bird`, `Aeroplane`, and `Helicopter`.

## Virtual Threads

A platform thread is an OS thread. Creating a million of them in `PlatformThreadsRunner` is expensive. A virtual thread is scheduled by the JVM onto a small set of platform threads, so a million sleeping tasks is practical:

```java
Thread.startVirtualThread(new SleepingThread());
// Thread.ofVirtual().start(new SleepingThread());

ExecutorService executor = Executors.newVirtualThreadPerTaskExecutor();
executor.execute(new SleepingThread());
```

`SleepingThread` sleeps for one second. The runners loop one million times and print the index, so they are demonstrations rather than something to leave running.

## Sequenced Collections

Java 21 adds a uniform way to reach the first and last element.

`SequencedCollection` (`ArrayList`, and `LinkedHashSet` as a `SequencedSet`):

```java
courseDetails.addFirst("Spring Boot");
courseDetails.addLast("Spring Boot in AI");
courseDetails.getFirst();
courseDetails.getLast();
courseDetails.removeFirst();
courseDetails.removeLast();
courseDetails.reversed();
```

`SequencedMap` (`LinkedHashMap`):

```java
courseDetails.putFirst(10, "Spring Security");
courseDetails.putLast(20, "Spring Cloud");
courseDetails.firstEntry();
courseDetails.lastEntry();
courseDetails.pollFirstEntry();
courseDetails.pollLastEntry();
courseDetails.sequencedKeySet();
courseDetails.reversed();
```

## Pattern Matching

`instanceof` can bind the cast variable in the same test:

```java
if (objStr instanceof String str) {
    System.out.println(str.toLowerCase());
}
```

A record pattern unpacks the components, including nested records:

```java
if (obj instanceof Order(
        Customer(String name, String email),
        Product(String productName, double price))) {
    System.out.println("Customer " + name + " ordered " + productName);
}
```

## Stream Gatherers

`Stream.gather` takes a `Gatherer`. `java.util.stream.Gatherers` provides built-in ones. `StreamGathererRunner` calls `windowSliding()`:

| Gatherer | Effect |
| --- | --- |
| `windowFixed(3)` | non-overlapping groups of 3 |
| `windowSliding(3)` | overlapping groups of 3 |
| `fold(() -> 0, (sum, i) -> sum + i)` | one running total |
| `mapConcurrent(20, i -> i * i)` | `map`, up to 20 at a time |

```java
IntStream.rangeClosed(1, 20).boxed()
    .gather(Gatherers.windowSliding(3))
    .forEach(System.out::println);
```

## Run

From `31-java-new-api-features`, with a JDK new enough for that example:

```bash
javac -d bin src/com/in28minutes/api/d/*.java
java -cp bin com.in28minutes.api.d.StringNewApiRunner
```

`FileReadWriteRunner` reads `resources/sample.txt` and writes `resources/sample-new.txt`.
