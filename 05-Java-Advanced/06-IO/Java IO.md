
---
# 1. What Java I/O really is

Java I/O stands for **input and output**.

In practice, it means letting a Java program communicate with the outside world instead of only working with memory inside the JVM.

Typical examples include:

- reading a text file
    
- writing logs
    
- creating or deleting files
    
- traversing directories
    
- reading binary data
    
- saving objects to disk
    

A simple but important way to understand it is this:

> Java code in memory is only one part of the system. I/O is the bridge between your program and the operating system, file system, disk, external resources, and data formats.

---

# 2. Why I/O is different from normal code

Normal in-memory code usually runs inside JVM-managed memory:

```java
int a = 10;
int b = 20;
int c = a + b;
```

This kind of code is predictable.

I/O code is different because it depends on the outside world:

- the file may not exist
    
- the path may be wrong
    
- the process may not have permission
    
- the file may be locked
    
- the encoding may not match
    
- the disk may fail
    

That is why Java I/O is tightly connected to:

- checked exceptions
    
- resource management
    
- cleanup logic
    

---

# 3. Exception handling in Java I/O

## 3.1 Why exceptions are central to I/O

I/O operations can fail for reasons outside the program itself, so Java forces the programmer to deal with that possibility.

For example:

```java
import java.io.FileReader;

public class Main {
    public static void main(String[] args) {
        FileReader reader = new FileReader("test.txt");
    }
}
```

This does not compile, because the constructor may throw a checked exception.

---

## 3.2 Checked vs unchecked exceptions

### Checked exceptions

These must be handled or declared with `throws`.

Common I/O examples:

- `IOException`
    
- `FileNotFoundException`
    
- `NoSuchFileException`
    

### Unchecked exceptions

These are usually programming mistakes and are not enforced by the compiler.

Examples:

- `NullPointerException`
    
- `IllegalArgumentException`
    
- `ArrayIndexOutOfBoundsException`
    

A useful mental model:

- **checked exception** = the external world may fail
    
- **unchecked exception** = the code itself is probably wrong
    

---

## 3.3 try-catch-finally

```java
try {
    // code that may fail
} catch (IOException e) {
    // error handling
} finally {
    // cleanup
}
```

- `try` contains risky code
    
- `catch` handles the failure
    
- `finally` is typically used for cleanup
    

Example:

```java
import java.io.FileReader;
import java.io.IOException;

public class Main {
    public static void main(String[] args) {
        try {
            FileReader reader = new FileReader("missing.txt");
            System.out.println("Opened successfully");
        } catch (IOException e) {
            System.out.println("I/O error: " + e.getMessage());
        } finally {
            System.out.println("Program finished");
        }
    }
}
```

---

# 4. Resource management

## 4.1 What is a resource

A resource is not just a normal Java object. It usually represents something backed by the operating system.

Examples:

- file handles
    
- network sockets
    
- database connections
    
- readers and writers
    
- streams
    

These resources are limited and must be released.

---

## 4.2 Why resources must be closed

If a file is opened and never closed, problems may include:

- resource leaks
    
- file locking issues
    
- unflushed buffered output
    
- failure in long-running applications
    

Traditional cleanup looks like this:

```java
import java.io.FileReader;
import java.io.IOException;

public class Main {
    public static void main(String[] args) {
        FileReader reader = null;

        try {
            reader = new FileReader("test.txt");
            System.out.println("Opened");
        } catch (IOException e) {
            System.out.println("Error: " + e.getMessage());
        } finally {
            if (reader != null) {
                try {
                    reader.close();
                } catch (IOException e) {
                    System.out.println("Failed to close reader");
                }
            }
        }
    }
}
```

This works, but it is verbose and easy to get wrong.

---

## 4.3 try-with-resources

Modern Java uses `try-with-resources`.

```java
import java.io.FileReader;
import java.io.IOException;

public class Main {
    public static void main(String[] args) {
        try (FileReader reader = new FileReader("test.txt")) {
            System.out.println("Opened successfully");
        } catch (IOException e) {
            System.out.println("I/O error: " + e.getMessage());
        }
    }
}
```

Any resource declared inside the parentheses is automatically closed.

