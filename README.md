# Task Management API

A standalone Spring Boot backend for managing tasks. It exposes a REST API with JWT authentication, role-based access control, request validation, centralised error handling, and versioned database migrations.

## Features

- **Authentication** — stateless JWT-based login; tokens are required on all task endpoints.
- **Authorisation** — role-based access; administrative endpoints are restricted to the `ADMIN` role.
- **Task management** — create, read, update, delete, and search tasks by criteria.
- **Validation** — declarative request validation on incoming payloads.
- **Error handling** — centralised exception handling via `@ControllerAdvice`, returning consistent error responses.
- **Database migrations** — schema managed and versioned with Flyway.
- **Object mapping** — entity/DTO mapping generated at compile time with MapStruct.
- **API documentation** — interactive Swagger UI.
- **Containerisation** — Dockerfile and `docker-compose` setup for running the service with its database.

## Tech stack

Java · Spring Boot · Spring Security (JWT) · Spring Data JPA / Hibernate · PostgreSQL · Flyway · MapStruct · Swagger / OpenAPI · Maven · Docker

## Prerequisites

- JDK 11 or higher
- Maven
- PostgreSQL
- Docker and Docker Compose (optional)

## Configuration

The application reads all environment-specific values from environment variables — nothing sensitive is committed to the repository.

| Variable | Description |
| --- | --- |
| `DB_URL` | JDBC connection string for PostgreSQL |
| `DB_USER` | Database username |
| `DB_PASSWORD` | Database password |
| `USER_EMAIL` | Sender address for outbound notification emails |
| `EMAIL_PASSWORD` | App-specific password for the sender account |

For Gmail, generate an **App Password** under your Google Account security settings and use it as `EMAIL_PASSWORD`. Keep two-factor authentication enabled — App Passwords are designed to work alongside it.

## Running the service

### Option 1 — locally with Maven

```bash
git clone https://github.com/mo-wadoud/task-management-api.git
cd task-management-api

mvn clean install -DskipTests

mvn spring-boot:run -Dspring-boot.run.arguments="\
  --DB_URL=<jdbc-url> \
  --DB_USER=<db-user> \
  --DB_PASSWORD=<db-password> \
  --USER_EMAIL=<sender-email> \
  --EMAIL_PASSWORD=<app-password>"
```

### Option 2 — with Docker Compose

Create a `.env` file in the project root:

```
DB_URL=
DB_USER=
DB_PASSWORD=
USER_EMAIL=
EMAIL_PASSWORD=
```

Then start the stack:

```bash
docker-compose up --build -d --remove-orphans
```

Flyway applies the schema migrations automatically on startup.

## API documentation

Once the service is running, the Swagger UI is available at:

```
http://localhost:8080/swagger-ui.html
```

A Postman collection covering every endpoint is included at `postman_collection.json`.

## Endpoints

### Authentication

| Method | Path | Description |
| --- | --- | --- |
| `POST` | `/auth/login` | Authenticate and receive a JWT |

### Tasks

| Method | Path | Description |
| --- | --- | --- |
| `GET` | `/api/tasks/{id}` | Retrieve a task by id |
| `POST` | `/api/tasks` | Create a task |
| `PUT` | `/api/tasks/{id}` | Update a task |
| `DELETE` | `/api/tasks/{id}` | Delete a task |
| `POST` | `/api/tasks/search` | Search tasks by criteria |
| `GET` | `/api/tasks/admin/all-tasks` | Retrieve all tasks — `ADMIN` only |

All task endpoints require a valid token:

```
Authorization: Bearer <your-token>
```

## Demo users

The Flyway migrations seed two accounts for local testing — an `ADMIN` user and a standard `USER`. Their credentials are defined in the migration scripts under `src/main/resources/db-migration`.

These accounts exist for local development only and should never be used in a deployed environment.
