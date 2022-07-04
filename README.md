Spring Boot Microservices - V2

## Spring and Spring Cloud Services Used: 
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

## URLs
### Limits Service (Ports: 8080, 8081)
- http://localhost:8080/limits/hardcoded/
- http://localhost:8080/limits/properties/
- http://localhost:8081/limits/hardcoded/
- http://localhost:8081/limits/properties/

### Cloud Config Server (Ports: 8888)
- http://localhost:8888/LIMITS-SERVICE/default
- http://localhost:8888/LIMITS-SERVICE/dev
- http://localhost:8888/LIMITS-SERVICE/qa
- http://localhost:8888/LIMITS-SERVICE/stag
- http://localhost:8888/LIMITS-SERVICE/prod

### Currency Exchange Service (Ports: 8000, 8001)
- http://localhost:8000/currency-exchange/from/USD/to/INR
- http://localhost:8001/currency-exchange/from/EUR/to/INR

### Currency Conversion Service (Ports: 8100, 8101)
- http://localhost:8100/currency-conversion-rest-template/from/USD/to/INR/quantity/10
- http://localhost:8100/currency-conversion-feign/from/USD/to/INR/quantity/10
- http://localhost:8101/currency-conversion-rest-template/from/USD/to/INR/quantity/100
- http://localhost:8101/currency-conversion-feign/from/USD/to/INR/quantity/100

### Eureka (Ports: 8761)
- http://localhost:8761/
