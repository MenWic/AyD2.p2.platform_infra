
# Discovery Service

Micro-service discovery service based on Spring Cloud Netflix Eureka Server.

Part of the repository:

```text
platform_infra/
└── discovery_service/
```

## Responsibility

`discovery_service` registers available micro-services so that other internal components
can discover them.

It is not an API Gateway and must not be consumed directly by the frontend.

Expected final flow:

```text
frontend -> api_gateway -> Eureka -> micro-services
```

## Stack

```text
Java 21
Spring Boot 4.0.6
Spring Cloud 2025.1.1
Gradle
Eureka Server
Actuator
```

## Port

```text
8761
```

Dashboard:

```text
http://localhost:8761/
```

Healthcheck:

```text
http://localhost:8761/actuator/health
```

## Main configuration

File:

```text
src/main/resources/application.properties
```

Base configuration:

```properties
spring.application.name=discovery-service

server.port=8761

eureka.client.register-with-eureka=false
eureka.client.fetch-registry=false

management.endpoints.web.exposure.include=health,info
```

Development profile (`application-dev.properties`):

```properties
# Disable self-preservation in dev for faster instance eviction during local development loops.
# Production defaults to self-preservation=true (Spring default).
eureka.server.enable-self-preservation=false
```

## Run the service

### Windows PowerShell

From `platform_infra/discovery_service`:

```powershell
.\gradlew clean bootRun
```

Or from `platform_infra`:

```powershell
cd discovery_service
.\gradlew clean bootRun
```

### Linux / macOS / Git Bash

From `platform_infra/discovery_service`:

```bash
./gradlew clean bootRun
```

Or from `platform_infra`:

```bash
cd discovery_service
./gradlew clean bootRun
```

## Run tests

### Windows PowerShell

```powershell
.\gradlew test
```

### Linux / macOS / Git Bash

```bash
./gradlew test
```

## Clean build

### Windows PowerShell

```powershell
.\gradlew clean
```

### Linux / macOS / Git Bash

```bash
./gradlew clean
```

## Expected output on startup

The console should display something equivalent to:

```text
Started Eureka Server
Tomcat started on port 8761
Started DiscoveryServiceApplication
```

In the browser:

```text
http://localhost:8761/
```

The Spring Eureka dashboard should appear.

If no micro-services are running yet, you will see:

```text
No instances available
```

That is correct.

## Normal warnings during development

These warnings may appear locally and do not block startup:

```text
Cannot determine local hostname
Spring Cloud LoadBalancer is currently working with the default cache
Self preservation mode is turned off
```

For production, review network settings, self-preservation, cache, and monitoring configuration.

## Must not be added to this service

```text
- business endpoints;
- user JWT security;
- database connection;
- Flyway;
- JPA;
- IAM, Conference, or Wallet logic;
- API Gateway;
- frontend routes.
```

## Environment-specific configuration files

This project must not track environment-specific configuration files.
Contact the platform team for environment-specific configuration files.

Files excluded from Git:

```text
build/
.gradle/
.idea/
.env
.env.*
application-local.*
application-secret.*
application-private.*
application-prod.*
logs/
*.log
keys/certificates/keystores
```
