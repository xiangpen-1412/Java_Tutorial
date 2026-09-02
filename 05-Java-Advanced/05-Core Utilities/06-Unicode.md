# Unicode

## 1. What is Unicode?

Unicode is a universal standard for representing text.

Computers ultimately store numbers and bytes, not letters. Unicode assigns a unique numerical value to characters used by writing systems around the world.

Examples:

```text
A  -> U+0041
中 -> U+4E2D
é  -> U+00E9
😀 -> U+1F600
```

The value written as `U+XXXX` is called a **Unicode code point**.

Unicode contains far more than English letters. It includes:

- Chinese, Japanese, Korean, Arabic, Cyrillic, and many other scripts
- punctuation and mathematical symbols
- currency symbols
- emoji
- combining marks and formatting characters

Unicode answers this question:

```text
Which abstract character does this number represent?
```

It does not, by itself, determine how that number is stored as bytes. That is the responsibility of an encoding such as UTF-8 or UTF-16.

---

## 2. Character, Glyph, Code Point, and Byte

These terms describe different layers and should not be treated as synonyms.

### 2.1 Character

A character is an abstract element of text, such as the letter `A`, the Chinese character `中`, or an emoji.

### 2.2 Glyph

A glyph is the visual shape drawn by a font.

The same character can look different in different fonts, while visually similar glyphs can represent different characters.

### 2.3 Code point

A code point is the number Unicode assigns to an abstract character.

```text
Character: A
Code point: U+0041
```

The Unicode code point range is:

```text
U+0000 to U+10FFFF
```

### 2.4 Byte

A byte is the unit used by files, networks, and memory storage.

An encoding converts Unicode code points into bytes and converts bytes back into Unicode text.

```text
Unicode code points
        ↓ encode
bytes
        ↓ decode
Unicode code points
```

---

## 3. Unicode Is Not UTF-8

Unicode defines the character set. UTF-8 defines one way to encode Unicode code points as bytes.

```text
Unicode -> what the characters and code points are
UTF-8   -> how those code points are represented as bytes
```

Common Unicode encodings include:

- UTF-8
- UTF-16
- UTF-32

The same text has the same Unicode meaning but a different byte representation in each encoding.

| Character | Code point | UTF-8 length | UTF-16 length |
|---|---:|---:|---:|
| `A` | U+0041 | 1 byte | 1 code unit / 2 bytes |
| `é` | U+00E9 | 2 bytes | 1 code unit / 2 bytes |
| `中` | U+4E2D | 3 bytes | 1 code unit / 2 bytes |
| `😀` | U+1F600 | 4 bytes | 2 code units / 4 bytes |

---

## 4. UTF-8

UTF-8 is the dominant encoding for web pages, JSON, APIs, source files, and many databases.

It uses a variable number of bytes:

```text
U+0000   - U+007F   -> 1 byte
U+0080   - U+07FF   -> 2 bytes
U+0800   - U+FFFF   -> 3 bytes
U+10000  - U+10FFFF -> 4 bytes
```

ASCII characters use the same byte values in UTF-8 as they do in ASCII. This compatibility is one reason UTF-8 became widely adopted.

Advantages of UTF-8 include:

- compatibility with ASCII
- compact storage for English-heavy text
- support for every Unicode code point
- no byte-order ambiguity
- broad support across browsers, APIs, operating systems, and databases

For most Java web applications, UTF-8 should be the default external text encoding.

---

## 5. UTF-16 and UTF-32

### 5.1 UTF-16

UTF-16 represents text using 16-bit code units.

Code points in the Basic Multilingual Plane usually require one code unit. Supplementary code points require a pair of code units called a **surrogate pair**.

For example, `😀` is one Unicode code point but requires two UTF-16 code units.

### 5.2 UTF-32

UTF-32 uses one 32-bit code unit for each code point.

This makes code-point indexing conceptually simpler, but it usually consumes more space. It is therefore less common for ordinary files and network protocols.

### 5.3 Byte order

UTF-16 and UTF-32 contain multi-byte units, so byte order can matter:

```text
UTF-16LE -> little-endian
UTF-16BE -> big-endian
UTF-32LE -> little-endian
UTF-32BE -> big-endian
```

A Byte Order Mark, or BOM, may be used to indicate encoding or byte order, although its use depends on the file format and protocol.

---

## 6. Unicode in Java

Java `String` values represent Unicode text using UTF-16 code units as the programming model.

A Java `char` is a single 16-bit code unit, not necessarily a complete Unicode character.

```java
String text = "A中😀";
```

The string contains three Unicode code points, but `String.length()` returns four because the emoji uses a surrogate pair:

```java
int codeUnits = text.length();
// 4

int codePoints = text.codePointCount(0, text.length());
// 3
```

This distinction matters when processing emoji and characters outside the Basic Multilingual Plane.

### 6.1 Iterating by `char`

This iterates over UTF-16 code units:

```java
for (int i = 0; i < text.length(); i++) {
    char codeUnit = text.charAt(i);
}
```

It can split a supplementary character into two surrogate values.

### 6.2 Iterating by code point

Use `codePoints()` when the operation should process Unicode code points:

```java
text.codePoints().forEach(codePoint -> {
    System.out.println(
            new String(Character.toChars(codePoint))
    );
});
```

### 6.3 A code point is not always a user-perceived character

Even code-point counting does not always match what a user sees as one character.

For example, one visible emoji can contain:

- multiple code points
- skin-tone modifiers
- variation selectors
- zero-width joiners

This visible unit is often called a **grapheme cluster**.

Therefore:

```text
char count != code point count != visible character count
```

---

## 7. Converting Between Strings and Bytes in Java

Always specify the charset explicitly at system boundaries.

