# Lecture 04：UTF-16 为什么会把笑脸拆成两部分？

[课程导航](00-Reading-Guide.md) · 上一讲：[逐步手算 UTF-8](03-UTF-8-Step-by-Step.md) · 下一讲：[Java 中的字符写法与 escapes](05-Java-Literals-and-Escapes.md)

**预备知识：** code point 是统一编号；UTF-8 会把编号的 bits 放入带 prefix 的 byte 模板。

**本讲目标：** 推导 UTF-16 的 surrogate pair，理解 byte order，并知道 UTF-32 如何表示同样的编号。

UTF-16 不会给 `😀` 重新分配一个 Unicode 编号。它的 code point 仍然是 `U+1F600`。换掉的是“怎样表示这个编号”的规则。

**可以分两次读：** 第一次到第 6 节为止，算通 surrogate pair 的正反转换；第二次再看这些 code units 怎样排列成文件 bytes，以及 UTF-32 的区别。

## 1. 从 16-bit 单位开始，最初看起来更直接

UTF-16 使用 **16-bit code unit**：

```text
1 个 UTF-16 code unit = 16 bits = 2 bytes
```

一个 16-bit 单位可以表达的数值范围是：

```text
hex：0000～FFFF
decimal：0～65535
```

Unicode 中 `U+0000～U+FFFF` 这个范围叫 **Basic Multilingual Plane，BMP**。先记它是一个编号范围即可。

对于这个范围中可以直接编码的 code point，UTF-16 使用一个 16-bit 单位，数值与 code point 相同。这里要排除 `U+D800～U+DFFF` 这一段特殊保留范围，后面马上解释。

例如 `A`：

```text
code point：U+0041

UTF-16 code unit：0041

完整 16 bits：0000 0000 0100 0001
```

再看 `中`：

```text
code point：U+4E2D

UTF-16 code unit：4E2D

完整 16 bits：0100 1110 0010 1101
```

这里没有像 UTF-8 那样添加三 byte 模板的 prefix。UTF-16 在这个范围使用的是直接对应规则。

暂时先把结果写成一个 16-bit 数 `4E2D`，不要急着决定两个 bytes 的排列顺序；那是本讲后半部分的 byte order 问题。

## 2. 问题来了：😀 的编号放不进一个 16-bit 单位

`😀` 的 code point 是 `U+1F600`，十进制 128512。

```text
一个 16-bit 单位的最大值：65535

😀 的编号：128512
```

因此，一个 16-bit 单位装不下它。

Unicode 不只包含 BMP，也包含 `U+10000～U+10FFFF` 的 code points，它们叫 **supplementary code points**。

UTF-16 为这个范围使用两个 16-bit 单位。两者合起来叫 **surrogate pair**。

```text
一个 supplementary code point
       ↓ UTF-16
[一个 16-bit 单位] [另一个 16-bit 单位]
       high                 low
```

所以 UTF-16 的一个 code point 可能占 2 bytes，也可能占 4 bytes。

## 3. 为什么不能随便切成两个 16-bit 数？

假设随便把大数字切成两份，读取方会遇到一个问题：

> 眼前两个 16-bit 单位，究竟是两个独立字符，还是同一个字符的两半？

UTF-16 解决这个问题的办法，是专门保留两个数值区域：

```text
high surrogate：D800～DBFF
low surrogate： DC00～DFFF
```

它们合起来覆盖 `D800～DFFF`。

这些区域的值不用于直接表示独立字符，而是作为两部分的标识。解码器看到 high surrogate，就知道还必须接一个 low surrogate，才能还原一个 supplementary code point。

从 binary 看，这两个范围分别具有固定的前 6 bits：

```text
high surrogate：110110 xxxxxxxxxx
low surrogate： 110111 xxxxxxxxxx
                固定6位  剩下10位
```

每个单位留下 10 个 payload bits，两部分总共能携带 20 bits。

这与上一讲 UTF-8 的 prefix 思路有相通之处：格式必须能区分“我是什么角色”和“我携带什么数据”。具体规则则是不同的。

## 4. 为什么先减去 0x10000？

两个 surrogate 的 20 个 payload bits，可以表示：

```text
00000～FFFFF
```

这是十六进制范围，共有 `2^20` 种取值。

需要它们表示的 supplementary 范围却是：

```text
10000～10FFFF
```

两者大小相同，但起点不同。只要统一减去 `10000`，范围就正好对齐：

```text
原范围起点：10000 - 10000 = 00000
原范围终点：10FFFF - 10000 = FFFFF
```

因此，UTF-16 存入两部分的不是原始 code point 直接切片，而是：

```text
code point - 0x10000
```

这个“先平移到从 0 开始的范围”是 surrogate 计算里最容易漏掉的一步。

## 5. 完整推导：😀 → D83D DE00

### 第一步：得到原始 code point

