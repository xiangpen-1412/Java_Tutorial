# Lecture 06 — Java 的下标究竟在数什么

前置：[Lecture 04](04-UTF-16-and-UTF-32.md) 的 UTF-16，以及 [Lecture 05](05-Java-Literals-and-Escapes.md) 的 source code 写法。

本讲把前面的规则放入一个具体 String：`"A😀中"`。目标是看懂同一段文字为什么可以得到不同的长度，以及 `charAt` 实际取到了什么。

## 1. 一个 char 对应一个 UTF-16 code unit

Java 的 `char` 是一个 16-bit unsigned value，数值范围是 `0–65535`。这正好容纳一个 UTF-16 code unit。

先看没有 surrogate pair 的 A：

```text
A
code point：U+0041
UTF-16 code unit：0041
```

于是：

```java
char letter = 'A';
int number = letter;

System.out.println(number); // 65
```

这里 `char` 转成 `int`，取到的是那个 code unit 的数值。对于 A，这个数值恰好也就是 code point 的数值。

对中同样成立：

```java
char letter = '中';
System.out.println((int) letter); // 20013
```

但不能因此推广成“所有 Unicode code point 都装在一个 char 中”。

## 2. 笑脸需要占两个下标位置

上一讲已经算出：

```text
😀：
一个 code point U+1F600
两个 UTF-16 code unit D83D DE00
```

因此 `"A😀中"` 的 UTF-16 视图是：

```text
Java 下标：      0       1       2       3
code unit：   0041    D83D    DE00    4E2D
文字对应：      A      └── 😀 ──┘      中
```

这里画的是 String API 暴露的 code unit 序列，不是 JVM 对象在内存中的完整布局。

我们先预测，再用程序验证：

```java
String text = "A😀中";

System.out.println(text.length()); // 4
```

`length()` 数的是上图四个下标位置。笑脸不是意外多算了一次，而是按 UTF-16 规则本来就用了两个位置。

## 3. charAt 只拿一个位置

```java
System.out.println(text.charAt(0)); // A
System.out.println(text.charAt(3)); // 中
```

那么 `text.charAt(1)` 呢？

它只会取出 `D83D`。`text.charAt(2)` 则取出 `DE00`。每个返回值都只有一个 `char` 的容量。

为了看清数字，不要依赖终端怎样绘制孤立 surrogate：

```java
System.out.println((int) text.charAt(1)); // 55357，即 hex D83D
System.out.println((int) text.charAt(2)); // 56832，即 hex DE00
```

`128512` 才是整个笑脸的 code point；`55357`、`56832` 是两个 UTF-16 code unit 的数值。

这两个 code unit 必须配合解释，不能把任意一半当成那个表情。

## 4. codePointAt 会识别合法 surrogate pair

Java 另有专门返回 code point 的方法：

```java
int cp = text.codePointAt(1);

System.out.println(cp); // 128512，即 hex 1F600
```

这次的操作是：

```text
从下标 1 读到 D83D
    ↓
发现它是 high surrogate
    ↓
下一个位置是配套的 low surrogate DE00
    ↓
按 Lecture 04 的逆向规则组合
    ↓
返回完整 code point 128512
```

`codePointAt` 返回 `int`，因为一个 `char` 容不下所有完整 code point。

一个很容易忽略的细节：`codePointAt(1)` 的 `1` **仍是 UTF-16 下标**，不是“第一个/第二个 code point”的新编号体系。你要从完整 code point 的起始位置调用它。

## 5. 三种长度，分别问了三个问题

对同一个 `text = "A😀中"`：

```java
int units = text.length();
int points = text.codePointCount(0, text.length());
int bytes = text.getBytes(java.nio.charset.StandardCharsets.UTF_8).length;
```

前两个方法到这里应该已经能理解；第三行的 Java 参数下一讲会拆开解释，现在只看它在数“按 UTF-8 编码后的 byte”。

手工数一遍：

```text
UTF-16 code units：
0041 | D83D | DE00 | 4E2D
共 4 个

Unicode code points：
0041 | 1F600 | 4E2D
共 3 个

UTF-8 bytes：
41 | F0 9F 98 80 | E4 B8 AD
1  +     4      +    3       = 8 个 byte
```

所以程序得到 `4`、`3`、`8`，没有哪一个“错误”。它们分别回答 code unit 数、code point 数、UTF-8 byte 数。

不要把 `String.length()` 理解成“文件大小”。即使没有任何 emoji，`"中".length()` 是 1，但它的 UTF-8 表示也已经是 3 byte。

