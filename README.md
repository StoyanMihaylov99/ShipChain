

#### Shipchain



----

## Project Summary 

ShipChain is a system for orders and delivery tracking platform. Users register, create orders, pay (simulated), track order status, and receive notifications.

--- 
#### Tech Stack and Why

| Technology                  | Role                  | Why it's chosen                                                                 |
| --------------------------- | --------------------- | ------------------------------------------------------------------------------- |
| Java 21+                    | Core language         | Virtual threads, pattern matching, current LTS — shows you follow the ecosystem |
| Spring Boot                 | Framework             | Industry standard, fast setup                                                   |
| Spring Security + JWT/OAuth | AuthN/AuthZ           | Stateless auth, role-based access (USER/ADMIN)                                  |
| Spring Data JPA / Hibernate | ORM                   | Relational data access, migrations                                              |
| PostgreSQL                  | Primary DB            | ACID, strong for order/inventory consistency                                    |
| Redis                       | Cache + rate limiting | Product catalog cache, session/token blacklist, distributed locks for inventory |
| Apache Kafka                | Event backbone        | Order events, async notification pipeline, decoupling between modules           |
| Maven/Gradle                | Build tool            | Standard                                                                        |
| JUnit 5 + Mockito           | Unit tests            | Mandatory for any "clean code" claim                                            |
| Testcontainers              | Integration tests     | Real Postgres/Kafka/Redis in tests, not mocks — a strong quality signal         |
| Docker / Docker Compose     | Local environment     | Reproducible local dev/demo                                                     |
| GitHub Actions              | CI/CD                 | Build → test → docker image → (deploy)                                          |
| AWS/Azure/GCP               | Deployment            | Shows you can ship something real to the cloud, not just localhost              |
| SLF4J + Logback             | Logging               | Structured logging, correlation ID per request/order                            |
| Micrometer + Prometheus     | Metrics collection    | Latency, throughput, error rate per endpoint                                    |
| Grafana                     | Dashboards            | Metrics visualization — good for demo video/screenshots in the CV               |
| OpenAPI / Swagger           | API documentation     | Auto-generated, usable by frontend/third parties                                |

---- 

#### Architecture


Services

- API Gateway (Spring Cloud Gateway/ AWS API Gateway) — single entry point, JWT validation, routing, rate limiting

- Auth Service — registration/login, JWT issuance + refresh tokens, owns auth_db, Redis for token blacklist

- Inventory Service — stock levels, reservation/release logic, owns inventory_db, optimistic locking or Redis distributed lock for concurrent reservations

- Payment Service — simulated payment provider, payment state machine, owns payment_db, retry/backoff on transient failures

- Order Service — order lifecycle, owns order_db, acts as the saga orchestrator coordinating Inventory + Payment

- Notification Service — pure Kafka consumer, sends notifications, owns notification_db


Data

- Database-per-service — no shared DB, ever. Each service owns its schema; cross-service reads go through the service's API or through events, never a JOIN across DBs


Communication

- Kafka — event backbone for the async flow (order.created → inventory.reserved → payment.succeeded/failed → order.status-changed → notification.sent)

- Sync REST/gRPC between Order Service and Inventory/Payment where you need an immediate response (e.g. reservation check before accepting the order)

- Saga pattern — pick orchestration (Order Service drives the steps, explicit and easier to reason about) over choreography (services react to each other's events, more decoupled but harder to trace) — orchestration is the better story for a 2-person team and for interviews, since it's easier to explain and debug
    
