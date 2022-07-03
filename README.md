Spring Boot Microservices - V2

## Spring and Spring Services Used: 
1. Spring Cloud Config Server and Bus
2. Load balancing with Ribbon and Feign (V2: Spring Cloud Load Balancer)
3. Naming server with Eureka
4. API Gateway with Zulu (V2: Spring Cloud Gateway)
5. Distributed tracing with Zipkin
6. Fault tolerance with Hystrix (V2: Resilience4j)

## Limitations of microservices:
1. Bounded context of each microservice
2. Configuration management
3. Dynamic scale up and scale down
4. Visibility - Traceable/ Monitoring
5. Pack of cards - if all microservices are not designed properly then its like a pack of cards - Fault tolerance

## Advantages of microservices:
1. New technology and process adaptation
2. Dynamic scaling
3. Faster release cycle

## Microservice Details:

### limits-service (LIMITS-SERVICE)
**_Dependencies_**: Spring Boot Starter Web, Spring Boot Starter Actuator, Spring Cloud Starter Config, Spring Boot Dev Tools

### spring-cloud-config-server (SPRING-CLOUD-CONFIG-SERVER)
**_Dependencies_**: Spring Boot Starter Web, Spring Boot Starter Actuator, Spring Cloud Starter Config, Spring Boot Dev Tools
**_Steps_**:
1. In GIT repository : Save multiple application.properties for each env i.e dev, qa, stag and prod e.g ```limits-service-dev.properties``` etc
2. Add below properties in spring-cloud-config-service ```application.properties``` file
```
spring.cloud.config.server.git.uri=https://github.com/sshukla154/micro-service-example
spring.cloud.config.server.git.username=GIT_USERNAME
spring.cloud.config.server.git.password=GIT_PASSWORD
```
3. Now enable SpringCloudConfig Server using ```@EnableConfigServer```

## Ports

|     Application       |     Port          |
| ------------- | ------------- |
| Limits Service | 8080, 8081, ... |
| Spring Cloud Config Server | 8888 |


## URLs
### Limits Service
- http://localhost:8080/limits/hardcoded/
- http://localhost:8080/limits/properties/

### Cloud Config Server
- http://localhost:8888/LIMITS-SERVICE/default
- http://localhost:8888/LIMITS-SERVICE/dev
- http://localhost:8888/LIMITS-SERVICE/qa
- http://localhost:8888/LIMITS-SERVICE/stag
- http://localhost:8888/LIMITS-SERVICE/prod

