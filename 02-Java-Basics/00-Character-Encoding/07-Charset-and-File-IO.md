# Lecture 07：一个“中”怎样从 Java 走进文件，再走回来？

[课程目录](00-Reading-Guide.md) · [上一讲：Java char 和 String](06-Java-char-and-String.md) · [下一讲：为什么需要 Normalization](08-Why-Normalization.md)

这一讲只解决一个问题：**你已经有一个 Java `String`，把它保存到文件时，究竟发生了什么？再读回来，又发生了什么？**

前几讲已经算过 UTF-8、UTF-16 的规则。本讲把这些规则接到真实程序上。这里始终讨论 **Java String**，没有 JavaScript；`String` 是 Java 的字符串类型。

## 1. 先只看一个字，不看代码

假设 Java 程序里有一个字符串，内容只有“中”。

```text
文字：             中
Unicode code point：U+4E2D
Java UTF-16 code unit：4E2D
```

最后一行表示 Java API 怎样理解这个字符串，不是在宣称 JVM 内部一定摆着一个 `char[]`。

现在你说：“我要把它保存为 UTF-8 文件。”这句话要求程序完成两件事：

1. 按 UTF-8 的规则，把文本变成 bytes。
2. 把这些 bytes 写入文件。

第一件事叫 **encode**；第二件事叫文件写入。它们可以由同一个方便的方法完成，但概念上是两件事。

复习 UTF-8 的计算：`U+4E2D` 落在三字节范围。把 code point 补成 16 bits，按 `4 + 6 + 6` 分组，再放入三字节模板。

```text
code point 的 bits：0100 111000 101101

UTF-8 模板：       1110xxxx 10xxxxxx 10xxxxxx
放入上述 bits：    11100100 10111000 10101101
用 hex 写 bytes：  E4       B8       AD
```

于是文件内容就是这 3 个 bytes：

```text
E4 B8 AD
```

文件里没有自动附送一张“我是中文”或“我是 UTF-8”的说明书。某些文件格式、协议会另存编码信息，但一个普通文本文件不能指望单凭扩展名 `.txt` 告诉你编码。

## 2. 读回来时，把这条路反着走

首先，读取文件得到的还是 bytes：

```text
E4 B8 AD
```

还不能把“读取了 bytes”直接理解为“已经得到了字符”。我们要告诉程序：**请按 UTF-8 解释这些 bytes。**

这一步叫 **decode**。

UTF-8 decoder 看到第一个 byte 以 `1110` 开头，知道这应该是一组三字节表示；接着检查后两个 byte 是否以 `10` 开头。去掉这些结构前缀：

```text
1110 0100    10 111000    10 101101
     └──┘       └────┘       └────┘
       0100       111000       101101

拼回：0100 111000 101101
即：  0100 1110 0010 1101
hex： 4    E    2    D
```

恢复的 code point 是 `U+4E2D`，也就是“中”。Java 把结果提供为一个 `String`，在 UTF-16 code unit 层面是 `4E2D`。

完整来回现在是：

```text
Java String：中
    ↓ 用 UTF-8 encode
bytes：E4 B8 AD
    ↓ 写入文件
文件里的 bytes：E4 B8 AD
    ↓ 读取文件
bytes：E4 B8 AD
    ↓ 用 UTF-8 decode
Java String：中
```

**UTF-8 是这次出入文件时采用的规则。不是给 Java String 贴上的永久标签。**

## 3. `StandardCharsets.UTF_8` 究竟是什么？

先看这一行，暂时不处理文件：

```java
byte[] data = text.getBytes(StandardCharsets.UTF_8);
```

从左到右理解：

| 代码部分 | 在这里做什么 |
|---|---|
| `text` | 已经存在的 Java String，例如“中” |
| `getBytes(...)` | 把这个 String encode 成 bytes |
| `StandardCharsets` | Java 提供的一个类，集中放置几个标准编码的常量 |
| `.` | 访问这个类里的成员 |
| `UTF_8` | 一个常量字段，指向表示 UTF-8 规则的 `Charset` 对象 |
| `byte[] data` | 接住转换结果，也就是 byte 数组 |

`StandardCharsets.UTF_8` 既不是你的文本，也不是装文本的盒子。可以把它理解成你选好的**转换规则**。

类名也不是“Standard Character Set 是一种新编码”。`StandardCharsets` 只是 Java 这个类的名字，真正选中的编码是它里面的 `UTF_8`。

