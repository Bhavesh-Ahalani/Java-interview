## 1. What are the key differences between cookies, localStorage, and sessionStorage?

- **Cookies:**

  - 4KB size limit
  - Sent with every HTTP request
  - Can set expiration
  - Accessible on both server/client
  - Best for: auth tokens, user preferences

- **localStorage:**

  - 5-10MB storage
  - Persists until explicitly deleted
  - Client-side only
  - Best for: app settings, cached data

- **sessionStorage:**
  - Like localStorage but only for current session
  - Cleared when tab closes
  - Best for: temporary form data

---

## 2. What information is typically stored in a JWT token?

- Three parts: Header, Payload, Signature
- **Header:** Algorithm & token type
- **Payload stores:**
  - User ID (sub)
  - Expiration time (exp)
  - Issue time (iat)
  - User roles/permissions
  - Never store sensitive data like passwords

---

## 3. Where should JWT tokens be stored in a web application?

- **Best Option: HttpOnly Cookies**

  - Protected against XSS
  - Automatically sent with requests
  - Can't be accessed by JavaScript

- **Alternatives:**
  - localStorage (not recommended, vulnerable to XSS)
  - Memory (state) - secure but lost on refresh

---

## 4. How does a JWT token look like in structure?

```
header.payload.signature

Example:
eyJhbGciOiJIUzI1NiJ9.
eyJzdWIiOiIxMjM0NTY3ODkwIn0.
dozjgNryP4J3jVmNHl0w5N_XgL0n3I9PlFUP0THsR8
```

---

## 5. What happens if a JWT token is modified before sending to backend?

- Token becomes invalid immediately
- Server verification fails
- Request rejected with 401/403
- This is because signature is cryptographically secured

---

## 6. What is CORS and why is it important?

- Cross-Origin Resource Sharing
- Security mechanism for cross-origin requests
- Prevents unauthorized domain access
- Controlled by HTTP headers
- Browser enforced security feature

---

## 7. How can CORS issues be resolved?

- **Server-side:**

  - Configure proper CORS headers
  - Allow specific origins
  - Set up proxy server

- **Development:**
  - Use development proxy
  - Browser extensions (development only)

---

## 8. How does Google search handle CORS?

- Uses API keys for authentication
- Whitelisted domains
- Public API with proper CORS headers
- Rate limiting implementation

---

## 9. What are the key differences between Angular and React?

- **React:**

  - Library, flexible
  - Virtual DOM
  - One-way binding
  - JSX
  - Smaller learning curve

- **Angular:**
  - Full framework
  - Real DOM + Virtual DOM
  - Two-way binding
  - TypeScript based
  - More structured

---

## 10. What is Virtual DOM and why is it needed?

- JavaScript representation of actual DOM
- Used for performance optimization
- Minimizes actual DOM manipulation
- Batches DOM updates
- Reduces browser repaints

---

## 11. How would you implement a next value generator using closures?

```javascript
// Implementation
function createNextValue(start = 0, step = 1) {
  let current = start;
  return function () {
    const value = current;
    current += step;
    return value;
  };
}

// Usage
const getNext = createNextValue(1, 2);
console.log(getNext()); // 1
console.log(getNext()); // 3
console.log(getNext()); // 5
```

---

## 12. How would you handle large calculations (like finding prime numbers) without blocking the browser?

```javascript
async function findPrimes(count) {
  const primes = [];
  let num = 2;

  async function processChunk() {
    let chunkSize = 1000;
    const startTime = performance.now();

    while (primes.length < count && performance.now() - startTime < 50) {
      if (isPrime(num)) {
        primes.push(num);
      }
      num++;
    }

    // Update UI with progress
    updateProgress(primes.length, count);

    if (primes.length < count) {
      // Let UI breathe and process next chunk
      await new Promise((resolve) => setTimeout(resolve, 0));
      await processChunk();
    } else {
      displayResult(primes);
    }
  }

  await processChunk();
}

// Helper function for prime check
function isPrime(num) {
  for (let i = 2; i <= Math.sqrt(num); i++) {
    if (num % i === 0) return false;
  }
  return num > 1;
}

// Usage
findPrimes(1000000).catch(console.error);
```

Key points:

- Breaks computation into chunks
- Uses async/await for non-blocking
- Maintains UI responsiveness
- Shows progress updates
- Handles large calculations efficiently

---
