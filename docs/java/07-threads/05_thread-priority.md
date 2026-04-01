---
title: Thread Priority
parent: Threads
nav_order: 5
---

# Thread Priority

----

Thread priority is a hint to the thread scheduler about which thread should get CPU preference.

In Java:

```
Thread.MIN_PRIORITY = 1
Thread.NORM_PRIORITY = 5 (default)
Thread.MAX_PRIORITY = 10
```

```java
public class Main {
    public static void main(String[] args) {

        Thread t1 = new Thread(() -> {
            System.out.println("Low priority thread");
        });

        Thread t2 = new Thread(() -> {
            System.out.println("High priority thread");
        });

        t1.setPriority(Thread.MIN_PRIORITY);
        t2.setPriority(Thread.MAX_PRIORITY);

        t1.start();
        t2.start();
    }
}
```

### Expectation:

- t2 runs first (higher priority)

### Reality:

- Not guaranteed

----


## Why Thread Priority Exists

Historically, it was introduced to:

**1. Influence Scheduling**

- Suggest which threads are more important

**2. Optimize CPU usage**

- High priority → critical tasks
- Low priority → background tasks


----

## How It Works Internally

> Thread → JVM → OS Scheduler → CPU

**Important:**

- Java does NOT control scheduling directly
- OS scheduler makes final decision

-----
# Note:

**Thread priority is just a hint, NOT a guarantee.**

-----

# Why You Should NOT Rely on Priority

#### ❌ 1. OS dependent
Windows vs Linux → different behavior

#### ❌ 2. JVM implementation dependent
#### ❌ 3. Can cause starvation
Low priority threads may never run


> Thread priority is a hint to the scheduler, but since scheduling is OS-dependent, it is not reliable and should not be used for controlling execution order in real applications.

