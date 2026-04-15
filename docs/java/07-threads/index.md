---
title: Threads
parent: Java
nav_order: 7
---


```java

 Thread t = new Thread(() -> {
            System.out.println("thread");
 });

```

**Thread Methods:**

- getState()
- start()
- run()
- getName()
- setName()
- wait()
- join()
- setDaemon()
- setPriority()

### sleep()

- Pauses the current thread for a fixed time
- Does NOT release lock
- Belongs to Thread class


### wait()

- Makes thread wait until another thread notifies it
- Must be inside synchronized
- Releases lock
- Belongs to Object class


### join()

- Current thread waits until another thread completes
- Does NOT need synchronized
- Internally uses wait()

| Feature            | sleep()        | wait()          | join()           |
| ------------------ | -------------- | --------------- | ---------------- |
| Purpose            | Pause for time | Wait for signal | Wait for thread  |
| Class              | Thread         | Object          | Thread           |
| Lock release       | ❌ No           | ✅ Yes           | ✅ Yes (indirect) |
| Needs synchronized | ❌ No           | ✅ Yes           | ❌ No             |
| Wake-up            | Time ends      | notify()        | thread ends      |



