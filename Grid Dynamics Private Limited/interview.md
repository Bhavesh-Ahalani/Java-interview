## 1. Java 21 Features

1. **Record Patterns** - Enables deconstruction of records for pattern matching.
2. **Virtual Threads** - Lightweight threads that significantly improve scalability.
3. **Sequenced Collections** - Introduces interfaces like `SequencedCollection`, `SequencedSet`, `SequencedMap`.
4. **Pattern Matching for `switch`** - More flexible and expressive switch statements.
5. **Unnamed Classes & Instance Main Methods** - Simplifies code by removing unnecessary boilerplate.
6. **String Templates (Preview)** - Improves string concatenation.

---

## 2. Java 17 Features

1. **Sealed Classes** - Restricts which classes can extend a superclass.
2. **Pattern Matching for `switch` (Preview)** - Expands pattern matching capabilities.
3. **New macOS Rendering Pipeline** - Uses Metal for rendering.
4. **Removal of Deprecated Features** - No longer supports security manager, RMI activation system, etc.
5. **Foreign Function & Memory API (Incubator)** - Enhances interoperability with native code.

---

## 3. Java 11 Features

1. **Var in Lambda Expressions** - Enables the use of `var` in lambda parameters.
2. **New `String` Methods** - `isBlank()`, `lines()`, `strip()`, `repeat()`.
3. **HTTP Client (Standard)** - Introduces a fully asynchronous HTTP client.
4. **Garbage Collector (Epsilon)** - Introduces a no-op GC.
5. **Flight Recorder** - A profiling and diagnostic tool.

---

## 4. What is Actuator?

Spring Boot Actuator provides production-ready features such as monitoring, metrics, health checks, and logging.

---

## 5. How to Change Head URI in Actuator?

Update `management.endpoints.web.base-path` in `application.properties`:

```properties
management.endpoints.web.base-path=/new-actuator
```

---

## 6. N+1 Problem in Hibernate

Occurs when an initial query loads a list of entities, but additional queries fetch related data for each entity individually, leading to performance degradation.

### Solution:

- Use `JOIN FETCH`
- Enable `@BatchSize`
- Use Entity Graphs
- Use pagination

---

## 7. Hibernate Features

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

## 8. Difference Between `Hashtable` and `ConcurrentHashMap`

### 1. **Thread Safety**

- **`Hashtable`:**  
  `Hashtable` is **synchronized**. This means that all methods are thread-safe. However, this comes with a performance cost, as only one thread can access a method of the `Hashtable` at a time.

- **`ConcurrentHashMap`:**  
  `ConcurrentHashMap` is designed to provide thread safety in a more efficient way. It allows **concurrent read access** and enables updates to occur in parallel, thus improving performance compared to `Hashtable`.

### 2. **Null Values**

- **`Hashtable`:**  
  Does not allow **null keys** or **null values**. Attempting to insert a `null` will throw a `NullPointerException`.

- **`ConcurrentHashMap`:**  
  Does not allow **null keys** or **null values** either. However, unlike `Hashtable`, it avoids the synchronized block for **get** and **containsKey** methods, making it more efficient.

### 3. **Performance**

- **`Hashtable`:**  
  Since `Hashtable` is synchronized at the method level, it can become a performance bottleneck under high concurrency, as it allows only one thread to access the map at a time.

- **`ConcurrentHashMap`:**  
  Uses a finer-grained locking mechanism. It locks only a portion (segment) of the map, allowing multiple threads to access different segments concurrently, thus improving performance.

### 4. **Method Synchronization**

- **`Hashtable`:**  
  All methods in `Hashtable` are synchronized, meaning only one thread can access any method at a time.

- **`ConcurrentHashMap`:**  
  Uses **lock striping** and only locks small parts of the map (segments), enabling better concurrency. It locks individual segments rather than the entire map, allowing multiple threads to operate on different segments simultaneously.

### 5. **Usage Context**

