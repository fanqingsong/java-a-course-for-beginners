# Exception Handling

## What an Exception Is

`ExceptionHandlingRunner` calls `method1`, which calls `method2`. `method2` calls `length()` on a `null` string. That throws `NullPointerException`. The method stops, the exception walks back up the call stack, and the lines after `str.length()` never run.

The stack trace names the exception, the message, and each method that was still on the stack.

## try and catch

A `try` block is the code that might fail. A `catch` block runs when the thrown exception matches its type. More specific catches come first. A later `catch (Exception ex)` matches anything the earlier catches missed:

```java
try {
    int[] i = { 1, 2 };
    int number = i[3];
} catch (NullPointerException ex) {
    System.out.println("Matched NullPointerException");
} catch (ArrayIndexOutOfBoundsException ex) {
    System.out.println("Matched ArrayIndexOutOfBoundsException");
} catch (Exception ex) {
    System.out.println("Matched Exception");
}
```

`i[3]` throws `ArrayIndexOutOfBoundsException`, so the second catch runs. `printStackTrace()` prints the stack without stopping the program.

## finally

`finally` runs whether the `try` succeeded, a `catch` ran, or an exception left the method. Use it to release something you opened, and only close it if it was created:

```java
Scanner scanner = null;
try {
    scanner = new Scanner(System.in);
    int number = numbers[21];
} catch (Exception e) {
    e.printStackTrace();
} finally {
    if (scanner != null) {
        scanner.close();
    }
}
```

## Checked and Unchecked

| Kind | Examples | Compiler |
| --- | --- | --- |
| Unchecked (`RuntimeException` and its subtypes) | `NullPointerException`, `ArrayIndexOutOfBoundsException` | does not force a `try` or a `throws` |
| Checked (`Exception` that is not a runtime exception) | `InterruptedException`, `IOException` | the method must catch it or declare `throws` |
| Error | `OutOfMemoryError`, `StackOverflowError` | not meant to be caught in ordinary code |

`Thread.sleep` throws the checked `InterruptedException`, so the caller must handle it or declare it:

```java
private static void someOtherMethod() throws InterruptedException {
    Thread.sleep(2000);
}
```

A method that throws only `RuntimeException` does not need `throws` in its signature.

## Throwing an Exception

`Amount.add` refuses to add two amounts when the currencies differ. `throw` creates the exception and leaves the method immediately, so `this.amount` is not updated.

`CurrenciesDoNotMatchException` extends `Exception`, so it is checked. `add` declares `throws`, and `main` declares it too:

```java
public void add(Amount that) throws CurrenciesDoNotMatchException {
    if (!this.currency.equals(that.currency)) {
        throw new CurrenciesDoNotMatchException(
                "Currencies Don't Match " + this.currency + " & " + that.currency);
    }
    this.amount = this.amount + that.amount;
}

class CurrenciesDoNotMatchException extends Exception {
    public CurrenciesDoNotMatchException(String msg) {
        super(msg);
    }
}
```

## try-with-resources

A resource declared in the `try` parentheses must implement `AutoCloseable`. It is closed when the block ends, including when an exception is thrown. That replaces the `finally` close:

```java
try (Scanner scanner = new Scanner(System.in)) {
    int number = numbers[21];
}
```

## Run

From `16-ExceptionHandling`:

```bash
javac -d bin src/com/in28minutes/exceptionhandling/*.java
java -cp bin com.in28minutes.exceptionhandling.ExceptionHandlingRunner
java -cp bin com.in28minutes.exceptionhandling.ExceptionHandlingRunner2
java -cp bin com.in28minutes.exceptionhandling.FinallyRunner
java -cp bin com.in28minutes.exceptionhandling.ThrowingExceptionRunner
```

`ExceptionHandlingRunner` and `ThrowingExceptionRunner` are written to throw. The first ends with `NullPointerException`. The second ends with `CurrenciesDoNotMatchException` because USD and EUR do not match.
