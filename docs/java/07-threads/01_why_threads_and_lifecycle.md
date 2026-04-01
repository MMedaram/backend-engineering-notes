---
title: Thread Lifecycle
parent: Threads
nav_order: 1
---

# What is a Thread

A thread is the smallest unit of execution inside a process.

- A process = your Java application (JVM instance)
- A thread = a path of execution inside that process

### Example:
- Your Spring Boot app → 1 process
- Each HTTP request → handled by a separate thread


---

# Why Threads?

#### Without threads:

```
task1();
task2();
task3();
```

👉 Runs sequentially (slow)

#### With threads:

```
task1 -> Thread-1
task2 -> Thread-2
task3 -> Thread-3
```

👉 Runs concurrently (faster, better CPU usage)

----

# Thread Lifecycle 

> **NEW → RUNNABLE ↔ RUNNING → (BLOCKED / WAITING / TIMED_WAITING) → TERMINATED**

**Important:**

- Java does NOT have a separate RUNNING state in API
- Both ready + running = RUNNABLE

**A thread state. A thread can be in one of the following states:**

**NEW**

A thread that has not yet started is in this state.

**RUNNABLE**

A thread executing in the Java virtual machine is in this state.

**BLOCKED**

A thread that is blocked waiting for a monitor lock is in this state.

**WAITING**

A thread that is waiting indefinitely for another thread to perform a particular action is in this state.

**TIMED_WAITING**

A thread that is waiting for another thread to perform an action for up to a specified waiting time is in this state.

**TERMINATED**

A thread that has exited is in this state.
A thread can be in only one state at a given point in time. These states are virtual machine states which do not reflect any operating system thread states.

---

### 1. NEW State

```java
Thread t = new Thread(() -> {
    System.out.println("Task");
});
```

👉 Thread created but not started

> t.getState(); // NEW

---

### 2. RUNNABLE State (After start())

> t.start();

**What happens internally:**
- JVM calls native method → creates OS thread
- Thread moves to RUNNABLE pool

👉 It may:

- Run immediately OR
- Wait for CPU

---

### 3. BLOCKED State (Waiting for Lock)

```java
public static void main(String[] args) {
    
    Object lock = new Object();
    
    Thread t1 = new Thread(() -> {
        synchronized (lock) {
            try { Thread.sleep(3000); } catch (Exception e) {}
        }
    });
    
    Thread t2 = new Thread(() -> {
        synchronized (lock) {
            System.out.println("Got lock");
        }
    });
    
    t1.start();
    t2.start();

}
```

**What happens:**

- t1 gets lock
- t2 tries → goes to BLOCKED

👉 State:

> t2.getState(); // BLOCKED

---

### 4. WAITING State (Indefinite Wait)

**Trigger methods:**

#### wait()

```java
Object lock = new Object();

Thread t = new Thread(() -> {
    synchronized (lock) {
        try {
            lock.wait(); // WAITING
        } catch (Exception e) {}
    }
});

```

#### join()

```
Thread t1 = new Thread(() -> {
    try { Thread.sleep(2000); } catch (Exception e) {}
});

t1.start();
t1.join(); // main thread WAITING

System.out.println("Done");
```

👉 Main thread goes to: 
> WAITING

----

### 5. TIMED_WAITING State

**Trigger methods:**
- sleep(ms)
- wait(ms)
- join(ms)

```java
Thread t = new Thread(() -> {
    try {
        Thread.sleep(3000); // TIMED_WAITING
    } catch (Exception e) {}
});
```

---

### 6. TERMINATED State

**When:**
run() method completes

```
Thread t = new Thread(() -> {
    System.out.println("Done");
});

t.start();
t.join();

System.out.println(t.getState()); // TERMINATED
```