- **`Hashtable`:**  
  Typically used in older Java codebases, but due to its limitations in scalability and concurrency, it is now considered obsolete.

- **`ConcurrentHashMap`:**  
  A preferred choice for modern Java applications when working with concurrent programming, as it is designed specifically for high concurrency.

### 6. **Iterator Behavior**

- **`Hashtable`:**  
  The `Iterator` returned by `Hashtable` is **not fail-fast**. This means that if the map is modified while iterating over it, it may or may not throw an exception.

- **`ConcurrentHashMap`:**  
  The `Iterator` returned by `ConcurrentHashMap` is **fail-safe**, meaning it will not throw `ConcurrentModificationException` even if the map is modified while iterating. However, it might reflect inconsistent data due to concurrent changes.

### 7. **Legacy vs Modern**

- **`Hashtable`:**  
  `Hashtable` is part of the original version of Java and is considered a **legacy** class. It has been largely replaced by more modern alternatives like `ConcurrentHashMap` and `HashMap`.

- **`ConcurrentHashMap`:**  
  `ConcurrentHashMap` is part of the **java.util.concurrent** package introduced in Java 5 and is designed specifically to handle multi-threaded scenarios efficiently.

### Summary Table

| Feature                    | `Hashtable`                 | `ConcurrentHashMap`               |
| -------------------------- | --------------------------- | --------------------------------- |
| **Thread Safety**          | Synchronized (method level) | Fine-grained locking              |
| **Null Keys/Values**       | Not allowed                 | Not allowed                       |
| **Performance**            | Poor in high concurrency    | Efficient with concurrency        |
| **Method Synchronization** | All methods synchronized    | Lock striping, segment locks      |
| **Usage**                  | Legacy                      | Modern, preferred for concurrency |
| **Iterator Behavior**      | Not fail-fast               | Fail-safe                         |

---

## 9. Difference Between `HashMap`, `ConcurrentHashMap`, and `Hashtable`

### 1. **Thread Safety**

- **`HashMap`:**  
  `HashMap` is **not synchronized**, meaning it is **not thread-safe**. It is not suitable for concurrent access by multiple threads unless external synchronization is applied.

- **`ConcurrentHashMap`:**  
  `ConcurrentHashMap` is designed for **high concurrency**. It allows concurrent access to the map by multiple threads without the need for external synchronization. It uses **fine-grained locking** to allow multiple threads to access different segments of the map simultaneously.

- **`Hashtable`:**  
  `Hashtable` is **synchronized** at the method level, which makes it **thread-safe**. However, this can lead to performance bottlenecks in multi-threaded environments, as only one thread can access any method of `Hashtable` at a time.

### 2. **Null Keys and Null Values**

- **`HashMap`:**  
  `HashMap` allows **one null key** and **multiple null values**. It can store `null` as a key and as a value without any issues.

- **`ConcurrentHashMap`:**  
  `ConcurrentHashMap` does **not allow `null` keys or `null` values**. It throws `NullPointerException` if you attempt to insert a `null`.

- **`Hashtable`:**  
  `Hashtable` also **does not allow `null` keys or `null` values**. Attempting to insert a `null` will throw `NullPointerException`.

### 3. **Performance**

- **`HashMap`:**  
  Since `HashMap` is not synchronized, it is generally faster than `Hashtable` and `ConcurrentHashMap` in non-concurrent environments.

- **`ConcurrentHashMap`:**  
  `ConcurrentHashMap` provides better **concurrent performance** than `Hashtable`. It is optimized for concurrent access, making it more efficient than `Hashtable` under high concurrency conditions.

- **`Hashtable`:**  
  Due to **method-level synchronization**, `Hashtable` tends to be slower in multi-threaded environments, especially when the number of threads is high.

### 4. **Iterator Behavior**

- **`HashMap`:**  
  The iterator returned by `HashMap` is **fail-fast**, meaning that if the map is modified while iterating (except through the iterator), a `ConcurrentModificationException` will be thrown.

