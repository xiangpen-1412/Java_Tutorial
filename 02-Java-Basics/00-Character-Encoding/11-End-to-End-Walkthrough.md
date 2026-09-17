# Lecture 11：把源文件、String、Normalization、文本文件连成一条完整流程

[课程目录](00-Reading-Guide.md) · [上一讲：NFKD 和 NFKC](10-NFKD-and-NFKC.md) · [可选附录：其他编码与容易混淆的名字](90-Other-Encodings.md)

前面每讲只拆一个问题。这一讲不增加新的核心概念，专门把它们接起来。我们跟踪一段非常短的文本，让每一步都能检查。

主人公是：**一个 `e`，后面跟一个 combining acute accent，再跟一个笑脸。**

```text
显示时通常是：é😀

实际 code points：
U+0065    U+0301                 U+1F600
e         combining acute accent 😀
```

请先记住它一开始是 3 个 code points，而不是单码点 `é` 加笑脸。这个区别正是我们需要跟踪的内容。

## 1. 第一站：编辑器里这行 Java 源码

我们在 `.java` 里写：

```java
String original = "e\u0301😀";
```

这里混合使用了两种源码写法：

- `e` 和 `😀`：直接写出字符。
- `\u0301`：用 Java Unicode escape 指定 `U+0301`。

`\u0301` 中最前面是 **backslash** `\`，不是下划线；`u` 后面是四位 hex 数字。它的意义在 [Lecture 05](05-Java-Literals-and-Escapes.md) 中已经单独拆过。

先不要运行。编辑器把这份 `.java` 保存为 UTF-8 时，双引号之间这部分源码在文件中的 bytes 是：

```text
源码可见内容：e  \  u  0  3  0  1  😀

UTF-8 bytes：65 5C 75 30 33 30 31 F0 9F 98 80
```

注意 `\u0301` 此时还是源文件里真实的六个字符，因此这里有六个 ASCII bytes：

```text
\    u    0    3    0    1
5C   75   30   33   30   31
```

**源码的 bytes，与运行时字符串内容 encode 后的 bytes，不一定相同。** 编译器接下来还要理解源代码语法。

## 2. 第二站：编译器读取源码，理解 Unicode escape

编译命令：

```text
javac -encoding UTF-8 TextJourneyDemo.java
```

分开看编译器的两个相关步骤：

```text
源文件 bytes
    ↓ 按 UTF-8 decode
得到 Java 源码文本，其中有字面写法 \u0301
    ↓ Java 的 Unicode escape 处理
\u0301 被解释为 U+0301
    ↓ 后续编译过程
