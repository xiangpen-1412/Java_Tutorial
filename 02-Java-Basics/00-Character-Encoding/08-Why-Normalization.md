# Lecture 08：为什么两个 `é` 看起来相同，`equals()` 却是 false？

[课程目录](00-Reading-Guide.md) · [上一讲：Charset 与文件读写](07-Charset-and-File-IO.md) · [下一讲：NFD 与 NFC](09-NFD-and-NFC.md)

## 本讲只解决一个疑问

你已经知道：Unicode 为字符分配 code point，UTF-8 和 UTF-16 把 code point 表示为不同的 code unit 序列。

现在假设两位用户都输入了法语单词 `café`。屏幕上看起来一样，文件也都正确地用 UTF-8 读取，没有乱码。Java 比较时，却认为它们不是同一个字符串。

**问题发生在编码之前：两个人输入的 Unicode 序列本来就可能不同。** 我们先把这个差异看清楚，下一讲再解决它。

## 1. 先把 `é` 放大，不急着写代码

`é` 可以由一个 code point 表示：

```text
一个 code point：

     [ U+00E9 ]
          │
          ▼
          é
```

`U+00E9` 的名称是 `LATIN SMALL LETTER E WITH ACUTE`。它把字母与上方的 acute accent 一起表示。这类字符叫 **precomposed character**。

但是 Unicode 也允许另一种表示：先给出普通 `e`，再给出一个 **combining mark**。

```text
两个 code point：

     [ U+0065 ] [ U+0301 ]
          │         │
          e      acute accent
          └────┬────┘
               ▼
               é
```

`U+0301` 的名称是 `COMBINING ACUTE ACCENT`。在这个例子中，rendering 系统把它画在前面的 `e` 上方。它不是跟在字母后面的普通撇号 `'`。

为了单独展示 combining mark，资料有时写成 `◌́`。其中虚线圆 `◌` 是展示用的占位符。**实际的 `e + U+0301` 序列里没有那个圆。** 图中的方括号和空格也只是帮助分组，不是文本内容。

因此，两个结果通常看起来相同，但组成方式是：

```text
写法一：一个完整组件       [é]
写法二：基础字母加一个标记 [e] [acute accent]
```

