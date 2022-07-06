# Spring Boot Microservices - V2

---------------

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


## Server starting sequence:
1. naming-server
2. limits-service
3. spring-cloud-config-server
4. currency-conversion-microservice
5. currency-exchange-microservice
6. api-gateway-service

---------------

## Microservice Details:

### limits-service (LIMITS-SERVICE)
**_Dependencies_**: Spring Boot Starter Web, Spring Boot Starter Actuator, Spring Cloud Starter Config, Spring Boot Dev Tools

### spring-cloud-config-server (SPRING-CLOUD-CONFIG-SERVER)
**_Dependencies_**: Spring Boot Starter Web, Spring Boot Starter Actuator, Spring Cloud Starter Config, Spring Boot Dev Tools
**_Steps_**:
1. In GIT repository : Save multiple application.properties for each env i.e dev, qa, stag and prod
   e.g ```limits-service-dev.properties``` etc
2. Add below properties in spring-cloud-config-service ```application.properties``` file
```
spring.cloud.config.server.git.uri=https://github.com/sshukla154/micro-service-example
spring.cloud.config.server.git.username=GIT_USERNAME
spring.cloud.config.server.git.password=GIT_PASSWORD
```
3. Now enable SpringCloudConfig Server using ```@EnableConfigServer```

### currency-exchange-microservice (CURRENCY-EXCHANGE-SERVICE)
> **Dependencies**: Spring Boot Starter Web, Spring Boot Starter Actuator, Spring Cloud Starter Config, Spring Boot Dev Tools, Spring boot JPA and Any DB
**_Steps_**:
1. Integrate DB and configure JPA repository to fetch data form DB - TODO: Integrating RDB

### currency-conversion-microservice (CURRENCY-CONVERSION-SERVICE)
> **Dependencies**: Spring Boot Starter Web, Spring Boot Starter Actuator, Spring Cloud Starter Config, Spring Boot Dev Tools, Feign
**_Steps_**:
1. This microservice fetch the data from ```currency-exchange-service```, so we need to have any client like RestTemplate, Feign
2. In this we are integrating FEIGN client as following:
 - Add Feign dependency in pom.xml
   ```
   <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-openfeign</artifactId>
      <version>3.1.3</version>
   </dependency>
   ```
 - Create an interface as below:
   ```
   @FeignClient(name = "CURRENCY-EXCHANGE")
   public interface CurrencyExchangeClient {
    @GetMapping("/currency-exchange/from/{fromCurrency}/to/{toCurrency}")
    CurrencyExchange fetchExchangeValue(@PathVariable String fromCurrency, @PathVariable String toCurrency);
   }
   ```
   In above example, we can also have ```@FeignClient(name = "CURRENCY-EXCHANGE", url = "http://localhost:8000")``` 
   but in this case we are hard-coding the server port, instead we can take above approach where we just define the name of the 
   application and load balancing will be handled by Spring Cloud Load Balancer (present internally in Eureka dependency)


### naming-server (NAMING-SERVER)
> **Dependencies**: Spring Boot Starter Web, Spring Boot Starter Actuator, Spring Cloud Starter Config, Spring Boot Dev Tools, Feign
**_Steps_**:
1. Add Eureka server dependency in pom.xml
```
<dependency>
   <groupId>org.springframework.cloud</groupId>
   <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
</dependency>
```
2. In Application.java add, ```@EnableEurekaServer```
3. In application.properties add to ignore registration of this server
```
#Ignore NAMING-SERVER to register with Eureka
eureka.client.register-with-eureka=false
eureka.client.fetch-registry=false
```
4. Add Eureka client dependency in pom.xml of all the microservices which we need to register with Eureka
```
<dependency>
   <groupId>org.springframework.cloud</groupId>
   <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```
5. In their respective Application.java add, ```@EnableEurekaClient```
6. In application.properties add
```
eureka.client.serviceUrl.defaultZone=http://localhost:8761/eureka
eureka.client.instance.preferIpAddress=true
```

### api-gateway-service (API-GATEWAY-SERVICE)
> **Dependencies**: Spring Boot Starter Actuator, Spring Boot Dev Tools, Spring Cloud Starter Gateway
**_Steps_**:
1. We can have routing class of all the incoming request class e.g. ```by implementing gatewayRouter(RouteLocatorBuilder routeLocatorBuilder) API```
2. We can have logging/tracing class of all the incoming request class e.g. ```by implementing GlobalFilter interface```

