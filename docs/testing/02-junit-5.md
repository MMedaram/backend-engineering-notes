---
title: JUnit 5
parent: Testing
nav_order: 2
---

# JUnit 5

---

# 1️. What is JUnit?

JUnit is a testing framework for Java used to write and run automated tests.

JUnit 5 is the modern version and consists of three main components:

- **JUnit Platform** → Launches testing frameworks
- **JUnit Jupiter** → Programming model & API (most commonly used)
- **JUnit Vintage** → Supports JUnit 3 & 4 tests

Most backend projects use:

> org.junit.jupiter.*

# 2️. JUnit 5 Architecture

JUnit 5 = Platform + Jupiter + Vintage         

In Spring Boot projects, **spring-boot-starter-test** automatically includes JUnit Jupiter.

# 3️. Basic Test Structure

```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

class CalculatorTest {

    @Test
    void add_shouldReturnSum() {
        int result = 2 + 3;
        assertEquals(5, result);
    }
}
```

**Important Notes**

- Test class does NOT need to be public.
- Test methods do NOT need to be public.
- No main() method required.
- @Test marks executable test methods.

# 4️. Core Annotations

| Annotation     | Purpose                    |
| -------------- | -------------------------- |
| `@Test`        | Marks test method          |
| `@DisplayName` | Custom readable name       |
| `@BeforeEach`  | Runs before every test     |
| `@AfterEach`   | Runs after every test      |
| `@BeforeAll`   | Runs once before all tests |
| `@AfterAll`    | Runs once after all tests  |
| `@Disabled`    | Skips a test               |
| `@Nested`      | Groups related tests       |


# 5️. Test Lifecycle

## 5.1 @BeforeEach / @AfterEach

Executed before/after every test method.

```java
@BeforeEach
void setup() {
    System.out.println("Runs before each test");
}

@AfterEach
void cleanup() {
    System.out.println("Runs after each test");
}
```

## 5.2 @BeforeAll / @AfterAll

- Executed once per test class.
- ⚠ Must be static by default.

```java
@BeforeAll
static void initAll() {
    System.out.println("Runs once before all tests");
}
```

## 5.3 Test Instance Lifecycle

By default, JUnit creates a new instance of the test class per test method.

Default:

> PER_METHOD

Alternative:

> @TestInstance(TestInstance.Lifecycle.PER_CLASS)

This allows non-static @BeforeAll methods.

---

# 6️. Assertions

Assertions validate expected outcomes.

**Import:**    

> import static org.junit.jupiter.api.Assertions.*;

## Common Assertions

```
assertEquals(expected, actual);
assertNotEquals(unexpected, actual);
assertTrue(condition);
assertFalse(condition);
assertNull(object);
assertNotNull(object);
```

### 6.1 assertThrows

Used to verify exception behavior.

```
assertThrows(IllegalArgumentException.class, () -> {
service.process(null);
});
```

### 6.2 assertAll

Executes multiple assertions together.

```
assertAll(
() -> assertEquals(5, result),
() -> assertTrue(result > 0)
);
```

All assertions execute even if one fails

### 6.3 assertTimeout

Fails if execution exceeds time limit.

```
import java.time.Duration;

assertTimeout(Duration.ofSeconds(2), () -> {
service.longRunningMethod();
});

```

---

# 7. Assumptions

Assumptions skip tests if conditions are not met.

> import static org.junit.jupiter.api.Assumptions.*;

Assumptions works like this:

- 👉 When the assumption fails, JUnit throws a special exception       
- 👉 The test is marked SKIPPED (aborted)
- 👉 Execution stops immediately at that line

Example :     

```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assumptions.*;

class AssumeFlowExample {

    @Test
    void testFlow() {

        System.out.println("1 - First statement");
        System.out.println("2 - Second statement");

        assumeTrue(false, "Condition failed");   // 3rd statement

        System.out.println("4 - Fourth statement");
        System.out.println("5 - Fifth statement");
    }
}
```

**What happens?**          

Execution order:

```
1 - First statement   ✅ executed
2 - Second statement  ✅ executed
assumeTrue(false)     ❌ fails here
4 - Fourth statement  ❌ NOT executed
5 - Fifth statement   ❌ NOT executed
```

**Final Result:**      

- Test status → Skipped
- Statements before assumption → Executed
- Statements after assumption → Not executed

**Assumptions behave like:**           

```
if (!condition) {
throw TestAbortedException;
}
```


### Note :: 

- 👉 Put `assumeTrue()` at the top of the test      
- So you avoid partially executing test logic.

---

## assumeTrue()

👉 Test runs only if condition is true      
👉 If false → test is skipped, not failed      

