---
title: Logging, Monitoring, and Tracing
parent: API Gateway
nav_order: 3
---

# Logging, Monitoring, and Tracing

> Metrics help detect a problem. Traces help locate it. Logs help explain it.

The gateway observes traffic that passes through it. Backend services also need
their own instrumentation: gateway logs cannot explain every database query,
business operation, or background job inside those services.

These notes use one instance of each banking application. The Spring Boot
configuration targets **Boot 3.5.x with compatible Spring Cloud dependencies**.
Boot 4 tracing dependencies and properties can differ; check the project's
versions before implementation.

## Learning outcomes

- Distinguish logs, metrics, traces, dashboards, and alerts.
- Configure useful Spring Boot logs and management endpoints.
- Collect metrics from every service with Prometheus.
- Use Grafana to inspect traffic, errors, and latency.
- Correlate logs and requests across services using tracing.
- Define meaningful business metrics without exposing sensitive data.

---

## 1. Three types of information

| Type | Question answered | Banking example |
| --- | --- | --- |
| Logs | What happened? | A notification provider request failed. |
| Metrics | How often, how much, or how slow? | Account API p95 latency increased to 700 ms. |
| Traces | Where did one request spend its time? | Gateway → Account Service → Customer Service. |

If an account request becomes slow:

1. Metrics reveal rising latency.
2. A trace identifies the slow downstream operation.
3. Logs help explain the operation or failure.

A dashboard displays measurements. An alert actively notifies operators when a
condition needs attention. Logging and monitoring are useful even with only one
instance per service because requests can cross multiple applications.

---

## 2. Example deployment

Assume separate application processes on one machine:

| Application | Port |
| --- | ---: |
| API Gateway | 8080 |
| Config Service | 8888 |
| Branch Service | 8101 |
| Customer Service | 8102 |
| Account Service | 8103 |
| Notification Service | 8104 |
| Eureka Server | 8761 |

### Tools and responsibilities

| Tool | Responsibility |
| --- | --- |
| SLF4J + Logback | Write application logs. |
| Spring Boot Actuator + Micrometer | Provide health information and application metrics. |
| Prometheus | Periodically collect and retain metrics. |
| Grafana | Query a metrics backend and display dashboards. |
| Micrometer Tracing | Instrument and propagate trace context. |
| Zipkin, in this example | Receive and display exported traces. |
| Log collector and logging backend | Ship and centrally search logs when needed. |

Monitoring tools are separate processes/services. Adding Actuator does not
install Prometheus, Grafana, Zipkin, or a central logging system.

---

## 3. Write meaningful application logs

Spring Boot's usual starters provide logging support. In an existing
Notification Service, declare a logger:

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

// Inside NotificationService:
private static final Logger log =
        LoggerFactory.getLogger(NotificationService.class);
```

After a provider call produces a result:

```
log.info(
        "event=notification_provider_result channel={} outcome={}",
        "EMAIL",
        "ACCEPTED"
);
```

For a failure needing investigation:

```
log.error(
        "event=notification_provider_failure channel={}",
        "EMAIL",
        exception
);
```

These are snippets to place around actual operations. Provider acceptance does
not prove customer delivery. Log the outcome that is known.

### Useful events by application

| Application | Examples |
| --- | --- |
| Gateway | HTTP access, routing failure, upstream timeout |
| Config Service | Configuration-source failure, startup failure |
| Branch Service | Important branch updates and unexpected failures |
| Customer Service | Operation outcomes and integration failures |
| Account Service | Account-operation outcomes and transaction failures |
| Notification Service | Provider outcomes, retries, permanent failures |
| Eureka | Registration and connectivity problems |

Use parameterized `{}` logging. Do not log passwords, access tokens, complete
account numbers, or entire banking request bodies. Avoid duplicate stack traces
at every layer; log an exception where it is meaningfully handled or surfaced.

Operational logs do not automatically replace a durable business audit trail.

---

## 4. Configure readable logs

Customer Service example:

```yaml
spring:
  application:
    name: customer-service

server:
  port: 8102

logging:
  level:
    root: INFO
    com.example.bank: INFO

  pattern:
    console: "%d{yyyy-MM-dd HH:mm:ss.SSS} %-5level [${spring.application.name}] [trace=%X{traceId:-} span=%X{spanId:-}] %logger{36} - %msg%n"
