---
title: Daemon vs User Threads
parent: Threads
nav_order: 4
---

# Daemon vs User Threads

-----

The difference is about JVM life dependency

- **User Thread** → Keeps the application alive
- **Daemon Thread** → Runs in background, does NOT keep JVM alive

> **JVM exits when all user threads finish, even if daemon threads are still running.**

| Aspect         | User Thread                | Daemon Thread                 |
| -------------- | -------------------------- | ----------------------------- |
| Purpose        | Main work (business logic) | Background support tasks      |
| JVM dependency | JVM waits for it           | JVM does NOT wait             |
| Lifecycle      | Independent                | Ends when JVM ends            |
| Examples       | API requests, DB calls     | Garbage Collector, monitoring |
| Default type   | Yes                        | No                            |


### Example to Understand Clearly

```java

public class Main {
    public static void main(String[] args) {

        Thread t = new Thread(() -> {
            while (true) {
                System.out.println("Daemon running...");
            }
        });

        t.setDaemon(true); // mark as daemon
        t.start();

        System.out.println("Main thread finished");
    }
}
```

#### What happens?

- Main thread finishes quickly
- Only daemon thread remains
- JVM exits immediately

#### 👉 Output:

```
Main thread finished
Daemon running... (maybe few times or not at all)
```

### Same Example WITHOUT Daemon

```
t.setDaemon(false); // default
```

### Now:

- JVM will NOT exit
- Program keeps running forever

----

## 🧵 User Threads

Used for:

- Handling HTTP requests (Spring Boot)
- Business logic
- Kafka consumers

👉 These must complete → JVM waits

##  Daemon Threads

Used for:

- Garbage Collection (GC)
- Background cleanup
- Monitoring

👉 These are supporting tasks only

----

# Important Rules

- Must set daemon BEFORE start

```
t.setDaemon(true);
t.start();
```

- ❌ This will fail:

```
t.start();
t.setDaemon(true); // IllegalThreadStateException
```

