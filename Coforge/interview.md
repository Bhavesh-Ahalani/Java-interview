## 1. What is Null Pointer Exception?

A **NullPointerException (NPE)** occurs in Java when you try to access or modify a member (method or field) of an object that is **null** (i.e., not initialized).

Example:

```java
String str = null;
System.out.println(str.length()); // Throws NullPointerException
```

**How to prevent it:**

- Initialize variables before using them.
- Use `Optional.ofNullable()` for safer access.
- Add null checks using `if (object != null)`.
- Use annotations like `@NonNull` or `@Nullable`.

## 🕵️ How to Debug NullPointerException (NPE)

A **NullPointerException** occurs when you try to access or modify an object reference that is `null`.

Example:

```java
User user = null;
System.out.println(user.getName()); // 💥 NullPointerException
```

---

### 🧠 Step 1: Understand the Root Cause

It happens when you:

- Call a method on a `null` object.
- Access a field or array element of a `null` object.
- Use `null` in arithmetic or comparison.

---

### 🧩 Step 2: Read the Stack Trace

Example:

```
Exception in thread "main" java.lang.NullPointerException
    at com.example.Main.main(Main.java:10)
```

👉 Line 10 is your target — one of the objects on that line is `null`.

---

### 🧰 Step 3: Identify Which Variable is Null

If you have a long chain like:

```java
System.out.println(user.getAddress().getCity().getName());
```

You don’t know which call returns `null`.  
Break it down:

```java
System.out.println(user);
System.out.println(user.getAddress());
System.out.println(user.getAddress().getCity());
```

Run again — the one that prints `null` is your culprit.

---

### 🐞 Step 4: Use Debugger Instead of Guessing

In IntelliJ or VS Code:

- Place a **breakpoint** before the error line.
- Step through line-by-line.
- Hover over each variable to inspect its value.
- Check where the `null` starts in the flow.

---

### ⚙️ Step 5: Fix the Root Cause

**✅ Initialize Properly**

```java
User user = new User();
user.setName("Bhavesh");
```

**✅ Add Null Checks**

```java
if (user != null && user.getName() != null) {
    System.out.println(user.getName());
}
```

**✅ Use Optional (Java 8+)**

```java
Optional.ofNullable(user)
        .map(User::getName)
        .ifPresent(System.out::println);
```

**✅ Defensive Initialization**

```java
class Order {
    private Customer customer = new Customer();
}
```

---

### 🧹 Step 6: Avoid NPEs

- Use **constructor injection** (in Spring) instead of field injection.
- Always initialize collections:
  ```java
  private List<Item> items = new ArrayList<>();
  ```
- Use `Objects.requireNonNull(obj, "obj must not be null")`.
- Use `@NonNull` annotations where applicable.

---

### ⚡ Bonus Tip

`Objects.toString()` and `String.valueOf()` are **NPE-safe**:

```java
System.out.println(String.valueOf(userName));  // prints "null" safely
```

---

---

## 2. What are Checked and Unchecked Exceptions?

**Checked Exception:** Checked at compile-time. Must be handled using `try-catch` or declared with `throws`.

Examples:

- `IOException`
- `SQLException` (Checked exception, typically thrown during database operations)

**Unchecked Exception:** Occur at runtime and are subclasses of `RuntimeException`.

Examples:

- `NullPointerException`
- `ArrayIndexOutOfBoundsException`

---

## 3. How @Repository Handles SQLException in Spring Boot

Normally, `SQLException` is **checked**, but in Spring Boot, when you annotate a DAO class with `@Repository`, the **PersistenceExceptionTranslationPostProcessor** converts low-level **checked exceptions** like `SQLException` into **unchecked** exceptions (`DataAccessException`).

**Reason:**

- Keeps code cleaner (no repetitive try-catch).
- Unifies database exceptions across JDBC, JPA, Hibernate.

Example:

```java
@Repository
public class EmployeeRepository {
    @Autowired
    private JdbcTemplate jdbcTemplate;

    public Employee findById(int id) {
        return jdbcTemplate.queryForObject(
            "SELECT * FROM employee WHERE id = ?",
            new Object[]{id},
            new EmployeeMapper()
        );
    }
}
```

Even though JDBC might throw `SQLException`, you don’t need to catch it — Spring wraps it in a runtime `DataAccessException`.

---

## 4. How do you manage security (JWT in detail)?

**JWT (JSON Web Token)** is used for **stateless authentication** between client and server.

### JWT Structure:

```
header.payload.signature
```

