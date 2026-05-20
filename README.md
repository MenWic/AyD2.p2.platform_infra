# Platform Infra

Infrastructure repository for the cross-cutting services of the platform.

## Expected structure

```text
platform_infra/
├── discovery_service/   # Eureka Server
└── api_gateway/         # API Gateway
```

## Services

| Service | Port | Status | Responsibility |
|---|---:|---|---|
| discovery_service | 8761 | Active | Service Registry with Eureka Server |
| api_gateway | 8080 | Active | HTTP entry point for the frontend and routing to micro-services |
| iam-service | 8081 | External | IAM micro-service, registers as Eureka Client |
| conference-service | 8082 | External | Conference micro-service |
| wallet-service | 8083 | External | Wallet/payments micro-service |

## Expected flow

```text
frontend -> api_gateway -> Eureka -> micro-services
```

## Recommended startup order

```text
1. discovery_service
2. api_gateway
3-5. micro-services
6. frontend
```

## Discovery Service

`discovery_service` is the Eureka Server.

Responsibility:

```text
Register micro-services and allow internal service discovery.
```

Must not contain:

```text
- business logic;
- database;
- JWT;
- Flyway;
- business controllers;
- frontend routes.
```

## Run Discovery Service

From the root of `platform_infra`:

### Windows PowerShell

```powershell
cd discovery_service
.\gradlew clean bootRun
```

### Linux / macOS / Git Bash

```bash
cd discovery_service
./gradlew clean bootRun
```

## Verify Eureka

Open in browser:

```text
http://localhost:8761/
```

Healthcheck:

```text
http://localhost:8761/actuator/health
```

Expected result:

```text
Eureka dashboard visible.
No instances registered until IAM or other micro-services are started.
```

## Git rules

This repository must not track:

```text
- build/
- .gradle/
- .idea/
- .env files
- application-local.*
- application-secret.*
- application-private.*
- application-prod.*
- keys, certificates, or keystores
- logs
- generated reports
- temporary outputs
- *.zip archives
```

Must track:

```text
- build.gradle
- settings.gradle
- gradlew
- gradlew.bat
- gradle/wrapper/gradle-wrapper.jar
- src/
- README.md
- .gitignore
```

## Security note

Files containing credentials, secrets, certificates, private keys, production configurations,
or sensitive variables must be kept out of Git.

Use environment variables or locally-ignored files for sensitive values.
