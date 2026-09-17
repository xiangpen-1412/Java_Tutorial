# Lecture 09：NFD 与 NFC，怎样一步步统一两个 `é`

[课程目录](00-Reading-Guide.md) · [上一讲：为什么需要 normalization](08-Why-Normalization.md) · [下一讲：NFKD 与 NFKC](10-NFKD-and-NFKC.md)

## 从上一讲未解决的问题继续

我们已经确认以下两个序列 canonically equivalent，但 Java 直接比较时不相等：

```text
输入 A：[ U+00E9 ]          → é
输入 B：[ U+0065 U+0301 ]   → e + combining acute accent
```

现在需要建立规则，让它们都走到同一个结果。先理解转换过程，最后再把规则传给 Java。

## 1. NFD：选择分开的表示

`NF` 是 **Normalization Form**，`D` 是 **Decomposition**。

NFD 按 Unicode 定义的 **canonical decomposition** 规则展开序列。以 `é` 为例，Unicode 数据中已经规定：

```text
U+00E9 → U+0065 U+0301
```

因此，它不需要识别图片，也不需要猜测“这个字看起来像什么”。它读取字符对应的规则。

这张映射不是从 `00E9` 的二进制位计算出来的。与 UTF-8 填入 bit 模板不同，normalization 使用 Unicode 定义的字符关系数据。

对两个输入分别处理：

```text
输入 A：U+00E9
        ↓ 按 canonical decomposition 展开
结果：  U+0065 U+0301

输入 B：U+0065 U+0301
        ↓ 已经展开，无须再拆
结果：  U+0065 U+0301
```

两个结果相同了。**mark 还在，重音没有丢失。** NFD 选择了分开的表示，不是把 `é` 变成无重音的 `e`。

### 为什么说是 fully decompose，而不只是拆一层？

有些映射的结果中还包含能继续分解的字符。例如 `Ǻ`，即带 ring above 和 acute 的大写 A：

```text
U+01FA  Ǻ
    ↓ 第一次分解
U+00C5 U+0301
   Å     acute
    ↓ Å 还可以分解
U+0041 U+030A U+0301
   A     ring    acute
```

NFD 会继续展开，直到不再有适用的 canonical decomposition。这就是 **recursive decomposition**。

