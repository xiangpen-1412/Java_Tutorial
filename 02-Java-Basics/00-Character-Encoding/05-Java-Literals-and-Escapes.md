# Lecture 05 — 看懂 Java 里的 \u 写法

前置：[Lecture 04](04-UTF-16-and-UTF-32.md)。你已经知道一个 code point 怎样表示成 UTF-16 code unit。

本讲只解决一个问题：**Java 代码里写的 `"\u00E9"` 到底是什么意思，为什么它可以和 `"é"` 表示相同内容？**

## 1. 先只看最熟悉的 A

下面这两行都是 Java source code：

```java
String direct = "A";
String escaped = "\u0041";
```

第一行，你直接在编辑器里打出 `A`。

第二行，你在编辑器里打出一个 backslash `\`、一个 `u`，以及四个 hexadecimal digit `0041`。

第二种写法叫 **Unicode escape**。它的意思是：告诉 Java compiler，这个位置使用数值为 hexadecimal `0041` 的 UTF-16 code unit。

上一讲已经知道：

```text
0041 这个 UTF-16 code unit
    ↓
code point U+0041
    ↓
A
```

因此，代码中的两种写法得到内容相同的 String：

```java
System.out.println(direct.equals(escaped)); // true
System.out.println(escaped);               // A
```

程序运行时，这个 `escaped` 变量没有保存六个字符 `\ u 0 0 4 1`，它保存的是一个 `A`。

## 2. 谁把 \u0041 解释成 A？

是 **Java compiler 在处理 source code 时**。

顺序是：

```text
你写进 .java 文件：
String escaped = "\u0041";

compiler 处理 Unicode escape：
这个位置使用 0041 对应的 UTF-16 code unit

程序运行：
escaped 的文字内容是 A
```

这里不是调用 UTF-8 decoder，也不是运行时执行 `normalize`。它属于 Java source code 的书写规则。

更准确地说，Unicode escape 的处理发生在 compiler 正式识别 string literal 等结构之前，所以它不只是某个 String 方法的功能。本讲只用普通字符演示；换行、引号等特殊字符还涉及 Java 语法，不要把它当成任意字符都能随便替换的文本宏。

## 3. 把同一件事换成 é 和 中

Unicode 已经为这些字符分配了编号：

```text
é → U+00E9
中 → U+4E2D
```

它们都可以直接表示成一个 UTF-16 code unit。因此：

```java
String e1 = "é";
String e2 = "\u00E9";

String z1 = "中";
String z2 = "\u4E2D";

System.out.println(e1.equals(e2)); // true
System.out.println(z1.equals(z2)); // true
```

`"\u00E9"` 的意思不是“保存 U、0、0、E、9 这些字母和数字”，也不是“把字符串改成某种特殊 encoding”。它只是 **不用直接输入 é，也能在 Java source code 中指定同一个内容**。

为什么讲义喜欢这样写？因为某些 combining mark 很难单独看见。用 Unicode escape，可以明确告诉读者实际用了哪个 code unit。

## 4. 四种容易看混的写法

这些写法现在都有了具体含义，可以放在一起比较：

| 写法 | 出现在哪里 | 它表示什么 |
|---|---|---|
| `U+0041` | 文档、讲解 | 用 hexadecimal 标记 code point 65 |
| `0x41` | Java 等语言的数字写法 | 整数 65 |
| `'\u0041'` | Java source code | 值为 `A` 的 `char` |
| `"\u0041"` | Java source code | 内容为 `A` 的 String |

`U+` 是讲解 code point 时的记号；`0x` 是数字 literal 的前缀；`\u` 是 Java 的 Unicode escape。它们不是三个不同版本的 Unicode。

如果你写：

```java
String label = "U+0041";
```

这回引号中没有 Unicode escape。程序保存的就是六个普通字符：

```text
[U] [+] [0] [0] [4] [1]
```

## 5. 那么笑脸怎么写？

上一讲已经手算出：

```text
😀 的 code point：U+1F600
UTF-16 code units：D83D DE00
```

Java 每段普通 `\uXXXX` escape 指定一个 16-bit code unit，需要四个 hexadecimal digit。因此两部分分别写出来：

```java
String direct = "😀";
String escaped = "\uD83D\uDE00";

System.out.println(direct.equals(escaped)); // true
```

你没有把笑脸变成两个独立表情。你是在 source code 中明确写出了组成同一个笑脸的两个 UTF-16 code unit。

不要把 `\u1F600` 当作“给 \u 后面放五位即可”。Java 不按这种写法识别一个五位 code point：前四位会被解释成一个 escape，最后的 `0` 留在后面。若要按 code point 构造 String，下一讲会介绍另一个明确的方法。

## 6. 我真的想保存字符 \、u、0、0、4、1 呢？

Java string literal 中，`\\` 表示一个实际的 backslash：

```java
String actualA = "\u0041";
String writtenSequence = "\\u0041";

