### 1. What if we use @Service annotation in repository layer and @Repository annotation in service layer, will it give any error?

**Answer:**  
No, the program will run without any errors, but it is semantically incorrect and violates the intended use of these annotations.

- `@Service` should be used for service layer classes that contain business logic.
- `@Repository` should be used for classes that interact with the database (DAO layer).  
  Using the annotations incorrectly may lead to confusion and can potentially disrupt the proper configuration of Spring's exception translation mechanism (for `@Repository`).

Even though Spring won’t throw an error for this misannotation, it is **not recommended** to use the annotations inappropriately. The **primary issue** is the **semantics**:

- **`@Service`** marks classes that provide business services or logic.
- **`@Repository`** marks classes responsible for interacting with the database (data access logic).

### Will the program run?

- **Yes**, the program will run without errors.
- **No**, it’s not ideal because the annotations aren't being used as intended by the Spring framework.

In short: While your program won't throw errors, it’s best to follow Spring's conventions for better clarity, maintainability, and functionality.

---

### 2. How Spring does autoconfiguration?

**Answer:**  
Spring Boot's autoconfiguration feature automatically configures Spring application contexts based on the libraries present in the classpath and predefined properties.

- Spring Boot provides auto-configuration classes that attempt to configure beans based on the current environment.
- It uses `@EnableAutoConfiguration` internally, which is typically combined with `@SpringBootApplication`.
- The autoconfiguration classes are placed in `META-INF/spring.factories` files and are loaded based on the condition of the available beans and libraries in the application.

---

### 3. What if I don't use any annotation like @Service, @Bean, @Component, and use @Autowired for DI, will it work?

**Answer:**  
No, it will not work. Spring's DI relies on annotations like `@Component`, `@Service`, `@Repository`, or explicit bean configuration using `@Bean`.

- If you don’t annotate a class with `@Component`, Spring will not be able to identify it as a bean to be injected.
- `@Autowired` only works with classes that are already registered as beans in the Spring container.

---

### 4. Can we have a null key in HashMap?

**Answer:**  
Yes, `HashMap` allows a null key. You can store one `null` key in a `HashMap`. However, be cautious, as using `null` as a key can introduce potential bugs and make it harder to debug.

---

### 5. What is abstraction, how does it work? Can you give an example with the help of a program?

**Answer:**  
Abstraction is the concept of hiding the complex implementation details and showing only the essential features of an object or system. It helps in simplifying the interaction with complex systems.

- Abstract classes and interfaces are used to achieve abstraction in Java.
- Example:

```java
abstract class Animal {
    abstract void sound();
}

class Dog extends Animal {
    void sound() {
        System.out.println("Bark");
    }
}

public class Main {
    public static void main(String[] args) {
        Animal animal = new Dog();
        animal.sound(); // Output: Bark
    }
}
```

---

### 6. How do you hide methods in abstraction? Give a code example.

**Answer:**  
You can hide methods in abstraction by using abstract methods in abstract classes or interfaces. These methods are not implemented in the abstract class or interface, thus hiding their implementation from the user.

- **Example (Java)**:

```java
abstract class Shape {
    abstract void draw();  // Method is hidden here
}

class Circle extends Shape {
    @Override
    void draw() {
        System.out.println("Drawing Circle");
    }
}

public class Main {
    public static void main(String[] args) {
        Shape shape = new Circle();
        shape.draw(); // Output: Drawing Circle
    }
}
```

In this Java example, the method `draw()` is declared as abstract in the `Shape` class and is implemented in the `Circle` class. The implementation is hidden from the user of `Shape` and only the abstract method is visible.

---

### Spring Boot Example (Using Abstraction to Hide Methods)

In a Spring Boot example, you can use the same principles of abstraction to hide methods inside service classes or components, allowing the interface to expose only the necessary methods.

- **Example (Spring Boot)**:

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

public interface Animal {
    void makeSound();  // Abstract method hidden in the interface
}

@Service
public class Dog implements Animal {
    @Override
    public void makeSound() {
        System.out.println("Bark");
    }
}

@Service
public class Cat implements Animal {
    @Override
    public void makeSound() {
        System.out.println("Meow");
    }
}

@SpringBootApplication
public class AnimalApplication implements CommandLineRunner {

    private final Animal animal;

    @Autowired
    public AnimalApplication(@Qualifier("dog") Animal animal) {  // Hides method via interface
        this.animal = animal;
    }

    public static void main(String[] args) {
        SpringApplication.run(AnimalApplication.class, args);
    }

    @Override
    public void run(String... args) throws Exception {
        animal.makeSound();  // Output: Bark (depending on the qualifier or bean selected)
    }
}
```

**Explanation**:

- **Interface (`Animal`)**: Abstracts away the `makeSound()` method.
- **Concrete Classes (`Dog`, `Cat`)**: Implement the `makeSound()` method, but users of the `Animal` interface don’t see the actual implementation.
- **Spring Boot (`AnimalApplication`)**: The `CommandLineRunner` uses the `@Autowired` annotation to inject a specific implementation (like `Dog` or `Cat`) based on Spring's configuration. The actual `makeSound()` method implementation is hidden from the user.

In this Spring Boot example, you can switch between the `Dog` or `Cat` implementation of `Animal` by using annotations like `@Qualifier`, while keeping the method implementation abstracted behind the interface.

---

### 7. Give an example where we use @Qualifier, also write a program for it?

**Answer:**  
`@Qualifier` is used when you have multiple beans of the same type and want to specify which bean to inject. This helps to resolve ambiguity when Spring has more than one candidate to inject.

- Example:

```java
@Component
class Dog implements Animal {
    public void speak() {
        System.out.println("Woof");
    }
}

