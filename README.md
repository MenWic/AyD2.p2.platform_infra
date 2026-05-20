# Platform Infra

Repositorio de infraestructura para los servicios transversales de la plataforma.

## Estructura esperada

```text
platform_infra/
├── discovery_service/   # Eureka Server
└── api_gateway/         # Pendiente
```

## Servicios

| Servicio | Puerto | Estado | Responsabilidad |
|---|---:|---|---|
| discovery_service | 8761 | Activo | Service Registry con Eureka Server |
| api_gateway | 8080 | Activo | Entrada HTTP para el frontend y enrutamiento a microservicios |
| iam_service_api | 8081 | Externo | Microservicio IAM, se registrará como Eureka Client |
| conference_service | 8082 | Externo| Microservicio de conferencias |
| wallet_service | 8083 | Externo | Microservicio de wallet/pagos |

## Flujo esperado

Cuando esté listo el Gateway:

```text
frontend -> api_gateway -> Eureka -> microservicios
```

## Orden recomendado de arranque

```text
1. discovery_service
2. api_gateway
3-5. mciro-services 
6. frontend
```

## Discovery Service

`discovery_service` es el Eureka Server.

Responsabilidad:

```text
Registrar microservicios y permitir service discovery interno.
```

No debe contener:

```text
- lógica de negocio;
- base de datos;
- JWT;
- Flyway;
- controladores de negocio;
- rutas para frontend.
```

## Ejecutar Discovery Service

Desde la raíz de `platform_infra`:

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

## Verificar Eureka

Abrir en el navegador:

```text
http://localhost:8761/
```

Healthcheck:

```text
http://localhost:8761/actuator/health
```

Resultado esperado:

```text
Eureka dashboard visible.
Sin instancias registradas hasta levantar IAM u otros microservicios.
```

## Reglas de Git

Este repositorio no debe trackear:

```text
- build/
- .gradle/
- .idea/
- archivos .env
- application-local.*
- application-secret.*
- application-private.*
- application-prod.*
- llaves, certificados o keystores
- logs
- reportes generados
- outputs temporales
```

Sí debe trackear:

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

## Nota de seguridad

Los archivos con credenciales, secretos, certificados, llaves privadas, configuraciones productivas o variables sensibles deben mantenerse fuera de Git.

Usar variables de entorno o archivos locales ignorados para valores sensibles.
