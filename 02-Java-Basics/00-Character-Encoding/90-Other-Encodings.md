# 可选附录：其他编码，以及看起来像编码的名字

[课程目录](00-Reading-Guide.md) · [上一讲：完整流程](11-End-to-End-Walkthrough.md) · [回看 Lecture 07：Charset 与文件](07-Charset-and-File-IO.md)

**这篇不用一口气学完。** 前 11 讲是主线；这里是以后读文件、看 Java API 时遇到陌生名字，可以回来查的补充。

最重要的判断仍然只有一个：这个名字是在讲字符编号、bytes 的表示规则、文本内容变化，还是别的东西？

## 1. ASCII、Latin-1、Windows-1252：从一个 byte 开始

ASCII 定义 0～127 的字符编号，包括英文字母、数字、标点和控制字符。实际保存时通常每个用一个 byte，最高位为 0。

```text
A → 65 decimal → 41 hex → 01000001
```

ASCII 没有中文，也没有 `é`。早期系统于是采用不同的扩展方案，利用一个 byte 可以表示的 0～255 范围，容纳更多字符。

例如在 ISO-8859-1 中：

```text
é → E9
```

它只有一个 byte。但同一个 `é`，在 UTF-8 中是：

```text
é → C3 A9
```

这再次说明：**字符相同，不代表 bytes 相同；要看使用的规则。**

`ISO-8859-1` 也叫 Latin-1，是 ISO-8859 系列中的一个成员。`ISO` 本身是标准组织的名字，不是一种独立编码。

Windows-1252 与 Latin-1 很相似，却不是完全一样。一个明确的区别是 byte `80`：

```text
ISO-8859-1：80 → U+0080，一个控制字符
Windows-1252：80 → U+20AC，€
```

所以“这文件用的是扩展 ASCII”仍然不够明确。还要知道它究竟是 Latin-1、Windows-1252，还是其他 code page。

## 2. 中文、日文相关编码：先认用途，不必背每张映射表

| 编码名称 | 在哪里常遇到 | 先掌握什么 |
|---|---|---|
| GB2312 | 较早的简体中文数据 | 能表达的字符范围有限 |
| GBK | 传统 Windows 中文文件等 | 覆盖范围比 GB2312 更广；“中”常见表示是 `D6 D0` |
| GB18030 | 要求较广中文及 Unicode 覆盖的系统 | 使用 1、2、4-byte sequences；不要简单当成“每字两 bytes” |
| Big5 | 传统繁体中文数据 | 与 GBK 不是同一套映射 |
| Shift_JIS | 传统日文数据 | 与 UTF-8 不是同一套规则 |

知道文件的来源格式，比看到中文就猜一个编码更可靠。

Java 里可以按名字取得规则：

```java
Charset gbk = Charset.forName("GBK");
Charset big5 = Charset.forName("Big5");
```

