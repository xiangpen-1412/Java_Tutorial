# Java Stream 转 int[] 的关键知识

## 1. 普通 Stream 不能直接变成 int[]

在 Java 里，如果你有一个普通对象流：

```java
Stream<Integer>
```

它里面装的是 `Integer` 对象，不是 primitive `int`。

所以如果你直接：

```java
stream.toArray()
```

得到的通常是：

```java
Object[]
```

不是：

```java
int[]
```

这就是为什么有时候不能直接把普通 Stream 转成 `int[]`。

---

## 2. map 和 mapToInt 的区别

### map

```java
.map(x -> x)
```

返回的是普通对象流：

```java
Stream<Integer>
```

它处理的是包装类型 `Integer`。

### mapToInt

```java
.mapToInt(x -> x)
```

返回的是：

```java
IntStream
```

它处理的是 primitive `int`。

所以：

```java
.mapToInt(...)
.toArray()
```

最后可以直接得到：

```java
int[]
```

---

## 3. 为什么需要 mapToInt

如果当前流里的元素是对象，比如：

```java
Stream<Map.Entry<Integer, Integer>>
```

你想取出每个 entry 的 key，并返回 `int[]`。

这时不能只停留在对象流里。

你需要把：

```text
Map.Entry<Integer, Integer>
```

转换成：

```text
int
```

所以要用：

```java
.mapToInt(Map.Entry::getKey)
```

它的作用是：

```text
把每个对象元素转换成 primitive int
```

然后：

```java
.toArray()
```

才能得到：

```java
int[]
```

---

## 4. 数据形态变化

这类代码真正要看的是数据形态变化：

```text
Stream<Map.Entry<Integer, Integer>>
↓
mapToInt(Map.Entry::getKey)
↓
IntStream
↓
toArray()
↓
int[]
```

如果用普通 `map`，变化是：

```text
Stream<Map.Entry<Integer, Integer>>
↓
map(Map.Entry::getKey)
↓
Stream<Integer>
```

它还是对象流，不是 `IntStream`。

---

## 5. 一句话总结

当最终目标是：

```java
int[]
```

而当前数据还在普通对象流里时，就需要：

```java
mapToInt(...)
```

因为它会把对象流转换成 `IntStream`，这样 `toArray()` 才能生成真正的 primitive `int[]`。