Example:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.
eyJ1c2VySWQiOjEyMywiZW1haWwiOiJ0ZXN0QGdtYWlsLmNvbSJ9.
QmZJk2xXGDSDsdD23ssfsd33sddDfs3D
```

### Flow:

1. User logs in → Server validates credentials.
2. Server generates JWT and sends it to client.
3. Client stores token (e.g., localStorage or cookie).
4. Subsequent requests include token:
   ```http
   Authorization: Bearer <token>
   ```
5. Server validates the token signature and authorizes the user.

### Key Points:

- Stateless (no session storage).
- Can include user roles, expiry, etc.
- Should always use **HTTPS** and proper **secret key rotation**.

---

## 5. What is Service Discovery (Eureka)?

In a microservices setup, **Service Discovery** helps services locate each other dynamically.

**Eureka Components:**

- **Eureka Server:** Registry where all services register.
- **Eureka Client:** Service that registers itself with the Eureka server.

**Example Flow:**

1. Order Service registers with Eureka.
2. API Gateway queries Eureka to route to the correct service.
3. This avoids hardcoding IPs or hostnames.

---

## 6. What is an API Gateway?

An **API Gateway** acts as a **single entry point** for all clients in a microservice architecture.

**Responsibilities:**

- Routing requests to correct microservice.
- Authentication & Authorization.
- Logging, rate limiting, and load balancing.

**Examples:** Netflix Zuul, Spring Cloud Gateway, NGINX.

---

## 7. OOPs Principles

1. **Encapsulation:** Grouping data and behavior together.
2. **Abstraction:** Hiding implementation details.
3. **Inheritance:** Reusing logic from parent classes.
4. **Polymorphism:** One interface, multiple implementations.

---

## 8. SOLID Principles (Detailed with Examples)

### 🧱 S — Single Responsibility Principle (SRP)

> A class should have only one reason to change.

**Bad Example:**

```java
class Invoice {
    public void calculateTotal() { /* logic */ }
    public void saveToDatabase() { /* DB logic */ }
}
```

**Good Example:**

```java
class InvoiceCalculator {
    public void calculateTotal() {}
}
class InvoiceRepository {
    public void save(Invoice invoice) {}
}
```

---

### 🔓 O — Open/Closed Principle (OCP)

> Classes should be open for extension, but closed for modification.

**Bad Example:**

```java
class NotificationService {
    public void send(String message, String type) {
        if (type.equals("EMAIL")) { /* send email */ }
        else if (type.equals("SMS")) { /* send sms */ }
    }
}
```

**Good Example:**

```java
interface Notification { void send(String message); }
class EmailNotification implements Notification {
    public void send(String message) {}
}
class SMSNotification implements Notification {
    public void send(String message) {}
}
class NotificationService {
    private List<Notification> channels;
    public NotificationService(List<Notification> channels) {
        this.channels = channels;
    }
    public void notifyAll(String msg) {
        channels.forEach(c -> c.send(msg));
    }
}
```

---

### 🧬 L — Liskov Substitution Principle (LSP)

> Subclasses should behave like their base class without breaking functionality.

**Bad Example:**

```java
class Bird { void fly() {} }
class Ostrich extends Bird {
    void fly() { throw new UnsupportedOperationException(); }
}
```

**Good Example:**

```java
interface Bird { void eat(); }
interface FlyingBird extends Bird { void fly(); }
class Sparrow implements FlyingBird {
    public void eat() {}
    public void fly() {}
}
class Ostrich implements Bird {
    public void eat() {}
}
```

---

### 🧩 I — Interface Segregation Principle (ISP)

> Don’t force classes to implement unused methods.

**Bad Example:**

```java
interface Worker { void work(); void eat(); }
class Robot implements Worker {
    public void work() {}
    public void eat() {} // not applicable
}
```

**Good Example:**

```java
interface Workable { void work(); }
interface Eatable { void eat(); }
class Human implements Workable, Eatable {
    public void work() {}
    public void eat() {}
}
class Robot implements Workable {
    public void work() {}
}
```

---

### 🪜 D — Dependency Inversion Principle (DIP)

> Depend on abstractions, not concrete classes.

**Bad Example:**

```java
class MySQLDatabase {
    void saveData(String data) {}
}
class UserService {
    private MySQLDatabase db = new MySQLDatabase();
    void saveUser(String data) { db.saveData(data); }
}
```

**Good Example:**

```java
interface Database { void saveData(String data); }
class MySQLDatabase implements Database { public void saveData(String data) {} }
class MongoDatabase implements Database { public void saveData(String data) {} }
class UserService {
    private final Database db;
    public UserService(Database db) { this.db = db; }
    void saveUser(String data) { db.saveData(data); }
}
```

---

## 9. StringBuilder vs StringBuffer

| Feature         | StringBuilder      | StringBuffer   |
| --------------- | ------------------ | -------------- |
| Thread Safety   | ❌ Not Thread Safe | ✅ Thread Safe |
| Performance     | Faster             | Slower         |
| Synchronization | No                 | Yes            |
| Use Case        | Single-threaded    | Multi-threaded |

---

## 10. Pipes in Angular (and Custom Pipes)

**Pipes** transform data directly in the template.

Example:

```html
{{ user.name | uppercase }}
```

**Custom Pipe Example:**

```typescript
import { Pipe, PipeTransform } from "@angular/core";

