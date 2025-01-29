## 1. Java 21 Features

**Answer:**

1. **Record Patterns** - Enables deconstruction of records for pattern matching.
2. **Virtual Threads** - Lightweight threads that significantly improve scalability.
3. **Sequenced Collections** - Introduces interfaces like `SequencedCollection`, `SequencedSet`, `SequencedMap`.
4. **Pattern Matching for `switch`** - More flexible and expressive switch statements.
5. **Unnamed Classes & Instance Main Methods** - Simplifies code by removing unnecessary boilerplate.
6. **String Templates (Preview)** - Improves string concatenation.

---

## 2. Java 17 Features

**Answer:**

1. **Sealed Classes** - Restricts which classes can extend a superclass.
2. **Pattern Matching for `switch` (Preview)** - Expands pattern matching capabilities.
3. **New macOS Rendering Pipeline** - Uses Metal for rendering.
4. **Removal of Deprecated Features** - No longer supports security manager, RMI activation system, etc.
5. **Foreign Function & Memory API (Incubator)** - Enhances interoperability with native code.

---

## 3. Java 11 Features

**Answer:**

1. **Var in Lambda Expressions** - Enables the use of `var` in lambda parameters.
2. **New `String` Methods** - `isBlank()`, `lines()`, `strip()`, `repeat()`.
3. **HTTP Client (Standard)** - Introduces a fully asynchronous HTTP client.
4. **Garbage Collector (Epsilon)** - Introduces a no-op GC.
5. **Flight Recorder** - A profiling and diagnostic tool.

---

## 4. What is Actuator?

**Answer:**  
Spring Boot Actuator provides production-ready features such as monitoring, metrics, health checks, and logging.

---

## 5. How to Change Head URI in Actuator?

**Answer:**  
Update `management.endpoints.web.base-path` in `application.properties`:

```properties
management.endpoints.web.base-path=/new-actuator
```

---

## 6. N+1 Problem in Hibernate

**Answer:**  
Occurs when an initial query loads a list of entities, but additional queries fetch related data for each entity individually, leading to performance degradation.

### Solution:

- Use `JOIN FETCH`
- Enable `@BatchSize`
- Use Entity Graphs
- Use pagination

---

## 7. Hibernate Features

**Answer:**

### 1. **Lazy & Eager Loading**

**Explanation:**

- **Lazy Loading**: Data is loaded only when it's accessed for the first time. It's useful for optimizing performance by reducing unnecessary queries.
- **Eager Loading**: Data is loaded immediately along with the parent entity, regardless of whether it is accessed.

**Implementation in Spring Boot:**

```java
@Entity
public class Author {

    @OneToMany(fetch = FetchType.LAZY)
    private List<Book> books; // Lazy loading by default

    @OneToMany(fetch = FetchType.EAGER)
    private List<Book> booksEager; // Eager loading
}
```

In this example, `books` will be lazily loaded, while `booksEager` will be eagerly loaded.

### 2. **Caching (First & Second Level)**

**Explanation:**

- **First-Level Cache**: The default cache in Hibernate, scoped to the current session. It caches data that has been loaded or modified during the session.
- **Second-Level Cache**: A session factory-wide cache shared across sessions. It's useful for caching data across multiple sessions (e.g., for frequently accessed data).

**Implementation in Spring Boot:**

To enable **second-level cache**, you need to include the cache provider (e.g., Ehcache, Hazelcast) and configure it in `application.properties`.

```properties
spring.jpa.properties.hibernate.cache.use_second_level_cache=true
spring.jpa.properties.hibernate.cache.region.factory_class=org.hibernate.cache.ehcache.EhCacheRegionFactory
```

For **first-level cache**, Hibernate handles this automatically, so you don't need extra configuration.

For example:

```java
Book book = entityManager.find(Book.class, 1);  // First-level cache in action
```

### 3. **Criteria API for Querying**

**Explanation:**  
The Criteria API is a type-safe way to build dynamic queries in Hibernate. It's useful when building complex queries that may change based on user input or other conditions.

**Implementation in Spring Boot:**

```java
@Autowired
private EntityManager entityManager;

public List<Book> findBooksByTitle(String title) {
    CriteriaBuilder cb = entityManager.getCriteriaBuilder();
    CriteriaQuery<Book> cq = cb.createQuery(Book.class);
    Root<Book> book = cq.from(Book.class);

    cq.select(book).where(cb.equal(book.get("title"), title));

    return entityManager.createQuery(cq).getResultList();
}
```

This query dynamically selects books based on the provided title.

### 4. **Automatic Schema Generation**

**Explanation:**  
Hibernate can automatically generate the database schema (tables, constraints, etc.) based on the entities and their mappings. This is great for development, as it reduces the need for manual database schema creation.

**Implementation in Spring Boot:**

You can configure this feature in `application.properties`.

```properties
spring.jpa.hibernate.ddl-auto=update  # or 'create' for a fresh schema, 'validate' for validation
```