```

Replace the package with the project's actual package. Apply the common format
to other applications, changing their names and ports.

Example log, with shortened illustrative trace identifiers:

```text
INFO [notification-service] [trace=abc123 span=def456]
NotificationService - event=notification_provider_result channel=EMAIL outcome=ACCEPTED
```

Trace fields are populated only when tracing context is available. Startup logs
and unrelated background work may have no trace context.

| Level | Use |
| --- | --- |
| DEBUG | Detailed diagnostics, enabled selectively |
| INFO | Meaningful normal operations |
| WARN | Unexpected condition that is handled or needs attention |
| ERROR | Failure requiring investigation |

Avoid global DEBUG logging in production. It can create excessive output and
expose sensitive details. JSON logging is useful for central collection, but
readable console logs are a good first learning step.

---

## 5. Gateway access logs

For a WebFlux gateway running on Reactor Netty, start it with this JVM property:

```powershell
java -Dreactor.netty.http.server.accessLogEnabled=true -jar api-gateway.jar
```

This is a **Java system property**, rather than an ordinary Spring Boot YAML
property. Inspect the configured access-log format for the available fields.

Access logs help show which HTTP requests arrived and what responses were
returned. Business logs explain what happened inside a service. Be careful with
paths and query strings if they can contain sensitive data.

---

## 6. Add Actuator and Prometheus support

Add these to each application you want to monitor:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>

<dependency>
    <groupId>io.micrometer</groupId>
    <artifactId>micrometer-registry-prometheus</artifactId>
</dependency>
```

Use the versions supplied by compatible Spring Boot dependency management.

Merge this configuration into each application's existing YAML:

```yaml
management:
  endpoints:
    web:
      exposure:
        include: health,info,metrics,prometheus

  endpoint:
    health:
      show-details: never
      probes:
        enabled: true

  metrics:
    tags:
      application: ${spring.application.name}

    distribution:
      percentiles-histogram:
        http.server.requests: true
```

### Endpoints

| Endpoint | Purpose |
| --- | --- |
| `/actuator/health` | Application health information |
| `/actuator/health/readiness` | Application readiness state |
| `/actuator/metrics` | Diagnostic inspection of registered metrics |
| `/actuator/prometheus` | Prometheus-format metrics for scraping |

Example:

```http
GET http://localhost:8102/actuator/health
```

```json
{"status":"UP"}
```

Example HTTP metric inspection:

```http
GET http://localhost:8102/actuator/metrics/http.server.requests
```

Endpoint exposure and authorization are separate. Spring Security may require
credentials or deny these requests. Allow the monitoring system appropriate
access and keep management endpoints off public gateway routes. Do not broadly
disable application security to make scraping work.

Readiness and `UP` status do not automatically prove every business workflow
works. Define any additional checks according to what the application needs to
serve, without making liveness depend on every downstream service.

---

## 7. Automatic metrics and gateway-specific visibility

Depending on the stack and integrations, Actuator/Micrometer can provide:

- HTTP request counts and durations
- JVM heap usage and garbage-collection measurements
- Thread counts and process CPU usage
- Database connection-pool measurements

You do not need a manual timer around every controller. Check existing automatic
instrumentation before adding annotations or custom observations.

With Actuator, Spring Cloud Gateway also provides a timer:

```text
spring.cloud.gateway.requests
```

Its dimensions include route ID, method, and status. This helps distinguish
Customer-route traffic from Account-route traffic. Its scope is gateway route
processing; do not assume it captures every earlier security rejection or all
backend work outside the request.

### Keep metric labels bounded

Useful labels include application, route, method, outcome, and status. Avoid
customer IDs, account IDs, trace IDs, arbitrary URLs, or tokens as metric labels.
An unbounded number of values creates excessive time series and monitoring cost.

---

## 8. Prometheus collects directly from each application

For a learning environment with Prometheus and all applications running directly
on the same machine, use this example `prometheus.yml`:

```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: banking-services
    metrics_path: /actuator/prometheus

    static_configs:
      - targets:
          - localhost:8080
          - localhost:8888
          - localhost:8101
          - localhost:8102
          - localhost:8103
          - localhost:8104
          - localhost:8761
```

Every target must provide an accessible Prometheus endpoint. Configure scraper
authentication/TLS if the endpoints require them.

