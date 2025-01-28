# Java and Spring Boot Q&A

## 1. What are the features of Java 8?

- **Lambda Expressions**: Simplify writing inline implementations for functional interfaces.
- **Stream API**: Process collections and arrays in a functional way.
- **Default and Static Methods in Interfaces**: Allow methods with implementations in interfaces.
- **Optional**: Handle `null` values safely to avoid `NullPointerException`.
- **New Date-Time API**: Improved date and time handling using `java.time` package.
- **Functional Interfaces**: Introduced `@FunctionalInterface` and pre-built interfaces like `Predicate`, `Consumer`, etc.

---

## 2. Map vs FlatMap

- **Map**: Transforms each element in a stream and produces a single output for each input.
- **FlatMap**: Transforms each element into multiple elements (a stream of streams is flattened into a single stream).

Example:

```java
List<String> words = List.of("hello", "world");
List<Integer> wordLengths = words.stream().map(String::length).collect(Collectors.toList()); // [5, 5]

List<String> flatMapped = words.stream().flatMap(word -> Arrays.stream(word.split(""))).collect(Collectors.toList());
// [h, e, l, l, o, w, o, r, l, d]
```

---

## 3. What is the Stream API?

The **Stream API** in Java 8 is used to process collections and arrays using functional programming. It allows operations like `map`, `filter`, `reduce`, etc., to be performed in a declarative way.

Example:

```java
List<String> names = List.of("John", "Jane", "Jack");
List<String> filteredNames = names.stream().filter(name -> name.startsWith("J")).collect(Collectors.toList());
```

---

## 4. What are filters and map in Java 8?

- **Filter**: Filters elements based on a condition.
- **Map**: Transforms elements into another form.

Example:

```java
List<Integer> numbers = List.of(1, 2, 3, 4, 5);
List<Integer> evenNumbers = numbers.stream().filter(n -> n % 2 == 0).collect(Collectors.toList()); // [2, 4]
List<Integer> squaredNumbers = numbers.stream().map(n -> n * n).collect(Collectors.toList()); // [1, 4, 9, 16, 25]
```

---

## 5. Find the longest common substring for:

S1 = "homewords"  
S2 = "thisisnewwordblahblah"

**Solution**:
Using dynamic programming:

```java
public static String longestCommonSubstring(String s1, String s2) {
    int m = s1.length(), n = s2.length();
    int[][] dp = new int[m + 1][n + 1];
    int maxLen = 0, endIndex = -1;

    for (int i = 1; i <= m; i++) {
        for (int j = 1; j <= n; j++) {
            if (s1.charAt(i - 1) == s2.charAt(j - 1)) {
                dp[i][j] = dp[i - 1][j - 1] + 1;
                if (dp[i][j] > maxLen) {
                    maxLen = dp[i][j];
                    endIndex = i;
                }
            }
        }
    }
    return endIndex == -1 ? "" : s1.substring(endIndex - maxLen, endIndex);
}
```

### Explanation:

#### **Parameters:**

You have two strings, `s1` and `s2`, and the goal is to find the longest common substring (LCS) between them.

#### **Dynamic Programming Table (dp):**

A 2D array `dp` is created where `dp[i][j]` represents the length of the longest common suffix (substring) ending at `s1[i-1]` and `s2[j-1]`. This table helps avoid redundant calculations.

#### **Variables:**

- `maxLen`: Tracks the length of the longest common substring found so far.
- `endIndex`: Stores the ending index of the longest common substring in `s1`, used to extract the substring.

#### **Main Logic:**

- The outer loops iterate through each character of `s1` and `s2.
- If `s1.charAt(i-1) == s2.charAt(j-1)`, meaning the characters match, the code updates `dp[i][j] = dp[i-1][j-1] + 1`, indicating the common substring has grown by one character.
- If the new length exceeds `maxLen`, update `maxLen` and store the current `i` as `endIndex`.
- If no match, `dp[i][j]` remains 0.

#### **Returning the Result:**

After processing, the method returns the longest common substring using `endIndex` and `maxLen` to slice `s1`.

#### **Time Complexity:**

- **Filling the DP Table:**
  Iterating over each character of `s1` and `s2` results in `O(m * n)` time, where `m` is the length of `s1` and `n` is the length of `s2`.

- **Final Substring Extraction:**
  Extracting the substring from `s1` takes `O(m)` time since we may have to copy up to `m` characters.

#### **Overall Time Complexity:**

The overall time complexity is `O(m * n)`, where `m` and `n` are the lengths of `s1` and `s2`.

---

## 6. How to rollback data in Table 1 if an error occurs in Table 2?

Use Spring's **Transaction Management**:

```java
@Transactional
public void insertData() {
    try {
        table1Repository.save(data1);
        table2Repository.save(data2); // If this fails, everything rolls back
    } catch (Exception e) {
        throw new RuntimeException("Rollback transaction", e);
    }
}
```

---

## 7. What if we don’t want to rollback?

You can use `@Transactional(noRollbackFor = Exception.class)` to not cause a rollback for any Exception:

```java
@Transactional(noRollbackFor = MyCustomException.class)
public void myTransactionalMethod() {
    // Some database operations that won't trigger a rollback for MyCustomException
}