@Pipe({ name: "reverse" })
export class ReversePipe implements PipeTransform {
  transform(value: string): string {
    return value.split("").reverse().join("");
  }
}
```

Usage:

```html
{{ 'Bhavesh' | reverse }}
<!-- Output: hsevahB -->
```

---

## 11. Data Binding in Angular

**Data Binding** connects component logic with DOM.

Types:

- **Interpolation:** `{{ value }}`
- **Property Binding:** `[property]="value"`
- **Event Binding:** `(event)="handler()"`
- **Two-way Binding:** `[(ngModel)]="value"`

---

## 12. What is *ngIf, *ngFor, and \*ngSwitch?

```html
<!-- ngIf -->
<div *ngIf="isVisible">Visible Content</div>

<!-- ngFor -->
<div *ngFor="let item of items">{{ item }}</div>

<!-- ngSwitch -->
<div [ngSwitch]="role">
  <p *ngSwitchCase="'admin'">Admin</p>
  <p *ngSwitchDefault>User</p>
</div>
```

---

## 13. Java Code to Get All Non-Repeating Characters in a String

```java
import java.util.*;

public class NonRepeatingChars {
    public static void main(String[] args) {
        String input = "programming";
        Map<Character, Integer> map = new LinkedHashMap<>();

        for (char c : input.toCharArray()) {
            map.put(c, map.getOrDefault(c, 0) + 1);
        }

        for (Map.Entry<Character, Integer> entry : map.entrySet()) {
            if (entry.getValue() == 1) {
                System.out.print(entry.getKey() + " ");
            }
        }
    }
}
```

**Output:**

```
p o a n
```

---

---

## 14. Garbage Collection in Java

**Garbage Collection (GC)** automatically manages memory by removing objects no longer reachable by any reference.

### ⚙️ How It Works

1. JVM allocates memory in the **heap** when you create objects.
2. When no reference points to an object, it becomes **eligible for GC**.
3. The GC reclaims memory by deleting those objects.

---

### 🔍 JVM Memory Areas

- **Young Generation:** newly created objects; contains _Eden_ and _Survivor_ spaces.
- **Old Generation (Tenured):** long-lived objects.
- **Permanent Generation (MetaSpace in Java 8+):** class metadata.

---

### 🧠 Garbage Collection Process

1. **Mark:** Identify objects still referenced.
2. **Sweep:** Remove unreferenced (garbage) objects.
3. **Compact:** Rearrange remaining objects to avoid fragmentation.

---

### 🧰 Common GC Algorithms

| Algorithm                 | Description                                           |
| ------------------------- | ----------------------------------------------------- |
| **Serial GC**             | Single-threaded; suitable for small apps.             |
| **Parallel GC**           | Multi-threaded; faster for large heaps.               |
| **G1 GC (Garbage First)** | Region-based, low-latency, default since Java 9.      |
| **ZGC / Shenandoah**      | Ultra-low pause collectors for high-performance apps. |

---

### 💡 Tips

- Avoid unnecessary object creation.
- Set large objects to `null` after use if long-lived scope.
- Use profiling tools like **VisualVM** or **JConsole** to analyze GC behavior.

---

## 15. How HashMap Works Internally

A **HashMap** stores key–value pairs using **hashing**.  
It provides **O(1)** average time for `get()` and `put()` operations.

---

### 🧩 Internal Structure

- Backed by an **array of buckets** (`Node<K,V>[] table`).
- Each bucket is a **linked list** or **balanced tree** (since Java 8).

---

### ⚙️ How `put(key, value)` Works

1. Calculate the **hash** of the key using `hashCode()`.
2. Map hash → bucket index using:
   ```java
   index = (n - 1) & hash
   ```
3. If no element in that bucket → insert new node.
4. If bucket has entries:
   - Compare keys using `equals()`.
   - If same key → update value.
   - If different key → append to linked list.
   - If list size > 8 → convert to **tree (Red-Black Tree)** for performance.

---

### ⚙️ How `get(key)` Works

1. Compute the hash and bucket index.
2. Traverse the list/tree in that bucket.
3. Compare keys using `equals()`.
4. Return value if key matches; else `null`.

---

### ⚔️ Hash Collision

A **hash collision** occurs when two keys produce the same hash index.

**Example:**

```java
"FB".hashCode() == "Ea".hashCode()  // true
```

But they’re different strings → same bucket, different node.

To handle this:

- Java stores entries in a **linked list** (pre-Java 8) or **tree** (Java 8+).
- Lookup complexity changes from **O(1)** to **O(n)** (worst) or **O(log n)** (if tree).

---

### 🔢 Resizing (Rehashing)

When load factor (`size / capacity`) > 0.75 → HashMap **doubles its capacity** and rehashes all entries.

Formula:

```java
newCapacity = oldCapacity * 2;
```

This ensures performance but costs CPU temporarily.

---

### 💡 Summary

| Concept                | Description                             |
| ---------------------- | --------------------------------------- |
| **Hash Function**      | Converts key → integer (hash).          |
| **Bucket**             | Array index storing entries.            |
| **Collision Handling** | LinkedList → TreeMap (after threshold). |
| **Load Factor**        | 0.75 by default (triggers resize).      |
| **Complexity**         | O(1) average, O(n)/O(log n) worst.      |

---

### ✅ Quick Example

```java
Map<String, Integer> map = new HashMap<>();
map.put("Nomad", 1);
map.put("Zephyr", 2);