## 6. substring 的位置仍按 code unit 计算

`substring(start, end)` 使用包含 start、不包含 end 的范围。

回到位置图：

```text
0       1       2       3
0041    D83D    DE00    4E2D
```

所以：

```java
String wholeFace = text.substring(1, 3); // 同时包含下标 1、2
System.out.println(wholeFace.equals("😀")); // true
```

而：

```java
String half = text.substring(1, 2); // 只包含 D83D
```

会留下一个孤立的 high surrogate。Java 可以容纳这种 String，但它不是一个完整笑脸，也不是良好的完整 UTF-16 字符序列。后续编码可能触发错误处理或替换。

这就是为什么处理任意 Unicode 文本时，不能总是假设 `i + 1` 就是下一个完整字符。

## 7. 按 code point 往前走，为什么有时加 1，有时加 2？

```java
for (int i = 0; i < text.length(); ) {
    int cp = text.codePointAt(i);
    System.out.println(cp);
    i += Character.charCount(cp);
}
```

逐轮执行：

| 本轮 i | 读取的 code point | 对应文字 | charCount 返回 | 下一轮 i |
|---|---|---|---:|---:|
| 0 | U+0041 | A | 1 | 1 |
| 1 | U+1F600 | 😀 | 2 | 3 |
| 3 | U+4E2D | 中 | 1 | 4，结束 |

`Character.charCount(cp)` 的问题很具体：“这个 code point 按 UTF-16 表示，需要几个 char？”

如果熟悉 stream，也可以用 `text.codePoints()`，它会按 code point 遍历；`text.chars()` 遍历的则是逐个 char 的数值。两者的名字相近，单位不同。

反过来，从完整 code point 构造文字可以用：

```java
String face = new String(Character.toChars(0x1F600));
```

逐步看：

```text
0x1F600：
一个 int，值为 128512

Character.toChars：
按 UTF-16 规则得到两个 char，数值 D83D、DE00

new String：
由这两个 char 构造内容为 😀 的字符串
```

这和上一讲直接写 `"\uD83D\uDE00"`，最终指定的是相同的 UTF-16 序列。

## 8. 先留一个边界：code point 数也未必等于眼睛看到的数量

上一讲见过：

```java
String accented = "e\u0301";
```

实际有 `U+0065`、`U+0301` 两个 code point，但 combining mark 和 e 通常共同显示为一个带重音的字母。

所以“避开 surrogate pair 的问题”之后，还可能有“多个 code point 共同构成一个阅读单位”的问题。处理后一种边界时会用到 **grapheme cluster**。第 08 讲会通过两种 é 继续展开；这里先不要把它与 code point 合并成一个概念。

对只有 `a`、`b`、`c` 的算法输入，每个字母恰好对应一个 code point、一个 char 和一个 UTF-8 byte。此时使用 `charAt` 是合理的，不需要为了通用性把题目变复杂。

## 9. 内部存储只补一句

本讲讨论的是 String 的 API 语义。现代 OpenJDK 可以用 compact strings 优化具体存储，因此“按 UTF-16 code unit 提供下标”不代表每个 String 对象内部永远就是一个 `char[]`。

这不影响以上任何下标、长度、比较结果。实际存储实现放在[可选附录](90-Other-Encodings.md)，第一遍可以略过。

## 自测

1. `"A😀中".length()` 是 4，能否推出它保存成 UTF-8 文件一定占 8 byte？为什么这个例子是 8，但不能只凭 length 推断？
2. 为什么 `(int) "😀".charAt(0)` 不是 128512？
3. `codePointAt` 已经返回完整 code point，为什么遍历时不能固定让 i 加 1？

<details>
<summary>核对答案</summary>

1. 不能只凭 length 推断。length 只告诉你 UTF-16 code unit 数，不告诉你每个 code point 在 UTF-8 中的长度。本例根据 A、😀、中的具体 UTF-8 表示才算出 1 + 4 + 3 = 8。
2. charAt(0) 只取到 high surrogate D83D；完整 code point 需要将 D83D、DE00 配对还原。
3. 因为 i 仍是 UTF-16 下标。笑脸占两个位置，读完它后必须跳过两个位置，才到下一个 code point 的开头。

</details>

## 对照资料

- [Java String — UTF-16 indexing and code point APIs](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/String.html)
- [Java Character — Unicode character representations](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Character.html)

[上一讲：Java Literals and Escapes](05-Java-Literals-and-Escapes.md) · [课程入口](00-Reading-Guide.md) · [下一讲：Charset and File IO](07-Charset-and-File-IO.md)