@Component
class Cat implements Animal {
    public void speak() {
        System.out.println("Meow");
    }
}

@Service
class PetService {
    private final Animal animal;

    @Autowired
    public PetService(@Qualifier("dog") Animal animal) {
        this.animal = animal;
    }

    public void makeSound() {
        animal.speak();
    }
}

public interface Animal {
    void speak();
}
```

In this example, `@Qualifier("dog")` tells Spring to inject the `Dog` bean when there are multiple possible candidates (`Dog` and `Cat`). Without `@Qualifier`, Spring would not know which bean to inject.

---

### 8. Can we use @Primary if we don't use @Qualifier?

**Answer:**  
Yes, `@Primary` can be used if you have multiple beans of the same type, and you want to specify the default bean to be injected. `@Qualifier` is not required when `@Primary` is used.

---

### 9. What is ApplicationContext in Spring?

**Answer:**  
`ApplicationContext` is a central interface to the Spring Framework that provides configuration, bean management, and environment abstraction. It is used to access Spring beans and handle the lifecycle and scope of those beans.

- It is an extension of `BeanFactory` and is responsible for loading, managing, and wiring beans.

---

### 10. Class `Employee` {

````java
public long id;
public String name;
public String depName;
public int age;
}
Suppose this is a class, I want to store 1000 users in a HashMap like
```java
HashMap<Long, Employee> emp = new HashMap<>();
where the key is emp id, how to do it? Use Java 8.
````

**Answer:**

You can store employees in a `HashMap` with the employee ID as the key. Here’s how you can do it using Java 8:

```java
import java.util.HashMap;

class Employee {
    public long id;
    public String name;
    public String depName;
    public int age;

    public Employee(long id, String name, String depName, int age) {
        this.id = id;
        this.name = name;
        this.depName = depName;
        this.age = age;
    }
}

public class Main {
    public static void main(String[] args) {
        HashMap<Long, Employee> empMap = new HashMap<>();
        for (int i = 1; i <= 1000; i++) {
            empMap.put((long) i, new Employee(i, "Name" + i, "Dept" + i, 20 + i % 10));
        }

        // Example to get employee with id 5
        Employee emp = empMap.get(5L);
        System.out.println(emp.name); // Output: Name5
    }
}
```

---

### 11. Write a program in Java 8 to filter employees whose age is > 25 and give names.

**Answer:**

```java
import java.util.*;
import java.util.stream.Collectors;

class Employee {
    public long id;
    public String name;
    public String depName;
    public int age;

    public Employee(long id, String name, String depName, int age) {
        this.id = id;
        this.name = name;
        this.depName = depName;
        this.age = age;
    }
}

public class Main {
    public static void main(String[] args) {
        List<Employee> employees = Arrays.asList(
                new Employee(1, "Alice", "HR", 30),
                new Employee(2, "Bob", "Finance", 22),
                new Employee(3, "Charlie", "Engineering", 28)
        );

        List<String> names = employees.stream()
                .filter(emp -> emp.age > 25)
                .map(emp -> emp.name)
                .collect(Collectors.toList());

        System.out.println(names); // Output: [Alice, Charlie]
    }
}
```

---

### 12. What is a functional interface?

**Answer:**  
A functional interface is an interface that has exactly one abstract method. It can have multiple default or static methods.

- Example: `Runnable`, `Comparator`, `Callable`.

---

### 13. Types of functional interfaces that Java provides?

**Answer:**  
Java provides several built-in functional interfaces in the `java.util.function` package:

- `Predicate<T>`
- `Function<T, R>`
- `Consumer<T>`
- `Supplier<T>`
- `UnaryOperator<T>`
- `BinaryOperator<T>`

---

### 14. What is Predicate, Consumer?

**Answer:**

- **Predicate:** It represents a function that takes one argument and returns a boolean value. It is used for conditional checks.
  - Example: `Predicate<String> isEmpty = str -> str.isEmpty();`
- **Consumer:** It represents an operation that takes a single argument and returns no result. It's used for operations like printing or processing.
  - Example: `Consumer<String> print = str -> System.out.println(str);`

---

### 15. Which functional interface does `filter` and `map` use internally?

**Answer:**

- `filter` uses `Predicate<T>` as it checks whether a condition is true or false.
- `map` uses `Function<T, R>` as it transforms one type of object into another.

---

### 16. Give an example of Predicate.

**Answer:**

```java
import java.util.function.Predicate;

public class Main {
    public static void main(String[] args) {
        Predicate<Integer> isEven = num -> num % 2 == 0;
        System.out.println(isEven.test(4)); // Output: true
        System.out.println(isEven.test(5)); // Output: false
    }
}
```

---

### 17. Java 8 features

**Answer:**

- **Lambda expressions**
- **Streams API**
- **Functional interfaces**
- **Default and static methods in interfaces**
- **New date and time API (java.time)**
- **Optional class**
- **Method references**
- **Nashorn JavaScript engine**

---
