# LeetCode 49：Group Anagrams 背后的知识

## 1. 题目核心

这道题给你一个字符串数组，让你把字母组成相同的字符串分到同一组。

比如：

```text
eat, tea, ate
```

它们的字母都是：

```text
a, e, t
```

只是顺序不同，所以它们属于同一组。

---

## 2. 这道题真正考什么

这道题不是单纯考字符串比较。

它真正考的是：

> 怎么给每个字符串找到一个统一的分类标准，然后按照这个标准分组。

比如：

```text
eat -> aet
tea -> aet
ate -> aet
```

排序之后，它们都变成同一个 key：`aet`。

所以它们应该放到同一组。

---

## 3. 为什么要用排序

字符串本身顺序不同，不方便直接比较。

比如：

```text
eat
tea
ate
```

直接比较，它们都不相等。

但是把每个字符串内部字符排序后：

```text
eat -> aet
tea -> aet
ate -> aet
```

它们就有了相同的标准形式。

这个排序后的字符串就可以作为 HashMap 的 key。

---

## 4. 为什么要用 HashMap

这道题的关键词是：

```text
group
分组
```

分组问题通常可以想：

```text
Map<分类标准, 属于这一类的所有元素>
```

在这道题里：

```text
key：排序后的字符串
value：原始字符串列表
```

比如：

```text
"aet" -> ["eat", "tea", "ate"]
"ant" -> ["tan", "nat"]
"abt" -> ["bat"]
```

所以这道题的本质是：

> 用排序后的字符串作为分类 key，把原始字符串放进对应的 List。

---

## 5. 容易犯的错误

### 1. 不要用 char[].toString()

`char[]` 调用 `.toString()` 得到的不是字符内容，而是类似：

```text
[C@3e3abc88
```

正确写法是：

```java
String key = new String(chars);
```

---

### 2. 排序后的字符串只是 key

排序后的字符串只是用来分组的，不是最终答案。

最终答案里要保存的是原始字符串：

```text
"eat", "tea", "ate"
```

不是：

```text
"aet", "aet", "aet"
```

---

### 3. 不需要两两比较

暴力思路可能是：

```text
eat 和 tea 比
eat 和 ate 比
tea 和 tan 比
```

这样会很麻烦。

更好的思路是：

```text
每个字符串生成 key
相同 key 的自动进入同一组
```

---

## 6. 这道题背后要学什么

这道题主要训练：

```text
1. 字符串转 char[]
2. char[] 排序
3. new String(chars) 生成 key
4. HashMap 分组
5. 理解 key 和 value 的关系
6. 从“两两比较”升级到“按 key 分类”
```

---

## 一句话总结

LeetCode 49 真正要学的是：

> 遇到分组问题时，不要只想着两两比较，而是先找到每个元素的分类 key，再用 HashMap 把相同 key 的元素放到同一组。