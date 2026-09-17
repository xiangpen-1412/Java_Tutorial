# Lecture 03：UTF-8 怎样把一个 code point 变成 bytes？

[课程导航](00-Reading-Guide.md) · 上一讲：[读懂编号写法](02-Code-Points-and-Notation.md) · 下一讲：[UTF-16 与 UTF-32](04-UTF-16-and-UTF-32.md)

**预备知识：** `U+4E2D` 是一个用十六进制书写的 Unicode 编号；编号本身与文件中的 bytes 要分开看。

**本讲目标：** 不依赖 Java API，亲手算出 `A`、`é`、`中`、`😀` 的 UTF-8 bytes，并能反向解释其中两个例子。

这一讲的重点是过程，不是背住 `中 = E4 B8 AD`。看完后，换一个合法 code point，你也应该知道怎样按相同规则推导。

**可以分两次读：** 第一次读到第 5 节，自己算通 `中`；第二次再读笑脸、反向 decode 和多个字符的边界。先把一个算例理解透，再增加另一种长度。

## 1. UTF-8 的 8，到底限制了什么？

UTF-8 使用 **8-bit code unit**。在这里，一个 code unit 就是一个 byte。

```text
每一小块的宽度固定：8 bits = 1 byte。

一个 code point 需要的小块数量：可能是 1、2、3 或 4。
```

因此，“UTF-8 的一个 code point 就是 8 bits”这句话不对。

更准确的说法是：**UTF-8 用一个或多个 8-bit 单位，表示一个 code point。**

为什么要这样设计？小编号可以少占空间，大编号也能够表示。同时，读取方必须能从这些 bytes 里认出一个 code point 到哪里结束。

为此，UTF-8 把一个 byte 中的 bits 分成两种用途：

- 一部分是固定的 **prefix**，告诉读取方这个 byte 扮演什么角色。
- 另一部分是 **payload**，承载 code point 数值中的 bits。

下面就看这些位置具体长什么样。

## 2. 最简单的 A：一个 byte 就够

`A` 的 code point 是 `U+0041`，也就是十进制 65。

UTF-8 规定，`U+0000～U+007F` 使用一个 byte，格式是：

```text
0xxxxxxx
│└─────┘
│ 7 个 payload bits
└ 固定 prefix：0
```

这里的 `x` 是待填的位置，不是真正保存的字母 x。

65 的 binary 使用 7 bits 是：

```text
1000001
```

把这 7 bits 放进模板中的 7 个 x：

```text
模板：0 xxxxxxx
填写：0 1000001
结果：01000001
hex： 41
```

所以 UTF-8 保存 `A` 的结果就是一个 byte：`41`。

这也解释了为什么 ASCII 与 UTF-8 在 ASCII 范围内兼容：这些字符使用的 byte 值完全相同。兼容性来自明确的编码规则，不是“所有字符都天然只需要一个 byte”。

## 3. 编号放不进 7 个 payload bits 时，怎样安排？

UTF-8 使用四种模板。每个由空格隔开的区块，恰好是一个 byte：

```text
1 byte：  0xxxxxxx

2 bytes： 110xxxxx 10xxxxxx

3 bytes： 1110xxxx 10xxxxxx 10xxxxxx

4 bytes： 11110xxx 10xxxxxx 10xxxxxx 10xxxxxx
```

先看固定部分的意义：

```text
0...      → 当前 code point 只需要这个 byte。
110...    → 当前 code point 总共需要 2 bytes。
1110...   → 当前 code point 总共需要 3 bytes。
11110...  → 当前 code point 总共需要 4 bytes。

10...     → 这是前面某个起始 byte 的后续 byte。
```

`10` 开头的后续 byte 通常叫 **continuation byte**。

再看 x 的数量：

```text
1 byte：  7 个 payload bits
2 bytes： 5 + 6 = 11 个 payload bits
3 bytes： 4 + 6 + 6 = 16 个 payload bits
4 bytes： 3 + 6 + 6 + 6 = 21 个 payload bits
```

因此，规则并不是简单地“把编号的 binary 每 8 bits 切一块”。**每个 byte 要预留 prefix 的位置，剩下的位置才能装编号。**

用哪个模板，取决于 code point 的数值范围：

| Code point 范围 | Bytes 数量 | Payload bits |
|---|---:|---:|
| `U+0000～U+007F` | 1 | 7 |
| `U+0080～U+07FF` | 2 | 11 |
| `U+0800～U+FFFF`，排除 `U+D800～U+DFFF` | 3 | 16 |
| `U+10000～U+10FFFF` | 4 | 21 |

