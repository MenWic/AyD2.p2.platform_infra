# API Gateway

Gateway HTTP de entrada para el frontend y enrutamiento hacia microservicios registrados en Eureka.

Forma parte del repositorio:

```text
platform_infra/
└── api_gateway/
```

## Responsabilidad

`api_gateway` recibe las peticiones del frontend y las enruta hacia los microservicios correspondientes usando Eureka Service Discovery.

Flujo esperado:

```text
frontend -> api_gateway -> Eureka -> microservicios
```

No es un microservicio de negocio. No debe contener lógica de IAM, Conference o Wallet.

## Stack

```text
Java 21
Spring Boot 4.0.6
Spring Cloud 2025.1.1
Spring Cloud Gateway Server WebMVC
Eureka Discovery Client
Actuator
Gradle
```

## Puerto

```text
8080
```

Healthcheck:

```text
http://localhost:8080/actuator/health
```

## Servicios esperados

| Servicio | Puerto | Nombre en Eureka |
|---|---:|---|
| discovery_service | 8761 | discovery-service |
| api_gateway | 8080 | api-gateway |
| iam_service_api | 8081 | iam-service |
| conference_service | 8082 | conference-service |
| wallet_service | 8083 | wallet-service |

## Rutas activas

| Path público | Servicio destino |
|---|---|
| `/api/v1/auth/**` | `lb://iam-service` |
| `/api/v1/users/**` | `lb://iam-service` |

## Rutas futuras

Estas rutas deben activarse cuando los microservicios correspondientes existan y estén registrados en Eureka.

| Path público | Servicio destino |
|---|---|
| `/api/v1/congresses/**` | `lb://conference-service` |
| `/api/v1/conferences/**` | `lb://conference-service` |
| `/api/v1/wallet/**` | `lb://wallet-service` |
| `/api/v1/payments/**` | `lb://wallet-service` |

## Configuración principal

Archivo:

```text
src/main/resources/application.properties
```

Configuración base:

```properties
spring.application.name=api-gateway

server.port=8080

eureka.client.register-with-eureka=true
eureka.client.fetch-registry=true
eureka.client.service-url.defaultZone=http://localhost:8761/eureka/

eureka.instance.prefer-ip-address=true

management.endpoints.web.exposure.include=health,info,gateway
management.endpoint.health.show-details=always

spring.cloud.gateway.server.webmvc.routes[0].id=iam-auth-route
spring.cloud.gateway.server.webmvc.routes[0].uri=lb://iam-service
spring.cloud.gateway.server.webmvc.routes[0].predicates[0]=Path=/api/v1/auth/**

spring.cloud.gateway.server.webmvc.routes[1].id=iam-users-route
spring.cloud.gateway.server.webmvc.routes[1].uri=lb://iam-service
spring.cloud.gateway.server.webmvc.routes[1].predicates[0]=Path=/api/v1/users/**
```

## Ejecutar el servicio

### Windows PowerShell

Desde `platform_infra/api_gateway`:

```powershell
.\gradlew clean bootRun
```

O desde `platform_infra`:

```powershell
cd api_gateway
.\gradlew clean bootRun
```

### Linux / macOS / Git Bash

Desde `platform_infra/api_gateway`:

```bash
./gradlew clean bootRun
```

O desde `platform_infra`:

```bash
cd api_gateway
./gradlew clean bootRun
```

## Ejecutar tests

### Windows PowerShell

```powershell
.\gradlew test
```

### Linux / macOS / Git Bash

```bash
./gradlew test
```

## Limpiar build

### Windows PowerShell

```powershell
.\gradlew clean
```

### Linux / macOS / Git Bash

```bash
./gradlew clean
```

## Orden de arranque local

```text
1. discovery_service
2. microservicios con perfil discovery
3. api_gateway
4. frontend
```

Ejemplo con IAM:

```text
1. discovery_service
2. iam_service_api con --spring.profiles.active=discovery
3. api_gateway
4. frontend
```

## Levantar IAM para que el Gateway lo encuentre

IAM debe levantarse con el perfil `discovery` para registrarse en Eureka.

Desde `iam_service_api`:

### Windows PowerShell

```powershell
.\gradlew bootRun --args="--spring.profiles.active=discovery"
```

### Linux / macOS / Git Bash

```bash
./gradlew bootRun --args="--spring.profiles.active=discovery"
```

En IntelliJ IDEA:

```text
Run > Edit Configurations > Active profiles: discovery
```

## Resultado esperado al levantar

En consola debe aparecer algo equivalente a:

```text
Registering application API-GATEWAY with eureka with status UP
DiscoveryClient_API-GATEWAY/... - registration status: 204
Tomcat started on port 8080
Started ApiGatewayApplication
```

En Eureka:

```text
http://localhost:8761/
```

Deben aparecer los servicios levantados:

```text
API-GATEWAY
IAM-SERVICE
```

## Validar Gateway

Healthcheck:

```text
http://localhost:8080/actuator/health
```

Ruta IAM pasando por Gateway:

```text
http://localhost:8080/api/v1/users/me
```

Sin JWT, el resultado esperado es:

```text
401 Unauthorized
```

Eso confirma que el request llegó hasta IAM.

## Comportamiento esperado en `/`

Abrir esta ruta:

```text
http://localhost:8080/
```

puede devolver `404 Whitelabel Error Page`.

Eso es normal. El Gateway no tiene una home page configurada. Solo enruta las rutas declaradas.

## Advertencias normales en desarrollo

Estas advertencias pueden aparecer en local y no bloquean el arranque:

```text
Cannot determine local hostname
Spring Cloud LoadBalancer is currently working with the default cache
```

Para producción se revisan valores de red, cache, seguridad, monitoreo y configuración externa.

## No debe agregarse a este servicio

```text
- lógica de negocio;
- controllers de negocio;
- JPA;
- Flyway;
- conexión a base de datos;
- validaciones de dominio;
- reglas de IAM, Conference o Wallet;
- seguridad JWT propia del Gateway todavía;
- rutas activas hacia servicios que aún no existen.
```

## Archivos de configuración de entorno fuera de Git (solicitar a @MenWic)

Este proyecto no debe trackear:

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
llaves/certificados/keystores
```

El archivo base `application.properties` puede trackearse solo si no contiene secretos.
