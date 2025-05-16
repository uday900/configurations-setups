
# 📦 Zipkin Tracing Setup for Spring Boot Microservices

This guide walks you through integrating [Zipkin](https://zipkin.io/) for distributed tracing in a Spring Boot microservices architecture.

---

## 📁 Microservices Structure



root-project/
├── gateway-service/
├── eureka-server/
├── quiz-service/
├── question-service/
├── docker-compose.yml
└── README.md



## ✅ Prerequisites

- Docker & Docker Compose installed
- Spring Boot microservices project
- Each service configured with Eureka (if applicable)


## ⚙️ Step 1: Add Zipkin Docker Service

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
````

Then start Zipkin:

```bash
docker-compose up -d
```

Access Zipkin UI at: [http://localhost:9411](http://localhost:9411)

---

## 📦 Step 2: Add Zipkin Dependencies

Add the following dependencies to each service's `pom.xml`:

```xml
<!-- Actuator for metrics -->
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>

<!-- Micrometer Tracing Bridge for Brave -->
<dependency>
  <groupId>io.micrometer</groupId>
  <artifactId>micrometer-tracing-bridge-brave</artifactId>
</dependency>

<!-- Zipkin Reporter -->
<dependency>
  <groupId>io.zipkin.reporter2</groupId>
  <artifactId>zipkin-reporter-brave</artifactId>
</dependency>

<!-- Optional: Feign client tracing -->
<dependency>
  <groupId>io.github.openfeign</groupId>
  <artifactId>feign-micrometer</artifactId>
</dependency>
```

---

## 🛠️ Step 3: Application Properties Configuration

In each service’s `application.properties` or `application.yml`:

```properties
# Tracing configuration
management.tracing.sampling.probability=1.0
management.zipkin.tracing.endpoint=http://localhost:9411/api/v2/spans
management.endpoints.web.exposure.include=*
```

🔁 Replace `localhost` with the Docker service name if using a Docker network.

---

## 🔍 Step 4: View Traces

Once services are running and making requests to each other:

1. Open [http://localhost:9411](http://localhost:9411)
2. Click **"Find Traces"**
3. Select a service and view request flow between microservices.

---

## 📌 Notes

* Zipkin uses **in-memory storage** by default. For persistence, configure a MySQL or Elasticsearch backend.
* In production, secure the `/actuator` endpoints or limit exposure.

---

## ✅ You're All Set!

Now your microservices are traceable through Zipkin 🚀
Track latency, bottlenecks, and request flow with ease.

---

### 📚 References

* [Zipkin Docs](https://zipkin.io/pages/quickstart)
* [Spring Cloud Sleuth (Deprecated)](https://spring.io/projects/spring-cloud-sleuth) — replaced by Micrometer Tracing

```