@Transactional(noRollbackFor = Exception.class)
public void myTransactionalMethod() {
    // Database operations that will not cause a rollback for any Exception
}


```

---

## 8. How do you insert data in tables using Hibernate?

Use `save` or `persist` methods provided by the `EntityManager`:

```java
@Autowired
private EntityManager entityManager;

@Transactional
public void insertData(MyEntity entity) {
    entityManager.persist(entity);
}
```

---

## 9. How do you use custom queries?

Using **JPQL** or **Native Queries**:

```java
@Query("SELECT u FROM User u WHERE u.name = :name")
List<User> findUsersByName(@Param("name") String name);

@Query(value = "SELECT * FROM users WHERE name = :name", nativeQuery = true)
List<User> findUsersByNameNative(@Param("name") String name);
```

---

## 10. What are joins? Explain in detail.

**Joins** combine rows from two or more tables based on a related column:

- **Inner Join**: Returns matching records.
- **Left Join**: Returns all records from the left table and matched ones from the right table.
- **Right Join**: Opposite of Left Join.
- **Full Join**: Returns all records with `NULL` for non-matching rows.

Example:

```sql
SELECT e.name, d.name
FROM Employee e
JOIN Department d ON e.dept_id = d.id;
```

---

## 11. What is Eureka used for?

**Eureka** is a service registry in Spring Cloud for service discovery in microservices architecture. It allows services to register themselves and discover other services.

---

## 12. What library do you use for logging purposes?

- Common libraries: **SLF4J**, **Logback**, or **Log4j**.
  Example using Logback:

```properties
logging.level.root=INFO
logging.file.name=app.log
```

---

## 13. What is Amplitude used for?

Amplitude is an analytics platform to track user behavior and product usage patterns, often integrated for insights in applications.

---

## 14. What is Kafka? Explain its working.

**Kafka** is a distributed event-streaming platform used for building real-time data pipelines:

- **Producers**: Publish data to topics.
- **Consumers**: Read data from topics.
- **Topics**: Categories for messages.
- **Brokers**: Store and manage data.

Example use in Spring Boot:

```java
@Autowired
private KafkaTemplate<String, String> kafkaTemplate;

public void sendMessage(String topic, String message) {
    kafkaTemplate.send(topic, message);
}
```

---

## 15. What is Elasticsearch? Explain and give an example.

**Elasticsearch** is a distributed search and analytics engine. It stores data as JSON and provides advanced search capabilities.

Example:

```java
@Document(indexName = "products")
public class Product {
    @Id
    private String id;
    private String name;
    private double price;
}

@Autowired
private ElasticsearchRestTemplate template;

public void saveProduct(Product product) {
    template.save(product);
}
```

---

## 16. How to flatten `[[1, 2, 3], [4, 5, 6]]` into `[1, 2, 3, 4, 5, 6]` using `flatMap`?

Yes, this can be done using `flatMap`. Here’s an example:

```java
import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;

public class FlatMapExample {
    public static void main(String[] args) {
        List<List<Integer>> nestedList = Arrays.asList(
                Arrays.asList(1, 2, 3),
                Arrays.asList(4, 5, 6)
        );

        // Flatten the nested list using flatMap
        List<Integer> flatList = nestedList.stream()
                .flatMap(List::stream)
                .collect(Collectors.toList());

        System.out.println(flatList); // Output: [1, 2, 3, 4, 5, 6]
    }
}
```

### Explanation:

1. **`nestedList.stream()`**: Converts the outer list into a stream.
2. **`flatMap(List::stream)`**: Takes each inner list, converts it into a stream, and flattens it into a single stream.
3. **`collect(Collectors.toList())`**: Collects the flattened stream into a single list.

---

## 17. What is the Bean Lifecycle in Spring Boot?

### Answer:

The **bean lifecycle** in Spring Boot involves the process from the creation of a bean to its destruction within the Spring container. Here's the lifecycle in detail:

1. **Instantiation**:

   - The bean is instantiated using its constructor.

2. **Populating Properties**:

   - Dependencies are injected (via constructor, setter, or field injection).

3. **Set Bean Name**:

   - The `setBeanName()` method of `BeanNameAware` interface is called if implemented.

4. **Set Bean Factory**:

   - The `setBeanFactory()` method of `BeanFactoryAware` is called if implemented.

5. **Post-Initialization**:

   - Any `BeanPostProcessor` implementations process the bean (before and after initialization).

6. **Custom Initialization**:

   - The `afterPropertiesSet()` method of `InitializingBean` or custom methods annotated with `@PostConstruct` are executed.

7. **Ready for Use**:

   - The bean is fully initialized and ready to use.

8. **Destruction**:
   - When the application shuts down, `@PreDestroy` or methods in the `DisposableBean` interface are executed.

### Example:

```java
@Component
public class MyBean {