System.out.println(map.get("Bhavesh")); // 1
```

---

## 16. Java 8 Functional Interfaces — Complete Reference

Java 8 introduced **functional interfaces** to support **lambda expressions** and **functional programming** style.  
A **functional interface** is an interface with exactly **one abstract method** (SAM — Single Abstract Method).

---

### 🧩 Common Functional Interfaces in `java.util.function` Package

| Interface               | Method                   | Description                                           |
| ----------------------- | ------------------------ | ----------------------------------------------------- |
| **Predicate<T>**        | `boolean test(T t)`      | Tests a condition and returns true/false.             |
| **Function<T, R>**      | `R apply(T t)`           | Takes one argument, returns a result.                 |
| **Consumer<T>**         | `void accept(T t)`       | Performs an action on a given input, returns nothing. |
| **Supplier<T>**         | `T get()`                | Supplies or returns a value without input.            |
| **BiPredicate<T, U>**   | `boolean test(T t, U u)` | Predicate with two inputs.                            |
| **BiFunction<T, U, R>** | `R apply(T t, U u)`      | Function with two inputs and one output.              |
| **BiConsumer<T, U>**    | `void accept(T t, U u)`  | Consumes two inputs and returns nothing.              |
| **UnaryOperator<T>**    | `T apply(T t)`           | Function from T → T (same input and output type).     |
| **BinaryOperator<T>**   | `T apply(T t1, T t2)`    | Takes two same-type inputs, returns same type output. |

---

### 🧠 Predicate<T>

Used for filtering and condition checking.

Example:

```java
Predicate<Integer> isEven = n -> n % 2 == 0;
System.out.println(isEven.test(10)); // true
System.out.println(isEven.test(7));  // false
```

#### Chaining Predicates

```java
Predicate<Integer> isPositive = n -> n > 0;
Predicate<Integer> isEvenAndPositive = isEven.and(isPositive);
System.out.println(isEvenAndPositive.test(4)); // true
```

---

### ⚙️ Function<T, R>

Used for data transformation (like map operations).

Example:

```java
Function<String, Integer> lengthFunc = s -> s.length();
System.out.println(lengthFunc.apply("Bhavesh")); // 7
```

#### Function Chaining

```java
Function<Integer, Integer> doubleIt = x -> x * 2;
Function<Integer, Integer> squareIt = x -> x * x;

Function<Integer, Integer> combined = doubleIt.andThen(squareIt);
System.out.println(combined.apply(3)); // (3*2)^2 = 36
```

---

### 🧾 Consumer<T>

Consumes input and returns nothing — used for logging, printing, etc.

Example:

```java
Consumer<String> greet = name -> System.out.println("Hello, " + name);
greet.accept("Bhavesh"); // Hello, Bhavesh
```

#### Chaining Consumers

```java
Consumer<String> c1 = s -> System.out.print(s.toUpperCase() + " ");
Consumer<String> c2 = s -> System.out.println(s.length());