Prometheus scrapes each application directly; it does not need to route every
scrape through the API Gateway. Eureka discovery is not required for this small
static target list.

If Prometheus runs in a container, its `localhost` refers to that container.
Change targets to addresses reachable from the scraper. The same rule applies
to Zipkin and other separate tools.

---

## 9. Grafana dashboards and example queries

Run Grafana and add Prometheus as its data source.

| Panel | What it reveals |
| --- | --- |
| Target availability | Which endpoints Prometheus can scrape |
| Requests/second | Traffic volume per application |
| HTTP errors | Which APIs are failing |
| p95 latency | Which operations are slowing down |
| JVM memory and GC | Whether memory pressure contributes |
| Database connection pool | Whether requests are waiting for connections |

### Scrape availability

```promql
up{job="banking-services"}
```

`1` means the scrape succeeded; `0` means it failed. This is scrape health, not
proof that all banking operations succeed. This metric is labeled by the scrape
target; it does not automatically inherit the application's common metric tags.

### Customer Service request rate

```promql
sum(
  rate(
    http_server_requests_seconds_count{
      application="customer-service"
    }[5m]
  )
)
```

### Customer Service p95 latency

```promql
histogram_quantile(
  0.95,
  sum by (le) (
    rate(
      http_server_requests_seconds_bucket{
        application="customer-service"
      }[5m]
    )
  )
)
```

The result is in seconds and requires histogram buckets. This query aggregates
the selected HTTP traffic; add appropriate filters to investigate a specific
endpoint or exclude management requests. Inspect actual exported metrics and
labels before building dashboards.

---

## 10. Trace one request across services

Suppose Account Service calls Customer Service during a request:

```text
Gateway → Account Service → Customer Service
```

A trace links those operations. Each participating operation has a span; related
spans share a trace ID. A request does not visit every service just because all
services are registered in Eureka.

| Application | Trace identity | Operation |
| --- | --- | --- |
| Gateway | Shared trace ID | Proxy the account request |
| Account Service | Same trace ID | Process the account request |
| Customer Service | Same trace ID | Process a downstream customer lookup |

### Boot 3.5 example: Brave and Zipkin

Add these alongside Actuator in participating applications:

```xml
<dependency>
    <groupId>io.micrometer</groupId>
    <artifactId>micrometer-tracing-bridge-brave</artifactId>
</dependency>

<dependency>
    <groupId>io.zipkin.reporter2</groupId>
    <artifactId>zipkin-reporter-brave</artifactId>
</dependency>
```

Merge these entries into the existing `management` block:

```yaml
management:
  tracing:
    sampling:
      probability: 1.0

  zipkin:
    tracing:
      endpoint: http://localhost:9411/api/v2/spans
```

Run a reachable Zipkin server at the configured endpoint. Sampling `1.0` records
all eligible traces for learning. Choose production sampling according to cost,
traffic, and diagnostic requirements. These Boot 3.5 dependencies/properties
should not be assumed identical in Boot 4.

### Preserve propagation

- Use Spring Boot's instrumented HTTP client builders.
- Preserve observability when adding discovery/load-balancing customizations.
- Verify gateway-to-service trace propagation with an actual request.
- Verify asynchronous jobs and messaging separately; arbitrary background tasks
  do not automatically inherit all request context.

Manually constructing clients can bypass automatic instrumentation. Eureka and
load balancing find an address; they do not independently guarantee tracing.

---

## 11. Add a meaningful business metric

HTTP `200` does not necessarily prove a notification reached a customer. Measure
provider attempts and delivery confirmations according to their actual meaning.

```java
import io.micrometer.core.instrument.MeterRegistry;
import org.springframework.stereotype.Component;

@Component
public class NotificationMetrics {

    private final MeterRegistry registry;

    public NotificationMetrics(MeterRegistry registry) {
        this.registry = registry;
    }

    public void recordProviderAttempt(boolean successful) {
        registry.counter(
                "bank.notifications.provider.attempts",
                "outcome",
                successful ? "success" : "failure"
        ).increment();
    }
}
```

Invoke this after an actual provider attempt produces a result. It counts
attempts, including retries, with two bounded outcome labels. It does not count
confirmed customer deliveries. Metrics also are not a durable accounting ledger:
in-process counters and scrape intervals have different guarantees from a
committed business record.

