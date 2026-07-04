---
title: Date and Time API
parent: Java-8
nav_order: 9
---

# Date and Time API

Java 8 introduced the `java.time` package.

It replaced most day-to-day usage of old classes like:

- `java.util.Date`
- `java.util.Calendar`
- `SimpleDateFormat`

---

## Why Was It Introduced?

Old date/time APIs had many problems:

- `Date` was mutable.
- `Calendar` was hard to use.
- `SimpleDateFormat` was not thread-safe.
- Time zone handling was confusing.

Java 8 introduced a cleaner, immutable, thread-safe date/time API.

---

## Main Classes

| Class               | Use                                       |
|---------------------|-------------------------------------------|
| `LocalDate`         | Date only, no time                        |
| `LocalTime`         | Time only, no date                        |
| `LocalDateTime`     | Date and time, no time zone               |
| `ZonedDateTime`     | Date and time with time zone              |
| `Instant`           | Machine timestamp in UTC                  |
| `Duration`          | Time-based amount, like seconds/hours     |
| `Period`            | Date-based amount, like days/months/years |
| `DateTimeFormatter` | Format and parse date/time                |

---

## LocalDate

```java
package exercise.java8.datetime;

import java.time.LocalDate;

public class LocalDateDemo {
    public static void main(String[] args) {
        //1️⃣ Current Date
        LocalDate today = LocalDate.now();     //2025-10-01
        //2️⃣ Specific Date
        LocalDate birthDate = LocalDate.of(1990, 4, 5);      //1990-04-05
        // 3️⃣From String
        LocalDate parsed = LocalDate.parse("2025-12-31");    // 2025-12-31
        //4️⃣Getting Components
        LocalDate date = LocalDate.of(2025, 10, 1);
        System.out.println(date.getYear());        // 2025
        System.out.println(date.getMonth());       // OCTOBER
        System.out.println(date.getMonthValue());  // 10
        System.out.println(date.getDayOfMonth());  // 1
        System.out.println(date.getDayOfWeek());   // WEDNESDAY
        System.out.println(date.lengthOfMonth());  // 31
        System.out.println(date.isLeapYear());     // false
        //5️⃣Date Arithmetic
        System.out.println(today.plusDays(5));     // 2025-10-06
        System.out.println(today.minusWeeks(2));   // 2025-09-17
        System.out.println(today.plusMonths(1));   // 2025-11-01
        System.out.println(today.minusYears(1));   // 2024-10-01
        //6️⃣ Comparisons
        LocalDate d1 = LocalDate.of(2025, 10, 1);
        LocalDate d2 = LocalDate.of(2025, 12, 31);
        System.out.println(d1.isBefore(d2));   // true
        System.out.println(d1.isAfter(d2));    // false
        System.out.println(d1.isEqual(d2));    // false


    }
}

```

Good for birthdate, joining date, and invoice date.

Edge case:

```java
LocalDate d = LocalDate.of(2024, 2, 30); // Exception in thread "main" java.time.DateTimeException: Invalid date 'FEBRUARY 30'
```

---

## Instant

Machine timestamp in UTC.

```java
Instant now = Instant.now();
```

Good for created/updated timestamps and audit events.

---

## ZonedDateTime

```java
package exercise.java8.datetime;

import java.time.LocalDateTime;
import java.time.ZoneId;
import java.time.ZonedDateTime;
//Represents date + time + timezone + offset.
public class ZonedDateTimeDemo {
    public static void main(String[] args) {
        ZonedDateTime now = ZonedDateTime.now(); //2025-10-03T13:15:04.549648+05:30[Asia/Calcutta]

        ZonedDateTime newYork = ZonedDateTime.now(ZoneId.of("America/New_York"));
        System.out.println("NY Time: " + newYork);

        LocalDateTime ldt = LocalDateTime.of(2025, 10, 1, 14, 30);
        ZonedDateTime zoned = ldt.atZone(ZoneId.of("Asia/Kolkata"));
        System.out.println("Zoned: " + zoned);

        ZonedDateTime zdt = ZonedDateTime.now();
        System.out.println(zdt.getZone());         // Asia/Kolkata
        System.out.println(zdt.getOffset());       // +05:30
        System.out.println(zdt.getDayOfWeek());    // FRIDAY
        System.out.println(zdt.getHour());         // 13 (local hour)

        System.out.println(now.plusHours(5));   // add 5 hours
        System.out.println(now.minusDays(2));   // subtract 2 days

        ZoneId.getAvailableZoneIds().stream().limit(10).forEach(System.out::println);

    }
}

```

