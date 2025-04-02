# crystal-grpc-package
Crystal public shared gRPC package

## 🔧 Guideline Usage in Other Services

### 🛠 Add Maven Dependency

```xml
<dependency>
  <groupId>com.crystaltechbd.hsms</groupId>
  <artifactId>crystal-grpc-package</artifactId>
  <version>0.0.1</version>
</dependency>
```

### ⚙️ Configure gRPC Endpoints in `application.yml`

```yaml
grpc:
  client:
    opd:
      address: static://localhost:9091
    registration:
      address: static://localhost:9092
    organization:
      address: static://localhost:9093
```

### 🔁 Add Redis and Resilience Configuration (optional)

```yaml
spring:
  data:
    redis:
      host: localhost
      port: 6379
  redis:
    ttl:
      enabled: true
      default: 10m
      users: 30m

resilience4j:
  retry:
    instances:
      redis-retry:
        maxAttempts: 3
        waitDuration: 500
  circuitbreaker:
    instances:
      redis-cb:
        failureRateThreshold: 50
        waitDurationInOpenState: 10s
        slidingWindowSize: 10
```

### ⚙️ Alternatively, use `application.properties`

```properties
grpc.client.opd.address=static://localhost:9091
grpc.client.registration.address=static://localhost:9092
grpc.client.organization.address=static://localhost:9093

spring.data.redis.host=localhost
spring.data.redis.port=6379
spring.data.redis.ttl.enabled=true
spring.data.redis.ttl.default=10m
spring.data.redis.ttl.users=30m

resilience4j.retry.instances.redis-retry.maxAttempts=3
resilience4j.retry.instances.redis-retry.waitDuration=500

resilience4j.circuitbreaker.instances.redis-cb.failureRateThreshold=50
resilience4j.circuitbreaker.instances.redis-cb.waitDurationInOpenState=10s
resilience4j.circuitbreaker.instances.redis-cb.slidingWindowSize=10
```

### 🧩 Autowire the GrpcClientFacade and Use

```java
@Autowired
private GrpcClientFacade grpcClientFacade;

public Mono<PrescriptionDTO> getFinalPrescription(String consultationId) {
    PrescriptionRequest request = PrescriptionRequest.newBuilder()
        .setConsultationId(consultationId)
        .build();

    return grpcClientFacade.resolve("prescription", request, PrescriptionDTO.class);
}
```

### 📦 Supported gRPC Resolvers

- ✅ Patient
- ✅ Department
- ✅ Prescription

All can be extended by implementing `GrpcEntityResolver<T, RQ>` and registering via `@GrpcApi`.

