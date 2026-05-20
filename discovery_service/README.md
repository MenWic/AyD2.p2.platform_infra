
# Discovery Service

Servicio de descubrimiento de microservicios basado en Spring Cloud Netflix Eureka Server.

Forma parte del repositorio:

```text
platform_infra/
└── discovery_service/
```

## Responsabilidad

`discovery_service` registra los microservicios disponibles para que otros componentes internos puedan descubrirlos.

No es un API Gateway y no debe ser consumido directamente por el frontend.

Flujo final esperado:

```text
frontend -> api_gateway -> Eureka -> microservicios
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

## Puerto

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

## Configuración principal

Archivo:

```text
src/main/resources/application.properties
```

Configuración base:

```properties
spring.application.name=discovery-service

server.port=8761

eureka.client.register-with-eureka=false
eureka.client.fetch-registry=false

eureka.server.enable-self-preservation=false

management.endpoints.web.exposure.include=health,info
```

## Ejecutar el servicio

### Windows PowerShell

Desde `platform_infra/discovery_service`:

```powershell
.\gradlew clean bootRun
```

O desde `platform_infra`:

```powershell
cd discovery_service
.\gradlew clean bootRun
```

### Linux / macOS / Git Bash

Desde `platform_infra/discovery_service`:

```bash
./gradlew clean bootRun
```

O desde `platform_infra`:

```bash
cd discovery_service
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

## Resultado esperado al levantar

En consola debe aparecer algo equivalente a:

```text
Started Eureka Server
Tomcat started on port 8761
Started DiscoveryServiceApplication
```

En navegador:

```text
http://localhost:8761/
```

Debe mostrarse el dashboard de Spring Eureka.

Si todavía no hay microservicios levantados, debe verse:

```text
No instances available
```

Eso es correcto.

## Advertencias normales en desarrollo

Estas advertencias pueden aparecer en local y no bloquean el arranque:

```text
Cannot determine local hostname
Spring Cloud LoadBalancer is currently working with the default cache
Self preservation mode is turned off
```

Para producción se revisan valores de red, self-preservation, cache y monitoreo.

## No debe agregarse a este servicio

```text
- endpoints de negocio;
- seguridad JWT de usuarios;
- conexión a base de datos;
- Flyway;
- JPA;
- lógica de IAM, Conference o Wallet;
- API Gateway;
- rutas de frontend.
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

