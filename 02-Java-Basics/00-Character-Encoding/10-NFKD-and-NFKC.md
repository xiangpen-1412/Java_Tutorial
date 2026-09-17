# Lecture 10：NFKD 与 NFKC，什么时候要把 `Ａ` 当成 `A`？

[课程目录](00-Reading-Guide.md) · [上一讲：NFD 与 NFC](09-NFD-and-NFC.md) · [下一讲：完整流程回放](11-End-to-End-Walkthrough.md)

## 先提出一个与 `é` 不太一样的问题

假设产品编号是 `ABC`，用户却输入了 `ＡＢＣ`。后者是 fullwidth letters，往往来自输入法的全角模式。

你可能希望搜索仍然成功。但是对输入做 NFC 之后，它还是 `ＡＢＣ`。上一讲的方案为什么没有解决这个问题？

因为 `Ａ` 与 `A` 有明确的宽度形式差异，不是 `é` 与 `e + acute` 那种 canonical equivalence。这一讲讨论是否要进一步合并这些 **compatibility** 差异。

## 1. Compatibility equivalence 到底是什么？

先看四组具体关系。方括号只用来划分 code point，不属于文本。

| 输入 | Code point | Compatibility decomposition 的结果 |
|---|---|---|
| 全角 `Ａ` | `U+FF21` | `[A]`，即 `U+0041` |
| 圈号 `①` | `U+2460` | `[1]`，即 `U+0031` |
| 连字 `ﬁ` | `U+FB01` | `[f][i]`，即 `U+0066 U+0069` |
| 上标 `²` | `U+00B2` | `[2]`，即 `U+0032` |

