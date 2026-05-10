---
title: HTTP Status Codes
parent: REST
nav_order: 2
---

# HTTP Status Codes 

## 1. What are HTTP Status Codes?

HTTP status codes are **standardized responses** returned by the server to indicate the result of a client’s request.

They are critical for:

* Communication between client and server
* Debugging
* API correctness
* Proper REST design

---

## 2. Status Code Categories

| Range | Category      | Meaning            |
| ----- | ------------- | ------------------ |
| 1xx   | Informational | Rarely used        |
| 2xx   | Success       | Request successful |
| 3xx   | Redirection   | Resource moved     |
| 4xx   | Client Errors | Issue with request |
| 5xx   | Server Errors | Server failed      |

---

## 3. 2xx Success Codes (Most Common)

### 3.1 200 OK

* Standard success response
* Used for GET, PUT, PATCH

```http
GET /users/1 → 200 OK
```

---

### 3.2 201 Created

* Resource successfully created
* MUST include `Location` header

```http
POST /users → 201 Created
Location: /users/123
```

👉 Spring Example:

```
return ResponseEntity
        .created(URI.create("/users/" + user.getId()))
        .body(user);
```

---

### 3.3 204 No Content

* Successful but no response body

```http
DELETE /users/123 → 204 No Content
```

👉 Use when:

* No data needs to be returned
* Clean API design

---

## 4. 4xx Client Errors (Very Important)

### 4.1 400 Bad Request

* Invalid input / malformed request

Examples:

* JSON parsing error
* Validation failure

```
@Valid @RequestBody UserRequest request
```

---

### 4.2 401 Unauthorized

* Authentication required or failed

👉 Example:

* Missing/invalid JWT

---

### 4.3 403 Forbidden

* Authenticated but NOT allowed

👉 Example:

* User trying to access admin resource

---

### 4.4 404 Not Found

* Resource does not exist

```http
GET /users/999 → 404
```

👉 Never return 200 with "not found" message ❌

---

### 4.5 409 Conflict

* Conflict with current state

👉 Example:

* Duplicate email
* Version conflicts

---

### 4.6 422 Unprocessable Entity

* Request is valid, but cannot be processed logically

👉 Example:

* Business rule violation

---

## 5. 5xx Server Errors

### 5.1 500 Internal Server Error

* Unexpected failure

👉 Should NOT expose internal details

---

### 5.2 503 Service Unavailable

* Service temporarily down

👉 Used in:

* Circuit breakers
* Maintenance

---

## 6. Status Codes in Spring Boot

### Using @ResponseStatus

```java
@ResponseStatus(HttpStatus.NOT_FOUND)
public class UserNotFoundException extends RuntimeException {}
```

---

### Using ResponseEntity

```
return ResponseEntity.status(HttpStatus.BAD_REQUEST).body("Invalid input");
```

---

### Global Exception Handling

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<?> handleNotFound() {
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body("User not found");
    }
}
```

---

## 7. Best Practices

✅ Use correct status codes (don’t default to 200)      
✅ Be consistent across APIs      
✅ Combine with meaningful error body    
✅ Use 201 for creation     
✅ Use 204 for delete/update without response    

---

## 8. Common Mistakes (Very Important)

❌ Returning 200 for everything      
❌ Using 500 for client errors        
❌ Not differentiating 401 vs 403         
❌ Returning body with 204            
❌ Ignoring Location header in 201          

---

## 9. Real-World Mapping

| Scenario             | Status Code |
| -------------------- | ----------- |
| Fetch resource       | 200         |
| Create resource      | 201         |
| Delete resource      | 204         |
| Invalid input        | 400         |
| Not authenticated    | 401         |
| Not authorized       | 403         |
| Resource not found   | 404         |
| Duplicate / conflict | 409         |
| Server crash         | 500         |

---

## 10. Senior-Level Insights

* Status codes are part of your **API contract**
* Clients rely on them for logic
* Clean APIs use **status codes + structured error body**
* Don’t overuse exotic codes—stick to commonly understood ones

---

## 11. Summary

* Status codes communicate outcome clearly
* Correct usage improves API reliability
* Critical for microservices communication
* Misuse leads to debugging nightmares

---
