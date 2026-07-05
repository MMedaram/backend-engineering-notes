---
title: HTTP Client Standard API
parent: Java-11
nav_order: 2
---

# HTTP Client Standard API

Java 11 standardized the new HTTP Client API.

Package:

```
java.net.http
```

Main classes:

- `HttpClient`
- `HttpRequest`
- `HttpResponse`
- `WebSocket`

Important status:

- Incubator API in Java 9 and Java 10.
- Standard production API in Java 11.

---

## Why Was It Introduced?

Before Java 11, the JDK had `HttpURLConnection`.

Problems:

- Old API style.
- Harder to use for modern HTTP.
- No clean async support.
- No simple HTTP/2 API.

Developers often used external libraries:

- Apache HttpClient
- OkHttp
- Spring `RestTemplate`
- Spring `WebClient`

Java 11 added a modern built-in client.

---

## Basic GET Example

```
HttpClient client = HttpClient.newHttpClient();

HttpRequest request = HttpRequest.newBuilder()
    .uri(URI.create("https://api.example.com/users/1"))
    .GET()
    .build();

HttpResponse<String> response = client.send(
    request,
    HttpResponse.BodyHandlers.ofString()
);

System.out.println(response.statusCode());
System.out.println(response.body());
```

`send()` is blocking.

It throws:

- `IOException`
- `InterruptedException`

---

## Async GET Example

```
HttpClient client = HttpClient.newHttpClient();

HttpRequest request = HttpRequest.newBuilder()
    .uri(URI.create("https://api.example.com/users/1"))
    .GET()
    .build();

CompletableFuture<HttpResponse<String>> future = client.sendAsync(
    request,
    HttpResponse.BodyHandlers.ofString()
);

future.thenApply(HttpResponse::body)
    .thenAccept(System.out::println);
```

`sendAsync()` returns:

```
CompletableFuture<HttpResponse<T>>
```

---

## POST JSON Example

```java
String json = "{"
    + "\"name\":\"Mohan\","
    + "\"email\":\"mohan@test.com\""
    + "}";
```

Request:

```java
HttpRequest request = HttpRequest.newBuilder()
    .uri(URI.create("https://api.example.com/users"))
    .header("Content-Type", "application/json")
    .POST(HttpRequest.BodyPublishers.ofString(json))
    .build();

HttpResponse<String> response = client.send(
    request,
    HttpResponse.BodyHandlers.ofString()
);
```

---

## Timeout

Set client connection timeout:

```java
HttpClient client = HttpClient.newBuilder()
    .connectTimeout(Duration.ofSeconds(3))
    .build();
```

Set request timeout:

```java
HttpRequest request = HttpRequest.newBuilder()
    .uri(URI.create("https://api.example.com/users"))
    .timeout(Duration.ofSeconds(5))
    .GET()
    .build();
```

If request times out, Java can throw:

```text
HttpTimeoutException
```

---

## HTTP Version

Ask for HTTP/2:

```java
HttpClient client = HttpClient.newBuilder()
    .version(HttpClient.Version.HTTP_2)
    .build();
```

Important:

This is a preference, not a guarantee.

If server or network does not support HTTP/2, the client may use HTTP/1.1.

Check actual version:

```
System.out.println(response.version());
```

---

## Redirects

By default, redirects are not automatically followed.

Configure:

```java
HttpClient client = HttpClient.newBuilder()
    .followRedirects(HttpClient.Redirect.NORMAL)
    .build();
```

Redirect options:

- `NEVER`
- `NORMAL`
- `ALWAYS`

Use `ALWAYS` carefully because it may follow redirects across protocols.

---

## Daily Backend Use Cases

### 1. Calling An External Service

```java
public String fetchUser(Long id) throws IOException, InterruptedException {
    HttpRequest request = HttpRequest.newBuilder()
        .uri(URI.create("https://api.example.com/users/" + id))
        .timeout(Duration.ofSeconds(3))
        .GET()
        .build();

    HttpResponse<String> response = client.send(
        request,
        HttpResponse.BodyHandlers.ofString()
    );

    if (response.statusCode() >= 400) {
        throw new IllegalStateException("External API failed: " + response.statusCode());
    }

    return response.body();
}
```

### 2. Download To File

```java
HttpResponse<Path> response = client.send(
    request,
    HttpResponse.BodyHandlers.ofFile(Path.of("response.json"))
);
```

### 3. Ignore Response Body

```java
HttpResponse<Void> response = client.send(
    request,
    HttpResponse.BodyHandlers.discarding()
);
```

---

## Common Edge Cases

### Invalid URI

```
URI.create("not a valid uri");
```

Can throw:

```text
IllegalArgumentException
```

### Interrupted Blocking Request

```
client.send(request, BodyHandlers.ofString());
```

Can throw:

```text
InterruptedException
```

Best practice:

```
catch (InterruptedException e) {
    Thread.currentThread().interrupt();
    throw e;
}
```

### Async Failure

In async calls, exceptions are wrapped inside the `CompletableFuture`.

```
client.sendAsync(request, BodyHandlers.ofString())
    .exceptionally(error -> {
        System.out.println("Request failed: " + error.getMessage());
        return null;
    });
```

### Large Response Body

Avoid `ofString()` for huge responses.

Use:

```
HttpResponse.BodyHandlers.ofFile(path)
```

or streaming handlers.

---

## Java 11 HTTP Client vs Spring Clients

Use Java 11 HTTP Client when:

- You want a simple JDK-only client.
- You are writing a small tool or library.
- You do not need Spring-specific features.

Use Spring `WebClient` when:

- You are already in a Spring reactive stack.
- You need filters, codecs, and reactive integration.

Use existing company-standard clients when:

- Observability, retries, metrics, auth, and tracing are already built around them.

---

## Best Practices

- Always set timeouts.
- Check status codes; Java does not throw just because status is 404 or 500.
- Use `ofFile` or streaming for large responses.
- Reuse `HttpClient` instead of creating one for every request.
- Restore interrupt status when catching `InterruptedException`.
- Be careful with automatic redirects.
- Wrap HTTP logic in a service/client class instead of spreading it across code.

---

## Quick Summary

Java 11 made the HTTP Client a standard production API. It supports HTTP/1.1, HTTP/2, synchronous calls, asynchronous calls through `CompletableFuture`, WebSocket support, timeouts, redirects, and different body handlers.