```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assumptions.*;

class AssumeTrueExample {

    @Test
    void testOnlyOnDevEnvironment() {

        String env = System.getProperty("ENV");

        assumeTrue("DEV".equals(env), "Test runs only in DEV environment");

        System.out.println("Running test in DEV environment");
    }
}
```

| ENV value | Result       |
| --------- | ------------ |
| DEV       | Test runs    |
| PROD      | Test skipped |
| null      | Test skipped |


## assumeFalse()

👉 Test runs only if condition is false       
👉 If true → test is skipped      

```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assumptions.*;

class AssumeFalseExample {

    @Test
    void testNotForWindows() {

        String os = System.getProperty("os.name");

        assumeFalse(os.contains("Windows"), "Test skipped on Windows");

        System.out.println("Running test on non-Windows OS");
    }
}
```

| OS          | Result       |
| ----------- | ------------ |
| Windows     | Test skipped |
| Linux / Mac | Test runs    |


## assumingThat()

👉 Conditional execution inside test      
👉 If condition false → block skipped        
👉 But rest of test still executes         

```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assumptions.*;
import static org.junit.jupiter.api.Assertions.*;

class AssumingThatExample {

    @Test
    void testWithOptionalBlock() {

        String env = System.getProperty("ENV");

        assumingThat("DEV".equals(env), () -> {
            System.out.println("Running DEV specific assertions");
            assertEquals(2, 1 + 1);
        });

        // This always runs
        assertTrue(true);
        System.out.println("Common test logic executed");
    }
}
```

| ENV  | DEV block | Rest of test |
| ---- | --------- | ------------ |
| DEV  | Executed  | Executed     |
| PROD | Skipped   | Executed     |


---

| Method       | If Condition Fails | Entire Test Skipped? |
| ------------ | ------------------ | -------------------- |
| assumeTrue   | Yes                | Yes                  |
| assumeFalse  | Yes                | Yes                  |
| assumingThat | Only block skipped | No                   |

---

# 8️. Parameterized Tests

Used to run same test with multiple inputs.

## 8.1 @ValueSource

```java

@ParameterizedTest
@ValueSource(ints = {2, 4, 6, 8})
void shouldBeEven(int number) {
    assertTrue(number % 2 == 0);
}

```

## 8.2 @CsvSource

```java
@ParameterizedTest
@CsvSource({
"apple, 5",
"banana, 6"
})
void checkLength(String fruit, int length) {
assertEquals(length, fruit.length());
}
```

## 8.3 @CsvFileSource

```java
@ParameterizedTest
@CsvFileSource(resources = "/data.csv", delimiter = ';')
void testFromFile(String input, int expected) {
    assertEquals(expected, input.length());
}
```

---

# 9️. Repeated Tests

```java
@RepeatedTest(5)
void repeatTest() {
    assertTrue(true);
}
```

Runs same test multiple times.


---

# 10. Nested Tests

Used to group related test scenarios.

```java
@Nested
class ValidInputTests {

    @Test
    void shouldReturnSuccess() {
        assertTrue(true);
    }
}
```

Improves readability.

---

# 11. Test Ordering

⚠ Not recommended unless necessary.

Using @Order

```java
@TestMethodOrder(MethodOrderer.OrderAnnotation.class)
class OrderedTests {

    @Test
    @Order(1)
    void first() {}

    @Test
    @Order(2)
    void second() {}
}
```

## Alphanumeric Order

Executes test methods in alphanumeric order based on their method names.


```java
@TestMethodOrder(MethodOrderer.Alphanumeric.class)
class OrderedTests {

    @Test
    void first() {}

    @Test
    void second() {}
}
```

---

# 12️. Conditional Test Execution

Run tests only in specific environments.

Examples:        

```
@EnabledOnOs(OS.WINDOWS)
@DisabledOnJre(JRE.JAVA_17)
@EnabledIfSystemProperty(named = "env", matches = "test")
@Disabled
```

---

# 13. Parallel Execution

JUnit 5 supports parallel execution.

Enable via junit-platform.properties:

> junit.jupiter.execution.parallel.enabled=true

Use carefully:

- Tests must be independent
- No shared mutable state

---

# 14️. Display Names & Reporting

> @DisplayName("Should calculate interest correctly")

Improves readability in reports.

---

# 15. Maven Configuration

JUnit runs automatically via: `maven-surefire-plugin`

To generate reports:

> mvn surefire-report:report

Report location:

> target/site/surefire-report.html

---

# Best Practices

- Use meaningful test names
- Follow Arrange–Act–Assert
- One behavior per test
- Avoid testing implementation details
- Keep tests independent
- Do not rely on execution order
- Prefer parameterized tests for multiple inputs

