# ElectricPay

ElectricPay is a microservices-based payment and transaction platform built with Java and Spring Boot. It supports user and account management, deposits and transfers, Stripe-backed payment operations, bank reconciliation, Kafka event processing, and email notifications.

> Recommended project name: **ElectricPay** — clear, professional, and aligned with the existing `electric-titans` package namespace.

## Features

- User registration, login, logout, profile, and KYC status management
- Account creation, updates, balances, and account status management
- Transaction creation, scheduling, cancellation, and history
- Stripe account, customer, deposit, and payment operations
- Kafka-based asynchronous communication between services
- Bank reconciliation and webhook handling
- SendGrid email notifications for account and transaction events
- JWT authentication and Redis-backed token blacklist support
- PostgreSQL persistence and Spring Data JPA
- Swagger/OpenAPI documentation where enabled

## Architecture

| Service | Port | Responsibility |
|---|---:|---|
| `user-service` | 8081 | Authentication, users, profiles, administration, and KYC |
| `transaction-service` | 8082 | Transaction creation, scheduling, cancellation, and queries |
| `payment-gateway-service` | 8083 | Stripe payment and account operations |
| `bank-reconciliation-service` | 8084 | Stripe reconciliation, reports, and webhooks |
| `account-service` | 8085 | Account balances, account lifecycle, and account events |
| `notification-service` | 8087 | SendGrid notifications triggered by Kafka events |
| `kafbat-ui` | 8080 | Kafka cluster monitoring |

## Technology Stack

- Java 17
- Spring Boot 3.3.1
- Spring Security and JWT
- Spring Data JPA
- PostgreSQL 13
- Apache Kafka
- Redis
- Stripe Java SDK
- SendGrid
- Maven
- Docker Compose
- SpringDoc OpenAPI

## Prerequisites

- Java 17 or newer
- Maven 3.8+ or the included Maven Wrapper
- Docker Desktop or Docker Engine
- A Stripe account for payment features
- A SendGrid account for email notifications

## Getting Started

### 1. Clone the repository

```bash
git clone <repository-url>
cd payment-gateway-services
```

### 2. Start infrastructure services

```bash
docker compose up -d
```

This starts PostgreSQL, Kafka, ZooKeeper, Redis, and Kafka UI.

Service URLs:

- PostgreSQL: `localhost:5432`
- Kafka: `localhost:9092`
- Redis: `localhost:6379`
- Kafka UI: `http://localhost:8080`

### 3. Configure local secrets

Create database and external-service credentials for each service before starting them. The relevant configuration is in:

```text
*/src/main/resources/application.yml
```

At minimum, configure:

```text
SPRING_DATASOURCE_PASSWORD
SPRING_KAFKA_BOOTSTRAP_SERVERS
JWT_SECRET
STRIPE_API_KEY
STRIPE_WEBHOOK_SECRET
SENDGRID_API_KEY
```

You can override YAML values using environment variables. Example:

```bash
SPRING_DATASOURCE_PASSWORD=postgres \
JWT_SECRET=replace-with-a-long-random-secret \
STRIPE_API_KEY=your-stripe-key \
./mvnw spring-boot:run -pl user-service -am
```

On Windows PowerShell:

```powershell
$env:SPRING_DATASOURCE_PASSWORD="postgres"
$env:JWT_SECRET="replace-with-a-long-random-secret"
$env:STRIPE_API_KEY="your-stripe-key"
.\mvnw.cmd spring-boot:run -pl user-service -am
```

> **Security:** Do not commit real API keys, webhook secrets, JWT secrets, database passwords, or SendGrid keys. The current configuration files contain development/example values and should be moved to environment variables or a secret manager before deployment. Stripe, SendGrid, and any exposed JWT credentials should be rotated if they have been shared or committed publicly.

## Run the Services

Build the complete Maven multi-module project:

```bash
./mvnw clean package
```

Start each service from the repository root in a separate terminal:

```bash
./mvnw spring-boot:run -pl user-service -am
./mvnw spring-boot:run -pl transaction-service -am
./mvnw spring-boot:run -pl payment-gateway-service -am
./mvnw spring-boot:run -pl bank-reconciliation-service -am
./mvnw spring-boot:run -pl account-service -am
./mvnw spring-boot:run -pl notification-service -am
```

## Test the Services

Run tests across all modules:

```bash
./mvnw test
```

Run tests for one service:

```bash
./mvnw test -pl user-service -am
```

## API Groups

| Area | Base path |
|---|---|
| Authentication | `/api/v1/auth` |
| Users | `/api/v1/users` |
| Accounts | `/api/v1/accounts` |
| Transactions | `/api/v1/transactions` |
| Reconciliation | `/api/reconciliations` |
| Stripe integration | `/api/stripe` |

Swagger UI is normally available at the service base URL under `/swagger-ui/index.html` when SpringDoc is enabled.

## Kafka Event Topics

The services communicate through event topics including:

- `USER-CREATED`
- `DEPOSIT-REQUEST` and `DEPOSIT-SUCCESS`
- `WITHDRAW-REQUEST` and `WITHDRAW-SUCCESS`
- `TRANSFER-REQUEST` and `TRANSFER-SUCCESS`
- `RECONCILIATION-STARTED` and `RECONCILIATION-FINISHED`

## Project Structure

```text
payment-gateway-services/
├── common/
├── user-service/
├── account-service/
├── transaction-service/
├── payment-gateway-service/
├── bank-reconciliation-service/
├── notification-service/
├── docker-compose.yml
└── pom.xml
```

## Production Considerations

- Replace development credentials and secrets with environment variables or a secret manager.
- Configure HTTPS and production CORS policies.
- Use a managed Kafka, PostgreSQL, and Redis deployment.
- Add health probes and central logging/metrics.
- Restrict Swagger and actuator endpoints.
- Validate Stripe webhook signatures and reconcile idempotency keys.
- Rotate keys and review repository history if credentials were previously committed.

## License

Add the project license here (for example, MIT or Apache-2.0).
