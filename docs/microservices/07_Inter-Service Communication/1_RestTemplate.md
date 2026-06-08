---
title: RestTemplate
parent: Inter-Service Communication
nav_order: 1
---

# RestTemplate in Spring Boot

# 1. What is RestTemplate?

RestTemplate is Spring's traditional synchronous HTTP client used for inter-service communication.

It follows:

```text
Request -> Wait -> Response
```

The calling thread is blocked until the response is received.

---

# 2. Current Status

Spring Framework Recommendation:

```text
RestTemplate = Legacy / Maintenance Mode
RestClient = New Synchronous Client
WebClient = Reactive / Async Client
```

However:

RestTemplate is still heavily used in:

* Banking applications
* Enterprise monoliths
* Existing microservices
* Legacy Spring Boot systems

Knowing RestTemplate is mandatory because most large organizations still have it in production.

---

# 3. Internal Architecture

When:

```
restTemplate.getForObject(...)
```

is executed, internally:

```text
RestTemplate
    ↓
ClientHttpRequestFactory
    ↓
HTTP Client (Apache HttpClient/JDK)
    ↓
Remote Service
    ↓
Response
    ↓
HttpMessageConverter
    ↓
Java Object
```

Key Components:

| Component                | Purpose                       |
|--------------------------|-------------------------------|
| RestTemplate             | Main API                      |
| ClientHttpRequestFactory | Creates HTTP requests         |
| HttpMessageConverter     | JSON ↔ Java conversion        |
| ResponseErrorHandler     | Handles HTTP errors           |
| Interceptors             | Request/Response interception |

---

# 4. Bean Configuration

Recommended:

```java
@Configuration
public class RestTemplateConfig {

    @Bean
    public RestTemplate restTemplate(
            RestTemplateBuilder builder) {

        return builder.build();
    }
}
```

Never create:

```
new RestTemplate()
```

everywhere.

Use Spring-managed singleton beans.

---

# 5. GET Operations (READ)

## Method 1 - getForObject()

Returns only response body.

```java
CustomerResponse customer =
        restTemplate.getForObject(
                "http://customer-service/customers/{id}",
                CustomerResponse.class,
                customerId);
```

---

## Method 2 - getForEntity()

Returns full response.

```java
ResponseEntity<CustomerResponse> response =
        restTemplate.getForEntity(
                "http://customer-service/customers/{id}",
                CustomerResponse.class,
                customerId);
```

Useful when you need:

* Status code
* Headers
* Response body

---

# Banking Example

Payment Service:

```text
Payment Service
      ↓
Customer Service
```

Before payment:

* Verify customer exists
* Verify KYC status
* Verify account state

---

# 6. POST Operations (CREATE)

## postForObject()

Returns created object.

```java
CreateAccountResponse response =
        restTemplate.postForObject(
                "http://account-service/accounts",
                request,
                CreateAccountResponse.class);
```

---

## postForEntity()

Returns ResponseEntity.

```java
ResponseEntity<CreateAccountResponse> response =
        restTemplate.postForEntity(
                "http://account-service/accounts",
                request,
                CreateAccountResponse.class);
```

---

## postForLocation()

Returns Location header.

```java
URI location =
        restTemplate.postForLocation(
                "http://account-service/accounts",
                request);
```

---

# Banking Examples

POST commonly used for:

* Open Account
* Create Beneficiary
* Apply Loan
* Create Transfer Request
* Create Fixed Deposit

---

# 7. PUT Operations (UPDATE)

Used for full replacement updates.

```
restTemplate.put(
        "http://customer-service/customers/{id}",
        request,
        customerId);
```

Returns:

```
void
```

---

# Banking Examples

PUT used for:

* Update customer profile
* Update address
* Update nominee details
* Update account preferences

---

# 8. PATCH Operations (PARTIAL UPDATE)

Used for partial updates.

```java
ProfileResponse response =
        restTemplate.patchForObject(
                "http://customer-service/profile/{id}",
                request,
                ProfileResponse.class,
                customerId);
```

---

# Banking Examples

PATCH used for:

* Update mobile number
* Update email
* Change notification preference
* Change communication settings

---

# Important

PATCH requires HTTP client support.

Apache HttpClient is commonly used.

---

# 9. DELETE Operations

```
restTemplate.delete(
        "http://beneficiary-service/beneficiaries/{id}",
        beneficiaryId);
```

---

# Banking Examples

DELETE used for:

* Delete beneficiary
* Cancel draft transaction
* Delete standing instruction
* Remove scheduled transfer

---

# 10. HEAD Operations

Retrieve only headers.

```java
HttpHeaders headers =
        restTemplate.headForHeaders(
                "http://account-service/accounts/{id}",
                accountId);
```

---

# Banking Use Cases

* Check ETag
* Check metadata
* Verify existence
* Check last-modified timestamps

---

# 11. OPTIONS Operations

Find supported HTTP methods.

```java
Set<HttpMethod> methods =
        restTemplate.optionsForAllow(
                "http://customer-service/customers/{id}",
                customerId);
```

---

# Use Cases