这里的 `8` 表示 UTF-8 使用 8-bit code units。**8 bits = 1 byte**。它不意味着每个 code point 固定占 1 byte，更不是 2 bytes。

因此，下列写法只是把那份规则先存进一个变量：

```java
Charset rule = StandardCharsets.UTF_8;
byte[] data = text.getBytes(rule);
```

`Charset` 是表示编码规则的类型。它还可以提供 encoder、decoder，但不需要先手工创建这两个工具才能使用常见转换。[Java Charset 官方定义](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/nio/charset/Charset.html)

## 4. 两个最基本的方向，不要记反

### 4.1 String → bytes

```java
String text = "中";
byte[] data = text.getBytes(StandardCharsets.UTF_8);
```

结果：`data` 包含 `E4 B8 AD`。

### 4.2 bytes → String

```java
String restored = new String(data, StandardCharsets.UTF_8);
```

两个参数分别是：

1. `data`：等着被解释的原始 bytes。
2. `StandardCharsets.UTF_8`：解释这些 bytes 时采用的规则。

这不是“把 String 改成 UTF-8 格式”。此时还没有结果 String，构造器正按照指定规则，从 bytes 创建它。

```java
text.equals(restored) // true
```

Java 的 `byte` 有正负号，直接打印数组可能看到 `-28, -72, -83`。这是同一组 8-bit 数据按 Java signed byte 数值打印的结果，**不是 bytes 被改了**。本课程用 `E4 B8 AD` 这样的 hex 写法，方便检查原始 bit patterns。

## 5. 真正写一次文件，再读一次

下面是可以独立运行的 Java 17 程序。请先把上面的过程看懂，再看代码。

它会在系统临时目录创建一个练习文件，读回后删除该文件，不会在笔记目录留下输出。

```java
import java.nio.charset.StandardCharsets;
import java.nio.file.Files;
import java.nio.file.Path;
import java.util.HexFormat;

public class EncodingFileDemo {
    public static void main(String[] args) throws Exception {
        String text = "中";

        // 第一步：String encode 成 UTF-8 bytes。
        byte[] encoded = text.getBytes(StandardCharsets.UTF_8);

        Path file = Files.createTempFile("encoding-lesson-", ".txt");
        try {
            // 第二步：原样保存这些 bytes。
            Files.write(file, encoded);

            // 第三步：原样读回 bytes，还没有 decode。
            byte[] loaded = Files.readAllBytes(file);

            // 第四步：按 UTF-8 decode，得到 String。
            String restored = new String(loaded, StandardCharsets.UTF_8);

            System.out.println(
                HexFormat.ofDelimiter(" ").withUpperCase().formatHex(loaded)
            );
            System.out.println(text.equals(restored));
            System.out.printf("U+%04X%n", restored.codePointAt(0));
        } finally {
            Files.deleteIfExists(file);
        }
    }
}
```

把它保存为 `EncodingFileDemo.java`，源文件选择 UTF-8，运行：

```text
javac -encoding UTF-8 EncodingFileDemo.java
java EncodingFileDemo
```

输出：

```text
E4 B8 AD
true
U+4E2D
```

辅助语法只要理解到这里：`import` 让下面可以直接使用类的短名字；`Path` 表示文件的位置；`HexFormat` 只是把 bytes 打印成 hex，不参与编码；`finally` 保证练习结束时清理临时文件。`main` 是程序入口，`throws Exception` 让这个教学程序把文件错误交给运行环境报告。

懂得四个步骤后，可以使用更短的 API：

```java
Files.writeString(file, text, StandardCharsets.UTF_8);
String restored = Files.readString(file, StandardCharsets.UTF_8);
```

`writeString` 把 encode 和写文件放在一起；`readString` 把读取和 decode 放在一起。这里显式写出编码，是为了让使用的规则清楚可见。[Files 的 readString / writeString](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/nio/file/Files.html)

## 6. 如果选错 decode 规则，会发生什么？

换一个较短的例子：单个 `é`，code point 是 `U+00E9`。

```text
é 按 UTF-8 encode：C3 A9
```

如果按 UTF-8 decode，`C3 A9` 作为一个两字节组合，恢复 `U+00E9`。

但是 ISO-8859-1 是另一套规则。在这套规则中，每个 byte 独立映射到一个编号相同的 code point。因此，它会这样解释：

```text
原始 bytes：C3 A9

错误地按 ISO-8859-1 decode：
C3 → U+00C3 → Ã
A9 → U+00A9 → ©
```