Good for user-facing local time and scheduling by timezone.

Edge case:

```
ZoneId.of("Invalid/Zone"); // DateTimeException
```

---

## Period vs Duration

`Period` is date-based.

```java
package exercise.java8.datetime;

import java.time.LocalDate;
import java.time.Period;

public class PeriodDemo {
    public static void main(String[] args) {
        Period p1 = Period.ofYears(2); //P2Y
        Period p2 = Period.ofMonths(6); //P6M
        Period p3 = Period.ofDays(15); //P15D
        Period p = Period.of(2, 6, 15); // P2Y6M15D

        LocalDate today = LocalDate.now(); //2026-07-03
        LocalDate birthDate = LocalDate.of(1994, 1, 3);

        Period age = Period.between(birthDate, today); //P32Y6M

        Period period = Period.ofMonths(15);  // P15M
        System.out.println(period.normalized());    // P1Y3M


    }
}

```

`Duration` is time-based.

```java
package exercise.java8.datetime;

import java.time.Duration;
import java.time.LocalTime;
//Duration → time differences (H/M/S/Nanos).
public class DurationDemo {
    public static void main(String[] args) {
        Duration d1 = Duration.ofHours(5); // PT5H
        Duration d2 = Duration.ofMinutes(90); //PT1H30M
        Duration d3 = Duration.ofSeconds(45); //PT45S

        LocalTime start = LocalTime.of(10, 0);
        LocalTime end = LocalTime.of(12, 30);

        Duration diff = Duration.between(start, end); //PT2H30M
        Duration d = Duration.ofHours(2).plusMinutes(30).plusSeconds(45);
        System.out.println("Hours: " + d.toHours());       // 2
        System.out.println("Minutes: " + d.toMinutes());   // 150
        System.out.println("Seconds: " + d.getSeconds());  // 9045
        System.out.println("Nanos: " + d.getNano());       // remainder nanos
    }
}
```

Use `Period` for days/months/years.

Use `Duration` for seconds/minutes/hours.

---

## Formatting And Parsing

```java
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("dd-MM-yyyy");

String text = LocalDate.now().format(formatter);
LocalDate date = LocalDate.parse("20-05-1995", formatter);
```

Edge case:

```
LocalDate.parse("31-02-2024", formatter); // DateTimeParseException
```

---

## Convert Old Date To Instant

```java
Date oldDate = new Date();
Instant instant = oldDate.toInstant();
```

Convert Instant to Date:

```java
Date date = Date.from(instant);
```

---

## Daily Backend Rules

- Store timestamps as `Instant` when possible.
- Use `LocalDate` for date-only values.
- Use `ZonedDateTime` when timezone matters.
- Avoid using `LocalDateTime` for cross-system timestamps.
- Use `DateTimeFormatter`, not `SimpleDateFormat`.

---

## Common Exceptions

| Case                 | Exception                |
|----------------------|--------------------------|
| Invalid date         | `DateTimeException`      |
| Invalid parse text   | `DateTimeParseException` |
| Invalid zone id      | `DateTimeException`      |
| Null formatter/input | `NullPointerException`   |

---

## Quick Summary

Java 8 Date and Time API is immutable, thread-safe, and clearer than old `Date` and `Calendar`. Use `Instant` for backend timestamps, `LocalDate` for date-only values, and `ZonedDateTime` when timezone matters.