```java
import java.nio.charset.StandardCharsets;

String text = "Hello 世界";

byte[] bytes = text.getBytes(StandardCharsets.UTF_8);
String restored = new String(bytes, StandardCharsets.UTF_8);
```

The encoding used to write text must match the encoding used to read it:

```text
String
  ↓ UTF-8 encode
UTF-8 bytes
  ↓ UTF-8 decode
original String
```

Avoid relying on the platform default charset:

```java
// Avoid when data crosses a system boundary
byte[] bytes = text.getBytes();
String restored = new String(bytes);
```

The default charset can differ between machines, runtime versions, containers, and operating systems.

---

## 8. Why Mojibake Happens

Mojibake is garbled text caused by decoding bytes with the wrong character encoding.

```text
Original text
    ↓ UTF-8 encode
UTF-8 bytes
    ↓ incorrectly decode as another charset
garbled text
```

For example, UTF-8 bytes interpreted as Windows-1252 may produce text such as:

```text
CafÃ©
```

The problem is not that Unicode failed. The writer and reader disagreed about the encoding.

To prevent this:

- use UTF-8 consistently
- declare the charset in protocols and file formats when required
- use `StandardCharsets.UTF_8` in Java
- configure databases and database connections consistently
- do not decode the same bytes multiple times

---

## 9. Unicode Normalization

Unicode can represent text that looks the same using different code-point sequences.

The visible character `é` can be represented as:

```text
U+00E9
```

or as:

```text
U+0065 U+0301
```

These strings can look identical but compare as unequal at the code-point level.

Unicode normalization converts text into a consistent representation.

Java provides:

```java
import java.text.Normalizer;

String normalized = Normalizer.normalize(
        input,
        Normalizer.Form.NFC
);
```

The four standard normalization forms are:

| Form | Behavior |
|---|---|
| NFC | Canonical decomposition followed by composition |
| NFD | Canonical decomposition |
| NFKC | Compatibility decomposition followed by composition |
| NFKD | Compatibility decomposition |

### 9.1 Canonical normalization

NFC and NFD handle representations that Unicode considers canonically equivalent.

NFC is a common choice for storing and comparing ordinary human-language text.

### 9.2 Compatibility normalization

NFKC and NFKD also fold compatibility characters into more ordinary forms.

For example, NFKC can convert many full-width characters:

```text
１２３ＡＢＣ
```

into:

```text
123ABC
```

Compatibility normalization can be useful for identifiers and search, but it can discard distinctions that matter in some domains. The normalization policy should therefore match the business meaning of the data.

---

## 10. Case Conversion and Locale

Case conversion is also a Unicode operation and can vary by language.

For machine identifiers, use a locale-independent rule:

```java
import java.util.Locale;

String normalized = value.toUpperCase(Locale.ROOT);
```

For user-facing language, use the user's actual locale when appropriate.

Do not assume that every character has a simple one-to-one uppercase and lowercase mapping.

---

## 11. Unicode and Databases

A database column must use a Unicode-capable character set and a suitable collation.

These are different concerns:

```text
Character set -> which characters can be stored
Collation     -> how strings are compared and sorted
```

Questions to decide include:

- Is comparison case-sensitive?
- Are accents significant?
- Which language's sorting rules apply?
- Should normalized and non-normalized forms compare as equal?

PostgreSQL stores text in the database encoding, commonly UTF-8. Application code must still define normalization and comparison rules appropriate to each domain.

---

## 12. Unicode and Security

Unicode introduces security considerations because visually similar text can contain different code points.

Examples include:

- Latin `a` and Cyrillic `а`
- ordinary spaces and invisible Unicode spaces
- zero-width characters
- right-to-left formatting characters
- full-width and half-width forms
- visually identical composed and decomposed sequences

These can cause:

- confusing usernames or domain names
- duplicate identifiers that bypass naive comparisons
- misleading log entries
- validation bypasses
- spoofing and phishing

Security-sensitive identifiers should use a documented normalization and validation policy. However, normalization must not be used blindly: two characters that look similar are not always semantically equivalent.

---

## 13. Common Mistakes

### Mistake 1: Treating Unicode and UTF-8 as synonyms

```text
Unicode defines characters.
UTF-8 encodes those characters as bytes.
```

### Mistake 2: Assuming one `char` equals one character

A Java `char` is one UTF-16 code unit. Some code points require two `char` values, and some visible characters require multiple code points.

### Mistake 3: Using the default charset

Use an explicit charset such as `StandardCharsets.UTF_8` whenever text crosses a file, network, hashing, encryption, or database boundary.

### Mistake 4: Comparing visually identical strings without normalization

Canonically equivalent strings can have different code-point sequences.

### Mistake 5: Normalizing every field with the same rule

Human names, passwords, search terms, legal identifiers, and source code do not necessarily require the same normalization policy.

### Mistake 6: Counting `String.length()` as visible characters

`String.length()` counts UTF-16 code units, not Unicode code points or grapheme clusters.

---

## 14. Summary

```text
Unicode
    defines characters and assigns code points

UTF-8 / UTF-16 / UTF-32
    encode Unicode code points into code units and bytes

Java String
    exposes text through UTF-16 code units

Unicode normalization
    converts equivalent or compatible representations into a chosen form

Charset agreement
    prevents text from becoming garbled when moving between systems
```

The most important practical rules are:

1. Use UTF-8 for external text unless a protocol requires something else.
2. Specify `StandardCharsets.UTF_8` explicitly in Java.
3. Do not assume one Java `char` equals one user-visible character.
4. Define normalization and case rules according to the meaning of the data.
5. Treat visually similar Unicode characters carefully in security-sensitive identifiers.

