# Files

These examples use `java.nio.file`: `Path`, `Paths`, and `Files`. Paths are relative to the directory you launch the program from. Run them from `17-Files` so `./resources` resolves.

## List a Directory

`Files.list` returns a stream of the entries in one directory. It does not enter subdirectories:

```java
Path currentDirectory = Paths.get(".");
Files.list(currentDirectory).forEach(System.out::println);
```

## Walk and Find

`Files.walk(start, maxDepth)` visits the directory and its descendants, up to `maxDepth` levels. `filter` keeps the paths you care about:

```java
Predicate<Path> predicate = path -> String.valueOf(path).contains(".java");
Files.walk(currentDirectory, 4)
    .filter(predicate)
    .forEach(System.out::println);
```

`Files.find` does the same walk, and the matcher also receives `BasicFileAttributes`, so you can test whether the entry is a directory:

```java
BiPredicate<Path, BasicFileAttributes> directoryMatcher =
        (path, attributes) -> attributes.isDirectory();

Files.find(currentDirectory, 4, directoryMatcher)
    .forEach(System.out::println);
```

`DirectoryScanRunner` calls `Files.find` with the directory matcher.

## Read a File

`Files.readAllLines` loads every line into a `List<String>`.

`Files.lines` returns a `Stream<String>`. The stream methods from the functional programming chapter apply directly. This runner lowercases each line and keeps lines that contain `a`:

```java
Path pathFileToRead = Paths.get("./resources/data.txt");

Files.lines(pathFileToRead)
    .map(String::toLowerCase)
    .filter(str -> str.contains("a"))
    .forEach(System.out::println);
```

## Write a File

`Files.write` writes each list element as a line and creates the file if it does not exist:

```java
Path pathFileToWrite = Paths.get("./resources/file-write.txt");
List<String> list = List.of("Apple", "Boy", "Cat", "Dog", "Elephant");
Files.write(pathFileToWrite, list);
```

`list`, `walk`, `find`, `lines`, `readAllLines`, and `write` throw the checked `IOException`. These `main` methods declare `throws IOException`.

## Run

From `17-Files`:

```bash
javac -d bin src/files/*.java
java -cp bin files.DirectoryScanRunner
java -cp bin files.FileReadRunner
java -cp bin files.FileWriteRunner
```

`resources/data.txt` is the input for the reader. `FileWriteRunner` overwrites `resources/file-write.txt`.
