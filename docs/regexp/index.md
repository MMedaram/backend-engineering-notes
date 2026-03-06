---
title: Regular Expressions
parent: Backend Engineering Notes
nav_order: 9
---

# Regular Expressions (Regex)

## 1. Definition

**Regular Expression (Regex)** is a **pattern used to search, match, validate, or extract text from a string**.

In **Java and Spring applications**, regex is commonly used for:

- Input validation
- Data parsing
- Log processing
- Text extraction
- Request validation
- Data cleaning

Example:

```
String text = "Order123";
text.matches("\\d+");  // false
```

## 2. Where Regex is Used in Java / Spring

| Area                   | Example                         |
| ---------------------- | ------------------------------- |
| Input validation       | Email, phone number             |
| Spring validation      | `@Pattern` annotation           |
| Parsing logs           | Extract IDs or timestamps       |
| Data processing        | Extract numbers from text       |
| Replace operations     | Remove unwanted characters      |
| Security               | Password validation             |
| API request validation | Validate request payload fields |


## 3. Java Regex Classes

Java provides classes in `java.util.regex` package.

| Class               | Purpose                 |
| ------------------- | ----------------------- |
| `Pattern`           | Compiled regex pattern  |
| `Matcher`           | Performs matching       |
| `Pattern.compile()` | Compile regex           |
| `matcher()`         | Apply regex on text     |
| `find()`            | Find next match         |
| `matches()`         | Check full string match |
| `group()`           | Get matched text        |


## 4. Character Classes

Match one character from a set.

| Regex      | Meaning           | Example |
| ---------- | ----------------- | ------- |
| `[abc]`    | a OR b OR c       | a       |
| `[a-z]`    | lowercase letters | hello   |
| `[A-Z]`    | uppercase letters | JAVA    |
| `[0-9]`    | digits            | 123     |
| `[a-zA-Z]` | letters           | Java    |


### Example

```
String text = "abc123";

Pattern p = Pattern.compile("[a-z]");
Matcher m = p.matcher(text);

while(m.find()){
    System.out.println(m.group());
}
```

### Output

```
a
b
c
```

---

## 5. Predefined Character Classes

Shortcuts for common patterns.

| Regex | Meaning        | Equivalent     |
| ----- | -------------- | -------------- |
| `\d`  | digit          | `[0-9]`        |
| `\D`  | non digit      | `[^0-9]`       |
| `\w`  | word character | `[a-zA-Z0-9_]` |
| `\W`  | non word char  | special chars  |
| `\s`  | whitespace     | space/tab      |
| `\S`  | non whitespace | letters        |


---

## 6. Quantifiers (Repetition)

Control how many times a pattern appears.

| Regex   | Meaning         | Example   |
| ------- | --------------- | --------- |
| `*`     | 0 or more       | `a*`      |
| `+`     | 1 or more       | `a+`      |
| `?`     | 0 or 1          | `a?`      |
| `{n}`   | exactly n       | `\d{4}`   |
| `{n,}`  | at least n      | `\d{2,}`  |
| `{n,m}` | between n and m | `\d{2,5}` |

```
String pin = "1234";
boolean valid = pin.matches("\\d{4}");  //true
```

## 7. Anchors

Define position in string.

| Regex | Meaning         |
| ----- | --------------- |
| `^`   | start of string |
| `$`   | end of string   |
| `\b`  | word boundary   |


> "12345".matches("^\\d+$");  // true

Matches only digits.

## 8. Grouping

Groups allow capturing values.

```
Pattern p = Pattern.compile("(\\d{4})-(\\d{2})-(\\d{2})");
Matcher m = p.matcher("2026-03-05");

if(m.find()){
    System.out.println(m.group(1)); // year
    System.out.println(m.group(2)); // month
    System.out.println(m.group(3)); // day
}
```

## 9. Java String Regex Methods

| Method         | Purpose             |
| -------------- | ------------------- |
| `matches()`    | full string match   |
| `replaceAll()` | replace regex match |
| `split()`      | split using regex   |


**matches()**      

> "1234".matches("\\d+"); //true

**replaceAll()**     

```
String text = "abc123";
text.replaceAll("\\d", "");
```

> Result : abc

**split()**      

```java
String data = "one,two,three";
String[] arr = data.split(",");
```

## 10. Regex with Java Streams

```
    List<String> list = List.of("abc","123","xyz","456","-120");
    
    List<String> result = list.stream()
        .filter(s -> s.matches("-?\\d+"))
        .toList();
    System.out.println(result); //[123, 456, -120]
```

## Regex Testing Tools

Useful tools

- https://regex101.com
- https://regexr.com