这些关系是 Unicode 数据里的明确映射；并不是任意字母都可以拆开。[Unicode Character Database](https://www.unicode.org/Public/17.0.0/ucd/UnicodeData.txt)

## 2. NFD 还负责 canonical ordering

只做分解仍可能留下另一种差异：几个 combining marks 的先后次序不同。

考虑“`e`，上方有 acute，下方有 dot”的序列：

```text
输入：U+0065 U+0301 U+0323
         e     acute   dot below
```

Unicode 给这些 mark 定义了 **Canonical Combining Class**，简称 **CCC**：

| Code point | 含义 | CCC |
|---|---|---:|
| `U+0065` | `e` | 0 |
| `U+0301` | combining acute accent | 230 |
| `U+0323` | combining dot below | 220 |

注意 CCC 是另一个属性值，既不是 code point 编号，也不是 UTF-8 byte。

在这个例子中，canonical ordering 按 CCC 把两个非零 class 的 mark 排好：

```text
原序列：e   acute   dot below
CCC：   0    230      220

NFD：   e   dot below   acute
CCC：   0      220       230

结果：  U+0065 U+0323 U+0301
```

`e` 没有被移动，两个 mark 也都还在。统一的是可交换标记的次序。

这里不能理解成“把整个字符串按数字大小排序”。它按 canonical ordering 规则处理局部序列；CCC 为 0 的字符是边界，相同 CCC 的 mark 保留相对次序。不是所有 mark 都能随便交换。

因此，NFD 的完整主线是：

```text
按 canonical decomposition 递归展开
                 ↓
按 canonical ordering 整理 mark 次序
                 ↓
得到 NFD
```

## 3. NFC：先统一展开，再按规则组合

`C` 是 **Composition**。NFC 在逻辑上先完成 NFD 的过程，再做 **canonical composition**。

为什么不直接“找到两个零件就粘起来”？因为输入可能有多种分解层次和 mark 次序，先统一展开与排序，才能让不同输入沿着同一套规则到达同一结果。

对输入 A：

```text
U+00E9
    ↓ canonical decomposition
U+0065 U+0301
    ↓ canonical ordering：本例无须调整
U+0065 U+0301
    ↓ canonical composition
U+00E9
```

对输入 B：

```text
U+0065 U+0301
    ↓ 已经分解且顺序符合规则
U+0065 U+0301
    ↓ canonical composition
U+00E9
```

第一条看起来“拆了又装回去”，这是规则的逻辑描述。实现可以直接识别已经符合 NFC 的文本，不必真的分配每个中间结果。

还记得上一节的两个 mark 吗？它们的 NFC 结果是：

```text
U+0065 U+0301 U+0323
    ↓ 分解并排序
U+0065 U+0323 U+0301
    ↓ e 与 dot below 可以按规则组合
U+1EB9 U+0301
   ẹ     acute
```

两个 mark 没有消失：dot below 成了 `U+1EB9` 的一部分，acute 仍然是独立 code point。

这套组合算法还包含 **blocking** 与 **composition exclusion** 规则。当前只需要知道：组合必须得到规则允许的结果；并非“只要有一个对应的 precomposed character，就一定可以随时合并”。[Unicode 规范化过程与组合规则](https://www.unicode.org/reports/tr15/#Description_Norm)

## 4. NFC 不保证一个看得见的字只剩一个 code point

看一个更简单的例子：

```text
q + acute accent
U+0071 U+0301
```

对这个组合没有适用的单个 precomposed code point，因此 NFC 后仍是：

```text
U+0071 U+0301
```

它可以显示成一个带重音的 `q`，但保留两个 code point，也保留两个 Java `char`。

所以 NFC 的承诺是“按 canonical composition 规则统一表示”，不是“把每个字素簇压成一个 code point”。

## 5. 理解规则后，再读 Java 的 `normalize()`

首先导入类：

```java
import java.text.Normalizer;
```

我们使用的方法声明是：

```java
public static String normalize(CharSequence src, Normalizer.Form form)
```

先只读这四个信息：

- `static`：通过 `Normalizer.normalize(...)` 调用，不需要 `new Normalizer()`。
- `src`：要处理的文本。`String` 实现了 `CharSequence`，因此可以直接传入。
- `form`：指定统一成哪种形式，这一讲选择 `NFD` 或 `NFC`。
- 返回值 `String`：处理后的文本。原 String 不会被修改。

现在把刚才画的箭头写成调用：

```java
String original = "e\u0301";
String composed = Normalizer.normalize(original, Normalizer.Form.NFC);

System.out.println(original.length()); // 2：原来的字符串没变
System.out.println(composed.length()); // 1：NFC 的结果
System.out.println(composed.equals("\u00E9")); // true
```

如果只写一行 `Normalizer.normalize(original, Normalizer.Form.NFC);`，却不接返回值，那么之后继续使用的 `original` 仍然是原来的序列。

需要比较两份来源不同的文本时，按同一个 form 处理双方：

```java
String left = "\u00E9";
String right = "e\u0301";

String normalizedLeft = Normalizer.normalize(left, Normalizer.Form.NFC);
String normalizedRight = Normalizer.normalize(right, Normalizer.Form.NFC);

System.out.println(normalizedLeft.equals(normalizedRight)); // true
```

改成双方都用 NFD，本例也会得到 `true`。不要一边选 NFC，一边选 NFD，再指望它们的底层序列相同。

## 6. `isNormalized()` 是检查，不是修改

对应声明：

```java
public static boolean isNormalized(CharSequence src, Normalizer.Form form)
```

参数含义相同，但它只回答：“这个文本现在是否已经符合这个 form？”

```java
String text = "\u00E9";

System.out.println(Normalizer.isNormalized(text, Normalizer.Form.NFC)); // true
System.out.println(Normalizer.isNormalized(text, Normalizer.Form.NFD)); // false
```

它不会为了回答问题而修改 `text`。通常可以直接调用 `normalize()`；不必为了正确性先检查一次再决定是否处理。[Java 17 Normalizer API](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/text/Normalizer.html)

对同一种 form 重复 normalize，内容不会继续变化，这叫 **idempotence**：

```text
某段文本 → NFC → 结果 X → 再做 NFC → 仍然是 X
```

但这不意味着以后拼接、插入文本也永远保持 NFC。例如 `"e"` 和单独的 `"\u0301"` 各自符合 NFC，拼起来以后还可以合成 `é`。需要保证最终文本符合 NFC 时，应考虑最终完整文本。

## 自测

1. 对 `é` 做 NFD，会得到 `e`，还是 `e + acute accent`？
2. NFD 对 `e + acute + dot below` 为什么会交换两个 mark？它在按 code point 大小排序吗？
3. 对 `q + acute` 做 NFC，为什么 `length()` 仍可能是 2？

<details>
<summary>展开答案</summary>

1. 得到 `e + acute accent`。分解没有删除 mark。
2. 两个 mark 的 CCC 分别是 230 和 220，canonical ordering 将它们整理为 220、230。使用的是 CCC 属性与局部规则，不是把 code point 从小到大排序。
3. 没有适用的 canonical composition 把这对 code point 合成一个。NFC 不承诺一个可见文字单位只用一个 code point。

</details>

[下一讲：为什么全角 `Ａ`、圈号 `①` 和连字 `ﬁ` 又需要另外两种 form](10-NFKD-and-NFKC.md)
