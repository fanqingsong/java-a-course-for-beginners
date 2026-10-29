# Collections

## Why Collections?

An array has a fixed size and stores values of one type. The Collections framework stores a group of objects and lets you add, remove, search, and sort them.

Four main interfaces:

- **List** — ordered, allows duplicates, index based
- **Set** — unique elements only
- **Queue** — elements processed in a defined order
- **Map** — key–value pairs (does not extend `Collection`)

`List`, `Set`, and `Queue` extend `Collection`. `Collection` extends `Iterable`.

## Collection Interface

```java
interface Collection<E> extends Iterable<E> {
  int size();
  boolean isEmpty();
  boolean contains(Object o);
  Iterator<E> iterator();
  boolean add(E e);
  boolean remove(Object o);
  boolean containsAll(Collection<?> c);
  boolean addAll(Collection<? extends E> c);
  boolean removeAll(Collection<?> c);
  boolean retainAll(Collection<?> c);
  void clear();
}
```

## List

A `List` cares about position.

- `add(element)` appends at the end
- `add(index, element)` inserts at a position
- `get(index)`, `set(index, element)`, `remove(index)`, `indexOf`, `lastIndexOf`

### Implementations

| Class | Notes |
| --- | --- |
| `ArrayList` | Backed by an array. Fast random access. Slower insert and delete in the middle. |
| `LinkedList` | Doubly linked. Faster insert and delete. Slower iteration than `ArrayList`. |
| `Vector` | Like `ArrayList`, but methods are synchronized (thread safe). |

`List.of(...)` returns an immutable list. Calling `add` or `remove` throws `UnsupportedOperationException`. Copy it into an `ArrayList` when you need to change it:

```java
List<String> words = List.of("Apple", "Bat", "Cat");
List<String> wordsArrayList = new ArrayList<>(words);
wordsArrayList.add("Dog");
wordsArrayList.add(2, "Ball");
wordsArrayList.set(0, "Fish");
wordsArrayList.remove(2);          // remove by index
wordsArrayList.remove("Dog");      // remove by value
```

`LinkedList` and `Vector` can be created from the same elements:

```java
List<String> wordsLinkedList = new LinkedList<>(words);
List<String> wordsVector = new Vector<>(words);
```

### Looping

```java
for (int i = 0; i < words.size(); i++) {
    System.out.println(words.get(i));
}

for (String word : words) {
    System.out.println(word);
}

Iterator<String> iterator = words.iterator();
while (iterator.hasNext()) {
    System.out.println(iterator.next());
}
```

Do not call `list.remove` inside a for-each loop. It throws `ConcurrentModificationException`. Use the iterator:

```java
List<String> wordsAl = new ArrayList<>(List.of("Apple", "Bat", "Cat"));
Iterator<String> iterator = wordsAl.iterator();
while (iterator.hasNext()) {
    if (iterator.next().endsWith("at")) {
        iterator.remove();
    }
}
```

### `remove(int)` vs `remove(Object)`

For `List<Integer>`, `remove(101)` removes the element **at index 101**. To remove the value 101, pass an `Integer`:

```java
numbersAl.remove(Integer.valueOf(101));
```

### Sorting a list of numbers

```java
List<Integer> numbersAl = new ArrayList<>(numbers);
Collections.sort(numbersAl);
```

## Sorting Objects: Comparable and Comparator

`Collections.sort` and `List.sort` need a way to compare elements.

`Student` implements `Comparable<Student>`. `compareTo` here sorts by id descending (`that.id` compared with `this.id`):

```java
public class Student implements Comparable<Student> {
    private int id;
    private String name;

    @Override
    public int compareTo(Student that) {
        return Integer.compare(that.id, this.id);
    }
}
```

A `Comparator` is a separate class, so the same list can be sorted in another order without changing `Student`:

```java
class AscendingStudentComparator implements Comparator<Student> {
    @Override
    public int compare(Student student1, Student student2) {
        return Integer.compare(student1.getId(), student2.getId());
    }
}
```

```java
List<Student> students = List.of(
        new Student(1, "Ranga"),
        new Student(100, "Adam"),
        new Student(2, "Eve"));

ArrayList<Student> studentsAl = new ArrayList<>(students);
Collections.sort(studentsAl);                              // Comparable: id descending
studentsAl.sort(new AscendingStudentComparator());         // Comparator: id ascending
```

## Set

A `Set` stores unique elements. If `obj1.equals(obj2)`, only one of them stays in the set.

| Class | Order |
| --- | --- |
| `HashSet` | Unordered, unsorted. Uses `hashCode()`. |
| `LinkedHashSet` | Insertion order. Unsorted. Uses `hashCode()`. |
| `TreeSet` | Sorted in natural order. Implements `NavigableSet`. |

