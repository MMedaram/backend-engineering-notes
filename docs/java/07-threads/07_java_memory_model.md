---
title: JavaMemoryModel, volatile
parent: Threads
nav_order: 7
---

# Java Memory Model (JMM)

## 1. Why JMM Exists

Each thread has its own working memory (CPU cache). Threads may not see the latest values from main memory.

```
Main Memory (Heap)
      ↑ ↓
Thread 1 (CPU Cache)
Thread 2 (CPU Cache)
```

---

## 2. Core Problems
- Visibility issue
- Atomicity issue
- Instruction reordering

---

## 3. Example (Visibility Problem)

```java
class FlagExample {
    static boolean flag = false;

    public static void main(String[] args) {
        new Thread(() -> {
            while (!flag) {}
            System.out.println("Stopped");
        }).start();

        flag = true;
    }
}
```

👉 Expected: thread stops

👉 Reality: ❌ may run forever

#### Why?
- Thread reads flag into its cache
- Never re-reads from main memory

👉 This is a visibility problem

---

## 4. Key Concepts

### Visibility
Ensures changes are visible across threads.

### Atomicity
Operation happens completely or not at all.

### Ordering
Execution order of instructions.

---

## 5. Happens-Before Rules

This is the heart of JMM

> If A happens-before B, then B sees effects of A

- Thread start()
  - All actions before start() are visible to new thread
  
- Thread join()
  - t.join(); 
  - All actions in thread are visible after join
  

- synchronized blocks
    - synchronized(lock) { }
    - Unlock happens-before next lock
  
- volatile variables
    - volatile boolean flag; 
    - Write → visible to all threads immediately    

---

## 6. volatile Keyword

```java
volatile boolean flag;
```

Guarantees:
- Visibility ✅
- Ordering ✅
- Atomicity ❌

---

## 7. Reordering Example

```java
int x = 0, y = 0;

Thread t1 = new Thread(() -> {
    x = 1;
    int r1 = y;
});

Thread t2 = new Thread(() -> {
    y = 1;
    int r2 = x;
});
```

Possible output:

r1 = 0, r2 = 0 

👉 Due to instruction reordering

Possible unexpected output due to reordering.

---

## 8. synchronized Guarantees

```
synchronized(lock) {
    // critical section
}
```

- Visibility
- Atomicity
- Ordering

---

## 9. When to Use

| Problem                 | Solution                        |
| ----------------------- | ------------------------------- |
| Only visibility needed  | `volatile`                      |
| Atomic operation needed | `synchronized` / Atomic classes |
| Complex logic           | Locks                           |

---

## 10. Common Mistakes

- Using volatile for counters
- Ignoring memory visibility
- Assuming execution order

---

## Final Insight

```
Thread 1 updates value
        ↓
Without sync → stays in cache ❌
With volatile/sync → goes to main memory ✅
        ↓
Other threads see updated value
```

JMM ensures predictable behavior across multi-threaded environments.
