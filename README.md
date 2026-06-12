# Food Delivery Config Repository

This repository serves as the centralized configuration source for the Food Delivery Microservices project using Spring Cloud Config Server.

**Main Project Repository:**
https://github.com/maulikk04/Food-Delivery-Microservices

The Config Server fetches configuration files from this repository and provides them to the corresponding microservices during startup.

> This repository is not intended to run independently. It is consumed by the Config Server of the Food Delivery Microservices project.

---

## Purpose

This repository externalizes service configurations from the application source code and enables centralized configuration management through Spring Cloud Config Server.

Benefits:

* Centralized configuration management
* Environment-specific configuration support
* Separation of code and configuration
* Secure handling of sensitive values using environment variables
* Easier maintenance across multiple microservices

---

## Configuration Files

### api-gateway.yml

Contains API Gateway route configurations.

```yaml
spring:
  cloud:
    gateway:
      server:
        webflux:
          discovery:
            locator:
              enabled: false
          routes:
            - id: user-service
              uri: lb://user-service
              predicates:
                - Path=/api/users/**

            - id: restaurant-service
              uri: lb://restaurant-service
              predicates:
                - Path=/api/restaurants/**
              filters:
                - AuthenticationFilter

            - id: order-service
              uri: lb://order-service
              predicates:
                - Path=/api/orders/**
              filters:
                - AuthenticationFilter
```

Responsibilities:

* Defines API Gateway routing rules
* Uses Eureka Service Discovery via `lb://SERVICE-NAME`
* Applies authentication filters to protected endpoints
* Centralizes route management outside application code

---

### user-service.yml

```yaml
spring:
  data:
    mongodb:
      uri: ${MONGODB_URI}
```

Provides MongoDB configuration for User Service.

---

### restaurant-service.yml

```yaml
spring:
  data:
    mongodb:
      uri: ${MONGODB_URI}
```

Provides MongoDB configuration for Restaurant Service.

---

### order-service.yml

```yaml
spring:
  data:
    mongodb:
      uri: ${MONGODB_URI}
```

Provides MongoDB configuration for Order Service.

---

## Configuration Flow

```text
food-delivery-config-repo
           │
           ▼
     Config Server
           │
           ▼
 ┌─────────────────┐
 │ API Gateway     │
 │ User Service    │
 │ Restaurant      │
 │ Order Service   │
 └─────────────────┘
```

1. Config Server connects to this repository.
2. Configuration files are loaded from GitHub.
3. API Gateway fetches route configurations during startup.
4. User Service, Restaurant Service, and Order Service fetch their configurations during startup.
5. Services start using the retrieved configurations.

---

## Environment Variables

MongoDB connection strings are externalized using environment variables.

Configuration files reference the MongoDB URI using placeholders:

```yaml
spring:
  data:
    mongodb:
      uri: ${MONGODB_URI}
```

Before starting a service, configure:

```text
MONGODB_URI=<your-mongodb-atlas-uri>
```

This keeps sensitive database credentials out of source control.

---