```text
😀 → U+1F600
```

`1F600` 是十六进制数字，这个约定来自 Unicode 的字符分配，不是 UTF-16 临时决定的。

### 第二步：减去 0x10000

以下运算中的数均为十六进制：

```text
1F600 - 10000 = F600
```

### 第三步：把结果写成 20 bits

`F600` 展开是 16 bits，左边补零到 20 bits：

```text
hex： 0    F    6    0    0
bits：0000 1111 0110 0000 0000

连起来：00001111011000000000
```

### 第四步：从中间切成 10 + 10

```text
0000111101 | 1000000000
 高 10 bits    低 10 bits
```

两部分的数值分别是：

```text
高部分：0000111101 → hex 003D → decimal 61
低部分：1000000000 → hex 0200 → decimal 512
```

`003D` 和 `0200` 仍然只是 payload 的数值，不是最终 surrogate。

### 第五步：把两部分放进各自的保留范围

给高部分加上 high surrogate 的起点 `D800`：

```text
D800 + 003D = D83D
```

给低部分加上 low surrogate 的起点 `DC00`：

```text
DC00 + 0200 = DE00
```

为什么加起点可以理解为加入 prefix？因为两个起点的低 10 bits 全为零，payload 又只占低 10 bits：

```text
high：110110 + 0000111101 = 1101100000111101
                            1101 1000 0011 1101
                              D    8    3    D

low： 110111 + 1000000000 = 1101111000000000
                            1101 1110 0000 0000
                              D    E    0    0
```

上面 binary 行中的 `+` 表示把固定 prefix 与 payload 拼接；前面的 hex 行则是正常数值加法。这两种描述在这里得到相同结果。

最后得到：

```text
😀
code point：       U+1F600
UTF-16 code units：D83D DE00
```

**不是 😀 变成了两个独立的 Unicode 字符，也不是它获得两个新的字符编号。两个 code units 合起来表示原来的一个 code point。**

## 6. 反向验证：D83D DE00 怎样恢复成 U+1F600？

第一步，识别这是一组合法角色：

```text
D83D 在 D800～DBFF → high surrogate
DE00 在 DC00～DFFF → low surrogate
```

第二步，减掉各自的起点，取回 payload：

```text
D83D - D800 = 003D
DE00 - DC00 = 0200
```

第三步，把两个 10-bit 数重新拼成 20-bit 数：

```text
0000111101 | 1000000000

拼接后：0000 1111 0110 0000 0000
hex：   0    F    6    0    0
```

这得到 `F600`。

最后，补回编码时减掉的 `10000`：

```text
F600 + 10000 = 1F600
```

于是恢复 `U+1F600`，即 `😀`。

把上面的过程写成公式是：

```text
codePoint = 0x10000
          + (high - 0xD800) × 0x400
          + (low - 0xDC00)
```

`0x400` 等于 1024，也就是 `2^10`。乘它相当于给高部分右边留出 10 个 binary 位置，再放低部分。

公式只是对刚才拼接过程的简写；理解图示以后，不必把公式当成孤立结论硬背。

如果只出现一半 surrogate，或者 high 后面不是 low，就不能按一个合法 surrogate pair 还原。这属于不完整或不合法的 UTF-16 序列。Java 为什么仍可能构造这样的 String，会在 `char` 与 `String` 一讲解释。

## 7. 得到了 16-bit code units，怎样真正写成文件 bytes？

到这里，`中` 的 UTF-16 单位已经确定为 `4E2D`。

但一个单位占两个 bytes：

```text
4E  和  2D
```

要把它写进文件，必须再约定哪个 byte 在前。这叫 **byte order**。

### Big-endian：高位 byte 在前

```text
16-bit 单位：4E2D

UTF-16BE bytes：4E 2D
```

BE 是 big-endian。

### Little-endian：低位 byte 在前

```text
16-bit 单位：4E2D

UTF-16LE bytes：2D 4E
```

LE 是 little-endian。

两者表示的单位都是 `4E2D`，也就表示同一个 `中`。如果写入方用 LE、读取方却按 BE 组装，就可能把它还原成另一个数值。

### 对 surrogate pair，也是在每个 16-bit 单位内部决定顺序

```text
😀 的两个 UTF-16 单位：D83D DE00

UTF-16BE：D8 3D DE 00
UTF-16LE：3D D8 00 DE
```

注意 LE 并不是把整个字符串或整组 bytes 全部倒过来。high surrogate 仍然在 low surrogate 前面，只是每个 16-bit 单位里的两个 bytes 顺序交换。

作为对照，UTF-8 的基本单位就是一个 byte，没有同一个 code unit 内部的 byte 顺序问题，也不存在 UTF-8BE 与 UTF-8LE 这两套标准形式。

## 8. BOM 是干什么的？

文件或协议可以在外部明确声明“这些 bytes 是 UTF-16LE”。也有 UTF-16 文本在开头放一个标记，让读取方识别 byte order。