    @PostConstruct
    public void init() {
        System.out.println("PostConstruct: Bean is initialized");
    }

    @PreDestroy
    public void destroy() {
        System.out.println("PreDestroy: Bean is about to be destroyed");
    }
}
```

---

## 18. What Are Various Bean Lifecycle Scopes in Spring Boot?

### Answer:

The **scope** of a bean determines its lifecycle and visibility within the Spring container. Here are the types:

### 1. Singleton Scope (Default):

- **Description**: Single instance per Spring container.
- **Example**:

```java
@Component
public class SingletonBean {
    public SingletonBean() {
        System.out.println("Singleton instance created");
    }
}
```

### 2. Prototype Scope:

- **Description**: New instance created every time the bean is requested.
- **Example**:

```java
@Scope("prototype")
@Component
public class PrototypeBean {
    public PrototypeBean() {
        System.out.println("Prototype instance created");
    }
}
```

### 3. Request Scope (Web Applications Only):

- **Description**: One instance per HTTP request.
- **Example**:

```java
@Scope("request")
@Component
public class RequestScopedBean {
    public RequestScopedBean() {
        System.out.println("Request scoped instance created");
    }
}
```

### 4. Session Scope (Web Applications Only):

- **Description**: One instance per HTTP session.
- **Example**:

```java
@Scope("session")
@Component
public class SessionScopedBean {
    public SessionScopedBean() {
        System.out.println("Session scoped instance created");
    }
}
```

### 5. Application Scope:

- **Description**: Shared across the entire application.
- **Example**:

```java
@Scope("application")
@Component
public class ApplicationScopedBean {
    public ApplicationScopedBean() {
        System.out.println("Application scoped instance created");
    }
}
```

### 6. Custom Scope:

- **Description**: Define a custom scope.
- **Example**:

```java
@Scope("custom")
@Component
public class CustomScopedBean {
    public CustomScopedBean() {
        System.out.println("Custom scoped instance created");
    }
}
```

---

## 19. How Do You Define Bean Scope in Spring Boot?

### Answer:

1. **Using Annotations**:

   - Example:

   ```java
   @Component
   @Scope("prototype")
   public class MyBean {
       public MyBean() {
           System.out.println("Prototype instance created");
       }
   }
   ```

2. **Using Java Config**:

   - Example:

   ```java
   @Bean
   @Scope("singleton")
   public MyBean myBean() {
       return new MyBean();
   }
   ```

3. **Using XML Config**:
   - Example:
   ```xml
   <bean id="myBean" class="com.example.MyBean" scope="prototype" />
   ```

---

## 20. How Do You Manage Memory in Java?

### Answer:

Memory management in Java is handled automatically by the **Java Virtual Machine (JVM)** through garbage collection. Here are the key aspects:

### 1. Memory Areas:

- **Heap Memory**: Used to store objects and JRE classes.
- **Stack Memory**: Stores method calls and local variables.
- **Metaspace**: Stores class metadata (introduced in Java 8).

### 2. Automatic Garbage Collection:

- The JVM automatically identifies and removes objects that are no longer in use.
- Common garbage collectors:
  - Serial GC
  - Parallel GC
  - G1 GC
  - ZGC

### 3. Best Practices for Memory Management:

- Use local variables wherever possible.
- Avoid creating unnecessary objects.
- Use appropriate data structures.
- Explicitly set objects to `null` when they are no longer needed (optional).
- Monitor memory usage using tools like **VisualVM**, **JConsole**, or profilers.

### Example:

```java
public class MemoryExample {
    public static void main(String[] args) {
        // Create an object
        String str = new String("Hello");

        // Object becomes eligible for GC after setting it to null
        str = null;

        // Request garbage collection (not guaranteed to run immediately)
        System.gc();
    }

    @Override
    protected void finalize() throws Throwable {
        System.out.println("Garbage collected");
    }
}
```

---
