___
## 1. BufferedReader on Binary File (.docx)

### 🧩 Problem

Tried to read a `.docx` file using `BufferedReader`:

```java
Path path = Path.of("C:\Users\xiang\javaFiles\File1.docx");

try (BufferedReader reader = Files.newBufferedReader(path)) {
    String line;
    while ((line = reader.readLine()) != null) {
        System.out.println(line);
    }
}
```

Runtime error:

```text
java.nio.charset.MalformedInputException: Input length = 1
```

---

### ❗ Root Cause

This is **not a path problem** and **not a syntax problem**.

Your path is valid because:

```java
System.out.println(Files.exists(path));
```

already returns `true`.

The real issue is:

- `.docx` is **not a plain text file**
    
- `BufferedReader` is designed to read **text**, not arbitrary binary file formats
    
- Java tries to decode the file content into characters
    
- `.docx` contains binary data, so decoding fails
    

---

### 🧠 Principle

`BufferedReader` does not read a file as raw bytes. It reads **characters**.

That means it only works when the file content can be interpreted as text with a valid charset.

This line:

```java
BufferedReader reader = Files.newBufferedReader(path);
```

is conceptually equivalent to something like:

```java
new BufferedReader(
    new InputStreamReader(
        new FileInputStream(file), charset
    )
)
```

So the internal chain is:

```text
File → InputStream → InputStreamReader → Charset Decoder → BufferedReader
```

What each layer does:

- `FileInputStream`: reads raw bytes from disk
    
- `InputStreamReader`: converts bytes into characters
    
- `Charset Decoder`: applies UTF-8 / default charset decoding rules
    
- `BufferedReader`: adds buffering and `readLine()` support
    

The failure happens **before `BufferedReader` can meaningfully read lines**.  
It happens during:

```text
bytes → characters
```

because the `.docx` file content is not valid plain text.

---

### 📌 Why `.docx` Fails Here

A `.docx` file is actually a **structured binary document format**.

It is essentially:

- a ZIP-based container
    
- with XML files and Office metadata inside
    

So although Word shows readable text to humans, the file itself is **not stored as plain text lines** the way a `.txt` or `.csv` file is.

That means this kind of API is wrong for the file type:

```java
reader.readLine()
```

because `.docx` does not represent its content as ordinary text lines in the raw file bytes.

---

### ⚠️ Why the Exception Appears

Error:

```text
MalformedInputException
```

means:

> Java encountered byte sequences that do not match the expected character encoding rules.

This usually happens in two common cases:

1. The file **is text**, but you used the **wrong charset**
    
2. The file is **not text at all**
    

Your case is the second one.

So this is **not primarily an encoding mismatch problem** like UTF-8 vs GBK.  
It is a **file type mismatch + wrong IO abstraction** problem.

---

### ❌ Wrong Usage

Using text APIs on a binary file:

```java
Path path = Path.of("C:\Users\xiang\javaFiles\File1.docx");

try (BufferedReader reader = Files.newBufferedReader(path)) {
    String line;
    while ((line = reader.readLine()) != null) {
        System.out.println(line);
    }
}
```

Why it is wrong:

- `BufferedReader` assumes text
    
- `.docx` is binary
    
- `readLine()` assumes newline-based textual structure
    
- `.docx` must be parsed using a document parser, not a text reader
    

---

### ✅ Correct Approach

Use:

- `InputStream` to read raw bytes
    
- a proper `.docx` parser to interpret the file format
    

For Java, the standard solution is **Apache POI**.

---

### ✅ Complete Fixed Code

```java
import org.apache.poi.xwpf.usermodel.XWPFDocument;
import org.apache.poi.xwpf.usermodel.XWPFParagraph;

import java.io.IOException;
import java.io.InputStream;
import java.nio.file.Files;
import java.nio.file.Path;

public class Main {
    public static void main(String[] args) {
        Path path = Path.of("C:\Users\xiang\javaFiles\File1.docx");

        if (!Files.exists(path)) {
            System.out.println("File does not exist.");
            return;
        }

        try (InputStream inputStream = Files.newInputStream(path);
             XWPFDocument document = new XWPFDocument(inputStream)) {

            for (XWPFParagraph paragraph : document.getParagraphs()) {
                String text = paragraph.getText();
                if (text != null && !text.isBlank()) {
                    System.out.println(text);
                }
            }

        } catch (IOException e) {
            throw new RuntimeException("Failed to read .docx file", e);
        }
    }
}
```

---

### 🔧 Why This Fix Works

This fix works because it matches the abstraction to the file type.

#### What changes

Instead of this:

```text
text reader → binary file
```

we now use:

```text
raw byte stream → docx parser
```

#### What Apache POI does

Apache POI understands:

- Office file structure
    
- `.docx` packaging format
    
- document paragraphs, runs, tables, and text content
    

So it does not try to pretend the file is plain text.  
It parses the format correctly and then extracts readable content.

---

### 🆚 Tool Selection Rule

|File Type|Correct Tool|
|---|---|
|`.txt`, `.csv`, `.json`, `.md`, `.java`|`BufferedReader` / `Files.readString()`|
|`.docx`|`InputStream` + Apache POI|
|`.pdf`|`InputStream` + PDF parser|
|`.png`, `.jpg`, `.zip`|byte stream APIs|

---

### 🔥 Takeaway

This error should be remembered as:

> **Never choose an IO API only by “I want to read a file.” Choose it by file type and data representation.**

Mental rule:

```text
Text file → Reader / BufferedReader
Binary structured file → InputStream + parser
```

This was a **design mistake**, not a syntax mistake.