这个标记叫 **BOM，Byte Order Mark**。

用于 BOM 的值是 `FEFF`。把它按两种顺序保存，就得到：

```text
BE 的标记 bytes：FE FF
LE 的标记 bytes：FF FE
```

一个“带 BOM、内容为 A”的 UTF-16 文件可以是：

```text
BE：FE FF | 00 41
    标记     A

LE：FF FE | 41 00
    标记     A
```

本讲所有没有特别标注 BOM 的编码算例，都只计算内容本身，不额外包含这个标记。

不是所有 UTF-16 数据都必须带 BOM；文件格式或协议也可以直接规定 byte order。Java 的 `UTF_16`、`UTF_16BE`、`UTF_16LE` 如何处理它，放在 [Lecture 07](07-Charset-and-File-IO.md)。

UTF-8 里也可能遇到 bytes `EF BB BF` 作为 BOM/signature。但 UTF-8 没有 BE/LE 之分，所以它在那里的作用是提示编码，而不是选择 byte order。

## 9. UTF-32：把有效编号直接放进一个 32-bit 单位

理解 UTF-16 的两个单位以后，UTF-32 就很容易比较了。

```text
1 个 UTF-32 code unit = 32 bits = 4 bytes
```

它给每个可独立编码的 Unicode code point 一个 32-bit 单位，单位数值就是 code point 的数值。

以 `😀` 为例：

```text
code point：U+1F600

补足到 8 位 hex：0001F600
完整 32 bits：   00000000 00000001 11110110 00000000
```

于是：

```text
UTF-32BE：00 01 F6 00
UTF-32LE：00 F6 01 00
```

这里不会先减 `10000`，也不会产生 surrogate pair。这些是 UTF-16 的规则，不是所有 UTF 编码都要经过的步骤。

`A` 的 UTF-32 表示也占 4 bytes：

```text
UTF-32BE：00 00 00 41
UTF-32LE：41 00 00 00
```

UTF-32 比较直观，但对于很多小编号，会使用更多空间。还要注意：单位虽有 32 bits，也不代表全部 32-bit 整数都能当作 Unicode。值仍不能超过 `10FFFF`，也不能把 surrogate 范围 `D800～DFFF` 当成独立值编码。

## 10. 现在再比较三套编码，不需要死记大表

我们已经亲手推导了每一种，可以把结果放在一起检查。以下 BE 形式均不含 BOM：

| 字符 | Code point | UTF-8 bytes | UTF-16BE bytes | UTF-32BE bytes |
|---|---|---|---|---|
| `A` | `U+0041` | `41` | `00 41` | `00 00 00 41` |
| `é` | `U+00E9` | `C3 A9` | `00 E9` | `00 00 00 E9` |
| `中` | `U+4E2D` | `E4 B8 AD` | `4E 2D` | `00 00 4E 2D` |
| `😀` | `U+1F600` | `F0 9F 98 80` | `D8 3D DE 00` | `00 01 F6 00` |

同一行的 code point 没有改变，改变的是表示方式。

还有一个边界先留在心里：**一个 code point，不保证等于用户眼睛看到的一个完整文字单位。** 后面 `String` 和 normalization 会用带重音的字母、组合 emoji 展开这个问题。

因此，UTF-32 的“一 code point 一单位”也不能自动解决所有“用户看到几个字”的问题。

## 自测

1. `😀` 在 UTF-16 中是两个 code units，这是否表示它有两个 code points？
2. 已知 `中` 的 UTF-16 单位是 `4E2D`，它的 UTF-16LE bytes 是什么？
3. `D83D DE00` 是 `😀` 的 UTF-16 单位序列。要转成标准 UTF-8，应该先还原到哪个数值？可以把两部分各自当独立 code point 编码吗？

<details>
<summary>展开答案</summary>

1. 不是。这个具体例子的 `😀` 只有一个 code point `U+1F600`，UTF-16 使用两个 code units 表示它。
2. `2D 4E`。一个 16-bit 单位内的低位 byte 在前。
3. 先把 surrogate pair 还原成 `U+1F600`，再按 UTF-8 编码，得到 `F0 9F 98 80`。不能把两部分作为独立 code points 编码；`D800～DFFF` 不能那样用于标准 UTF-8。

</details>

## 核对依据

- [RFC 2781，第 2、3 节](https://www.rfc-editor.org/rfc/rfc2781.txt)：UTF-16 编码、解码、surrogate 运算和 byte order。
- [Unicode：UTF-16、UTF-32 与 BOM FAQ](https://www.unicode.org/faq/utf_bom.html)：code unit 大小、surrogate 范围与 UTF-32 表示。

[下一讲：Java 里的 'A'、"A"、\u0041 分别是什么写法？](05-Java-Literals-and-Escapes.md)
