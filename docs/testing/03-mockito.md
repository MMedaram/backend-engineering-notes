---
title: Mockito
parent: Testing
nav_order: 3
---

# Mockito

---

# 1️. What is Mockito?

Mockito is a Java mocking framework used to:

- Create mock objects
- Stub behavior
- Verify interactions
- Isolate unit tests

It is primarily used in **unit testing** to isolate the class under test from its dependencies.

In modern Spring Boot projects, Mockito works with:

- JUnit 5
- @ExtendWith(MockitoExtension.class)

---

# 2️. Why Mocking is Needed

In unit testing, we isolate the class under test.

Example:

Service depends on:
- Repository
- External API
- Kafka producer
- Email service

In unit tests, we:
- Do NOT call real DB
- Do NOT call real APIs
- Do NOT send real Kafka messages

Instead, we mock dependencies.

---

# 3️. Core Annotations

Import:

```java
import org.mockito.*;
import org.mockito.junit.jupiter.MockitoExtension;
import org.junit.jupiter.api.extension.ExtendWith;
```

## 3.1  @ExtendWith(MockitoExtension.class)

Enables Mockito in JUnit 5.

```java
@ExtendWith(MockitoExtension.class)
class UserServiceTest {
}
```

This automatically:

- Creates @Mock objects
- Injects @InjectMocks
- Clears mocks between tests

## 3.2 @Mock

Creates a mock instance.

```java
@Mock
private UserRepository userRepository;
```

Mockito generates a dummy implementation.

## 3.3 @InjectMocks

Creates instance of class under test and injects mocks into it.

```java
@InjectMocks
private UserService userService;
```

Injection types supported:

- Constructor injection (preferred)
- Setter injection
- Field injection

---

# 4️. Creating Mocks Manually

Without annotations:

> UserRepository repo = Mockito.mock(UserRepository.class);


# 5️. Stubbing (Defining Behavior)

Stubbing defines what the mock should return.

## 5.1 thenReturn()

> when(userRepository.findById(1L)).thenReturn(Optional.of(new User(1L, "John")));

## 5.2 thenThrow()

> when(userRepository.findById(1L)).thenThrow(new RuntimeException("DB error"));

## 5.3 thenAnswer()

Dynamic behavior.

> when(userRepository.save(any(User.class))).thenAnswer(invocation -> invocation.getArgument(0));

## 5.4 thenCallRealMethod()

Calls actual method instead of mocking.

Used rarely.


# 6️. Verifying Interactions

Verifying ensures methods were called.

## 6.1 Basic Verify

> verify(userRepository).save(any(User.class));

## 6.2 Verify Times

> verify(userRepository, times(2)).save(any());

> verify(userRepository, never()).delete(any());

## 6.3 verifyNoMoreInteractions()

> verifyNoMoreInteractions(userRepository);

Ensures no unexpected calls occurred.

# 7️. Argument Matchers

Used when exact values are unknown.

Common Matchers

- any()
- anyString()
- anyInt()
- eq("value")
- isNull()
- notNull()

**Example:**      

> when(userRepository.findByEmail(anyString())).thenReturn(Optional.empty());

⚠ Important Rule:

If you use matchers, ALL arguments must use matchers.

**Correct:**        

> when(service.process(eq("abc"), anyInt()))

**Wrong:**          

> when(service.process("abc", anyInt())) ❌

# 8️. Mocking Void Methods

Void methods use doX() syntax.

## doNothing() (Default behavior)

> doNothing().when(emailService).sendEmail(any());


## doThrow()

> doThrow(new RuntimeException()).when(emailService).sendEmail(any());

# 9️. Spying (Partial Mocking)

Spy wraps real object.

> MyService spyService = spy(new MyService());

Allows:

- Real behavior by default
- Stubbing selected methods

Example:

> when(spyService.calculate()).thenReturn(100);

⚠ Use spy carefully — often indicates poor design.

# 10. Mocking Static & Final Methods (Modern Approach)

**Old belief:**

> Mockito cannot mock static/final methods.

Modern Mockito (with mockito-inline dependency) CAN mock:

- Static methods
- Final classes
- Final methods

**Example:**      

```
try (MockedStatic<Utility> mocked = mockStatic(Utility.class)) {
mocked.when(Utility::getMessage).thenReturn("Mocked");
}
```

> PowerMockito is rarely needed now.


# 11. ArgumentCaptor

Used to capture arguments passed to mocks.

```java
@Captor
ArgumentCaptor<User> userCaptor;
```

Example:

```
verify(userRepository).save(userCaptor.capture());

User savedUser = userCaptor.getValue();

assertEquals("John", savedUser.getName());
```

Useful for verifying constructed objects.

# 12. Timeout Verification

verify(mockService, timeout(100)).process();

Useful in async testing.

# 13. Resetting Mocks

reset(mockObject);

⚠ Avoid frequent reset — better to create fresh mocks per test.


# 14. Common Mistakes

- Over-mocking everything
- Testing implementation instead of behavior
- Verifying too many interactions
- Using spy unnecessarily
- Mocking the class under test
- Using @SpringBootTest for unit tests

# 15. Mockito + Spring Boot

Unit Test (Service Layer)

Use:

> @ExtendWith(MockitoExtension.class)

Controller Test

Use:

> @WebMvcTest       
> @MockBean

Note:

- @MockBean is Spring Boot annotation
- Replaces bean inside Spring context

# 16. Best Practices

- Mock only dependencies, not the class under test
- Prefer constructor injection in production code
- Avoid deep stubbing
- Keep tests readable
- Verify behavior, not implementation
- Avoid excessive verify() calls

# 17. When NOT to Mock

Do NOT mock:

- Simple data objects
- Value objects
- Collections
- Internal pure logic
- The class under test

Mock only:

- External dependencies
- DB calls
- External services
- Messaging systems

---

# 19️ Example

Typical Service Test:

```java
@ExtendWith(MockitoExtension.class)
class OrderServiceTest {

    @Mock
    private OrderRepository orderRepository;

    @InjectMocks
    private OrderService orderService;

    @Test
    void createOrder_shouldSaveOrder() {

        Order order = new Order("ABC");

        when(orderRepository.save(any(Order.class)))
                .thenReturn(order);

        Order result = orderService.createOrder("ABC");

        assertEquals("ABC", result.getCode());
        verify(orderRepository).save(any(Order.class));
    }
}
```


## Summary

Mockito enables:

- Isolation
- Faster unit tests
- Cleaner design
- Behavior verification

In professional backend systems:

- Service layer → Unit test with Mockito
- Controller → @WebMvcTest + @MockBean
- Repository → @DataJpaTest
- Full flow → @SpringBootTest

Mockito is essential for writing proper unit tests in microservices.