This is the standard modern style for Java I/O.

---

## 4.4 AutoCloseable and Closeable

A resource can be used in `try-with-resources` if it implements `AutoCloseable`.

Many I/O classes implement `Closeable`, which extends `AutoCloseable`.

That is why these can be used there:

- `BufferedReader`
    
- `BufferedWriter`
    
- `FileReader`
    
- `FileWriter`
    
- `InputStream`
    
- `OutputStream`
    

---

## 4.5 Multiple resources

```java
try (
    BufferedReader reader = Files.newBufferedReader(Path.of("input.txt"));
    BufferedWriter writer = Files.newBufferedWriter(Path.of("output.txt"))
) {
    // use both resources
}
```

Resources are closed in reverse order of creation.

---

# 5. File vs Path vs Files

This is one of the most important parts of modern Java I/O.

## 5.1 Core idea

- `Path` describes a path
    
- `Files` performs operations on that path
    
- `File` is the older API
    

You should think like this:

```text
Path = location description
Files = file system operations
File = older mixed design
```

---

## 5.2 Path

`Path` represents a location in the file system.

```java
import java.nio.file.Path;

Path path = Path.of("test.txt");
```

A `Path` object does **not** guarantee that the file exists.

That is a very important point.

---

## 5.3 Useful Path methods

```java
Path path = Path.of("folder", "file.txt");

System.out.println(path.getFileName());
System.out.println(path.getParent());
System.out.println(path.getRoot());
```

You can also combine paths safely:

```java
Path base = Path.of("folder");
Path full = base.resolve("file.txt");
System.out.println(full);
```

And normalize them:

```java
Path messy = Path.of("folder/../file.txt");
System.out.println(messy.normalize());
```

---

## 5.4 Files

`Files` is a utility class for file operations.

Examples:

```java
import java.nio.file.Files;
import java.nio.file.Path;

public class Main {
    public static void main(String[] args) throws Exception {
        Path path = Path.of("test.txt");

        System.out.println(Files.exists(path));
        System.out.println(Files.isRegularFile(path));
    }
}
```

---

## 5.5 File

`File` is the older API.

```java
import java.io.File;

File file = new File("test.txt");
System.out.println(file.exists());
```

It still works, but modern Java code usually prefers `Path` + `Files`.

---

# 6. Basic file operations

## 6.1 Create file

```java
Path path = Path.of("test.txt");
Files.createFile(path);
```

If the file already exists, this throws an exception.

Safer style:

```java
try {
    Files.createFile(path);
} catch (java.nio.file.FileAlreadyExistsException e) {
    System.out.println("File already exists");
}
```

---

## 6.2 Create directory

Single directory:

```java
Files.createDirectory(Path.of("myFolder"));
```

Multiple levels:

```java
Files.createDirectories(Path.of("a", "b", "c"));
```

`createDirectories` is usually more practical.

---

## 6.3 Delete file

```java
Files.delete(Path.of("test.txt"));
```

If the file does not exist, it throws an exception.

Safer version:

```java
Files.deleteIfExists(Path.of("test.txt"));
```

---

## 6.4 Copy and move

Copy:

```java
import java.nio.file.StandardCopyOption;

Path source = Path.of("a.txt");
Path target = Path.of("b.txt");

Files.copy(source, target, StandardCopyOption.REPLACE_EXISTING);
```

Move:

```java
Files.move(source, target, StandardCopyOption.REPLACE_EXISTING);
```

Without `REPLACE_EXISTING`, existing targets may cause exceptions.

---

# 7. Text I/O

Text I/O is the most common part of file processing.

The most important structure is:

```text
byte stream -> character stream -> buffered character stream
```

Or more practically:

```text
file -> reader/writer -> buffered reader/writer
```

---

## 7.1 Why BufferedReader is preferred

Using `FileReader` directly is usually not ideal because:

- buffering is not explicit
    
- encoding control is weak in older styles
    
- modern code is better written with `Files.newBufferedReader`
    

Preferred style:

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.nio.charset.StandardCharsets;
import java.nio.file.Files;
import java.nio.file.Path;

