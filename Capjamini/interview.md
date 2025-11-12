## 1. What is the Decorator Pattern?

The **Decorator Pattern** allows behavior to be added to individual objects dynamically by using composition instead of inheritance.

```java
// Component interface
interface Coffee {
    double cost();
    String description();
}

// Concrete Component
class SimpleCoffee implements Coffee {
    public double cost() { return 1.0; }
    public String description() { return "Simple Coffee"; }
}

// Decorator
abstract class CoffeeDecorator implements Coffee {
    protected Coffee decoratedCoffee;

    public CoffeeDecorator(Coffee coffee) {
        this.decoratedCoffee = coffee;
    }

    public double cost() { return decoratedCoffee.cost(); }
    public String description() { return decoratedCoffee.description(); }
}

// Concrete Decorators
class Milk extends CoffeeDecorator {
    public Milk(Coffee coffee) {
        super(coffee);
    }

    public double cost() { return super.cost() + 0.5; }
    public String description() { return super.description() + ", milk"; }
}
```

---

## 2. Different Types of Design Patterns (Briefly Explained)

Design patterns are categorized into three main types:

### Creational Design Patterns

These patterns provide ways to create objects while hiding the creation logic, rather than instantiating objects directly.

- **Singleton**: Ensures a class has only one instance and provides a global access point.
- **Factory Method**: Creates objects without specifying the exact class.
- **Abstract Factory**: Provides an interface for creating families of related objects.
- **Builder**: Constructs complex objects step by step.
- **Prototype**: Clones existing objects instead of creating new ones.

### Structural Design Patterns

These patterns deal with object composition, ensuring that different parts of a system work together efficiently.

- **Adapter**: Allows incompatible interfaces to work together.
- **Bridge**: Decouples abstraction from implementation.
- **Composite**: Organizes objects into tree structures to represent part-whole hierarchies.
- **Decorator**: Adds responsibilities to objects dynamically.
- **Facade**: Provides a simplified interface to a larger system.
- **Flyweight**: Minimizes memory usage by sharing common data.
- **Proxy**: Controls access to an object by acting as an intermediary.

### Behavioral Design Patterns

These patterns focus on communication between objects, ensuring flexibility and easy maintenance.

- **Chain of Responsibility**: Passes a request along a chain of handlers.
- **Command**: Encapsulates a request as an object.
- **Interpreter**: Implements a grammar for interpreting statements.
- **Iterator**: Provides a way to access elements sequentially without exposing underlying representation.
- **Mediator**: Defines an object that encapsulates communication between multiple objects.
- **Memento**: Captures an object’s state for restoration later.
- **Observer**: Allows multiple objects to observe and react to state changes.
- **State**: Allows an object to change its behavior when its state changes.
- **Strategy**: Defines a family of algorithms and makes them interchangeable.
- **Template Method**: Defines the skeleton of an algorithm but lets subclasses modify specific steps.
- **Visitor**: Separates algorithms from the objects on which they operate.

---

## 3. Parallel Stream vs. Sequential Stream

- **Sequential Stream** processes data in a single thread, one element at a time.
- **Parallel Stream** divides the data into multiple chunks and processes them concurrently using multiple threads, improving performance for large datasets.

### 3.1 How Will Output Be Different in Parallel Stream vs. Sequential Stream?

Sequential stream maintains the order of elements, while parallel stream may result in unpredictable ordering.

```java
import java.util.*;
import java.util.stream.*;

public class StreamExample {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

        System.out.println("Sequential Stream:");
        numbers.stream().forEach(System.out::println);

        System.out.println("Parallel Stream:");
        numbers.parallelStream().forEach(System.out::println);
    }
}
```

Key differences:

- Sequential streams process elements one after another in a single thread
- Parallel streams split the data into multiple parts and process them concurrently
- Output order is guaranteed in sequential streams but not in parallel streams
- Parallel streams are beneficial for CPU-intensive operations on large datasets

---

## 4. Propagation vs. Propagation New

### Transaction Propagation

1. **REQUIRED** (Default)

   - Supports current transaction
   - Creates new if none exists

2. **REQUIRES_NEW**
   - Always creates new transaction
   - Suspends current transaction if exists

Main differences:

- REQUIRED uses existing transaction if available
- REQUIRES_NEW always creates new transaction regardless

---

## 5. Different Types of Locking in Hibernate

- **Optimistic Locking**: Uses versioning to prevent conflicts.
  - Uses version column
  - Throws exception if version mismatch
  - Good for low contention
- **Pessimistic Locking**: Locks the record to prevent concurrent modifications.
  - PESSIMISTIC_READ
  - PESSIMISTIC_WRITE
  - PESSIMISTIC_FORCE_INCREMENT