原来的一个 `é`，变成了两个字符 `Ã©`。

```java
byte[] data = "é".getBytes(StandardCharsets.UTF_8);

String right = new String(data, StandardCharsets.UTF_8);
String wrong = new String(data, StandardCharsets.ISO_8859_1);
```

`right` 和 `wrong` 都是 Java String，但内容不同。**同一串 bytes，用不同规则解释，可能得到不同文本。**

错误 decode 不一定抛异常。这里两个 byte 在 ISO-8859-1 里都有合法含义，所以程序可能顺利运行，只是文字已经错了。

还有一种现象不要混淆：文本的 code points 完全正确，显示的字体却没有对应图形，于是出现方框。那属于显示问题。检查 code points 能帮助区分“decode 错了”还是“字体画不出来”。

## 7. “把 GBK 转成 UTF-8”具体指什么？

假设一个旧文件用 GBK 保存“中”，文件里是：

```text
D6 D0
```

GBK 与 UTF-8 的规则不同。正确转换过程是：

```text
GBK bytes：D6 D0
    ↓ 用 GBK decode
文本：中，code point U+4E2D
    ↓ 用 UTF-8 encode
UTF-8 bytes：E4 B8 AD
```

对应代码：

```java
Charset gbk = Charset.forName("GBK");

String text = new String(gbkBytes, gbk);
byte[] utf8Bytes = text.getBytes(StandardCharsets.UTF_8);
```

`Charset.forName("GBK")` 的意思是按名字取得 GBK 规则，前提是当前 Java 环境支持它；不是把字符串 `"GBK"` 当作要转换的文本。

中间的 `text` 不会记着“我从 GBK 来”。同样的“中”，无论来自 GBK、UTF-8，还是直接写在程序里，只要 decode 正确，String 的内容就可以完全相同。

## 8. 最后区分三个容易混淆的边界

### 源文件边界

你的 `.java` 本身也是文本文件。编辑器把它保存为 UTF-8，编译器就应该按 UTF-8 读取。

```text
javac -encoding UTF-8 EncodingFileDemo.java
```

这里 `-encoding UTF-8` 管的是**编译器怎么读源文件**，不会强制这个程序今后写出的所有文件都是 UTF-8。

### 程序输入输出边界

```java
text.getBytes(StandardCharsets.UTF_8)
```

这里的 UTF-8 管的是**这一次 String → bytes**。

### 终端显示边界

`System.out` 把文字输出后，终端还要正确接收并显示。终端的处理方式和字体也可能影响你看到的结果。上面的示例优先打印 hex 和 code points，就是为了让判断不依赖终端能否显示中文。

JDK 18 起默认 charset 改为 UTF-8，但标准输出、控制台等有单独的环境因素。课程示例显式指定编码，避免把“默认是什么”混入当前学习过程。[JEP 400：UTF-8 by Default](https://openjdk.org/jeps/400)

UTF-16 文件还有 byte order 和 BOM：Java 的 `UTF_16` encoder 会写开头的 `FE FF`，`UTF_16BE`、`UTF_16LE` encoder 不自动写 BOM。详细的 `A → 00 41 / 41 00` 例子放在[可选附录](90-Other-Encodings.md)，本讲不需要先背这些分支。

## 9. 停下来检查三件事

**问题 1：** 文件里是 `E4 B8 AD`。仅仅调用 `Files.readAllBytes`，是否已经得到了 String“中”？

<details>
<summary>展开答案</summary>

没有。得到的是 byte 数组。还需要按正确规则 decode；本例应使用 UTF-8。

</details>

**问题 2：** `new String(data, StandardCharsets.UTF_8)` 的第二个参数，是“把结果改成 UTF-8”的意思吗？

<details>
<summary>展开答案</summary>

不是。它指定如何解释输入的 bytes。结果是 Java String，不保存“来源为 UTF-8”的标签。

</details>

**问题 3：** 已知 `D6 D0` 是 GBK 的“中”，直接用 UTF-8 decode，能实现 GBK → UTF-8 转换吗？

<details>
<summary>展开答案</summary>

不能。先用 GBK decode 得到正确文本，再用 UTF-8 encode，才能得到 `E4 B8 AD`。

</details>

下一讲换一个问题：**假如 decode 完全正确，为什么两个看起来一样的 String，`equals` 仍然可能是 `false`？** 这时才轮到 Normalization 出场。

[课程目录](00-Reading-Guide.md) · [下一讲：为什么需要 Normalization](08-Why-Normalization.md)
