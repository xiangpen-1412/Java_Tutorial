# Java Stream 总结

## 什么时候用 variable.stream()

当变量本身是 Collection 时：

- List
    
- Set
    
- Queue
    
- Collection
    

这些类自己有 `.stream()` 方法。

```java
List<String> list = List.of("a", "b", "c");

list.stream()
    .forEach(System.out::println);
```

---

## 什么时候用 Class.stream(variable)

当变量本身没有 `.stream()` 方法时，需要工具类帮你创建 Stream。

最常见：

```java
Arrays.stream(array)
```

因为数组不是 Collection。

```java
int[] nums = {1, 2, 3};

Arrays.stream(nums)
      .forEach(System.out::println);
```

---

# 常见规则

|类型|写法|
|---|---|
|List / Set|variable.stream()|
|数组|Arrays.stream(array)|
|String|string.chars()|
|普通对象|不能直接 stream|

---

# map() 和 mapToObj()

## map()

用于：

```java
对象流 -> 对象流
```

比如：

```java
List<String> list = List.of("1", "2", "3");

list.stream()
    .map(Integer::parseInt)
    .forEach(System.out::println);
```

这里：

```java
Stream<String>
→
Stream<Integer>
```

---

# mapToObj()

用于：

```java
基本类型流 -> 对象流
```

这是它最核心的用途。

因为：

```java
chars()
```

返回的不是：

```java
Stream<Character>
```

而是：

```java
IntStream
```

所以需要：

```java
mapToObj()
```

把 primitive type 转成 object。

---

# 最经典例子

```java
String s = "abc";

s.chars()
 .mapToObj(c -> (char) c)
 .forEach(System.out::println);
```

这里：

```java
chars()
```

返回：

```java
IntStream
```

每个字符都会先变成 int：

```java
97
98
99
```

然后：

```java
mapToObj(c -> (char) c)
```

把：

```java
int
```

转回：

```java
Character
```

最终得到：

```java
Stream<Character>
```

---

# 为什么需要 mapToObj()

因为 Java Stream 分两种：

## 1. 对象流

```java
Stream<T>
```

例如：

```java
Stream<String>
Stream<Integer>
```

---

## 2. primitive stream

Java 为了性能专门做了：

```java
IntStream
LongStream
DoubleStream
```

这些不是：

```java
Stream<Integer>
```

而是 primitive stream。

所以：

```java
IntStream
```

如果想变成：

```java
Stream<Integer>
```

或者：

```java
Stream<Character>
```

就需要：

```java
mapToObj()
```

---

# 一句话总结

## stream()

Collection 自己创建 Stream。

---

## Arrays.stream()

数组创建 Stream。

---

## map()

对象 -> 对象。

---

## mapToObj()

primitive stream -> object stream。