public class Main {
    public static void main(String[] args) {
        Path path = Path.of("test.txt");

        try (BufferedReader reader = Files.newBufferedReader(path, StandardCharsets.UTF_8)) {
            String line;
            while ((line = reader.readLine()) != null) {
                System.out.println(line);
            }
        } catch (IOException e) {
            System.out.println("Error: " + e.getMessage());
        }
    }
}
```

---

## 7.2 Files.readString

For small text files, this is very convenient:

```java
String content = Files.readString(Path.of("test.txt"));
System.out.println(content);
```

But it reads the entire file into memory, so it is not appropriate for very large files.

---

## 7.3 Writing text

Simple case:

```java
Files.writeString(Path.of("output.txt"), "Hello World");
```

Append mode:

```java
import java.nio.file.StandardOpenOption;

Files.writeString(
    Path.of("output.txt"),
    "New line\n",
    StandardOpenOption.CREATE,
    StandardOpenOption.APPEND
);
```

---

## 7.4 BufferedWriter

```java
import java.io.BufferedWriter;
import java.io.IOException;
import java.nio.charset.StandardCharsets;
import java.nio.file.Files;
import java.nio.file.Path;

public class Main {
    public static void main(String[] args) {
        Path path = Path.of("output.txt");

        try (BufferedWriter writer = Files.newBufferedWriter(path, StandardCharsets.UTF_8)) {
            writer.write("Hello");
            writer.newLine();
            writer.write("World");
        } catch (IOException e) {
            System.out.println("Error: " + e.getMessage());
        }
    }
}
```

---

# 8. Character encoding

This is a very important topic in text I/O.

A text file is physically stored as bytes, but Java reads it as characters.

So text I/O always involves decoding:

```text
bytes -> characters
```

And writing text involves encoding:

```text
characters -> bytes
```

If the file encoding and the reader encoding do not match, the result may be:

- garbled text
    
- malformed input errors
    
- unreadable output
    

That is why explicitly using UTF-8 is a good standard habit.

```java
BufferedReader reader = Files.newBufferedReader(path, StandardCharsets.UTF_8);
```

---

# 9. Why BufferedReader failed on a `.docx` file

This is directly related to your current error note.

A `.docx` file is **not a plain text file**. It is a binary ZIP-based document format.

So this code is conceptually wrong:

```java
Path path = Path.of("C:\\Users\\xiang\\javaFiles\\File1.docx");

try (BufferedReader reader = Files.newBufferedReader(path)) {
    String line;
    while ((line = reader.readLine()) != null) {
        System.out.println(line);
    }
}
```

The path may be correct, and the file may exist, but `BufferedReader` expects text data.

A `.docx` file contains binary data, compressed internal XML parts, and file packaging metadata. So trying to decode it as plain text may lead to exceptions such as:

```text
java.nio.charset.MalformedInputException
```

That means the reader tried to interpret bytes as characters using a character set, but the byte sequence did not form valid text in that encoding.

### Correct conclusion

- this is **not** a path error
    
- this is **not** a syntax error
    
- this is a **wrong tool for the file format** problem
    

### Correct mental rule

- use `BufferedReader` for plain text files such as `.txt`, `.csv`, `.json`, `.md`, `.java`
    
- do **not** use `BufferedReader` for binary formats such as `.docx`, `.pdf`, `.jpg`, `.png`, `.zip`
    

If you want to process a `.docx` file properly, you usually need a dedicated library such as Apache POI.

---

# 10. Scanner

`Scanner` is often introduced alongside text input, but its role is different.

It is primarily a parsing tool.

```java
import java.nio.file.Path;
import java.util.Scanner;

public class Main {
    public static void main(String[] args) throws Exception {
        try (Scanner scanner = new Scanner(Path.of("test.txt"))) {
            while (scanner.hasNextLine()) {
                System.out.println(scanner.nextLine());
            }
        }
    }
}
```

`Scanner` is useful when you want to parse tokens, numbers, or structured user-like input.

But for normal file reading in industrial code, `BufferedReader` is usually preferred because it is simpler and faster.

---

# 11. Directory traversal

The file system is naturally a tree.

That means directory traversal is really tree traversal.

Java provides modern APIs for this.

---

## 11.1 Files.walk

```java
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.util.stream.Stream;

