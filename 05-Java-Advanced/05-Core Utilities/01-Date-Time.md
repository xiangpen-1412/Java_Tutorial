___
## Overview

Java 8 introduced the `java.time` package to replace legacy APIs (`Date`, `Calendar`).

Core idea:

> Separate **time points** (what moment it is) from **time intervals** (how long between moments).

---

# 1. Time Points (Core Classes)

## 1.1 `LocalDate`

Represents a date only (year, month, day).

```java
LocalDate date = LocalDate.of(2026, 3, 26);
LocalDate today = LocalDate.now();
```

### Common Methods

```java
int year = date.getYear();
int month = date.getMonthValue();
int day = date.getDayOfMonth();

LocalDate next = date.plusDays(5);
LocalDate prev = date.minusMonths(1);
```

---

## 1.2 `LocalTime`

Represents time only (hour, minute, second).

```java
LocalTime time = LocalTime.of(14, 30, 0);
LocalTime now = LocalTime.now();
```

```java
int hour = time.getHour();
LocalTime later = time.plusMinutes(30);
```

---

## 1.3 `LocalDateTime`

Represents date + time (no timezone).

```java
LocalDateTime dt = LocalDateTime.of(2026, 3, 26, 14, 30);
LocalDateTime now = LocalDateTime.now();
```

```java
LocalDate date = dt.toLocalDate();
LocalTime time = dt.toLocalTime();

LocalDateTime next = dt.plusDays(1);
```

⚠️ Important:

> `LocalDateTime` is NOT a global time. It has no timezone.

---

## 1.4 `Instant`

Represents a global timestamp (UTC).

```java
Instant now = Instant.now();
```

```java
long seconds = now.getEpochSecond();
long millis = now.toEpochMilli();
```

```java
Instant later = now.plusSeconds(3600);
```

Use case:

- database storage
    
- logs
    
- distributed systems
    

---

# 2. Time Zone (VERY IMPORTANT)

## 2.1 `ZoneId`

Represents a timezone.

```java
ZoneId zone = ZoneId.of("America/Toronto");
```

---

## 2.2 `ZonedDateTime`

Date + time + timezone.

```java
ZonedDateTime zdt = ZonedDateTime.now(ZoneId.of("America/Toronto"));
```

```java
ZonedDateTime custom = ZonedDateTime.of(
    2026, 3, 26,
    14, 30, 0, 0,
    ZoneId.of("Asia/Shanghai")
);
```

### Conversion

```java
LocalDateTime ldt = LocalDateTime.now();
ZonedDateTime zdt = ldt.atZone(ZoneId.of("America/Toronto"));

Instant instant = zdt.toInstant();
```

---

## 2.3 `OffsetDateTime`

Date + time + offset (no region rules).

```java
OffsetDateTime odt = OffsetDateTime.now();
```

Difference:

- `ZonedDateTime` → full timezone rules
    
- `OffsetDateTime` → fixed offset (e.g. +08:00)
    

---

# 3. Time Interval

## 3.1 `Period` (Date-Based)

Represents:

- years
    
- months
    
- days
    

```java
LocalDate start = LocalDate.of(2020, 1, 1);
LocalDate end = LocalDate.of(2026, 3, 26);

Period p = Period.between(start, end);

int years = p.getYears();
int months = p.getMonths();
int days = p.getDays();
```

Use case:

- age
    
- contract duration
    
- calendar logic
    

---

## 3.2 `Duration` (Time-Based)

Represents:

- seconds
    
- nanoseconds
    

```java
LocalTime t1 = LocalTime.of(10, 0);
LocalTime t2 = LocalTime.of(12, 30);

Duration d = Duration.between(t1, t2);

long hours = d.toHours();
long minutes = d.toMinutes();
```

### With `Instant`

```java
Instant t1 = Instant.now();
Instant t2 = t1.plusSeconds(1800);

Duration d = Duration.between(t1, t2);
```

Use case:

- timeout
    
- execution time
    
- performance measurement
    

---

# 4. Formatting & Parsing

## 4.1 `DateTimeFormatter`

```java
DateTimeFormatter f = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");

LocalDateTime dt = LocalDateTime.now();
String text = dt.format(f);
```

```java
LocalDateTime parsed = LocalDateTime.parse(text, f);
```

### Common Patterns

- yyyy → year
    
- MM → month
    
- dd → day
    
- HH → hour (24h)
    
- mm → minute
    
- ss → second
    

⚠️ Common bug:

- `MM` = month
    
- `mm` = minute
    

---

# 5. Conversion Summary

```java
// LocalDate + LocalTime → LocalDateTime
LocalDateTime dt = LocalDateTime.of(date, time);

// LocalDateTime → ZonedDateTime
ZonedDateTime zdt = dt.atZone(ZoneId.of("America/Toronto"));

// ZonedDateTime → Instant
Instant instant = zdt.toInstant();

// Instant → LocalDateTime
LocalDateTime ldt = LocalDateTime.ofInstant(instant, ZoneId.of("America/Toronto"));
```

---

# 6. Comparison

```java
LocalDate d1 = LocalDate.of(2026, 3, 26);
LocalDate d2 = LocalDate.of(2026, 4, 1);

boolean before = d1.isBefore(d2);
boolean after = d1.isAfter(d2);
boolean equal = d1.isEqual(d2);
```

---

# 7. Key Differences

|Concept|Class|Meaning|
|---|---|---|
|Date|LocalDate|calendar date|
|Time|LocalTime|clock time|
|DateTime|LocalDateTime|local datetime|
|Global Time|Instant|UTC timestamp|
|Timezone Time|ZonedDateTime|datetime + zone|
|Interval|Period|years/months/days|
|Interval|Duration|seconds/nanos|

---

# 8. Common Mistakes

## 1. Using `LocalDateTime` as global time

Wrong: no timezone → ambiguous

## 2. Mixing `Period` and `Duration`

- Period → calendar
    
- Duration → exact time
    

## 3. Forgetting immutability

```java
date.plusDays(1); // WRONG
```

Must assign:

```java
date = date.plusDays(1);
```

---

# 9. Mental Model (IMPORTANT)

Think in layers:

1. What am I describing?
    
    - date? → LocalDate
        
    - time? → LocalTime
        
    - local datetime? → LocalDateTime
        
    - global moment? → Instant
        
2. Do I need timezone?
    
    - yes → ZonedDateTime
        
3. Am I calculating difference?
    
    - calendar → Period
        
    - exact time → Duration
        

---

# 10. Interview Takeaway

> Always separate **time point vs time interval**, and choose the correct class.

- LocalDateTime ≠ global time
    
- Instant = safest storage format
    
- Period = human time
    
- Duration = machine time