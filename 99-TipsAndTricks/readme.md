# Java Tips

Short, separate examples. Each package is one tip. `EclipseTipsAndTricks` is a scratch class for editor features (generate constructor, `equals`, `hashCode`); it is not a language concept.

## Imports

`java.lang` is imported automatically. A static import lets you call `out.println` and `sort` without the class name:

```java
import static java.lang.System.out;
import static java.util.Collections.*;

out.println("Static Imports");
sort(new ArrayList<String>());
```

## Blocks and Constants

A block `{ ... }` limits the scope of the variables declared inside it. `i` declared in the block is not visible after the closing brace.

`public static final` is the usual shape of a constant. The name is uppercase, and the value is shared by the class:

```java
public static final int SECONDS_IN_MINUTE = 60;
public static final int SECONDS_IN_DAY =
        SECONDS_IN_MINUTE * MINUTES_IN_HOUR * HOURS_IN_DAY;
```

## equals and hashCode

`==` compares references. `equals` is how a class defines "same value". `Client` treats two instances as equal when `id` matches. `hashCode` uses that same field, so equal objects have the same hash code. Both methods need to agree: collections such as `HashSet` and `HashMap` depend on it.

```java
Client c1 = new Client(1);
Client c2 = new Client(1);
c1.equals(c2);   // true
```

## Access Modifiers

Class access is `public` or default (no modifier). A public class can be used from another package. `ClassAccessModifiers` is public, so `ClassAccessModifiersRunnerInOtherPackage` can construct it.

Method access, from `ExampleClass`:

| Modifier | Same class | Same package | Subclass | Other package |
| --- | --- | --- | --- | --- |
| `public` | yes | yes | yes | yes |
| `protected` | yes | yes | yes | no |
| default | yes | yes | no | no |
| `private` | yes | no | no | no |

`MethodAccessRunnerInsideSamePackage` can call `public`, `protected`, and default. `MethodAccessRunnerInDifferentPackage` can call only `public`. The other calls are commented out because they do not compile.

## final

- `final` class — no subclass (`FinalClass`)
- `final` method — a subclass cannot override it (`doSomething`)
- `final` variable — one assignment (`i = 5`, then no second assignment)
- `final` parameter — the method cannot reassign the parameter

## static

A `static` field belongs to the class. `Player.count` goes up for every `new Player(...)`, and `Player.getCount()` reads it without an instance.

A static method can use static fields and other static methods. It cannot use instance fields such as `name` without an instance. An instance method can read the static `count`, which `Player.getName` does.

## Nested Classes

An inner class (non-static) is tied to an outer instance and can use its fields. Create it with `outer.new InnerClass()`.

A static nested class has no outer instance. It cannot touch the instance field `i`. Create it with `new StaticNestedClass()`.

```java
NestedClassRunner nestedClassRunner = new NestedClassRunner();
InnerClass innerClass = nestedClassRunner.new InnerClass();
StaticNestedClass staticNestedClass = new StaticNestedClass();
```

## Anonymous Classes

An anonymous class implements an interface at the point of use. This one sorts strings by length. A lambda replaces this pattern when the interface has one abstract method.

```java
Comparator<String> lengthComparator = new Comparator<String>() {
    public int compare(String str1, String str2) {
        return Integer.compare(str1.length(), str2.length());
    }
};
Collections.sort(animals, lengthComparator);
```

## Enum

`Season` is a fixed set of values. Each constant can carry state through a constructor. `ordinal()` is the declaration index, starting at 0. `values()` returns every constant. `valueOf` looks one up by name.

```java
public enum Season {
    SPRING(4), SUMMER(1), WINTER(2), FALL(3);

    private int value;
    private Season(int value) { this.value = value; }
    public int getValue() { return value; }
}

Season.valueOf("WINTER");
Season.SPRING.ordinal();    // 0
Season.SPRING.getValue();   // 4
Season.values();
```

`java.time.Month` and `DayOfWeek` are enums in the JDK with the same shape.

## Run

From `99-TipsAndTricks`, compile the package you want. `equals` and `enums` are self-contained:

```bash
javac -d bin src/com/in28minutes/tips/enums/*.java
java -cp bin com.in28minutes.tips.enums.EnumRunner

javac -d bin src/com/in28minutes/tips/equals/*.java
java -cp bin com.in28minutes.tips.equals.EqualsRunner
```

The access-modifier classes need both packages on the compilation:

```bash
javac -d bin src/com/in28minutes/tips/access/package1/*.java \
             src/com/in28minutes/tips/access/package2/*.java
```