- **Explicit Locking**: Uses database-level locks (e.g., `SELECT FOR UPDATE`).

---

## 6. Create a Controller in Spring Boot Without `@RestController` or `@Controller`

Use `@Component` or `@Service` along with `DispatcherServlet` to manually handle requests.

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import java.io.IOException;

@Component
public class CustomHandler {
    @Autowired
    private HttpServletRequest request;

    @Autowired
    private HttpServletResponse response;

    public void handleRequest() throws IOException {
        response.setContentType("text/plain");
        response.getWriter().write("Handling request: " + request.getRequestURI());
    }
}
```

---

## 7. What is ExecutorService?

A framework for asynchronous task execution:

```java
ExecutorService executor = Executors.newFixedThreadPool(5);
Future<String> future = executor.submit(() -> "Task Result");
```

---

## 8. Configure Two Different Databases in a Spring Boot App

When your Spring Boot application needs to connect to multiple databases (for example, PostgreSQL for user data and MySQL for orders), you need to configure **two separate DataSources**, **EntityManagerFactories**, and **TransactionManagers**.

---

### 🧩 Scenario

Example setup:

- **User DB:** PostgreSQL
- **Order DB:** MySQL

---

### 1️⃣ Add Configuration in `application.yml`

```yaml
spring:
  datasource:
    userdb:
      url: jdbc:postgresql://localhost:5432/userdb
      username: postgres
      password: secret
      driver-class-name: org.postgresql.Driver

    orderdb:
      url: jdbc:mysql://localhost:3306/orderdb
      username: root
      password: root
      driver-class-name: com.mysql.cj.jdbc.Driver
```

---

### 2️⃣ Create Entities

#### User Entity

```java
@Entity
@Table(name = "users")
public class User {
    @Id
    private Long id;
    private String name;
}
```

#### Order Entity

```java
@Entity
@Table(name = "orders")
public class Order {
    @Id
    private Long id;
    private String product;
}
```

---

### 3️⃣ Create Repositories

#### User Repository

```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {}
```

#### Order Repository

```java
@Repository
public interface OrderRepository extends JpaRepository<Order, Long> {}
```

---

### 4️⃣ Configure the Primary Database (User DB)

```java
@Configuration
@EnableTransactionManagement
@EnableJpaRepositories(
        basePackages = "com.example.user",
        entityManagerFactoryRef = "userEntityManagerFactory",
        transactionManagerRef = "userTransactionManager"
)
public class UserDBConfig {

    @Primary
    @Bean(name = "userDataSource")
    @ConfigurationProperties(prefix = "spring.datasource.userdb")
    public DataSource userDataSource() {
        return DataSourceBuilder.create().build();
    }

    @Primary
    @Bean(name = "userEntityManagerFactory")
    public LocalContainerEntityManagerFactoryBean userEntityManagerFactory(
            EntityManagerFactoryBuilder builder,
            @Qualifier("userDataSource") DataSource dataSource
    ) {
        return builder
                .dataSource(dataSource)
                .packages("com.example.user")
                .persistenceUnit("userdb")
                .build();
    }

    @Primary
    @Bean(name = "userTransactionManager")
    public PlatformTransactionManager userTransactionManager(
            @Qualifier("userEntityManagerFactory") EntityManagerFactory emf
    ) {
        return new JpaTransactionManager(emf);
    }
}
```

---

### 5️⃣ Configure the Secondary Database (Order DB)

```java
@Configuration
@EnableTransactionManagement
@EnableJpaRepositories(
        basePackages = "com.example.order",
        entityManagerFactoryRef = "orderEntityManagerFactory",
        transactionManagerRef = "orderTransactionManager"
)
public class OrderDBConfig {

    @Bean(name = "orderDataSource")
    @ConfigurationProperties(prefix = "spring.datasource.orderdb")
    public DataSource orderDataSource() {
        return DataSourceBuilder.create().build();
    }

    @Bean(name = "orderEntityManagerFactory")
    public LocalContainerEntityManagerFactoryBean orderEntityManagerFactory(
            EntityManagerFactoryBuilder builder,
            @Qualifier("orderDataSource") DataSource dataSource
    ) {
        return builder
                .dataSource(dataSource)
                .packages("com.example.order")
                .persistenceUnit("orderdb")
                .build();
    }

    @Bean(name = "orderTransactionManager")
    public PlatformTransactionManager orderTransactionManager(
            @Qualifier("orderEntityManagerFactory") EntityManagerFactory emf
    ) {
        return new JpaTransactionManager(emf);
    }
}
```

---

### 6️⃣ Service Layer Example

```java
@Service
public class MultiDBService {

    private final UserRepository userRepo;
    private final OrderRepository orderRepo;