public class Main {
    public static void main(String[] args) throws IOException {
        Path root = Path.of("test");

        try (Stream<Path> stream = Files.walk(root)) {
            stream.forEach(System.out::println);
        }
    }
}
```

This recursively walks the tree and gives a stream of paths.

Filter only regular files:

```java
try (Stream<Path> stream = Files.walk(root)) {
    stream.filter(Files::isRegularFile)
          .forEach(System.out::println);
}
```

Find all `.txt` files:

```java
try (Stream<Path> stream = Files.walk(root)) {
    stream.filter(p -> p.toString().endsWith(".txt"))
          .forEach(System.out::println);
}
```

---

## 11.2 walkFileTree

For more control, use `walkFileTree`.

```java
import java.io.IOException;
import java.nio.file.FileVisitResult;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.SimpleFileVisitor;
import java.nio.file.attribute.BasicFileAttributes;

public class Main {
    public static void main(String[] args) throws IOException {
        Path root = Path.of("test");

        Files.walkFileTree(root, new SimpleFileVisitor<>() {
            @Override
            public FileVisitResult preVisitDirectory(Path dir, BasicFileAttributes attrs) {
                System.out.println("Dir: " + dir);
                return FileVisitResult.CONTINUE;
            }

            @Override
            public FileVisitResult visitFile(Path file, BasicFileAttributes attrs) {
                System.out.println("File: " + file);
                return FileVisitResult.CONTINUE;
            }
        });
    }
}
```

This is especially useful for advanced traversal logic and recursive delete operations.

---

# 12. Recursive delete pattern

Deleting a non-empty directory directly fails.

So the correct pattern is:

- delete files first
    
- delete directories after their contents are gone
    

Example:

```java
import java.io.IOException;
import java.nio.file.FileVisitResult;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.SimpleFileVisitor;
import java.nio.file.attribute.BasicFileAttributes;

public class Main {
    public static void main(String[] args) throws IOException {
        Path root = Path.of("test");

        Files.walkFileTree(root, new SimpleFileVisitor<>() {
            @Override
            public FileVisitResult visitFile(Path file, BasicFileAttributes attrs) throws IOException {
                Files.delete(file);
                return FileVisitResult.CONTINUE;
            }

            @Override
            public FileVisitResult postVisitDirectory(Path dir, IOException exc) throws IOException {
                Files.delete(dir);
                return FileVisitResult.CONTINUE;
            }
        });
    }
}
```

---

# 13. Binary I/O

Not all files are text.

Binary files must be treated as binary data.

This includes formats such as:

- `.docx`
    
- `.pdf`
    
- `.jpg`
    
- `.png`
    
- `.zip`
    
- `.class`
    

For structured binary reading and writing, Java provides `DataInputStream` and `DataOutputStream`.

Write binary data:

```java
import java.io.DataOutputStream;
import java.io.FileOutputStream;

public class Main {
    public static void main(String[] args) throws Exception {
        try (DataOutputStream out = new DataOutputStream(new FileOutputStream("data.bin"))) {
            out.writeInt(100);
            out.writeDouble(3.14);
            out.writeUTF("Hello");
        }
    }
}
```

Read binary data:

```java
import java.io.DataInputStream;
import java.io.FileInputStream;

public class Main {
    public static void main(String[] args) throws Exception {
        try (DataInputStream in = new DataInputStream(new FileInputStream("data.bin"))) {
            int a = in.readInt();
            double b = in.readDouble();
            String c = in.readUTF();

            System.out.println(a);
            System.out.println(b);
            System.out.println(c);
        }
    }
}
```

The read order must match the write order exactly.

---

# 14. RandomAccessFile

Normal streams are sequential. They usually read from the beginning and move forward.

`RandomAccessFile` allows random positioning inside a file.

```java
import java.io.RandomAccessFile;

public class Main {
    public static void main(String[] args) throws Exception {
        try (RandomAccessFile raf = new RandomAccessFile("data.bin", "rw")) {
            raf.writeInt(100);
            raf.writeInt(200);

            raf.seek(0);
            System.out.println(raf.readInt());

            raf.seek(4);
            System.out.println(raf.readInt());
        }
    }
}
```

This is useful for indexing, offset-based access, and some lower-level data storage ideas.

---

# 15. Serialization

Serialization means converting an object into bytes so that it can be stored or transmitted.

Example class:

```java
import java.io.Serializable;