c1.andThen(c2).accept("Bhavesh"); // BHAVESH 7
```

---

### 📦 Supplier<T>

Supplies values — commonly used for lazy initialization.

Example:

```java
Supplier<Double> randomValue = () -> Math.random();
System.out.println(randomValue.get());
```

---

### 🔁 BiPredicate<T, U>

Tests two inputs.

Example:

```java
BiPredicate<String, Integer> longerThan = (str, len) -> str.length() > len;
System.out.println(longerThan.test("Bhavesh", 5)); // true
```

---

### 🔁 BiFunction<T, U, R>

Takes two inputs and returns an output.

Example:

```java
BiFunction<Integer, Integer, Integer> add = (a, b) -> a + b;
System.out.println(add.apply(5, 10)); // 15
```

---

### 🔁 BiConsumer<T, U>

Consumes two inputs and returns nothing.

Example:

```java
BiConsumer<String, Integer> printAge = (name, age) ->
    System.out.println(name + " is " + age + " years old.");
printAge.accept("Bhavesh", 29);
```

---

### 🔄 UnaryOperator<T>

Special form of `Function<T, R>` where **input and output are of same type**.

Example:

```java
UnaryOperator<Integer> square = x -> x * x;
System.out.println(square.apply(5)); // 25
```

---

### ⚙️ BinaryOperator<T>

Special form of `BiFunction<T, U, R>` where **all three are same type**.

Example:

```java
BinaryOperator<Integer> multiply = (a, b) -> a * b;
System.out.println(multiply.apply(3, 4)); // 12
```

---

### 💡 Primitive Variants for Performance (Avoid Auto-Boxing)

| Type   | Predicate       | Function       | Consumer       | Supplier       |
| ------ | --------------- | -------------- | -------------- | -------------- |
| int    | IntPredicate    | IntFunction    | IntConsumer    | IntSupplier    |
| long   | LongPredicate   | LongFunction   | LongConsumer   | LongSupplier   |
| double | DoublePredicate | DoubleFunction | DoubleConsumer | DoubleSupplier |

Example:

```java
IntPredicate isEven = n -> n % 2 == 0;
System.out.println(isEven.test(4)); // true
```

---

### 🧰 Other Specialized Functional Interfaces

| Interface               | Method                            | Description           |
| ----------------------- | --------------------------------- | --------------------- |
| **ToIntFunction<T>**    | `int applyAsInt(T t)`             | Converts T → int      |
| **ToDoubleFunction<T>** | `double applyAsDouble(T t)`       | Converts T → double   |
| **ToLongFunction<T>**   | `long applyAsLong(T t)`           | Converts T → long     |
| **IntToDoubleFunction** | `double applyAsDouble(int value)` | Converts int → double |
| **IntToLongFunction**   | `long applyAsLong(int value)`     | Converts int → long   |
| **DoubleToIntFunction** | `int applyAsInt(double value)`    | Converts double → int |

---

### 🧠 Custom Functional Interface Example

You can create your own functional interface with `@FunctionalInterface` annotation.

```java
@FunctionalInterface
interface Calculator {
    int calculate(int a, int b);
}

public class Demo {
    public static void main(String[] args) {
        Calculator add = (a, b) -> a + b;
        System.out.println(add.calculate(5, 10)); // 15
    }
}
```

---

### ⚡ TL;DR Summary

| Category            | Interface                               | Purpose               |
| ------------------- | --------------------------------------- | --------------------- |
| Test Condition      | `Predicate<T>`, `BiPredicate<T,U>`      | Boolean test          |
| Transform Data      | `Function<T,R>`, `BiFunction<T,U,R>`    | Returns result        |
| Perform Action      | `Consumer<T>`, `BiConsumer<T,U>`        | Executes side effects |
| Provide Values      | `Supplier<T>`                           | Returns a value       |
| Same-Type Operation | `UnaryOperator<T>`, `BinaryOperator<T>` | In-place operations   |

---

#### 💬 Pro Tip

When using Java Streams, you’ll often see:

- `filter()` → uses `Predicate`
- `map()` → uses `Function`
- `forEach()` → uses `Consumer`
- `collect()` → uses `Supplier` and `BiConsumer` internally

So mastering these interfaces = mastering Streams.

---