- **`ConcurrentHashMap`:**  
  The iterator returned by `ConcurrentHashMap` is **fail-safe**. It does not throw a `ConcurrentModificationException` if the map is modified while iterating, although it might show inconsistent data due to concurrent changes.

- **`Hashtable`:**  
  The iterator returned by `Hashtable` is **not fail-fast**. Modifying the map while iterating might or might not cause an exception, depending on the situation.

### 5. **Use Cases**

- **`HashMap`:**  
  `HashMap` is best used in single-threaded applications or when you handle synchronization externally (like using `Collections.synchronizedMap()`).

- **`ConcurrentHashMap`:**  
  `ConcurrentHashMap` is ideal when working in multi-threaded environments and when you need high concurrency and performance with thread-safe operations.

- **`Hashtable`:**  
  `Hashtable` is considered **legacy** and is not generally recommended for new development. It is primarily found in older codebases and replaced by `ConcurrentHashMap` for concurrent use cases.

### 6. **Locking Mechanism**

- **`HashMap`:**  
  No locking mechanism; it is not thread-safe.

- **`ConcurrentHashMap`:**  
  Uses **fine-grained locking** (also known as lock striping) where only small portions (segments) of the map are locked, allowing higher concurrency. It uses a **segment lock** rather than a global lock for the entire map.

- **`Hashtable`:**  
  Uses **single global lock** for the entire map, which can cause performance issues in multi-threaded applications due to thread contention.

### 7. **Legacy vs Modern**

- **`HashMap`:**  
  A modern, widely used class for non-thread-safe scenarios.

- **`ConcurrentHashMap`:**  
  A modern class specifically designed for concurrent applications, preferred in multi-threaded environments.

- **`Hashtable`:**  
  A legacy class from the early Java versions that has been largely replaced by `ConcurrentHashMap` and `HashMap` in new development.

### Summary Table

| Feature               | `HashMap`                                 | `ConcurrentHashMap`                                      | `Hashtable`                           |
| --------------------- | ----------------------------------------- | -------------------------------------------------------- | ------------------------------------- |
| **Thread Safety**     | Not synchronized                          | Fine-grained locking, thread-safe                        | Synchronized (method level)           |
| **Null Keys/Values**  | Allows `null` keys and values             | Does not allow `null` keys and values                    | Does not allow `null` keys and values |
| **Performance**       | Fast in single-threaded contexts          | Optimized for concurrency, better performance under load | Slower due to synchronization         |
| **Iterator Behavior** | Fail-fast                                 | Fail-safe                                                | Not fail-fast                         |
| **Usage**             | Single-threaded, external synchronization | High concurrency, modern applications                    | Legacy, older codebases               |
| **Locking Mechanism** | No locks                                  | Segment-level locking                                    | Global lock                           |

---

## 10. Difference Between Observable and Promise

| Feature            | Observable | Promise |
| ------------------ | ---------- | ------- |
| Lazy Execution     | Yes        | No      |
| Multiple Emissions | Yes        | No      |
| Operators Support  | Yes        | No      |
| Cancelable         | Yes        | No      |

---

## 11. Creating a Custom Immutable Class

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

## 12. Custom Linked List and Swap Nodes

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

## 13. Find Missing Element in Array from range 1 to 10

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

## 14. What is `@Qualifier`?

It is used to resolve bean conflicts when multiple beans of the same type exist.

```java
@Autowired
@Qualifier("beanName")
private MyService myService;
```

---

## 15. Why Use Interface with `@Autowired`?

Using interfaces promotes loose coupling and enables dependency injection flexibility.

---

## 16. Can We Use a Class Instead of an Interface in `@Autowired`?

Yes, but using a class instead of an interface reduces flexibility and breaks the Dependency Inversion Principle, effectively tightly coupling the code.

---

## 17. Do We Need an Interface for `@Autowired`?

No, you can directly use a class, but it’s not a best practice.

---