System.out.println(actualA);        // A
System.out.println(writtenSequence); // 显示字面文本 \u0041
```

按运行时内容画出来：

```text
actualA：
[A]

writtenSequence：
[\] [u] [0] [0] [4] [1]
```

这里 source code 里的两个 backslash，是为了在结果中保留一个 backslash。不要把编辑器中写了多少字符，与运行时 String 内容有多少字符混起来。

这也不与前面“Unicode escape 先处理”矛盾：compiler 识别 Unicode escape 时，对连续 backslash 有明确的资格规则。在本例 `"\\u0041"` 中，第二个 backslash 不会被识别为 Unicode escape 的开头；之后两个 backslash 才按 string literal 的规则产生一个实际 backslash。不需要现在背整套资格规则，先记清这个常用写法的结果。

如果普通文本文件里本来就有字面文本 `\u0041`，用 UTF-8 读文件只会读到这六个字符。**读文本文件不会自动执行 Java source code 的 Unicode escape 规则。** 第 07、11 讲会把这件事放进完整文件流程。

## 7. 第一次看懂 e 后面跟着 \u0301

先只读写法，不急着学 Normalizer：

```java
String text = "e\u0301";
```

这一行指定了两个部分：

```text
e       → UTF-16 code unit 0065
\u0301  → UTF-16 code unit 0301
```

`U+0301` 是一个 **combining acute accent**：它会与前面的字母一起显示。因此这两部分通常看起来像 `é`。

```text
source code 的写法：
"e\u0301"

运行时的两个 code point：
U+0065 U+0301

通常显示：
é
```

这里只是两个内容依次放在 String 中，不是数学加法，也不是 Java 在运行时把文本里的数字算了一遍。

为什么 Unicode 既有单独的 `U+00E9`，又允许这两个 code point 组合？为什么它们看起来相同却可能比较不等？第 08 讲专门解决，先不在本讲叠加新规则。

另一个容易看错的例子：`\u03B7` 指定的是 `U+03B7`，也就是 Greek letter `η`；它不是 `\u0301` 那个重音。后面的 hexadecimal digit 不同，指定的内容就不同。

## 8. 可以自己运行的小程序

保存为 `EscapeDemo.java`：

```java
public class EscapeDemo {
    public static void main(String[] args) {
        System.out.println("A".equals("\u0041"));
        System.out.println("é".equals("\u00E9"));
        System.out.println("😀".equals("\uD83D\uDE00"));
        System.out.println("\\u0041");
    }
}
```

前三行输出都是 `true`。最后一行显示字面文本 `\u0041`。如果终端字体不能显示某个字符，比较结果仍然能帮助你检查实际内容。

本文件用 UTF-8 保存时，可以这样运行：

```text
javac -encoding UTF-8 EscapeDemo.java
java EscapeDemo
```

`-encoding UTF-8` 指定 compiler 如何读取 source file 的 byte；它不改变 `\uXXXX` 这条 Java 语法规则。第 07 讲继续解释这个边界。

## 自测

1. `"\u0041"` 与 `"U+0041"` 会得到一样的 String 吗？
2. 编译 source code 中的 `"\u4E2D"`，与读取普通文本文件里的六个字符 `\u4E2D`，是不是同一个过程？
3. `"\uD83D\uDE00"` 为什么需要两段 Unicode escape？

<details>
<summary>核对答案</summary>

1. 不一样。前者内容是 A；后者内容是 U、+、0、0、4、1。
2. 不是。前者有 Java compiler 的 Unicode escape 处理；普通文件读取只有文件 encoding 的解释，除非程序之后明确增加另一种解析规则。
3. 因为 😀 在 UTF-16 中需要 D83D、DE00 两个 code unit，每段 escape 指定一个。

</details>

## 对照资料

- [Java Language Specification 3.3 — Unicode Escapes](https://docs.oracle.com/javase/specs/jls/se17/html/jls-3.html#jls-3.3)
- [Java Language Specification 3.10 — Literals](https://docs.oracle.com/javase/specs/jls/se17/html/jls-3.html#jls-3.10)

[上一讲：UTF-16 and UTF-32](04-UTF-16-and-UTF-32.md) · [课程入口](00-Reading-Guide.md) · [下一讲：Java char and String](06-Java-char-and-String.md)
