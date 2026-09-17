# Java Character Encoding — 从规则到完整流程

这是一套按顺序阅读的 lecture，不是一张把所有名词放在一起的速查表。

你已经知道变量、字符串、十进制和二进制。本课程不重讲这些基础。我们从一个具体疑问开始：**字母 A 为什么对应 65，而 65、U+0041、UTF-8 中的 41 到底是什么关系？**

讲解使用中文，专业术语保留英文。每次只引入当前算例真正需要的术语，先把过程走通，再看 Java 如何执行同一件事。

## 怎么读

建议一次只读一讲。先看算例，自己在纸上走一遍转换，再做末尾的自测；折叠答案用于核对，不需要背诵术语。

如果后面出现看不懂的符号，先回到讲它的那一讲，不要靠记住一段 API 调用往下跳。尤其是 Lecture 02–04：后面的 Java、文件和 Normalizer 都建立在它们之上。

文中的 `A`、`é`、`中`、`😀` 会反复出现。这是为了让你跟踪同一段信息怎样改变表示，而不是不断换例子增加负担。

## 阅读顺序

| Lecture | 本讲只要回答清楚的问题 | 阅读重点 |
|---|---|---|
| [01 — Why Unicode](01-Why-Unicode.md) | 为什么已有 ASCII 和各种编码，还需要 Unicode？ | 字符编号需要共同约定 |
| [02 — Code Points and Notation](02-Code-Points-and-Notation.md) | `65`、`01000001`、`0x41`、`U+0041` 怎么对应？ | 编号、进制写法、实际字节分开看 |
| [03 — UTF-8 Step by Step](03-UTF-8-Step-by-Step.md) | 一个 code point 怎样按规则变成 1–4 个 byte？ | 逐位填模板，再反向读回来 |
| [04 — UTF-16 and UTF-32](04-UTF-16-and-UTF-32.md) | 为什么笑脸在 UTF-16 中要变成两部分？ | 手算 surrogate pair，理解 byte order |
| [05 — Java Literals and Escapes](05-Java-Literals-and-Escapes.md) | `"\u00E9"` 究竟是什么写法，谁在解释它？ | source code 中的写法与运行时内容 |
| [06 — Java char and String](06-Java-char-and-String.md) | Java 下标实际数的是什么？ | 把 UTF-16 的规则放进一个 String |
| [07 — Charset and File IO](07-Charset-and-File-IO.md) | 文件里的 byte 怎样进入 Java，又怎样保存出去？ | 手动过程与 Java 方法逐步对应 |
| [08 — Why Normalization](08-Why-Normalization.md) | 为什么同样看起来是 é，内部可以不一样？ | 先看到真正的问题，再引入解决目标 |
| [09 — NFD and NFC](09-NFD-and-NFC.md) | 分解、排序、重新组合到底做了什么？ | 按 code point 追踪每一步 |
| [10 — NFKD and NFKC](10-NFKD-and-NFKC.md) | 多一个 K，为什么全角字、圈号、连字会改变？ | 理解 compatibility 的具体含义 |
| [11 — End-to-End Walkthrough](11-End-to-End-Walkthrough.md) | 从 source/file 到 String，再到 Normalizer 和输出，完整过程是什么？ | 把前面学过的步骤连成一个真实案例 |
| [可选附录 — Other Encodings](90-Other-Encodings.md) | ISO、GBK、ANSI、Modified UTF-8 等名字放在哪一层？ | 遇到时查，不必第一次全读 |

### 第一段：先把转换算清楚

先读 01–04。到这里，你应当能解释：A 的编号不是由字母形状算出来的；`U+0041` 不是小数；UTF-8 的“8”不是一个 code point 的固定长度；UTF-16 的“16”也不是一个 code point 的固定长度。

最重要的成果不是背下 `E4 B8 AD`，而是能根据模板从 `U+4E2D` 推出它，再从它还原回去。

### 第二段：让 Java 对上这些规则

再读 05–07。先弄清源代码中的 `\u` 写法，然后才去读 `charAt`、`getBytes`、`new String` 和 `StandardCharsets.UTF_8`。

这里讨论的是 **Java String**。`String` 是 Java 中的类型名称，不是 JavaScript 的缩写。

### 第三段：最后才引入 Normalizer

读 08–10 时，前面的 encode/decode 已经是已知知识。此时再比较：为什么“把 byte 读成文字”与“把已经读出的文字统一表示”是不同工作。

最后读 11。你将跟踪同一个例子经过每个环节，而不是重新面对一张没有上下文的大流程图。

## 代码怎样使用

标成“完整程序”的代码可以单独保存成文中指定的 `.java` 文件，用 Java 17 或更新版本运行。普通片段用于观察当前一步，不要求逐个建立项目。

没有 Java 环境也可以先读：重要算例的 byte、bit、code point 和预期输出都写在讲义里。程序用于验证已经解释过的规则，不替代解释。

二进制分组中的空格、方括号、箭头是教学标记，不是要写入文件的数据。图中若出现 `◌́`，圆圈是展示 combining mark 的占位，讲义会明确指出实际保存的内容。

## 这套讲义放在哪里，为什么

主目录放在 `02-Java-Basics/00-Character-Encoding`：它讨论 `char`、`String`、source code 和文本输入输出的基础模型，不依赖继承、多态等 OOP 内容。

原来的 [Unicode 速查笔记](../../05-Java-Advanced/05-Core%20Utilities/06-Unicode.md) 继续保留，适合学完后快速查知识点。本课程承担从头建立概念的任务。

从这里开始：[Lecture 01 — Why Unicode](01-Why-Unicode.md)。