For other services, define business metrics around real operational questions
and clearly specified success boundaries.

---

## 12. Central logging and alerts

### Central logging

Separate consoles/files are sufficient for initial learning. In a deployed
environment:

1. Applications write logs to stdout or managed files.
2. A collector ships them to a logging backend.
3. Operators search by application, event, time, and trace ID.

Loki or Elasticsearch are examples of logging backends. A compatible collector
and retention policy are also needed. Actuator does not centralize log files.

### Alerts

Examples to define from SLOs and normal workload:

- A scrape endpoint remains unreachable.
- Account API error rate rises.
- Customer API p95 latency exceeds its target.
- Database connection-pool waiting increases.
- Notification provider errors or queue age rise.
- Gateway rate-limit rejections increase unexpectedly.

Prometheus alert rules and an alerting destination, commonly via Alertmanager,
or Grafana alerting can turn measurements into notifications. Running a
dashboard alone does not notify an operator. Define ownership and an action for
each alert.

---

## 13. Common mistakes

- Instrumenting only the gateway and missing service internals.
- Expecting Actuator to install dashboards or central logging.
- Scraping the diagnostic `/actuator/metrics` endpoint instead of `/actuator/prometheus`.
- Exposing all management endpoints publicly.
- Logging tokens, credentials, full banking payloads, or sensitive identifiers.
- Using unbounded customer/account IDs as metric labels.
- Creating duplicate metrics around already instrumented operations.
- Losing trace context through manually constructed clients or background tasks.
- Assuming HTTP success equals business completion or final notification delivery.
- Treating metrics/logs as a replacement for durable financial audit records.
- Building dashboards without actionable alerts.

---

## Practice questions and reference answers

### 1. Is gateway logging enough to monitor all services?

**Answer:** No. It sees gateway traffic and upstream behavior, but cannot
explain all database, business, or background processing inside each service.
Instrument those services too.

### 2. Prometheus shows `up = 1`. Does that prove account transfers work?

**Answer:** No. It proves the metrics scrape succeeded. Use request outcomes,
business measurements, appropriate health checks, and end-to-end checks to
assess business functionality.

### 3. Why use a trace ID when every service has only one instance?

**Answer:** Many requests can run simultaneously across different services.
The trace ID links the operations belonging to one request, regardless of
instance count.

### 4. Can we add `accountId` as a metric label to investigate each account?

**Answer:** Avoid that. It creates unbounded time-series cardinality and can
expose sensitive data. Use bounded metric dimensions; investigate specific
operations through approved logs, traces, and business audit records.

### 5. A notification endpoint returns `200`, but email delivery fails later. How do we see it?

**Answer:** Track asynchronous job/provider outcomes, retries, failures, and
queue age. Gateway HTTP metrics alone cannot represent later delivery results.

### 6. How do we investigate a slow Account request?

**Answer:** Detect increased latency in metrics, inspect a representative trace
to locate the slow operation, and examine correlated service logs. Compare
database pool, dependency latency, CPU, and memory signals as relevant.

## Hands-on learning sequence

1. Add meaningful logs and a consistent service-identifying format.
2. Enable Actuator and inspect the available measurements.
3. Connect Prometheus and Grafana.
4. Trace one real gateway-to-service request flow.
5. Introduce a controlled failure or delay in a test environment.
6. Explain the result using metrics, a trace, and relevant logs.

## References

- [Spring Boot 3.5 Metrics](https://docs.spring.io/spring-boot/3.5/reference/actuator/metrics.html)
- [Spring Boot 3.5 Prometheus Endpoint](https://docs.spring.io/spring-boot/3.5/api/rest/actuator/prometheus.html)
- [Spring Boot 3.5 Tracing](https://docs.spring.io/spring-boot/3.5/reference/actuator/tracing.html)
- [Spring Boot 3.5 Actuator Endpoints and Security](https://docs.spring.io/spring-boot/3.5/reference/actuator/endpoints.html)
- [Gateway Metrics Filter](https://docs.spring.io/spring-cloud-gateway/reference/spring-cloud-gateway-server-webflux/global-filters.html)
- [Gateway Reactor Netty Access Logs](https://docs.spring.io/spring-cloud-gateway/reference/4.2/spring-cloud-gateway/reactor-netty-access-logs.html)
