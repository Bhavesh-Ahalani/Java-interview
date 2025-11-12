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

```java
@Configuration
public class MultipleDBConfig {
    @Primary
    @Bean(name = "db1DataSource")
    @ConfigurationProperties(prefix = "spring.datasource.db1")
    public DataSource db1DataSource() {
        return DataSourceBuilder.create().build();
    }

    @Bean(name = "db2DataSource")
    @ConfigurationProperties(prefix = "spring.datasource.db2")
    public DataSource db2DataSource() {
        return DataSourceBuilder.create().build();
    }
}
```

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

- API Gateway
- Service Discovery
- Configuration Server
- Circuit Breaker (e.g., Hystrix)
- Load Balancer
- Logging & Monitoring (e.g., ELK Stack, Prometheus)

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
