# Locks, Atomic Operations, and Concurrent Collections

`i++` is three steps: read `i`, add one, write `i` back. Two threads can both read the same value and both write `old + 1`, so one increment is lost. The classes in this directory each close that gap in a different way.

`18-Concurrency.md` is a dump of the same source. This note explains the idea.

## synchronized

`synchronized` on a method locks the instance for the whole call. A second thread that wants any synchronized method on that same object waits.

```java
public class Counter {
    private int i = 0;

    synchronized public void increment() {
        i++;
    }

    public int getI() {
        return i;
    }
}
```

`BiCounter` has `incrementI` and `incrementJ`, and both methods are `synchronized`. A thread updating `i` blocks a thread that only wants to update `j`. The two counters do not share data, but they share one lock: the instance.

## ReentrantLock

A `Lock` is an object you lock and unlock yourself. `BiCounterWithLock` gives `i` and `j` separate locks, so the two increments can run at the same time:

```java
Lock lockForI = new ReentrantLock();
Lock lockForJ = new ReentrantLock();

public void incrementI() {
    lockForI.lock();
    i++;
    lockForI.unlock();
}
```

`unlock` belongs in a `finally` block in production code, so the lock is released when `i++` throws. The sample keeps the lock and unlock next to the increment.

## AtomicInteger

`AtomicInteger.incrementAndGet()` updates the value in one hardware-level step. No lock object is required, and `i` and `j` do not block each other:

```java
private AtomicInteger i = new AtomicInteger();

public void incrementI() {
    i.incrementAndGet();
}

public int getI() {
    return i.get();
}
```

## ConcurrentHashMap

A normal `HashMap` is not safe for concurrent updates. `Hashtable` synchronizes the whole map, so every update waits on one lock.

`ConcurrentHashMap` locks smaller regions, so updates to different keys can proceed together. `computeIfAbsent` creates the `LongAdder` once, even if several threads ask for the same missing key at the same time. `LongAdder.increment()` is itself safe for concurrent calls:

```java
ConcurrentMap<Character, LongAdder> occurances = new ConcurrentHashMap<>();
String str = "ABCD ABCD ABCD";

for (char character : str.toCharArray()) {
    occurances.computeIfAbsent(character, ch -> new LongAdder()).increment();
}
```

The commented block in `ConcurrentMapRunner` is the same count written with `Hashtable` and an explicit null check.

## CopyOnWriteArrayList

A copy-on-write list copies the underlying array on each write. Readers iterate a stable snapshot and do not need a lock. That fits a list that is read often and written rarely. The comments in the runner mark `add` as the rare path (a few threads) and the loop as the common path (many threads):

```java
List<String> list = new CopyOnWriteArrayList<>();
list.add("Ant");
list.add("Bat");
list.add("Cat");

for (String element : list) {
    System.out.println(element);
}
```

## Run

From `18-ConcurrencyLocksAtomicityAndCollections`:

```bash
javac -d bin src/com/in28minutes/concurrency/*.java
java -cp bin com.in28minutes.concurrency.ConcurrencyRunner
java -cp bin com.in28minutes.concurrency.ConcurrentMapRunner
java -cp bin com.in28minutes.concurrency.CopyOnWriteArrayListRunner
```

`ConcurrencyRunner` increments a `Counter` three times on one thread and prints `3`. The lock and atomic classes are the thread-safe versions of that counter. They are not started from `main` in this directory.