这段 string literal 描述的内容被编译进程序
```

Unicode escape 的处理属于 Java 源码处理规则，不是 UTF-8 decoder 的职责。[Java Language Specification：Unicode Escapes](https://docs.oracle.com/javase/specs/jls/se17/html/jls-3.html#jls-3.3)

这里省略了 `.class` 的二进制格式；`.class` 不是一份普通 UTF-8 文本文件。课程要追踪的是程序运行后得到什么文本，而不是把编译产物画成一段 UTF-16 文本文件。

运行后 `original` 的内容是：

```text
code points：    U+0065 U+0301 U+1F600
UTF-16 code units：0065   0301   D83D DE00
```

为什么第二行有 4 个单位？`e` 用一个，combining accent 用一个，笑脸用一个 surrogate pair，也就是两个。

所以：

```java
original.length()                              // 4
original.codePointCount(0, original.length())   // 3
```

以上是 Java String 的 API 语义，不是声明 JVM 实际内存里必须按这四个 16-bit 格子存放。内部优化不改变这些 API 结果。

## 3. 第三站：先把未经 Normalization 的内容保存为 UTF-8

按 Lecture 03 的规则，分别 encode 这 3 个 code points：

```text
U+0065  → 65
U+0301  → CC 81
U+1F600 → F0 9F 98 80
```

其中 `U+0301` 落在 UTF-8 两字节范围：

```text
code point bits：01100 000001
两字节模板：    110xxxxx 10xxxxxx
填入后：        11001100 10000001
hex bytes：     CC       81
```

拼起来，文本文件里就是：

```text
65 CC 81 F0 9F 98 80
```

总共 7 bytes。

这与第一站的源码 bytes 不同：**Java Unicode escape 已经被编译器解释过；这里保存的是运行时文本，不是源代码写法。**

## 4. 第四站：另一个程序读取这个文本文件

另一端做两件事：读取 bytes；按 UTF-8 decode。

```text
65           → U+0065
CC 81        → U+0301
F0 9F 98 80  → U+1F600
```

得到的 String 仍然是：

```text
[e] [combining acute accent] [😀]
```

UTF-8 decode 不会顺手把它变成单码点 `é`。它只负责还原输入 bytes 所表示的 code points。

因此，如果程序里另有：

```java
String expected = "é😀";
```

它的 code points 是 `U+00E9 U+1F600`。二者虽然通常看起来一样，直接 `equals` 仍然为 `false`。

这时问题已经不是“编码读错了”。两边的文本都被正确表示出来，只是它们采用了不同的 canonically equivalent sequences。

## 5. 第五站：做 NFC，变化发生在文本内部

现在对读取到的 String 做 NFC：

```java
String normalized = Normalizer.normalize(incoming, Normalizer.Form.NFC);
```

调用前：

```text
U+0065 U+0301 U+1F600
```

调用后：

```text
U+00E9 U+1F600
```

按照前面两讲的规则，`e + combining acute accent` 在这里组合成 `é`；笑脸不受影响。`incoming` 本身没有被修改，返回的 `normalized` 承载了结果。`Normalizer.normalize` 的输入和输出都是文本，不要求你提供 UTF-8 或 UTF-16 文件编码。[Java Normalizer](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/text/Normalizer.html)

对照变化：

| 观察层面 | NFC 前 | NFC 后 |
|---|---|---|
| code points | `0065 0301 1F600` | `00E9 1F600` |
| UTF-16 code units | `0065 0301 D83D DE00` | `00E9 D83D DE00` |
| Java `length()` | 4 | 3 |
| code point 数量 | 3 | 2 |

你看到的文字通常还是 `é😀`，但它的具体组成已经变了。

## 6. 第六站：再次 encode 为 UTF-8，保存新的结果

`é` 的 code point 是 `U+00E9`。这次不再分别保存 `e` 和 combining accent。

```text
U+00E9 的 bits：00011 101001
两字节模板：   110xxxxx 10xxxxxx
填入后：       11000011 10101001
UTF-8 bytes：  C3       A9
```

笑脸仍然是 `F0 9F 98 80`。

因此，新文件的 bytes 是：

```text
C3 A9 F0 9F 98 80
```

现在总共 6 bytes。

```text
原文件：65 CC 81 F0 9F 98 80   7 bytes
新文件：C3 A9    F0 9F 98 80   6 bytes
```

两个文件都用 UTF-8。长度改变是因为 NFC 改变了 code point sequence，再由 UTF-8 encode 得到不同的 bytes。

所以“从分解表示统一成 NFC”和“从 UTF-8 转成 UTF-16”不是同一件事。

## 7. 第七站：接收方按同样的规则比较

接收方按 UTF-8 读取新文件，就会得到 `U+00E9 U+1F600`。

如果接收方的参考文本也先做 NFC，两边就采用了同一种规范形式：

```java
String left = Normalizer.normalize(received, Normalizer.Form.NFC);
String right = Normalizer.normalize(expected, Normalizer.Form.NFC);

boolean same = left.equals(right);
```

这里“两边都做 NFC”的意义，是避免只规范化一边，而另一边仍采用分解表示。它解决的是 canonical equivalence；如果两个词本来就不同，NFC 不会把它们变成同一个词。

## 8. 把刚才的七站放进一个可运行程序

这份程序使用 Java 17。它只在系统临时目录创建两个练习文件，运行完删除；用 hex 输出结果，避免终端字体影响判断。

```java
import java.nio.charset.StandardCharsets;
import java.nio.file.Files;
import java.nio.file.Path;
import java.text.Normalizer;
import java.util.HexFormat;

