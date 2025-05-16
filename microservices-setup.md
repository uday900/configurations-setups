# 📦 Spring Boot Microservices Setup & Configuration Guide

This repository contains setup and configuration steps for building a Spring Boot-based microservices architecture with tools like Eureka, API Gateway, Docker, and Zipkin for distributed tracing.

---

## 📚 Table of Contents

1. [Eureka Server Setup](#1-eureka-server-setup)
2. [Zipkin Distributed Tracing Setup](#🔍-zipkin-distributed-tracing-setup)
---

## 1. Eureka Server Setup

> Eureka acts as a service registry for service discovery in microservices.

**Steps:**

1. Create a Spring Boot project: `eureka-server`.

2. Add dependency:

   ```xml
   <dependency>
     <groupId>org.springframework.cloud</groupId>
     <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
   </dependency>
   ```

3. Enable Eureka in your main class:

   ```java
   @EnableEurekaServer
   at main method level
   ```

4. Add configuration in `application.yml`:

   ```yaml
   server:
     port: 8761

   eureka:
     client:
       register-with-eureka: false
       fetch-registry: false
   ```
5. Create micro service project registering eureka server --Create a new Spring Boot project**, e.g., `quiz-service`.

6. **Add dependencies** in `pom.xml`:

   ```xml
   <!-- Eureka Discovery Client -->
   <dependency>
     <groupId>org.springframework.cloud</groupId>
     <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
   </dependency>
   ```

3. **Enable Eureka Client in the main class** (usually optional with dependency, but you can annotate explicitly):

   ```java
   @SpringBootApplication
   @EnableDiscoveryClient
   public class QuizServiceApplication {
       public static void main(String[] args) {
           SpringApplication.run(QuizServiceApplication.class, args);
       }
   }
   ```

4. **Configure `application.properties` or `application.yml`** in the microservice:

   #### If using `application.properties`:

   ```properties
   spring.application.name=quiz-service
   server.port=8081

   eureka.client.service-url.defaultZone=http://localhost:8761/eureka
   eureka.client.register-with-eureka=true
   eureka.client.fetch-registry=true
   ```

5. **Run Eureka Server first** by executing the `EurekaServerApplication`.

6. **Run the microservice**, and go to [http://localhost:8761](http://localhost:8761) to confirm it's registered under **"Instances currently registered with Eureka"**.

---

### 🧪 Test the Setup

* Open `http://localhost:8761` in your browser.


## 2. Zipkin Distributed Tracing Setup

### Step 1: Add Dependencies in Each Service

```xml
<!-- Actuator -->
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>

<!-- Micrometer Tracing -->
<dependency>
  <groupId>io.micrometer</groupId>
  <artifactId>micrometer-tracing-bridge-brave</artifactId>
</dependency>

<!-- Zipkin Reporter -->
<dependency>
  <groupId>io.zipkin.reporter2</groupId>
  <artifactId>zipkin-reporter-brave</artifactId>
</dependency>

<!-- Optional: Feign Micrometer -->
<dependency>
  <groupId>io.github.openfeign</groupId>
  <artifactId>feign-micrometer</artifactId>
</dependency>
```

---

### Step 2: Configuration in `application.properties` or `application.yml`

```properties
management.tracing.sampling.probability=1.0
management.zipkin.tracing.endpoint=http://localhost:9411/api/v2/spans
management.endpoints.web.exposure.include=*
```

Create a `docker-compose.yml` file in the **root directory**:

```yaml
version: '3.8'

services:
  zipkin:
    image: openzipkin/zipkin
    container_name: zipkin
    restart: always
    ports:
      - "9411:9411"
    environment:
      - STORAGE_TYPE=mem  # In-memory storage (not persistent)
```


📌 Use `http://zipkin:9411/...` if all services are in Docker.

---

### Step 3: View Traces in Zipkin

1. Run your services and simulate some requests.
2. Visit: [http://localhost:9411](http://localhost:9411)
3. Click on **"Find Traces"** to visualize the request flow.

---