    @Autowired
    public MultiDBService(UserRepository userRepo, OrderRepository orderRepo) {
        this.userRepo = userRepo;
        this.orderRepo = orderRepo;
    }

    @Transactional("userTransactionManager")
    public void saveUser(User user) {
        userRepo.save(user);
    }

    @Transactional("orderTransactionManager")
    public void saveOrder(Order order) {
        orderRepo.save(order);
    }
}
```

---

### 🧠 Key Notes for Interviews

| Aspect                    | Explanation                                                                 |
| ------------------------- | --------------------------------------------------------------------------- |
| **Multiple DBs**          | Each needs its own DataSource, EntityManagerFactory, and TransactionManager |
| **@Primary**              | Marks which bean should be used when multiple candidates exist              |
| **Packages**              | Repositories and Entities must be grouped and mapped separately             |
| **Transactions**          | Use the correct TransactionManager for each DB                              |
| **Cross-DB Transactions** | Require a JTA (Atomikos / Bitronix) setup                                   |

---

### ⚡ Quick Recap

```
[UserDBConfig]
 ├── DataSource (userDataSource)
 ├── EntityManagerFactory (userEntityManagerFactory)
 └── TransactionManager (userTransactionManager)

[OrderDBConfig]
 ├── DataSource (orderDataSource)
 ├── EntityManagerFactory (orderEntityManagerFactory)
 └── TransactionManager (orderTransactionManager)
