---
title: Creating Threads
parent: Threads
nav_order: 2
---

# Creating Threads

---

## Way 1: Extending Thread (Not recommended)

```java

class MyThread extends Thread {
    @Override
    public void run() {
        System.out.println("Thread running: " + Thread.currentThread().getName());
    }
}

public class Main {
    public static void main(String[] args) {
        MyThread t1 = new MyThread();
        t1.start(); // always use start()
    }
}

```

### Problem:

- Java doesn’t support multiple inheritance
- Not flexible

---

## Way 2: Using Runnable (Recommended)

```java

class MyTask implements Runnable {
    @Override
    public void run() {
        System.out.println("Running task in: " + Thread.currentThread().getName());
    }
}

public class Main {
    public static void main(String[] args) {
        Thread t1 = new Thread(new MyTask());
        t1.start();
    }
}
```

---

## Way 3: Using Lambda (Modern + Clean)

```java
public class Main {
    public static void main(String[] args) {

        Thread t1 = new Thread(() -> {
            System.out.println("Lambda thread: " + Thread.currentThread().getName());
        });

        t1.start();
    }
}
```