这里的“相同”有 Unicode 的规则支持，叫 **canonical equivalence**。它不等于“凡是字体画得像，就算同一个字符”。[Unicode 对 canonical equivalence 的说明](https://www.unicode.org/reports/tr15/#Canon_Compat_Equivalence)

## 2. 为什么要允许两种表示？

可以先想一个设计问题：如果每种“字母 + 标记”的组合，都必须预先获得一个专用 code point，那么每多一种字母或标记，都可能需要增加很多组合。

combining mark 提供了更灵活的表达方式：先表达基础字母，再表达附加标记。不必为每一种组合都准备一个 precomposed character。

另一方面，Unicode 也包含很多已有的 precomposed characters，并要兼容既有文本和字符编码。它没有要求所有旧文本都改成另一种写法。因此，这两种机制共同存在。

这里不需要把它理解成“Unicode 给同一个 code point 分配了两个数字”。实际情况是：**一个 code point 序列与另一个 code point 序列，可以表示同一个 abstract character。** 一个序列长度是 1，另一个长度是 2。[Unicode Normalization FAQ](https://www.unicode.org/faq/normalization.html)

## 3. 用已经学过的 UTF-8，把差异算出来

先看单个 `U+00E9`：它的编号超出 ASCII 范围，需要 UTF-8 的两字节模板。

```text
U+00E9 的 payload bits，补到 11 位：

00011 101001

放进两字节模板 110xxxxx 10xxxxxx：

11000011 10101001
   C3       A9
```

所以第一种写法的 UTF-8 bytes 是：

```text
[ U+00E9 ] → C3 A9
```

第二种写法要分别编码两个 code point。

`U+0065` 在 ASCII 范围，直接得到一个 byte：`65`。

`U+0301` 也使用 UTF-8 的两字节模板：

```text
U+0301 的 payload bits，补到 11 位：

01100 000001

放进模板：

11001100 10000001
   CC       81
```

连接两个 code point 的编码结果：

```text
[ U+0065 ] [ U+0301 ] → 65 CC 81
```

于是你会得到两组不同的合法 UTF-8 bytes：

| Unicode 序列 | UTF-8 bytes，hex | byte 数 |
|---|---|---:|
| `U+00E9` | `C3 A9` | 2 |
| `U+0065 U+0301` | `65 CC 81` | 3 |

**双方都使用 UTF-8，也不能保证 bytes 一样。** 编码规则相同，但输入的 code point 序列不同。

## 4. 现在才把这两种输入写进 Java

回忆 Lecture 05：源代码里的 `\u` 后面跟四位 hex digits，表示一个 Unicode escape。这里的反斜杠不是下划线，也不是字符串里的实际内容。

```java
String first = "\u00E9";
String second = "e\u0301";
```

逐个读：

- 第一行的 `\u00E9` 表示 `é`；编译后 String 中是 `U+00E9`，没有六个可见的 `\ u 0 0 E 9`。
- 第二行先有普通 `e`，后有 `\u0301` 表示的 combining mark；编译后是两个 code point。
- 这里使用双引号，因为我们要比较两个 String。第二个序列也不能放进一个 Java `char`。

```java
System.out.println(first);                 // é
System.out.println(second);                // 通常显示成相同的 é
System.out.println(first.equals(second));  // false

System.out.println(first.length());        // 1
System.out.println(second.length());       // 2
```

本例中的三个 code point 都在 BMP 内，因此各用一个 UTF-16 code unit：

```text
first 的 UTF-16 code units：  00E9
second 的 UTF-16 code units： 0065 0301
```

Java `String.equals()` 比较字符串内容序列，不会先把字画到屏幕上观察外观，也不会自动做 normalization。因此返回 `false`。

同样，用它们作 `HashMap` 的 key，或放进 `HashSet`，也会保留这个区别。

## 5. Normalization 要做的，是约定共同的代表形式

假设业务上认为两种写法是相同输入，你就需要一个共同的表示约定。例如：

```text
             ┌─ U+00E9 ──────────┐
不同的输入 ──┤                  ├── 统一成 U+00E9
             └─ U+0065 U+0301 ───┘
```

也可以约定统一成分开的形式：

```text
             ┌─ U+00E9 ──────────┐
不同的输入 ──┤                  ├── 统一成 U+0065 U+0301
             └─ U+0065 U+0301 ───┘
```

两种约定都能让这一对输入得到相同的序列。下一讲的 NFC 和 NFD 就分别实现这两种方向所涉及的规则。

注意 normalization 所在的位置：

```text
UTF-8 bytes          Unicode 文本                   新的 UTF-8 bytes
65 CC 81   ─decode→  U+0065 U+0301
                           │ normalize
                           ▼
                       U+00E9            ─encode→   C3 A9
```

中间的 normalize 改变的是 Unicode 序列。左右的 decode、encode 才负责 bytes 与文本之间的表示转换。**换用 UTF-16 不会自动统一这两个 `é`，因为换编码仍然可以忠实地保留原来的不同序列。**

本讲先停在这里：你应该能指出“不同”发生在哪一层，并解释为什么显示相同不保证 `equals()` 相同。接下来再学具体怎样统一。

## 自测

1. 两份文件都采用 UTF-8，屏幕上都显示 `é`，文件 bytes 一定相同吗？
2. `"e\u0301"` 运行时包含反斜杠、字母 `u` 和数字 `0301` 吗？
3. 把两种 `é` 都转成 UTF-16，能否代替 normalization？

<details>
<summary>展开答案</summary>

1. 不一定。`U+00E9` 得到 `C3 A9`；`U+0065 U+0301` 得到 `65 CC 81`。
2. 不包含。Java 先处理 Unicode escape，String 中是 `e` 加一个 combining mark。
3. 不能。UTF-16 仍分别表示原来的 code point 序列，差异不会凭空消失。

</details>

[下一讲：NFD 与 NFC，具体怎样分解、排序和组合](09-NFD-and-NFC.md)