* API discovery
* Dynamic clients
* Troubleshooting

---

# 12. exchange() - Most Powerful API

Most enterprise projects eventually use:

```
exchange()
```

because it supports:

* Any HTTP method
* Custom headers
* Generic types
* Full response control

---

# Example

```java
ResponseEntity<CustomerResponse> response =
        restTemplate.exchange(
                "http://customer-service/customers/{id}",
                HttpMethod.GET,
                null,
                CustomerResponse.class,
                customerId);
```

---

# Why exchange() is Popular

Supports:

* Authorization headers
* Correlation IDs
* Custom media types
* Generic collections

---

# 13. Custom Headers

Very common in microservices.

```
HttpHeaders headers = new HttpHeaders();

headers.setBearerAuth(token);

headers.add("X-Correlation-ID",correlationId);

headers.add("X-Tenant-ID", tenantId);
```

---

# Request Entity

```java
HttpEntity<TransferRequest> entity =
        new HttpEntity<>(
                request,
                headers);
```

---

# Exchange Example

```java
ResponseEntity<TransferResponse> response =
        restTemplate.exchange(
                url,
                HttpMethod.POST,
                entity,
                TransferResponse.class);
```

---

# Enterprise Headers

| Header             | Purpose                |
|--------------------|------------------------|
| Authorization      | JWT                    |
| X-Correlation-ID   | Tracing                |
| X-Tenant-ID        | Multi-tenancy          |
| Idempotency-Key    | Duplicate prevention   |

---

# 14. Generic Collection Responses

Problem:

```
List<CustomerResponse>
```

cannot be directly handled.

---

# Solution

```java
ResponseEntity<List<CustomerResponse>> response =
        restTemplate.exchange(
                url,
                HttpMethod.GET,
                null,
                new ParameterizedTypeReference<List<CustomerResponse>>() {});
```

---

# 15. Error Handling

Default behavior:

```text
4xx -> Exception
5xx -> Exception
```

---

# Common Exceptions

| Exception                  | Meaning              |
|----------------------------|----------------------|
| HttpClientErrorException   | 4xx                  |
| HttpServerErrorException   | 5xx                  |
| ResourceAccessException    | Timeout/Connection   |
| RestClientException        | Generic              |

---

# Banking Example

Customer Service unavailable:

```text
503 Service Unavailable
```

↓

```
HttpServerErrorException
```

---

# 16. Custom ResponseErrorHandler

Centralized error handling.

```java
@Component
public class CustomErrorHandler implements ResponseErrorHandler {

    @Override
    public boolean hasError(
            ClientHttpResponse response) {

        return response
                .getStatusCode()
                .isError();
    }

    @Override
    public void handleError(
            ClientHttpResponse response) {

        throw new CustomerServiceException();
    }
}
```

---

# 17. Interceptors

Equivalent of Filters for outgoing calls.

Used for:

* Logging
* JWT propagation
* Correlation IDs
* Metrics

---

# Example

```java
public class LoggingInterceptor
        implements ClientHttpRequestInterceptor {
}
```

---

# 18. Timeouts (VERY IMPORTANT)

Never use defaults.

Configure:

```java
@Bean
public RestTemplate restTemplate(
        RestTemplateBuilder builder) {

    return builder
            .setConnectTimeout(
                    Duration.ofSeconds(2))
            .setReadTimeout(
                    Duration.ofSeconds(5))
            .build();
}
```

---

# Banking Importance

Without timeout:

```text
Fraud Service hangs
    ↓
Transfer Service hangs
    ↓
Customer waits forever
```

---

# 19. Real Banking Transfer Flow

```text
Transfer Service
      ↓
Customer Service
      ↓
Account Service
      ↓
Limit Service
      ↓
Fraud Service
      ↓
Transfer Success
```

Every call may be:

```
RestTemplate
```

based.

---

# 20. Common Anti-Patterns

## ❌ Creating RestTemplate Everywhere

Bad:

```
new RestTemplate()
```

---

## ❌ No Timeouts

Causes hanging requests.

---

## ❌ No Error Handling

Results in ugly exceptions.

---

## ❌ No Correlation IDs

Makes production debugging difficult.

---

## ❌ Returning Raw Exceptions

Security risk.

---

# 21. Enterprise Best Practices

✅ Singleton RestTemplate Bean

✅ Configure Timeouts

✅ Use exchange() for advanced scenarios

✅ Use Interceptors

✅ Use Correlation IDs

✅ Centralize Error Handling

✅ Use ParameterizedTypeReference for collections

✅ Use DTOs

---

# 22. RestTemplate vs Modern Alternatives

| Feature                 | RestTemplate   | WebClient   | Feign       |
|-------------------------|----------------|-------------|-------------|
| Blocking                | Yes            | No          | Yes         |
| Reactive                | No             | Yes         | No          |
| Simplicity              | High           | Medium      | Very High   |
| Enterprise Usage        | High           | High        | Very High   |
| Future Recommendation   | No             | Yes         | Yes         |

---

# 23. Key Takeaway

RestTemplate is:

* synchronous
* blocking
* mature
* still heavily used in enterprise systems

