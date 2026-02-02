---
title: JVM Architecture
parent: Java
nav_order: 1
---

# JVM Architecture

**JVM (Java Virtual Machine)** is a virtual machine that:

- Executes Java **bytecode**
- Manages **memory**
- Handles **class loading**
- Provides **platform independence**

Java code is written once and runs anywhere because of JVM.

---

## High-Level JVM Architecture

JVM consists of **four main components**:

1. Class Loader Subsystem
2. Runtime Data Areas (Memory)
3. Execution Engine
4. Native Interface & Native Libraries

---

## 1️⃣ Class Loader Subsystem

- Loads `.class` files into JVM
- Converts bytecode into JVM internal format

### Steps involved
1. **Loading**
    - Reads `.class` file
    - Creates Class object

2. **Linking**
    - **Verification** → checks bytecode safety
    - **Preparation** → allocates memory for static variables
    - **Resolution** → resolves symbolic references

3. **Initialization**
    - Executes static blocks
    - Assigns actual static values

### Types of Class Loaders
- **Bootstrap ClassLoader** → loads core Java classes
- **Extension / Platform ClassLoader**
- **Application ClassLoader** → loads application classes

### Interview Point
> JVM follows **Parent Delegation Model**

---

## 2️⃣ Runtime Data Areas (Memory)

This is where data is stored while the program runs.


### 2.1 Method Area (Shared)

Stores:
- Class metadata
- Static variables
- Method bytecode
- Runtime constant pool

✔ Shared across all threads

### 2.2 Heap (Shared)

Stores:
- Objects
- Arrays

Characteristics:
- Shared across threads
- Managed by **Garbage Collector**
- Large memory area
- Can cause **OutOfMemoryError**

> Objects created using `new` are stored in heap.


### 2.3 Stack (Thread-Specific)

Stores:
- Method calls
- Local variables
- Method parameters
- Reference variables

Characteristics:
- One stack per thread
- Fast access
- Follows **LIFO**
- Can cause **StackOverflowError**

> Stack stores execution context, not objects.


### 2.4 PC Register (Thread-Specific)

Stores:
- Address of the current instruction being executed

Used by JVM to know:
- Which instruction to execute next


### 2.5 Native Method Stack

Stores:
- Native (C/C++) method calls

Used when Java interacts with OS-level code.

---

## 3️⃣ Execution Engine

Responsible for executing bytecode.

### Components

#### a) Interpreter
- Executes bytecode line by line
- Slower
- Faster startup

#### b) JIT Compiler (Just-In-Time)
- Converts frequently executed bytecode into native machine code
- Improves performance

#### c) Garbage Collector
- Removes unused objects from heap
- Runs automatically
- Frees memory

### Interview Point
> JVM first interprets code, then JIT optimizes hot code.

---

## 4️⃣ Native Interface & Libraries

### Java Native Interface (JNI)
- Allows Java to call native code (C/C++)
- Used for system-level operations

### Native Libraries
- Platform-specific libraries

---

## JVM Execution Flow

1. Java source code is compiled into bytecode
2. Class Loader loads the class
3. Memory is allocated in runtime data areas
4. Execution Engine runs the bytecode
5. Garbage Collector cleans unused objects
6. Native calls handled via JNI

---

- **Stack** → Method calls
- **Heap** → Objects
- **Method Area** → Class info

---


> **JVM loads classes using ClassLoader, stores data in runtime memory areas, executes bytecode using Execution Engine, and manages memory using Garbage Collector.**
