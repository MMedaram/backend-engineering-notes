---
title: Simple Web Server
parent: Java-18
nav_order: 2
---

# Java 18 - Simple Web Server

## What is the Simple Web Server?

The **Simple Web Server** is a lightweight HTTP server introduced in Java 18.

It is a built-in tool that allows Java developers to quickly serve **static files** from a directory without installing any external software.

It is designed for:

* learning
* local development
* quick testing
* demos
* serving static websites

It is **not** designed for enterprise applications.

---

# Simple Meaning

Imagine you have a folder like this:

```text
website/
    index.html
    style.css
    app.js
    logo.png
```

Normally, if you double-click `index.html`, some browser features may not work correctly.

Instead, you start Java's Simple Web Server.

Now Java acts as a small web server and serves these files through a browser.

---

# Why was it introduced?

Before Java 18, developers often needed another tool just to test a simple HTML page.

Common options were:

* Python HTTP Server
* Node.js
* Apache HTTP Server
* Nginx
* Spring Boot
* Tomcat

For a simple HTML page, these options were unnecessary.

Java 18 introduced a built-in solution.

---

# What problem does it solve?

Suppose you are learning HTML.

You create

```text
index.html
```

You want to open

```text
http://localhost
```

Instead of installing another server, Java can serve the page directly.

It saves time.

---

# Real-world Example

Suppose your frontend developer gives you

```text
login.html
```

Before integrating it with Spring Boot, you simply want to verify:

* page loads
* CSS works
* images load
* JavaScript works

Simple Web Server is perfect.

---

# Banking Example

Suppose a banking team creates

* online help page
* user guide
* API documentation
* HTML prototype
* sample UI screen

Developers can quickly preview these pages locally using the Simple Web Server.

However,

The actual banking application still runs on

* Spring Boot
* Tomcat
* Jetty
* Kubernetes
* Application Server

Simple Web Server is only a development helper.

---

# What can it serve?

It serves static files such as

* HTML
* CSS
* JavaScript
* Images
* PDF
* JSON
* Text files

---

# What it cannot do

It cannot

* connect to database
* execute Java business logic
* create REST APIs
* perform authentication
* process transactions
* replace Spring Boot
* replace Tomcat

Think of it as a **file server**, not an application server.

---

# How to Use

## Step 1

Create a folder

```text
website/
```

Inside it

```text
website/

index.html
style.css
app.js
```

---

## Step 2

Open terminal

Navigate to the folder

```bash
cd website
```

---

## Step 3

Start the server

```bash
jwebserver
```

By default

* Current folder is served
* Port = **8000**

---

## Step 4

Open browser

```text
http://localhost:8000
```

or

```text
http://127.0.0.1:8000
```

You will see

```text
index.html
```

---

# Serve Another Folder

Suppose

```text
C:\Projects\MyWebsite
```

Run

```bash
jwebserver -d C:\Projects\MyWebsite
```

Now Java serves files from that folder.

---

# Change Port

Default

```text
8000
```

Suppose another application is already using it.

Run

```bash
jwebserver -p 9000
```

Now open

```text
http://localhost:9000
```

---

# Bind to All Network Interfaces

Normally

Only your computer can access it.

To allow another computer on the same network

```bash
jwebserver -b 0.0.0.0
```

Use this carefully because other devices can now access the shared folder.

---

# Stop the Server

Simply press

```text
Ctrl + C
```

---

# Common Command Options

| Command      | Purpose            |
| ------------ | ------------------ |
| `jwebserver` | Start server       |
| `-d`         | Directory to serve |
| `-p`         | Port number        |
| `-b`         | Bind address       |
| `-o`         | Output level       |
| `-h`         | Help               |

---

# Complete Example

Folder

```text
website/

index.html

style.css

logo.png
```

index.html

```html
<!DOCTYPE html>
<html>
<body>

<h1>Hello Java 18</h1>

</body>
</html>
```

Run

```bash
cd website

jwebserver
```

Open

```text
http://localhost:8000
```

Output

```
Hello Java 18
```

No Spring Boot.

No Tomcat.

No Apache.

Just Java.

---

# Advantages

* Built into Java
* Very easy to use
* No installation
* Perfect for learning
* Good for demos
* Good for testing static files

---

# Limitations

* Static files only
* No database
* No backend logic
* No REST APIs
* No authentication
* No HTTPS
* No HTTP/2
* Not for production
* Not a replacement for Spring Boot

---

# Negative Cases / Common Mistakes

## 1. Thinking it replaces Spring Boot

Wrong.

It cannot run business logic.

---

## 2. Deploying production applications

Wrong.

It is only for development and testing.

---

## 3. Expecting REST APIs

Wrong.

It only serves files.

---

## 4. Expecting database connectivity

Wrong.

It cannot execute Java backend code.

---

## 5. Exposing sensitive folders

If you bind using

```bash
jwebserver -b 0.0.0.0
```

other machines may access the shared directory.

Never expose confidential files.

---

# When Should You Use It?

Use it for

* HTML practice
* CSS testing
* JavaScript testing
* UI demos
* Documentation preview
* Static website preview

---

# When Should You NOT Use It?

Do not use it for

* Spring Boot applications
* Banking applications
* Microservices
* REST APIs
* Authentication
* Database applications
* Enterprise deployments

---

# Developer Best Practice

Simple rule

> **Static files → Simple Web Server**

> **Business logic → Spring Boot**

---


---

# Quick Revision

* Introduced in Java 18
* Built-in lightweight HTTP server
* Started using `jwebserver`
* Default port is **8000**
* Serves static files only
* Useful for testing and demos
* No backend logic
* No REST APIs
* No database support
* Not for production

---

# One-line Summary

The Java 18 Simple Web Server is a lightweight built-in HTTP server that quickly serves static files for local development, testing, and demonstrations without requiring Spring Boot or any external web server.
