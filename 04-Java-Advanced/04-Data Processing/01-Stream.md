# Java Stream — Practical Engineering Guide

## 1. What is Stream (Core Idea)

A **Stream** is a pipeline for processing data in a **declarative** and **functional** way.

> Stream does NOT store data. It describes how to process data.

---

## 2. Mental Model (Must Understand)

```
Source → Intermediate Ops → Terminal Op
```

Example:

```java
List<Integer> result = list.stream()
    .filter(x -> x > 5)
    .map(x -> x * 2)
    .toList();
```

---

## 3. Three Parts of Stream

### 3.1 Source

Where data comes from:

```java
list.stream();
Arrays.stream(arr);
Stream.of(1,2,3);
```

---

### 3.2 Intermediate Operations (Lazy)

They build the pipeline, NOT execute it.

Common ones:

| Method   | Meaning       |
| -------- | ------------- |
| filter   | keep elements |
| map      | transform     |
| flatMap  | flatten       |
| sorted   | sort          |
| distinct | deduplicate   |
| limit    | take first n  |
| skip     | skip first n  |

---

### 3.3 Terminal Operations (Execute)

These trigger execution.

|Method|Meaning|
|---|---|
|toList|collect to list|
|collect|flexible collector|
|forEach|iterate|
|count|count|
|reduce|aggregate|

---

## 4. Execution Model (Very Important)

Stream works **element by element**, not stage by stage.

```
Element1 → filter → map
Element2 → filter → map
```

NOT:

```
filter all → map all
```

---

## 5. Lazy Evaluation

```java
list.stream()
    .filter(x -> {
        System.out.println(x);
        return x > 5;
    });
```

Nothing happens until terminal operation.

---

## 6. map vs flatMap (Critical)

### map

```java
.map(x -> x * 2)
```

T → R

---

### flatMap

```java
.flatMap(list -> list.stream())
```

T → Stream → flatten

Used for nested structures:

```
List<List<T>> → List<T>
```

---

## 7. collect() (Most Important Terminal)

```
stream → collect → result
```

Examples:

```java
.collect(Collectors.toList())
.collect(Collectors.toSet())
.collect(Collectors.toMap(...))
```

---

## 8. Collectors (Rules of Collection — Expanded)

`Collectors` defines HOW the stream result is accumulated.

---

### 8.1 toList / toSet / toCollection

```java
.collect(Collectors.toList())
.collect(Collectors.toSet())
.collect(Collectors.toCollection(LinkedList::new))
```

---

### 8.2 toMap (VERY IMPORTANT)

```java
.collect(Collectors.toMap(
    keyMapper,
    valueMapper
))
```

⚠️ Duplicate keys will throw exception.

```java
.collect(Collectors.toMap(
    keyMapper,
    valueMapper,
    (oldVal, newVal) -> oldVal
))
```

---

### 8.3 groupingBy (Core Skill)

```java
.collect(Collectors.groupingBy(classifier))
```

Rule:

```
key = classifier
value = downstream
```

---

### 8.4 groupingBy with downstream (CRITICAL)

#### Counting

```java
Map<K, Long>
.collect(Collectors.groupingBy(key, Collectors.counting()))
```

#### Mapping

```java
.collect(Collectors.groupingBy(
    key,
    Collectors.mapping(mapper, Collectors.toList())
))
```

#### Summing

```java
.collect(Collectors.groupingBy(
    key,
    Collectors.summingInt(mapper)
))
```

#### Averaging

```java
.collect(Collectors.groupingBy(
    key,
    Collectors.averagingInt(mapper)
))
```

#### Max / Min

```java
.collect(Collectors.groupingBy(
    key,
    Collectors.maxBy(Comparator.comparingInt(...))
))
```

---

### 8.5 partitioningBy

```java
.collect(Collectors.partitioningBy(predicate))
```

Result:

```
Map<Boolean, List<T>>
```

---

### 8.6 joining

```java
.collect(Collectors.joining())
.collect(Collectors.joining(", "))
.collect(Collectors.joining(", ", "[", "]"))
```

---

### 8.7 Numeric Collectors

```java
Collectors.counting()
Collectors.summingInt(...)
Collectors.averagingInt(...)
Collectors.summarizingInt(...)
```

`summarizingInt` returns:

- count
    
- sum
    
- min
    
- max
    
- average
    

---

### 8.8 Advanced Collectors (IMPORTANT)

#### mapping

```java
Collectors.mapping(mapper, downstream)
```

#### flatMapping (Java 9+)

```java
Collectors.flatMapping(x -> x.stream(), Collectors.toList())
```

#### filtering (Java 9+)

```java
Collectors.filtering(predicate, Collectors.toList())
```

#### collectingAndThen

```java
Collectors.collectingAndThen(
    Collectors.toList(),
    List::copyOf
)
```

---

### 8.9 Collector Design Insight (Interview Level)

Every Collector consists of:

1. supplier (create container)
    
2. accumulator (add element)
    
3. combiner (merge containers)
    
4. finisher (final transform)
    

---

## 9. reduce (Aggregation)

```java
int sum = list.stream()
    .reduce(0, Integer::sum);
```

Use for:

- sum
    
- max
    
- combine
    

---

## 10. Common Patterns (Must Know)

### Pattern 1 — filter + map

```java
list.stream()
    .filter(...)
    .map(...)
    .toList();
```

---

### Pattern 2 — flatMap

```java
list.stream()
    .flatMap(List::stream)
```

---

### Pattern 3 — grouping

```java
.collect(Collectors.groupingBy(...))
```

---

### Pattern 4 — top K

```java
list.stream()
    .sorted(Comparator.reverseOrder())
    .limit(k)
```

---

## 11. Common Pitfalls

### 1. Stream can only be used once

```java
stream.count();
stream.count(); // error
```

---

### 2. Side effects (BAD)

```java
list.stream().forEach(x -> externalList.add(x));
```

---

### 3. Wrong collector type

```
groupingBy + counting → Map<K, Long>
```

---

### 4. flatMap mistake

```java
.map(List::stream) // wrong
.flatMap(List::stream) // correct
```

---

## 12. Performance Notes

- Streams are not always faster than loops
    
- Avoid unnecessary object creation
    
- Prefer primitive streams (IntStream)
    

---

## 13. Interview Summary

You must be able to explain:

- Stream pipeline structure
    
- Lazy execution
    
- map vs flatMap
    
- collect vs reduce
    
- groupingBy logic
    
- common pitfalls
    

---

## 14. One-Line Summary

Stream is a **lazy, functional pipeline** for processing data, where:

- operations are chained
    
- execution is deferred
    
- final result is produced by terminal operations