- `create`: Drops the existing schema and creates a new one.
- `update`: Updates the existing schema to match the entity model.
- `validate`: Validates that the schema matches the entity model (without making any changes).
- `none`: Disables automatic schema generation.

### 5. **Transaction Management**

**Explanation:**  
Hibernate integrates seamlessly with Spring's transaction management system, ensuring that operations are atomic and consistent. It supports both programmatic and declarative transaction management.

**Implementation in Spring Boot:**

For **declarative transaction management**, you can use `@Transactional` annotations.

```java
@Service
public class BookService {

    @Autowired
    private BookRepository bookRepository;

    @Transactional
    public void addBook(Book book) {
        bookRepository.save(book);
        // Automatically wrapped in a transaction
    }
}
```

To enable transaction management, ensure that you have `@EnableTransactionManagement` in your configuration class:

```java
@Configuration
@EnableTransactionManagement
public class AppConfig {
    // other beans and configurations
}
```

This ensures that the `addBook` method is wrapped in a transaction.

---

## 8. Difference Between Observable and Promise

**Answer:**

| Feature            | Observable | Promise |
| ------------------ | ---------- | ------- |
| Lazy Execution     | Yes        | No      |
| Multiple Emissions | Yes        | No      |
| Operators Support  | Yes        | No      |
| Cancelable         | Yes        | No      |

---

## 9. Creating a Custom Immutable Class

**Answer:**

### Rules for Creating an Immutable Class in Java:

1. **Declare the class as `final`**  
   The class itself should be `final` to prevent subclassing. Subclassing could allow modification of the object's state.

   ```java
   public final class Person {
       // Fields and methods
   }
   ```

2. **Make all fields `private` and `final`**  
   The fields should be `private` to restrict direct access and `final` to ensure they are only assigned once.

   ```java
   private final String name;
   private final int age;
   ```

3. **Do not provide setters**  
   To ensure that the fields cannot be modified after the object is created, do not provide setter methods.

4. **Initialize all fields through the constructor**  
   Initialize all fields in the constructor so that they are set during object creation. The constructor should be the only way to modify the fields.

   ```java
   public Person(String name, int age) {
       this.name = name;
       this.age = age;
   }
   ```

5. **Ensure deep copying for mutable objects**  
   If the class contains fields that are mutable objects (e.g., arrays, collections, etc.), make sure to either:

   - **Copy the object** during construction and provide a copy when accessed.
   - **Use immutable objects** for the fields.

   ```java
   private final List<String> hobbies;

   public Person(String name, int age, List<String> hobbies) {
       this.name = name;
       this.age = age;
       // Create a defensive copy to prevent external modifications
       this.hobbies = new ArrayList<>(hobbies);
   }

   public List<String> getHobbies() {
       return new ArrayList<>(hobbies);  // Returning a copy, not the original list
   }
   ```

6. **Return copies for mutable objects**  
   When exposing mutable objects (like arrays or lists), always return a copy of the object, not the original reference, to maintain immutability.

```java
public final class ImmutableClass {
    private final String name;

    public ImmutableClass(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }
}
```

---

## 10. Custom Linked List and Swap Nodes

**Answer:**

```java
class Node {
    int data;
    Node next;

    Node(int data) {
        this.data = data;
        this.next = null;
    }
}

class LinkedList {
    Node head;

    void append(int data) {
        if (head == null) {
            head = new Node(data);
            return;
        }
        Node temp = head;
        while (temp.next != null) {
            temp = temp.next;
        }
        temp.next = new Node(data);
    }

    void swapPairs() {
        Node temp = head;
        while (temp != null && temp.next != null) {
            int k = temp.data;
            temp.data = temp.next.data;
            temp.next.data = k;
            temp = temp.next.next;
        }
    }

    void print() {
        Node temp = head;
        while (temp != null) {
            System.out.print(temp.data + " -> ");
            temp = temp.next;
        }
        System.out.println("null");
    }
}
```

---

## 11. Find Missing Element in Array from range 1 to 10

**Answer:**

```java
public class MissingNumber {
    public static void main(String[] args) {
        int[] arr = {1, 2, 3, ..., 99}; // missing a number
        int expectedSum = 100 * 99 / 2;
        int actualSum = Arrays.stream(arr).sum();
        System.out.println("Missing Number: " + (expectedSum - actualSum));
    }
}
```

---

## 12. What is `@Qualifier`?

**Answer:**

It is used to resolve bean conflicts when multiple beans of the same type exist.

```java
@Autowired
@Qualifier("beanName")
private MyService myService;
```

---

## 13. Why Use Interface with `@Autowired`?

**Answer:**
Using interfaces promotes loose coupling and enables dependency injection flexibility.

---

## 14. Can We Use a Class Instead of an Interface in `@Autowired`?

**Answer:**
Yes, but using a class instead of an interface reduces flexibility and breaks the Dependency Inversion Principle, effectively tightly coupling the code.

---

## 15. Do We Need an Interface for `@Autowired`?

No, you can directly use a class, but it’s not a best practice.

---
