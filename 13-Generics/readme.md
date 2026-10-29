# Generics

## Why Generics?

A raw `List` can hold any object. The compiler cannot tell you that you stored a `String` and later treated it as an `Integer`. Generics put the element type in the declaration, so the mismatch is a compile error.

```java
MyCustomList<String> list = new MyCustomList<>();
list.addElement("Element 1");
String value = list.get(0);   // no cast
```

The same class works for another type:

```java
MyCustomList<Integer> list2 = new MyCustomList<>();
list2.addElement(Integer.valueOf(5));
Integer number = list2.get(0);
```

## A Generic Class

`T` is a type parameter. `MyCustomList<T>` stores an `ArrayList<T>` and returns `T` from `get`:

```java
public class MyCustomList<T> {
    ArrayList<T> list = new ArrayList<>();

    public void addElement(T element) {
        list.add(element);
    }

    public T get(int index) {
        return list.get(index);
    }
}
```

## Generic Methods

A method can declare its own type parameter. The compiler infers `X` from the argument:

```java
static <X> X doubleValue(X value) {
    return value;
}

String value1 = doubleValue(new String());
Integer number1 = doubleValue(Integer.valueOf(5));
```

A bound limits what `X` can be. `X extends List` means the argument must be a `List` (or a subtype), so `addAll` is available:

```java
static <X extends List> void duplicate(X list) {
    list.addAll(list);
}

LinkedList<Integer> numbers = new LinkedList<>(List.of(1, 2, 3));
duplicate(numbers);   // [1, 2, 3, 1, 2, 3]
```

## Wildcards

### Upper bound: `? extends Number`

The list contains some subtype of `Number`. You can read elements as `Number`. You cannot add an `Integer` or a `Double`, because the compiler does not know the exact element type.

```java
static double sumOfNumberList(List<? extends Number> numbers) {
    double sum = 0.0;
    for (Number number : numbers) {
        sum += number.doubleValue();
    }
    return sum;
}

sumOfNumberList(List.of(1, 2, 3, 4, 5));
sumOfNumberList(List.of(1.1, 2.1, 3.1, 4.1, 5.1));
sumOfNumberList(List.of(1L, 2L, 3L, 4L, 5L));
```

### Lower bound: `? super Number`

The list is `Number` or a supertype of `Number`. You can add a `Number` (and its subtypes). Reading only gives you `Object`.

```java
static void addACoupleOfValues(List<? super Number> numbers) {
    numbers.add(1);
    numbers.add(1.0);
    numbers.add(1.0f);
    numbers.add(1L);
}
```

| Wildcard | You can | You cannot safely |
| --- | --- | --- |
| `? extends Number` | read as `Number` | add a specific `Number` subtype |
| `? super Number` | add a `Number` | read as a specific subtype |

## Run

From `13-Generics`:

```bash
javac -d bin src/com/in28minutes/generics/*.java
java -cp bin com.in28minutes.generics.GenericsRunner
```