这些字符保留了宽度、圈号、连字或上标等差异。Unicode 收录它们有兼容既有字符体系和文本的需求，并为这些例子定义了与基本形式之间的 compatibility mapping。[Unicode 对 compatibility equivalence 的说明](https://www.unicode.org/reports/tr15/#Canon_Compat_Equivalence)

这里的 mapping 不是视觉识别，也不是见到任何奇怪字体就删除装饰。它是 Unicode 数据明确规定的关系。[Unicode Character Database](https://www.unicode.org/Public/17.0.0/ucd/UnicodeData.txt)

对于搜索，你可能希望忽略这些差异；对于原文展示，某些差异则值得保留。因此，是否使用它们取决于你希望哪些输入视为相同。

## 2. NFKD：分解规则的范围扩大了

`K` 在这里代表 **Compatibility**。上一讲的 `C` 已经代表 Composition，所以 compatibility 使用 `K` 区分。

NFD 的逻辑是：

```text
canonical decomposition → canonical ordering
```

NFKD 的逻辑则是：

```text
canonical 与 compatibility decomposition
                   ↓
           都递归展开到底
                   ↓
           canonical ordering
```

**NFKD 包含 NFD 所使用的 canonical 规则，并额外处理 compatibility mappings。** 它并没有放弃 `é` 的分解。

用同一段混合文本走一遍：

```text
输入：Ａ①ﬁé

Code points：
U+FF21  U+2460  U+FB01        U+00E9
  Ａ       ①       ﬁ             é
   │       │       │             │
   ▼       ▼       ▼             ▼
U+0041  U+0031  U+0066 U+0069  U+0065 U+0301
   A       1       f      i       e     acute
```

前三个用到 compatibility decomposition；最后一个用到 canonical decomposition。本例只有最后一个 mark，无须再调整顺序。

NFKD 的最终结果是：

```text
[ A ] [ 1 ] [ f ] [ i ] [ e ] [ acute ]

U+0041 U+0031 U+0066 U+0069 U+0065 U+0301
```

视觉上通常显示为 `A1fié`，但末尾仍是两个 code point。这一点和上一讲完全一致：**D 形式保留分解后的表示。**

## 3. NFKC：做完 NFKD，再做 canonical composition

现在仍然使用同一输入。先得到刚才的 NFKD 结果：

```text
[ A ] [ 1 ] [ f ] [ i ] [ e ] [ acute ]
```

再应用 canonical composition：

```text
[ A ] [ 1 ] [ f ] [ i ] [ e ] [ acute ]
                              └────┬────┘
                                   ▼
[ A ] [ 1 ] [ f ] [ i ]          [ é ]
```

最终 code point 序列：

```text
U+0041 U+0031 U+0066 U+0069 U+00E9
```

结果仍然显示为 `A1fié`，但这一次末尾的 `é` 是一个 code point。

### 为什么 `f` 和 `i` 不重新合成 `ﬁ`？

因为最后一步只做 **canonical composition**。

```text
e + acute → é
属于允许的 canonical composition。

f + i → ﬁ
不能因为存在 compatibility mapping 的反方向，就自动做这个组合。
```

所以 NFKC 的准确流程是“compatibility decomposition，然后 canonical composition”，并不是“先把所有字符拆开，再把所有能合成的都合回去”。[Unicode 对 NFKC composition 的说明](https://www.unicode.org/reports/tr15/#Normalization_Forms)

## 4. 现在四种 form 可以放在同一张小表里

这张表是对刚才过程的收束，不需要提前背它。

| Form | Decomposition 阶段 | 排序之后是否 canonical composition？ |
|---|---|---|
| NFD | 只用 canonical mappings | 否 |
| NFC | 只用 canonical mappings | 是 |
| NFKD | canonical 与 compatibility mappings | 否 |
| NFKC | canonical 与 compatibility mappings | 是 |

同一输入 `Ａ①ﬁé` 的结果：

```text
NFD ：[Ａ] [①] [ﬁ] [e] [acute]
NFC ：[Ａ] [①] [ﬁ] [é]
NFKD：[A] [1] [f] [i] [e] [acute]
NFKC：[A] [1] [f] [i] [é]
```

再看 Java，不需要学习新方法，只是换第二个参数：

```java
String text = "Ａ①ﬁé";

String decomposed = Normalizer.normalize(text, Normalizer.Form.NFKD);
String composed = Normalizer.normalize(text, Normalizer.Form.NFKC);

System.out.println(decomposed.equals("A1fie\u0301")); // true
System.out.println(composed.equals("A1fi\u00E9"));   // true
```

## 5. Compatibility normalization 会合并哪些原本有意义的区别？

考虑这两个数学文本：

```text
x²       x2
```

如果把它们都做 NFKC，就都得到：

```text
x2
```

原来的上标信息没有保留。转换后，仅凭结果不能判断原来输入的是 `²` 还是普通 `2`。

同理，`Ａ → A` 以后，你无法仅凭 `A` 还原用户是否用了 fullwidth。NFKC 不是“比 NFC 更正确”的形式，而是选择忽略更多由 compatibility mappings 定义的区别。

实际可以保存原文，同时生成一个专用于搜索或比较的 key。这个 key 是否使用 NFKC，应取决于搜索规则。不要为了“统一”就无条件改变需要原样展示的正文。

## 6. “去重音”为什么又多出一个步骤？

假设想让搜索中的 `café` 能与 `cafe` 匹配。先追踪 NFD：

```text
café
  ↓ NFD
[c] [a] [f] [e] [acute]
```

acute 仍在。因此 NFD 本身不会得到 `cafe`。

要得到 `cafe`，必须再做一个不同操作：删除选定的 marks。

```text
[c] [a] [f] [e] [acute]
                    × 删除 mark
                    ↓
[c] [a] [f] [e]
```

常见演示代码是：

```java
String decomposed = Normalizer.normalize("café", Normalizer.Form.NFD);
String withoutMarks = decomposed.replaceAll("\\p{M}+", "");

System.out.println(withoutMarks); // cafe
```

这第二行有两层语法，不要把它当成神秘口令：

1. 正则引擎看到的表达式是 `\p{M}+`。其中 `\p{M}` 表示 Unicode 的 **Mark category**，`+` 表示连续一个或多个。
2. Java 源代码中的字符串要写成 `"\\p{M}+"`，因为 Java 用 `\\` 表示字符串里的一个实际反斜杠。
3. `replaceAll(..., "")` 把匹配到的部分替换为空文本，所以 marks 被删除。

这个 `\\` 是 Java 的反斜杠 escape；它和前面 `\u0301` 那种 Unicode escape 不是同一种写法。

`Mark category` 不只包括法语重音。广泛删除 marks 可能删掉其他语言中必要的组成部分。这里的代码用于理解两步机制，实际搜索规则需要明确是否允许这种变化。[Java 17 Pattern 对 Unicode categories 的说明](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/regex/Pattern.html)

另外，不是每个带特殊形状的字母都能通过这个方法“变回普通英文字母”：

```text
输入：café ø ß
NFD： c a f e + acute，后面的 ø 和 ß 不变
删 M：cafe ø ß
```

这不是任意语言到 ASCII 的转换器。

## 7. 大小写、视觉相似和乱码，仍然是另外的问题

NFKC 对 fullwidth `Ａ` 的处理到这里就结束了：

```text
Ａ → A
```

它没有要求变成小写 `a`。如果业务还要忽略大小写，需要另外选用大小写处理规则。例如针对语言无关的技术文本，可以明确使用：

```java
String normalized = Normalizer.normalize("Ａ", Normalizer.Form.NFKC);
String lowercase = normalized.toLowerCase(Locale.ROOT);
```

`Locale.ROOT` 选择不依赖默认语言环境的规则，而不是 normalization form。这里最终得到 `a`，是两个操作共同完成的；NFKC 本身不等于 case folding，`toLowerCase()` 也不能替代完整 Unicode case folding。

同样，拉丁字母 `a`（`U+0061`）与外观相似的西里尔字母 `а`（`U+0430`）不会只因为“看起来很像”，就在 NFKC 下变成同一个 code point。

最后，如果原本的 UTF-8 bytes 被误当成另一种 charset 读成了乱码，问题发生在 decode 阶段。Normalizer 接收到的已经是错误文本，不知道原始 bytes 应该怎样解释，因此不能靠换一种 form 修复乱码。

## 自测

1. NFKC 为什么把 `ﬁ` 变成 `fi`，却不会在最后再变回 `ﬁ`？
2. NFKD 处理 `é` 时，还会不会把它分成 `e + acute`？
3. `café → cafe` 是一次 NFD 的结果吗？`Ａ → a` 是一次 NFKC 的结果吗？

<details>
<summary>展开答案</summary>

1. 分解阶段使用 compatibility mapping；最后只做 canonical composition，不反向恢复 compatibility forms。
2. 会。NFKD 同时使用 canonical 与 compatibility decomposition，不是只处理后一类。
3. 都不是。前者需要在分解后再删除相应 mark；后者需要在 NFKC 后再做大小写处理。

</details>

[下一讲：把源代码、文件 bytes、String 和 normalization 放回同一个实际流程](11-End-to-End-Walkthrough.md)