```java
List<Character> characters = List.of('A', 'Z', 'A', 'B', 'Z', 'F');

Set<Character> treeSet = new TreeSet<>(characters);             // [A, B, F, Z]
Set<Character> linkedHashSet = new LinkedHashSet<>(characters); // [A, Z, B, F]
Set<Character> hashSet = new HashSet<>(characters);             // no guaranteed order
```

Adding a value that is already present does nothing.

### NavigableSet

`TreeSet` can find neighbors and ranges:

```java
TreeSet<Integer> numbers = new TreeSet<>(Set.of(65, 54, 34, 12, 99));

numbers.floor(40);    // greatest element <= 40  -> 34
numbers.lower(34);    // greatest element < 34   -> 12
numbers.ceiling(36);  // least element >= 36     -> 54
numbers.higher(34);   // least element > 34      -> 54

numbers.subSet(34, 65);                 // [34, 65)
numbers.subSet(34, true, 65, true);     // [34, 65]
numbers.headSet(50);                    // elements < 50
numbers.tailSet(50);                    // elements >= 50
```

## Queue

A queue is arranged in the order it will be processed. `Queue` extends `Collection`.

```java
interface Queue<E> extends Collection<E> {
    boolean add(E e);    // throws if it cannot insert
    boolean offer(E e);  // returns false if it cannot insert
    E remove();          // throws if empty
    E poll();            // returns null if empty
    E peek();            // looks at the head, returns null if empty
}
```

`PriorityQueue` orders elements by natural order, or by a `Comparator`. This one polls the longest string first:

```java
class StringLengthComparator implements Comparator<String> {
    @Override
    public int compare(String value1, String value2) {
        return Integer.compare(value2.length(), value1.length());
    }
}

Queue<String> queue = new PriorityQueue<>(new StringLengthComparator());
queue.addAll(List.of("Zebra", "Monkey", "Cat"));
queue.offer("Apple");
while (queue.peek() != null) {
    System.out.println(queue.poll());
}
```

## Map

A map stores key–value pairs. Keys are unique.

```java
interface Map<K, V> {
    int size();
    boolean isEmpty();
    boolean containsKey(Object key);
    boolean containsValue(Object value);
    V get(Object key);
    V put(K key, V value);
    V remove(Object key);
    Set<K> keySet();
    Collection<V> values();
    Set<Entry<K, V>> entrySet();
}
```

| Class | Notes |
| --- | --- |
| `HashMap` | Unsorted, unordered. Uses the key's `hashCode()`. One null key is allowed. |
| `LinkedHashMap` | Keeps insertion order. |
| `TreeMap` | Keys stay sorted. Implements `NavigableMap`. |
| `Hashtable` | Synchronized version of `HashMap`. Does not allow a null key. |

```java
Map<String, Integer> map = Map.of("A", 3, "Z", 5, "B", 10);
map.get("A");             // 3
map.containsKey("F");     // false
map.containsValue(3);     // true
map.keySet();
map.values();

HashMap<String, Integer> hashmap = new HashMap<>();
hashmap.put("Z", 5);
hashmap.put("A", 15);

LinkedHashMap<String, Integer> linkedHashMap = new LinkedHashMap<>();
linkedHashMap.put("F", 25);
linkedHashMap.put("A", 15);

TreeMap<String, Integer> treemap = new TreeMap<>();
treemap.put("F", 25);
treemap.put("A", 15);
treemap.put("Z", 5);
treemap.higherKey("B");    // least key > "B"
treemap.floorKey("B");     // greatest key <= "B"
treemap.ceilingKey("B");   // least key >= "B"
treemap.firstEntry();
treemap.lastEntry();
treemap.subMap("B", "Z");  // from "B" inclusive to "Z" exclusive
```

### Counting occurrences

`MapRunner` counts characters and words. `get` returns `null` when the key is new:

```java
String str = "This is an awesome occasion. "
        + "This has never happened before.";

Map<Character, Integer> occurances = new HashMap<>();
for (char character : str.toCharArray()) {
    Integer count = occurances.get(character);
    if (count == null) {
        occurances.put(character, 1);
    } else {
        occurances.put(character, count + 1);
    }
}

Map<String, Integer> stringOccurances = new HashMap<>();
for (String word : str.split(" ")) {
    Integer count = stringOccurances.get(word);
    if (count == null) {
        stringOccurances.put(word, 1);
    } else {
        stringOccurances.put(word, count + 1);
    }
}
```

## Run the examples

From `12-Collections`:

```bash
javac -d bin src/collections/*.java
java -cp bin collections.StudentsCollectionRunner
java -cp bin collections.SetRunner
java -cp bin collections.QueueRunner
java -cp bin collections.MapRunner
```

`commands.txt` is a jshell session for trying the same methods interactively. `commands-and-output.txt` is a saved transcript of that session.
