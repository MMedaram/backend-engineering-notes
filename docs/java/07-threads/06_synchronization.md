---
title: Synchronization
parent: Threads
nav_order: 6
---

# Synchronization

-----

## 1. Why Synchronization is Needed

**Problem:** Shared Data + Multiple Threads

```java
class Counter {
    int count = 0;

    void increment() {
        count++; // NOT thread-safe
    }
}
```

**Multi-threaded usage:**

```java
public static void main(String[] args) {

    Counter c = new Counter();

    Thread t1 = new Thread(() -> {
        for (int i = 0; i < 1000; i++) c.increment();
    });

    Thread t2 = new Thread(() -> {
        for (int i = 0; i < 1000; i++) c.increment();
    });

    t1.start();
    t2.start();

}
```

👉 Expected: 2000\
👉 Reality: ❌ unpredictable (like 1634, 1872…)

count++ is NOT atomic

It is actually:

1. read count
2. increment
3. write back

👉 Two threads interfere → race condition

----

## 2. What is Synchronization?

> Synchronization ensures only one thread accesses critical section at a time


---

## 3. synchronized Keyword

### 3.1 Method Level Synchronization

```java
class Counter {
    int count = 0;

    synchronized void increment() {
        count++;
    }
}
```

### 3.2 Block Level Synchronization (Preferred)

```java
class Counter {
    int count = 0;
    private final Object lock = new Object();

    void increment() {
        synchronized (lock) {
            count++;
        }
    }
}
```

----

## 4. How Lock Works Internally

```
Thread enters synchronized block
   ↓
Tries to acquire lock (monitor)
   ↓
If available → enters
If not → goes to BLOCKED state
   ↓
Executes critical section
   ↓
Releases lock
```

---

## 5. Example with Lock Behavior

```java
public static void main(String[] args) {
    Object lock = new Object();

    Thread t1 = new Thread(() -> {
        synchronized (lock) {
            System.out.println("T1 got lock");
            try { Thread.sleep(3000); } catch (Exception e) {}
        }
    });

    Thread t2 = new Thread(() -> {
        synchronized (lock) {
            System.out.println("T2 got lock");
        }
    });

    t1.start();
    t2.start();
}
```

**Result:**

```
T1 got lock
(after 3 sec)
T2 got lock
```

---

# 6. Intrinsic Lock (Monitor Lock)

Every Java object has an internal lock (monitor).

> synchronized(this) { }

means  -> lock on current object

------

## Without Sync (Bug)

```
Map<String, Integer> map = new HashMap<>();

// multiple threads updating
map.put("key", value); // unsafe
```

## With Sync

```
Map<String, Integer> map = Collections.synchronizedMap(new HashMap<>());
```

----

### Key Points

- synchronized ensures:
    - mutual exclusion
    - visibility (JMM guarantee)
- Lock is always tied to an object
- Only one thread can hold a lock at a time

