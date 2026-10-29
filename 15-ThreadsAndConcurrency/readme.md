# Threads and Concurrency

## Why a Thread?

`main` runs one task at a time. A thread lets another task run alongside it. This chapter prints three ranges: 101–199, 201–299, and 301–399. The first two run on their own threads. The third stays on `main`.

## Two Ways to Create a Thread

Extend `Thread` and override `run`:

```java
class Task1 extends Thread {
    public void run() {
        for (int i = 101; i <= 199; i++)
            System.out.print(i + " ");
    }
}

Task1 task1 = new Task1();
task1.start();   // starts a new thread; task1.run() would stay on main
```

Implement `Runnable` and pass it to a `Thread`:

```java
class Task2 implements Runnable {
    public void run() {
        for (int i = 201; i <= 299; i++)
            System.out.print(i + " ");
    }
}

Thread task2Thread = new Thread(new Task2());
task2Thread.start();
```

## States and Priority

A thread moves through these states:

- **NEW** — created, `start` not called
- **RUNNABLE** — eligible to run
- **RUNNING** — executing on a processor
- **BLOCKED / WAITING** — paused, for example inside `sleep` or `join`
- **TERMINATED** — `run` has finished

`setPriority` asks the scheduler to prefer one thread. `1` is the lowest, `10` is the highest. It is a hint, not a guarantee.

## `join`

`join` makes the caller wait until that thread finishes:

```java
task1.join();
task2Thread.join();
// Task3 on main starts only after both threads are done
```

`Thread.sleep` pauses the current thread. `yield` hints that another thread may run. `synchronized` lets only one thread execute a method or block at a time. Chapter 18 goes further into locks and atomic counters.

## ExecutorService

Creating a `Thread` for every task does not limit how many run at once. An executor owns a pool and runs submitted tasks on those threads.

```java
ExecutorService executorService = Executors.newFixedThreadPool(5);
executorService.execute(new Task(1));
executorService.execute(new Task(2));
// tasks 6 and 7 wait until a pool thread is free
executorService.shutdown();
```

`newSingleThreadExecutor()` uses a pool of one. `shutdown()` rejects new tasks and lets the ones already submitted finish.

## Callable and Future

`Runnable.run` returns nothing. `Callable<T>.call` returns a value. `submit` gives back a `Future<T>`. `get()` waits until the result is ready:

```java
class CallableTask implements Callable<String> {
    private String name;

    public String call() throws Exception {
        Thread.sleep(1000);
        return "Hello " + name;
    }
}

Future<String> welcomeFuture =
        executorService.submit(new CallableTask("in28Minutes"));
String welcomeMessage = welcomeFuture.get();
```

`invokeAll` waits for every task and returns a `Future` for each one:

```java
List<Future<String>> results = executorService.invokeAll(tasks);
for (Future<String> result : results) {
    System.out.println(result.get());
}
```

`invokeAny` returns the first task that finishes and cancels the rest:

```java
String result = executorService.invokeAny(tasks);
```

## Run

The classes in this directory have no package. `MultipleCallableRunner` and `MultipleAnyCallableRunner` use `CallableTask` from `CallableRunner.java`, so compile every file together.

From `15-ThreadsAndConcurrency`:

```bash
javac -d bin src/*.java
java -cp bin ThreadBasicsRunner
java -cp bin ExecutorServiceRunner
java -cp bin CallableRunner
java -cp bin MultipleCallableRunner
java -cp bin MultipleAnyCallableRunner
```