`U+D800～U+DFFF` 是为 UTF-16 surrogate 机制保留的范围，不能作为独立字符编号进行 UTF-8 encoding。下一讲解释它为什么存在，现在先把它当成需要排除的特殊区域。

接下来每个算例都使用同一套步骤：**确定范围 → 准备 payload bits → 按模板分组 → 加 prefix → 把每个 byte 写成 hex。**

## 4. 两个 bytes 的例子：é → C3 A9

这里使用的 `é` 是 code point `U+00E9`。关于为什么 `é` 还能有另一种组成方式，等 normalization 课程再讲。

### 第一步：选模板

`00E9` 位于 `0080～07FF`，所以需要两个 bytes：

```text
110xxxxx 10xxxxxx
```

两块合计有 11 个 payload 位置。

### 第二步：把编号写成 binary，并在左边补足 11 bits

```text
hex： E    9
bits：1110 1001

补足 11 bits：00011101001
```

注意，只在最左边补零。这个数字仍然是 `E9`，即十进制 233。

### 第三步：按 5 + 6 切开

```text
payload：00011 | 101001
          5 bits  6 bits
```

### 第四步：加上固定 prefix

```text
第一个 byte：110 + 00011  = 11000011
第二个 byte：10  + 101001 = 10101001
```

### 第五步：每个 byte 写成 hex

```text
1100 0011 → C3
1010 1001 → A9
```

最终得到：

```text
é
Unicode code point：U+00E9
UTF-8 bytes：       C3 A9
```

原编号是 `E9`，编码后不是简单保存单个 byte `E9`。这两个结果属于不同层面。

## 5. 三个 bytes 的例子：中 → E4 B8 AD

### 第一步：选模板

`中` 是 `U+4E2D`，位于三 byte 范围内：

```text
1110xxxx 10xxxxxx 10xxxxxx
```

这个模板总共提供 16 个 payload bits。

### 第二步：展开编号

```text
hex： 4    E    2    D
bits：0100 1110 0010 1101
```

这次已经写成 16 bits。

### 第三步：重新按 4 + 6 + 6 分组

请注意，上一行每四位分组是为了对应 hex 数字。现在要按 UTF-8 模板重新分组，分组边界发生变化：

```text
为阅读 hex 分组：0100 1110 0010 1101

去掉空格：      0100111000101101

按 UTF-8 分组： 0100 | 111000 | 101101
                4 bits 6 bits   6 bits
```

bits 的顺序没有变化，也没有丢失；变的只是我们在哪里画分隔线。

### 第四步：分别加 prefix

```text
第一个 byte：1110 + 0100   = 11100100
第二个 byte：10   + 111000 = 10111000
第三个 byte：10   + 101101 = 10101101
```

### 第五步：转成方便阅读的 hex

```text
1110 0100 → E4
1011 1000 → B8
1010 1101 → AD
```

完整过程：

```text
中
 ↓ Unicode 分配的编号
U+4E2D
 ↓ 同一个编号的 binary 写法
0100 1110 0010 1101
 ↓ 依模板切成 4 + 6 + 6
0100 | 111000 | 101101
 ↓ 插入 UTF-8 prefix
11100100 10111000 10101101
 ↓ 每个 byte 用 hex 展示
E4 B8 AD
```

中间真正做 encoding 的关键步骤，是**按模板分配 payload 并加入 prefix**。最后写成 hex，只是为了让人读起来短一点。

## 6. 四个 bytes 的例子：😀 → F0 9F 98 80

### 第一步：选模板

`😀` 是 `U+1F600`，位于 `U+10000～U+10FFFF`，使用：

```text
11110xxx 10xxxxxx 10xxxxxx 10xxxxxx
```

它有 21 个 payload 位置。

### 第二步：把编号补足到 21 bits

从 hex 展开：

```text
1    F    6    0    0
0001 1111 0110 0000 0000
```

上面是 20 bits，再在最左边补一个零：

```text
21 bits：000011111011000000000
```

### 第三步：按 3 + 6 + 6 + 6 分组

```text
000 | 011111 | 011000 | 000000
 3      6        6        6
```

### 第四步：加入 prefix

```text
第一个 byte：11110 + 000    = 11110000
第二个 byte：10    + 011111 = 10011111
第三个 byte：10    + 011000 = 10011000
第四个 byte：10    + 000000 = 10000000
```

### 第五步：写成 hex

```text
1111 0000 → F0
1001 1111 → 9F
1001 1000 → 98
1000 0000 → 80
```

最终：

```text
😀
Unicode code point：U+1F600
UTF-8 bytes：       F0 9F 98 80
```

它的编号只需 17 个有效 bits，模板提供 21 个 payload 位置，完整编码占 32 bits，也就是 4 bytes。三个数字不矛盾，各自在数不同的东西。