public class TextJourneyDemo {
    public static void main(String[] args) throws Exception {
        String original = "e\u0301😀";
        String expected = "é😀";

        Path beforeFile = Files.createTempFile("before-nfc-", ".txt");
        Path afterFile = null;

        try {
            afterFile = Files.createTempFile("after-nfc-", ".txt");

            Files.writeString(beforeFile, original, StandardCharsets.UTF_8);
            String incoming = Files.readString(beforeFile, StandardCharsets.UTF_8);

            String normalized = Normalizer.normalize(
                incoming, Normalizer.Form.NFC
            );

            Files.writeString(afterFile, normalized, StandardCharsets.UTF_8);
            String received = Files.readString(afterFile, StandardCharsets.UTF_8);

            HexFormat hex = HexFormat.ofDelimiter(" ").withUpperCase();
            System.out.println(hex.formatHex(Files.readAllBytes(beforeFile)));
            System.out.println(hex.formatHex(Files.readAllBytes(afterFile)));
            System.out.println(incoming.equals(expected));
            System.out.println(
                Normalizer.normalize(received, Normalizer.Form.NFC).equals(
                    Normalizer.normalize(expected, Normalizer.Form.NFC)
                )
            );
        } finally {
            Files.deleteIfExists(beforeFile);
            if (afterFile != null) {
                Files.deleteIfExists(afterFile);
            }
        }
    }
}
```

保存源文件为 UTF-8 后：

```text
javac -encoding UTF-8 TextJourneyDemo.java
java TextJourneyDemo
```

输出：

```text
65 CC 81 F0 9F 98 80
C3 A9 F0 9F 98 80
false
true
```

`readAllBytes` 在这里负责检查物理 bytes；`readString` 才会按编码得到文本。两者的职责不同。[Java Files](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/nio/file/Files.html)

## 9. 一个必须单独澄清的情况：普通文件里真的写着 `\u0041`

假设一个普通 `.txt` 文件包含下面这六个字符：

```text
\u0041
```

按 UTF-8 保存，它的 bytes 是：

```text
5C 75 30 30 34 31
```

再用 `Files.readString` 按 UTF-8 读取，得到的还是这六个字符，**不会自动变成 `A`**。

原因是：UTF-8 decode 只解释 bytes。Java Unicode escape 是 Java 编译器解释源码时的规则，普通文本读取不执行编译器的源码处理。

如果某种数据格式另行定义了 escape，例如 JSON，那是该格式的 parser 在后续处理，与 UTF-8 decoder 仍然是不同步骤。

## 10. 最后三道“能说出原因”的问题

**问题 1：** UTF-8 文件已经正确 decode 成 `e + combining acute accent`。为什么还可能需要 NFC？

<details>
<summary>展开答案</summary>

decode 还原了文件指定的 code points，没有错误。但另一个文本可能使用单码点 `é`。NFC 把两种 canonically equivalent 表示统一，才能用直接的 sequence equality 比较。它不是在修正 UTF-8 decode。

</details>

**问题 2：** NFC 前后都写成 UTF-8，为什么文件长度能从 7 bytes 变成 6 bytes？

<details>
<summary>展开答案</summary>

文本组成从 `U+0065 U+0301 U+1F600` 变成 `U+00E9 U+1F600`。前者的前两个 code points 用 `65 CC 81`，后者用 `C3 A9`。编码规则没变，输入文本变了。

</details>

**问题 3：** 普通文本文件中的字面 `\u0041`，与 Java 源码 string literal 中的 `"\u0041"`，为什么结果不同？

<details>
<summary>展开答案</summary>

前者只是六个普通文本字符。后者经过 Java 编译器的 Unicode escape 处理，指定 `U+0041`，运行时字符串内容是 `A`。差别在于是否经过 Java 源码处理，不在于 UTF-8 有两种解法。

</details>

主线课程到这里结束。若你能解释每一站**输入是什么、输出是什么、采用哪条规则**，就已经掌握了整条流程。遇到 GB18030、Modified UTF-8、BOM 等名字时，再查下面的可选附录。

[课程目录](00-Reading-Guide.md) · [可选附录：其他编码与容易混淆的名字](90-Other-Encodings.md)
