## 1. POST vs PUT vs PATCH

- **POST**: Creates a new resource. Not idempotent. Each request creates a new resource.
- **PUT**: Updates/replaces an entire resource. Idempotent. Multiple identical requests have same effect as single request.
- **PATCH**: Partially modifies an existing resource. Not idempotent. Only updates specified fields.

---

## 2. Bad Request Error (400)

A client-side error indicating the server cannot process the request due to:

- Invalid syntax
- Invalid request message framing
- Deceptive request routing
- Malformed request parameters

---

## 3. Micro-Frontend Integration

Common approaches include:

1. **Module Federation**: Webpack 5 feature allowing runtime sharing of components
2. **IFrames**: Simple but limited isolation of applications
3. **Web Components**: Creating custom, reusable HTML elements
4. **NPM Packages**: Publishing each micro-frontend as a package
5. **Build-time Integration**: Combining applications during build process

---

## 4.Route Guards

Route Guards control access to routes based on conditions. Main types:

- **CanActivate**: Controls if route can be activated
- **CanDeactivate**: Controls if user can leave route
- **CanLoad**: Controls if module can be loaded

Implementation Example:

```typescript
@Injectable({
  providedIn: "root",
})
export class AuthGuard implements CanActivate {
  canActivate(
    route: ActivatedRouteSnapshot,
    state: RouterStateSnapshot
  ): boolean {
    return this.checkAuth();
  }
}
```

---

## 5. Memory Leak Prevention in Angular

1. Unsubscribe from Observables:
   ```typescript
   ngOnDestroy() {
     this.subscription.unsubscribe();
   }
   ```
2. Use takeUntil operator
3. Use async pipe in templates
4. Clear timers and intervals
5. Remove event listeners

---

## 6. Parallel API Calls in Angular

Using forkJoin for parallel API calls:

```typescript
forkJoin([this.http.get("/api1"), this.http.get("/api2")]).subscribe(
  (results) => {
    const [api1Result, api2Result] = results;
  }
);
```

---

## 7. ForkJoin in Observable

- Combines multiple Observables
- Waits for all Observables to complete
- Returns array of final values
- Used for parallel HTTP requests

---

## 8. Java 11 Features

1. Local-Variable Syntax for Lambda Parameters
2. HTTP Client (Standard)
3. Nested Based Access Control
4. String Methods: isBlank(), lines(), strip()
5. Collection to Array
6. File Methods: readString(), writeString()

---

## 9. Streams: Intermediate vs Terminal Operations

- **Intermediate**: map(), filter(), sorted() - return stream
- **Terminal**: collect(), forEach(), reduce() - produce result

---

## 10. Custom Singleton Class

```java
public class Singleton {
    private static volatile Singleton instance;
    private Singleton() {}

    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

---

## 11. Custom Immutable Class

```java
public final class ImmutableClass {
    private final String name;
    private final List<String> list;

    public ImmutableClass(String name, List<String> list) {
        this.name = name;
        this.list = new ArrayList<>(list);
    }

    public String getName() {
        return name;
    }

    public List<String> getList() {
        return new ArrayList<>(list);
    }
}
```

---

## 12. Keywords: Volatile vs Transient

- **Volatile**: Ensures variable is read from main memory, not cache
- **Transient**: Marks field to be excluded from serialization

---

## 13. Thread Creation Methods

1. Extending Thread class
2. Implementing Runnable interface
3. Using Anonymous class
4. Using Lambda expressions
5. Using ExecutorService

Runnable interface has single method: `void run()`

---

## 14. Design Patterns in Java

Common patterns:

1. **Singleton**: Single instance
2. **Factory**: Object creation without exposing logic
3. **Builder**: Complex object construction
4. **Strategy**: Family of algorithms
5. **Observer**: One-to-many dependency
6. **Decorator**: Add responsibilities dynamically

---

## 15. Software Development Life Cycle (SDLC)

Phases:

1. Requirements Analysis
2. Planning
3. Design
4. Implementation
5. Testing
6. Deployment
7. Maintenance

---

## 16. Spring Boot REST APIs

Creation steps:

1. Add dependencies
2. Create model class
3. Create repository interface
4. Create service class
5. Create controller class with annotations:

```java
@RestController
@RequestMapping("/api")
public class UserController {
    @GetMapping("/users")
    public List<User> getUsers() {
        return userService.findAll();
    }
}
```

---

## 17. Exception Handling in Spring Boot

1. Using @ControllerAdvice
2. Using @ExceptionHandler
3. Custom exception classes
4. Global exception handling

---

## 18. Display: none vs Visibility: hidden

- **Display: none**: Removes element from DOM flow
- **Visibility: hidden**: Hides element but preserves space

---

## 19. Nav vs Anchor Tag

- **Nav**: Semantic container for navigation links
- **Anchor**: Individual link element

---

## 20. React vs Angular

### State Management

React:

- useState
- Context API
- Redux
- MobX

Angular:

- Services
- RxJS
- NgRx
- Akita

### Key Differences

1. **Architecture**:
   - Angular: Full framework
   - React: Library
2. **Learning Curve**:
   - Angular: Steeper
   - React: Moderate
3. **Language**:
   - Angular: TypeScript
   - React: JavaScript/TypeScript
4. **DOM**:
   - Angular: Real DOM
   - React: Virtual DOM