## 7. Decode：怎样把 E4 B8 AD 还原成中？

encode 的过程可逆。现在假设我们已经知道文件使用 UTF-8。

### 第一步：把 bytes 展开，识别结构

```text
E4       B8       AD
11100100 10111000 10101101
```

第一个 byte 以 `1110` 开头，所以这个 code point 总共使用三个 bytes。后面两个都以 `10` 开头，符合 continuation byte 的结构。

### 第二步：去掉 prefix，留下 payload

```text
1110 [0100]   10 [111000]   10 [101101]
        │           │            │
        └───────────┴────────────┘
                   拼接

0100 111000 101101
```

### 第三步：每四位分组，读成 hex

```text
拼接后：0100111000101101

四位组：0100 1110 0010 1101
hex：     4    E    2    D
```

所以恢复出的 code point 是 `U+4E2D`，Unicode 把这个编号分配给了 `中`。

这里的最后一步是根据 code point 识别字符；字符怎样画到屏幕上，还需要字体和文本显示系统，不是 UTF-8 算法负责画字形。

## 8. 再反向做一次：F0 9F 98 80 → 😀

```text
F0       9F       98       80
11110000 10011111 10011000 10000000
```

`11110` 表示共四个 bytes；接下来三个必须是 continuation bytes。

去掉 prefix：

```text
11110 [000]  10 [011111]  10 [011000]  10 [000000]

payload 拼接：000011111011000000000
```

去掉不影响数值的最前面零，再按 hex 对齐：

```text
0001 1111 0110 0000 0000
  1    F    6    0    0
```

得到 `U+1F600`，即 `😀`。

## 9. 一串文本放在一起，为什么不需要额外空格来分隔？

假设文本是：

```text
A中😀
```

UTF-8 bytes 连在一起是：

```text
41 E4 B8 AD F0 9F 98 80
```

读法是：

```text
41：开头 0       → 读 1 byte。
E4：开头 1110    → 连同后面读 3 bytes。
F0：开头 11110   → 连同后面读 4 bytes。
```

所以边界可以由 UTF-8 自身的结构识别：

```text
[41] [E4 B8 AD] [F0 9F 98 80]
 A       中          😀
```

示例中的空格和方括号只是讲义为了标出边界添加的，文件不需要为了这种分隔额外保存它们。

## 10. 模板能填进去，不代表一定是合法 UTF-8

这一点只需记三个边界，不必现在实现完整 decoder：

1. **必须使用该数值对应的最短模板。** `A` 的 65 已能用一个 byte 表示，不能人为用两个 bytes 包装。那属于非法的 overlong encoding。
2. **`U+D800～U+DFFF` 不能作为独立 code point 编码。** 它们是下一讲的 surrogate 保留范围。
3. **不能超过 `U+10FFFF`。** 四 byte 模板虽然提供 21 个 payload bits，也不能把所有 21-bit 整数都当成合法 Unicode 值。

实际 decoder 还会检查 continuation bytes 是否缺失、prefix 是否正确等情况。处理输入错误的 Java API 放到文件 I/O 课程。

## 自测

1. 为什么 `中` 的 UTF-8 不能直接写成 `4E 2D`？
2. 编码后的 `E4 B8 AD` 有 24 bits，为什么去掉 prefix 后只剩 16 个 payload bits？
3. 请把 `U+00A9`，也就是 `©`，按本讲步骤编码成 UTF-8。提示：它使用两个 bytes。

<details>
<summary>展开答案</summary>

1. `4E2D` 是 code point 的 hex 写法。UTF-8 要按三 byte 模板切分 payload 并加入 prefix，结果才是 `E4 B8 AD`。直接保存 `4E 2D` 是另一种布局，不符合这个字符的 UTF-8 表示。
2. 第一个 byte 使用 4 个 prefix bits，后面两个各使用 2 个，共 `4 + 2 + 2 = 8` 个结构 bits。`24 - 8 = 16`。
3. `A9` 的 binary 是 `10101001`，补足 11 bits 为 `00010101001`。切成 `00010 | 101001`，加 prefix 得 `11000010 10101001`，也就是 `C2 A9`。

</details>

## 核对依据

- [RFC 3629，第 3、4 节](https://www.rfc-editor.org/rfc/rfc3629.txt)：UTF-8 模板、编码和解码步骤、合法范围。
- [Unicode：UTF-8 FAQ](https://www.unicode.org/faq/utf_bom.html#utf8)：UTF-8 与其他 UTF 形式的关系及非法 surrogate 表示。

[下一讲：同一个笑脸，UTF-16 为什么写成 D83D DE00？](04-UTF-16-and-UTF-32.md)
