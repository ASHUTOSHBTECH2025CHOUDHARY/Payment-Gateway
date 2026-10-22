# Payment Gateway Service

A production-style payment gateway built with **Java 26, Spring Boot 4.1, PostgreSQL, Apache Kafka, Redis, and Stripe**. Implements Domain-Driven Design (DDD), Hexagonal Architecture, transactional outbox messaging, and idempotent payment processing.

## Features

- **Payments:** Authorize and capture card payments through Stripe.
- **Refunds:** Support full and partial refunds.
- **Transactions:** Track payment and refund transactions.
- **Merchants:** Register, update, and suspend merchants.
- **Customers:** Manage customer profiles and tokenized payment methods.
- **Reconciliation:** Reconcile merchant transactions against gateway records.
- **Webhooks:** Process Stripe events and deliver notifications to merchants.
- **Idempotency:** Use Redis and Redisson to coordinate retryable requests and prevent duplicate processing.
- **Transactional Outbox:** Persist business changes and events atomically before publishing to Kafka.
- **Resilience:** Implement retries, circuit breakers, bulkheads, and rate limiting.
- **Observability:** Monitor metrics and distributed traces with Prometheus, Grafana, and Zipkin.

## Architecture

The application follows Domain-Driven Design (DDD) and Hexagonal Architecture.

```text
┌─────────────────────────────────────────────┐
│              DOMAIN LAYER                   │
│ Payments, Refunds, Transactions, Merchants,  │
│ Customers, Reconciliation, Outbox           │
├─────────────────────────────────────────────┤
│           APPLICATION LAYER                 │
│ Use Cases, Commands, Responses, Ports        │
├─────────────────────────────────────────────┤
│          INFRASTRUCTURE LAYER               │
│ REST APIs, Kafka, PostgreSQL, Redis, Stripe  │
└─────────────────────────────────────────────┘
```

Dependency direction: `Infrastructure → Application → Domain`

Architecture boundaries are validated using ArchUnit tests.

## Technology Stack

| Category | Technology |
|---|---|
| Language | Java 26 |
| Framework | Spring Boot 4.1 |
| Build Tool | Maven |
| Database | PostgreSQL |
| ORM | Spring Data JPA, Hibernate |
| Migrations | Flyway |
| Messaging | Apache Kafka, Spring Kafka |
| Cache and Locks | Redis, Redisson |
| Payment Processing | Stripe Java SDK |
| Resilience | Resilience4j, Bucket4j |
| API Documentation | Springdoc OpenAPI, Swagger UI |
| Monitoring | Micrometer, Prometheus, Grafana |
| Distributed Tracing | Brave, Zipkin |
| Testing | JUnit 5, Testcontainers, ArchUnit |

## API Endpoints

| Feature | Endpoint |
|---|---|
| Payments | `POST /api/v1/payments` |
| Refunds | `/api/v1/refunds` |
| Transactions | `/api/v1/transactions` |
| Merchants | `/api/v1/merchants` |
| Customers | `/api/v1/customers` |
| Reconciliation | `/api/v1/reconciliation` |
| Webhooks | `/api/v1/webhooks` |

## Prerequisites

- Java 26 or later
- Docker and Docker Compose
- Maven (Maven Wrapper included)
- Stripe API credentials

## Getting Started

### 1. Clone the Repository

```bash
git clone <repository-url>
cd payment-gateway-service
```

### 2. Configure Environment Variables

```bash
cp .env.example .env
```

Configure database, Kafka, Redis, and Stripe credentials in the environment file.

Example local configuration:

```properties
DATASOURCE_URL=jdbc:postgresql://localhost:5433/payment_gateway
DATASOURCE_USERNAME=admin
DATASOURCE_PASSWORD=admin

KAFKA_BOOTSTRAP_SERVERS=localhost:19092

REDIS_HOST=localhost
REDIS_PORT=6380

SPRING_PROFILES_ACTIVE=local
```

### 3. Start Infrastructure

```bash
docker-compose up -d
```

This starts PostgreSQL, Kafka, Redis, Prometheus, Grafana, Zipkin, and the supporting development tools configured in Docker Compose.

### 4. Run the Application

```bash
./mvnw spring-boot:run
```

Alternatively, build and run the JAR:

```bash
./mvnw clean package -DskipTests
java -jar target/*.jar
```

### 5. Access the Services

| Service | URL |
|---|---|
| API | http://localhost:8080 |
| Swagger UI | http://localhost:8080/swagger-ui.html |
| Health Check | http://localhost:8080/actuator/health |
| Kafka UI | http://localhost:8082 |
| Grafana | http://localhost:3000 |
| Prometheus | http://localhost:9090 |
| Zipkin | http://localhost:9411 |
| pgAdmin | http://localhost:8083 |

## Reliability and Data Consistency

- **Idempotency:** Prevent duplicate processing of retried requests.
- **Transactional Outbox:** Store business changes and domain events in one database transaction.
- **Kafka Publishing:** Publish persisted events asynchronously.
- **Distributed Locking:** Coordinate concurrent retryable operations using Redis and Redisson.
- **Resilience Patterns:** Handle transient failures and protect external services.
- **Reconciliation:** Detect discrepancies between merchant and gateway transaction records.

## Monitoring and Observability

- **Micrometer:** Instrument application metrics.
- **Prometheus:** Collect metrics.
- **Grafana:** Visualize monitoring dashboards.
- **Zipkin:** Trace distributed requests.
- **Kafka UI:** Inspect Kafka topics and messages.

## Testing

The project uses:

- JUnit 5 for unit tests.
- Testcontainers for integration testing.
- ArchUnit for architecture validation.

Run the tests:

```bash
./mvnw test
```

Integration and end-to-end tests may require the supporting infrastructure.

## Documentation

Technical documentation is available in the `docs/` directory, covering API references, Stripe integration, webhooks, testing, security, deployment, troubleshooting, and architecture decisions.

## Security

This is a learning and development project. Configure secure credentials, protect API endpoints, validate webhook signatures, and manage secrets securely before deploying to a public or production environment.