```

---

### ✅ TL;DR

1. Define multiple data sources in YAML.
2. Create config classes for each DB.
3. Set up entity + repo packages per DB.
4. Use the proper TransactionManager for each operation.

---

## 9. How to Implement JWT in Your Application

- Use **jjwt** library to generate and validate JWT tokens.
- Store the token in the `Authorization` header.
- Implement an authentication filter to validate JWT in each request.

```java
public String generateToken(UserDetails userDetails) {
    return Jwts.builder()
        .setSubject(userDetails.getUsername())
        .setIssuedAt(new Date())
        .setExpiration(new Date(System.currentTimeMillis() + JWT_TOKEN_VALIDITY))
        .signWith(SignatureAlgorithm.HS512, secret)
        .compact();
}
```

---

## 10. Does the `finally` block execute after a `return` statement or `System.exit()` in try-catch?

- **After `return`**: Yes, `finally` executes before returning.
- **After `System.exit()`**: No, as `System.exit()` terminates the JVM.

## 11. Different Components of Microservices

Modern microservices-based systems are built on several key components that ensure scalability, fault tolerance, and maintainability. Below is a breakdown of the **core building blocks** you should know for interviews.

---

### 🧩 1️⃣ API Gateway

#### 🔹 What It Is

The **API Gateway** is the **entry point** for all client requests in a microservices system.

#### 🔹 Responsibilities

- Routes requests to the appropriate microservice
- Handles authentication & authorization
- Performs load balancing, caching, and rate limiting
- Transforms or aggregates responses from multiple services

#### 🔹 Examples

- **Spring Cloud Gateway**
- **Kong**, **NGINX**, **Zuul**, **Traefik**

#### 🧠 Interview Tip

> “The API Gateway is like a receptionist — it receives all external requests and directs them to the right microservice.”

---

### 🧭 2️⃣ Service Discovery

#### 🔹 What It Is

In a dynamic microservices environment (where instances scale up/down), **Service Discovery** helps locate services automatically instead of hardcoding URLs.

#### 🔹 Types

- **Client-Side Discovery:** The client looks up the service registry and calls the instance directly.
- **Server-Side Discovery:** The API Gateway or load balancer queries the registry and forwards the request.

#### 🔹 Examples

- **Netflix Eureka**
- **Consul**
- **Zookeeper**
- **Kubernetes Service Registry**

#### ⚙️ Example Flow

1. Service registers itself to the discovery server (e.g., Eureka).
2. When another service needs it, it queries Eureka for the current instance’s location.
3. Eureka returns a live instance address.

---

### ⚙️ 3️⃣ Configuration Server

#### 🔹 What It Is

Manages **centralized configuration** for all microservices, so you don’t have to store environment-specific configs inside each app.

#### 🔹 Benefits

- Centralized management of config values (URLs, credentials, feature flags, etc.)
- Supports versioning and rollback via Git
- Dynamic reloading of configurations

#### 🔹 Example

- **Spring Cloud Config Server**

#### ⚙️ Example Flow

1. Config Server loads configurations from Git.
2. Microservices fetch their configs at startup (or dynamically via `/refresh`).

---

### ⚡ 4️⃣ Circuit Breaker

#### 🔹 What It Is

A **Circuit Breaker** prevents cascading failures when one microservice fails or becomes slow.

Think of it like a fuse — if one service starts failing, it “opens the circuit” to prevent continuous failed calls.

#### 🔹 States

- **Closed:** Requests flow normally.
- **Open:** Requests fail immediately (avoiding strain on the failing service).
- **Half-Open:** Test a few requests before closing again.

#### 🔹 Tools

- **Netflix Hystrix (legacy but popular)**
- **Resilience4j (modern replacement)**

#### ⚙️ Example

If `PaymentService` keeps failing, `OrderService` will trip the circuit and use a **fallback** like:

> “Payment service is down, please try again later.”

---

### ⚖️ 5️⃣ Load Balancer

#### 🔹 What It Is

Distributes incoming traffic evenly across multiple service instances to prevent overload on a single instance.

#### 🔹 Types

- **Client-side (Ribbon):** The client decides which instance to call.
- **Server-side (NGINX, AWS ELB):** A proxy or load balancer decides.

#### 🔹 Examples

- **Ribbon** (deprecated in Spring Cloud)
- **Spring Cloud LoadBalancer**
- **NGINX**, **HAProxy**, **AWS ALB/ELB**

#### ⚙️ Example

If 3 instances of `UserService` are running, the load balancer distributes traffic among them automatically.

---

### 📊 6️⃣ Logging & Monitoring

#### 🔹 What It Is

Centralized logging and monitoring systems give visibility into what’s happening across services.

#### 🔹 Why Important

With dozens of microservices, traditional local logs don’t cut it. You need to track requests across services and monitor performance, latency, and errors.

#### 🔹 Common Tools

| Type           | Tools                                                |
| -------------- | ---------------------------------------------------- |
| **Logging**    | ELK Stack (Elasticsearch, Logstash, Kibana), Fluentd |
| **Monitoring** | Prometheus, Grafana, Datadog                         |
| **Tracing**    | Zipkin, Jaeger                                       |

#### ⚙️ Example Setup

- Each service sends logs to **Logstash → Elasticsearch**
- **Kibana** visualizes logs
- **Prometheus** scrapes metrics exposed by microservices
- **Grafana** visualizes metrics (dashboards)

---

#### 🧠 Summary Table

| Component                | Responsibility             | Common Tools                     |
| ------------------------ | -------------------------- | -------------------------------- |
| **API Gateway**          | Entry point, routing, auth | Spring Cloud Gateway, Zuul, Kong |
| **Service Discovery**    | Dynamic service registry   | Eureka, Consul, Zookeeper        |
| **Config Server**        | Centralized configs        | Spring Cloud Config              |
| **Circuit Breaker**      | Failure isolation          | Hystrix, Resilience4j            |
| **Load Balancer**        | Traffic distribution       | Ribbon, NGINX, AWS ELB           |
| **Logging & Monitoring** | Observability              | ELK Stack, Prometheus, Grafana   |

---

#### 💡 TL;DR for Interviews

- API Gateway → Routing & Auth
- Service Discovery → Dynamic registration
- Config Server → Centralized configuration
- Circuit Breaker → Fault tolerance
- Load Balancer → Scalability
- Logging & Monitoring → Observability & insights

---

## 12. What is Fault Isolation?

Fault isolation ensures that failures in one microservice do not affect the entire system. This is achieved through circuit breakers, retries, and fallback mechanisms.

## 13. What Happens if You Call a Static Method from a Null Reference?

```java
Test t = null;
t.call();
```

- It will execute the static method without throwing a `NullPointerException` because static methods are associated with the class, not an instance.

## 14. What is a Bad Request Error?

A **400 Bad Request** error occurs when the server cannot process the request due to malformed syntax or invalid data.

## 15. All HTTP Status Code Series

- **1xx (Informational)**: Request received, continuing process.
- **2xx (Success)**: Action successfully received, understood, and accepted.
- **3xx (Redirection)**: Further action needed to complete request.
- **4xx (Client Errors)**: Request contains bad syntax or cannot be fulfilled.
- **5xx (Server Errors)**: Server failed to fulfill a valid request.

## 16. Filter a List of Strings Based on Palindromes

### Given List:

```java
List<String> words = Arrays.asList("abc", "def", "aaa", "PpP", "par");
```

### Expected Output:

```java
["aaa", "PpP"]
```

### Java Code:

```java
import java.util.*;
import java.util.stream.*;

public class PalindromeFilter {
    public static void main(String[] args) {
        List<String> words = Arrays.asList("abc", "def", "aaa", "PpP", "par");
        List<String> palindromes = words.stream()
                .filter(word -> word.equalsIgnoreCase(new StringBuilder(word).reverse().toString()))
                .collect(Collectors.toList());
        System.out.println(palindromes);
    }
}
```
