# Java String 与 char[] 互相转换

## String 转 char[]

使用：

```java
String.toCharArray()
```

示例：

```java
String str = "hello";

char[] chars = str.toCharArray();
```

结果：

```text
['h', 'e', 'l', 'l', 'o']
```

---

## char[] 转 String

### 方法一：`new String()`

```java
char[] chars = {'h', 'e', 'l', 'l', 'o'};

String str = new String(chars);
```

### 方法二：`String.valueOf()`

```java
char[] chars = {'h', 'e', 'l', 'l', 'o'};

String str = String.valueOf(chars);
```

---

## 常见应用：对 String 排序

`String` 不能直接排序，需要先转换成 `char[]`：

```java
import java.util.Arrays;

String str = "dcba";

char[] chars = str.toCharArray();

Arrays.sort(chars);

String sortedStr = new String(chars);

System.out.println(sortedStr); // abcd
```

---

## 总结

```java
// String → char[]
char[] chars = str.toCharArray();

// char[] → String
String str1 = new String(chars);
String str2 = String.valueOf(chars);
```