class Person implements Serializable {
    private static final long serialVersionUID = 1L;

    String name;
    int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

Write object:

```java
import java.io.FileOutputStream;
import java.io.ObjectOutputStream;

public class Main {
    public static void main(String[] args) throws Exception {
        try (ObjectOutputStream out = new ObjectOutputStream(new FileOutputStream("person.dat"))) {
            out.writeObject(new Person("Alice", 25));
        }
    }
}
```

Read object:

```java
import java.io.FileInputStream;
import java.io.ObjectInputStream;

public class Main {
    public static void main(String[] args) throws Exception {
        try (ObjectInputStream in = new ObjectInputStream(new FileInputStream("person.dat"))) {
            Person p = (Person) in.readObject();
            System.out.println(p.name);
            System.out.println(p.age);
        }
    }
}
```

---

## 15.1 transient

Fields marked `transient` are skipped during serialization.

```java
import java.io.Serializable;

class User implements Serializable {
    private static final long serialVersionUID = 1L;

    String username;
    transient String password;

    public User(String username, String password) {
        this.username = username;
        this.password = password;
    }
}
```

This is important for sensitive data.

---

# 16. Best practices

## 16.1 Prefer modern APIs

Prefer:

- `Path`
    
- `Files`
    
- `Files.newBufferedReader`
    
- `Files.newBufferedWriter`
    
- `try-with-resources`
    

Over older styles such as:

- `File`
    
- manual `finally` cleanup
    
- direct `FileReader` or `FileWriter` as your main pattern
    

---

## 16.2 Always think about the data type

Before reading a file, ask:

- is it text?
    
- is it binary?
    
- is it an object serialization format?
    

This prevents wrong-tool problems like trying to use a text reader for a binary document.

---

## 16.3 Be explicit about encoding

UTF-8 should usually be your default standard unless there is a specific reason not to use it.

---

## 16.4 Do not swallow exceptions silently

Bad:

```java
try {
    // something
} catch (IOException e) {
}
```

At minimum, log or report the problem.

---

## 16.5 Use line-by-line reading for large text files

Do not load huge files fully into memory unless that is truly intended.

---

# 17. Final mental model

This is the compact model you should keep in your head:

```text
Java I/O
│
├── resource management
│   └── try-with-resources
│
├── file system abstraction
│   ├── Path
│   └── Files
│
├── text processing
│   ├── BufferedReader
│   ├── BufferedWriter
│   ├── Files.readString / writeString
│   └── encoding
│
├── directory traversal
│   ├── Files.walk
│   └── Files.walkFileTree
│
├── binary processing
│   ├── InputStream / OutputStream
│   ├── DataInputStream / DataOutputStream
│   └── RandomAccessFile
│
└── object persistence
    └── serialization
```

---

# 18. Final conclusions

## 18.1 The most important engineering rules

1. Always assume I/O may fail.
    
2. Always manage resources correctly.
    
3. Prefer `try-with-resources`.
    
4. Prefer `Path` + `Files` over old `File`-centric design.
    
5. Use text tools only for real text data.
    
6. Be explicit about encoding.
    
7. Use recursive traversal APIs for directory trees.
    
8. Understand the difference between text, binary, and object data.
    

---

## 18.2 One-sentence summary

Java I/O is the system that lets a Java program safely and correctly interact with files, directories, bytes, text, and persisted objects outside normal JVM memory.

---

## 18.3 Recommended next note after this one

After this summary note, the next highly practical note to create would be:

`04-Java-Advanced/06-File-IO/02-Common-IO-Errors.md`

That note can collect real mistakes such as:

- reading binary files as text
    
- wrong path assumptions
    
- forgetting encoding
    
- using `readString` on large files
    
- deleting non-empty directories directly
    
- wrong `Scanner` usage
    
- forgetting `REPLACE_EXISTING`
    

That would match your current `99-Errors` workflow very well while still keeping the knowledge note itself clean and structured.