---------------
### Configuring Distributed Tracing With Sleuth (for unique ID across all microservices), Zipkin (for dashboard/UI) and RabbitMQ/Kafka (for Asynchronous call)
**_Steps_**:
1. Add all 3 dependencies in pom.xml of all the microservices for which we need to enable tracing.
```
<!-- Dependency for Sleuth -->
<dependency>
   <groupId>org.springframework.cloud</groupId>
   <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
<!-- Dependency for integrating Sleuth with Zipkin -->
<dependency>
   <groupId>org.springframework.cloud</groupId>
   <artifactId>spring-cloud-sleuth-zipkin</artifactId>
</dependency>
<!-- Dependency for Integrating RabbitMQ -->
<dependency>
   <groupId>org.springframework.amqp</groupId>
   <artifactId>spring-rabbit</artifactId>
</dependency>
```
2. Add a Sample bean in Application.java
```
@Bean
public Sampler defaultSampler() {
  return Sampler.ALWAYS_SAMPLE;
}
```
3. Restart all the registered servers and verify
---------------

### Limits Service (Ports: 8080, 8081)

- http://localhost:8080/limits/hardcoded/
- http://localhost:8080/limits/properties/
- http://localhost:8081/limits/hardcoded/
- http://localhost:8081/limits/properties/

### Cloud Config Server (Ports: 8888)

- http://localhost:8888/limits-service/default
- http://localhost:8888/limits-service/dev
- http://localhost:8888/limits-service/qa
- http://localhost:8888/limits-service/stag
- http://localhost:8888/limits-service/prod

### Currency Exchange Service (Ports: 8000, 8001)

- http://localhost:8000/currency-exchange/from/USD/to/INR
- http://localhost:8001/currency-exchange/from/EUR/to/INR

### Currency Conversion Service (Ports: 8100, 8101)

- http://localhost:8100/currency-conversion-rest-template/from/USD/to/INR/quantity/150
- http://localhost:8100/currency-conversion-feign/from/USD/to/INR/quantity/15
- http://localhost:8101/currency-conversion-rest-template/from/USD/to/INR/quantity/1800
- http://localhost:8101/currency-conversion-feign/from/USD/to/INR/quantity/1500

### Eureka (Port: 8761)

- http://localhost:8761/

### Spring Cloud Api Gateway (Port: 8765)

#### Without any changes in application.properties file

- http://localhost:8765/CURRENCY-EXCHANGE/currency-exchange/from/EUR/to/INR
- http://localhost:8765/CURRENCY-CONVERSION/currency-conversion-rest-template/from/EUR/to/INR/quantity/500
- http://localhost:8765/CURRENCY-CONVERSION/currency-conversion-feign/from/EUR/to/INR/quantity/150

#### After adding properties in application.properties file as below

```
#To locate all the API from Eureka/locator
spring.cloud.gateway.discovery.locator.enabled=true

#To change the case from UPPER to LOWER while reading the Application name from Eureka/locator e.g.CURRENCY-CONVERSION to currency-conversion
spring.cloud.gateway.discovery.locator.lowerCaseServiceId=true
```

- http://localhost:8765/currency-exchange/currency-exchange/from/USD/to/INR
- http://localhost:8765/currency-conversion/currency-conversion-rest-template/from/USD/to/INR/quantity/10
- http://localhost:8765/currency-conversion/currency-conversion-feign/from/USD/to/INR/quantity/10

#### Introducing ```ApiGatewayConfiguration``` where need to convert the incoming request of ```currency-exchange/currency-exchange```, ```currency-conversion/currency-conversion-rest-template```, ```currency-conversion/currency-conversion-feign```

```
        Function<PredicateSpec, Buildable<Route>> exchangeRouteFunction = x -> x.path("/currency-exchange/**")
              .uri("lb://currency-exchange");
```

- http://localhost:8765/currency-exchange/from/USD/to/INR
- http://localhost:8765/currency-conversion-rest-template/from/USD/to/INR/quantity/10
- http://localhost:8765/currency-conversion-feign/from/USD/to/INR/quantity/10
- http://localhost:8765/currency-conversion-new/from/USD/to/INR/quantity/10

#### Also, in ```ApiGatewayConfiguration``` we can have redirected of any unavailable to specific URL eg: ```currency-conversion/currency-conversion-new``` to ```currency-conversion/currency-conversion-feign```
```
        Function<PredicateSpec, Buildable<Route>> newFeignConversionRouteFunction = x -> x.path("/currency-conversion-new/**")
                .filters(f -> f.rewritePath("/currency-conversion-new/(?<segment>.*)", "/currency-conversion-feign/${segment}"))
                .uri("lb://currency-conversion");
```

#### Advantages of API-Gateway:

1. Match request on request attribute like (Method name, param type, query etc)
2. Define Predicate and Filter
3. Integrates with Spring Cloud Load Balancer to handle request directly
4. Path Rewriting


##Note:

1. Currently, we have integrated H2 Db in-memory DB in currency-exchange-services, which we need to change to some other
   relational DB
2. Also, we need to introduce Spring Boot Vault for storing the credentials for all the microservices 