这些编码并非全部属于 Java 最低要求的标准 charset 集合。当前环境支持什么，可以用 `Charset.isSupported(...)` 检查，也可以查该 JDK 的文档。[Oracle JDK 17 Supported Encodings](https://docs.oracle.com/en/java/javase/17/intl/supported-encodings.html)

“ANSI 编码”也不是一个唯一的世界通用编码名。在一些 Windows 软件中，它指向当时所用的某个代码页。收到“请按 ANSI 读取”的要求，还需要确定实际代码页。

## 3. UTF-32：规则直观，但不保证“眼睛看到一个字就四 bytes”

UTF-32 使用 32-bit code units。一个 Unicode scalar value 用一个这样的单位，也就是 4 bytes。

这里的 **Unicode scalar value**，就是 `U+0000～U+10FFFF` 中排除 surrogate 保留范围 `U+D800～U+DFFF` 后的 code point。它不是另一个编码，也不是新分配的一套编号。

以 big-endian 顺序写：

```text
A：  U+0041  → 00 00 00 41
中： U+4E2D  → 00 00 4E 2D
😀： U+1F600 → 00 01 F6 00
```

它的好处是每个 code point 对应固定长度；代价是 ASCII 文本也每个占四 bytes。

但是 `e + combining acute accent` 仍是两个 code points，因此 UTF-32 需要 8 bytes。UTF-32 不会自动做 NFC，也不会自动把一个 grapheme cluster 变成一个 code point。

UTF-8、UTF-16、UTF-32 都能表示 Unicode scalar values；它们没有“中文用高级版、英文用低级版”的关系。

## 4. UTF-16BE、UTF-16LE 和 BOM

Lecture 04 已经解释 UTF-16 code units。这里再区分：一个 16-bit 单位进入文件时，要拆成两个 bytes，这两个 bytes 谁在前？

```text
A 的 UTF-16 code unit：0041

UTF-16BE：00 41
UTF-16LE：41 00
```

`BE` 是 Big Endian，高位 byte 在前；`LE` 是 Little Endian，低位 byte 在前。文本含义没有变。

**BOM 是放在数据开头、帮助表明 byte order 的标记。** 对 UTF-16：

```text
big-endian BOM：FE FF
little-endian BOM：FF FE
```

Java 的三个常量有不同约定：

| Java 编码选择 | encode `A` 得到的 bytes | 为什么 |
|---|---|---|
| `UTF_16BE` | `00 41` | 指定大端，不自动加 BOM |
| `UTF_16LE` | `41 00` | 指定小端，不自动加 BOM |
| `UTF_16` | `FE FF 00 41` | Java 这个 encoder 使用大端并加 BOM |

这里 `FE FF` 是额外标记，不是 `A` 的 code point。

decode 时，Java 的 `UTF_16` 根据开头 BOM 决定 byte order，没有 BOM 时默认 big-endian。明确使用 `UTF_16BE` 或 `UTF_16LE` 的 decoder 不会把开头正确对应的 `U+FEFF` 当作要自动剥掉的 byte-order 标记，而会把它当成文本中的字符。Lecture 07 的 Charset 官方链接列出了这些规则。

UTF-8 不区分 BE/LE，因为一个 code unit 就是一个 byte。你可能仍看到 UTF-8 文件开头的 `EF BB BF`：这是 `U+FEFF` 的 UTF-8 表示，有时被用作格式签名；它不是用来选择 UTF-8 的字节顺序。

Java 普通 UTF-8 encoder 不自动加这三个 bytes。普通 UTF-8 decode 也不应被想当然地认为“会帮我删掉开头这个字符”；若文件协议允许该签名，是否处理它应根据实际读取工具与格式约定确定。

## 5. Modified UTF-8：Java 中有个名字相似、规则不同的东西

`DataOutputStream.writeUTF()` 的 `UTF` 容易让人以为它就是向文件写普通 UTF-8。实际上，它使用 **Modified UTF-8**，而且整个输出还带有长度信息。

先看空字符 `U+0000`：

```text
标准 UTF-8： 00
Modified UTF-8 的内容 bytes：C0 80
```

再看笑脸 `U+1F600`：

```text
标准 UTF-8：F0 9F 98 80

Java UTF-16 surrogate pair：D83D DE00
Modified UTF-8 分别处理两个 code units：
D83D → ED A0 BD
DE00 → ED B8 80

合起来：ED A0 BD ED B8 80
```

所以这个笑脸在 Modified UTF-8 的内容部分占 6 bytes。

而一次 `writeUTF("A")` 的完整输出是：

```text
00 01 41
└───┘ └┘
长度  内容
```

前两个 bytes 记录后面 Modified UTF-8 内容的 byte 数量。一次 `writeUTF` 的内容长度最多是 65535 bytes；不是最多 65535 个视觉字符。

因此，`writeUTF` 的配套操作是了解这套格式的 `readUTF`，不能直接拿整段输出当普通 UTF-8 文本。[Java DataOutput：Modified UTF-8 与 writeUTF](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/io/DataOutput.html)

你不需要为了普通 `.txt` 文件学习这套格式。正常的 `getBytes(StandardCharsets.UTF_8)` 使用的是标准 UTF-8。

## 6. Base64 也叫 encoding，但它编码的是 bytes

假设文字是 `A`：

```text
文字 A
  ↓ 用 UTF-8 encode
byte：41
  ↓ 用 Base64 表示这串 bytes
文本：QQ==
```

Base64 甚至可以处理图片、压缩文件等 bytes，不要求输入先是文本。

所以处理收到的 Base64 文本时，可能要走两步：

```text
QQ==
  ↓ Base64 decode
byte：41
  ↓ UTF-8 decode
文本：A
```

第一次 decode 恢复 bytes，第二次 decode 解释文字。两次都叫 decode，但采用的规则和输入输出类型不同。

## 7. Compact Strings：Java 的 API 语义与内部存储实现分开看

本课程反复使用：

```text
String length 和下标按 UTF-16 code units 计算。
char 是一个 16-bit UTF-16 code unit。
```

这不意味着每个现代 JVM 的 String 内部都必须放一个 `char[]`。

OpenJDK 引入 Compact Strings 后，可以按内容选择更紧凑的内部存储。在典型实现中，String 使用一个 `byte[]` 加上标记，区分 Latin-1 和 UTF-16 形式。[JEP 254：Compact Strings](https://openjdk.org/jeps/254)

例如某个 String 只含 Latin-1 范围内的字符，实现可以节省空间。但从外部调用：

```java
"A".length()   // 1
"😀".length() // 2
```

含义不变。**实现怎样节省内存，不能拿来推翻 String API 的 UTF-16 下标规则。** 反过来，也不能根据 API 的 UTF-16 规则，直接推断一个对象占多少内存。

## 8. 遇到无法转换的数据：replacement 与 REPORT

ASCII 没有“中”，因此“把中 encode 为 ASCII”没有无损结果。UTF-8 decoder 也可能遇到不合法的 byte sequence，例如截断的数据。

不同 API 对这些问题有不同处理方式：有的采用 replacement，有的直接报错。不要假设所有 `String`、`Files`、reader 的方便方法都采用同一种策略。

需要明确控制时，使用 `CharsetEncoder` 或 `CharsetDecoder`，并指定 `CodingErrorAction.REPORT`。例如严格按 UTF-8 decode：

```java
CharsetDecoder decoder = StandardCharsets.UTF_8.newDecoder()
    .onMalformedInput(CodingErrorAction.REPORT)
    .onUnmappableCharacter(CodingErrorAction.REPORT);

String text = decoder.decode(ByteBuffer.wrap(data)).toString();
```

这段代码的重点只有一个：**遇到所选规则无法正确解释的数据，报告错误，而不是悄悄用替代字符继续。** `ByteBuffer.wrap` 只是把已有 byte 数组包装成 decoder 接受的输入形式。完整使用还需要相关 imports，并处理 `CharacterCodingException`。

但即使 strict decode 成功，也不能证明你选对了文件的原始编码。Lecture 07 的例子已经说明：把 UTF-8 的 `C3 A9` 当作 ISO-8859-1，仍然每个 byte 都“合法”，只是文本读错了。

## 9. 三道可选检查题

**问题 1：** 某个文件说自己是“ANSI”。是否已经能唯一确定该用哪个 Java Charset？

<details>
<summary>展开答案</summary>

不能。这不是唯一明确的编码名称，需要确定具体 code page 或实际格式约定。

</details>

**问题 2：** `writeUTF("A")` 与 `"A".getBytes(UTF_8)` 的输出一样吗？

<details>
<summary>展开答案</summary>

不一样。前者完整输出是 `00 01 41`，有两字节长度信息，并采用 Modified UTF-8。后者是标准 UTF-8 byte 数组 `41`。

</details>

**问题 3：** UTF-32 每个 code point 固定四 bytes，是否意味着用户看到的每个字固定四 bytes？

<details>
<summary>展开答案</summary>

不意味着。一个 grapheme cluster 可以包含多个 code points，例如 `e + combining acute accent` 就需要两个 UTF-32 code units，共 8 bytes。

</details>

[课程目录](00-Reading-Guide.md) · [回看完整流程](11-End-to-End-Walkthrough.md)
