<!-- markdownlint-disable MD036 -->
<!-- markdownlint-disable MD060 -->
<!-- markdownlint-disable MD050 -->
<!-- markdownlint-disable MD024 -->

# **JavaScript Error Handling**

## **Table of Contents**

- [**JavaScript Error Handling**](#javascript-error-handling)
  - [**Table of Contents**](#table-of-contents)
  - [**Understanding Error Handling**](#understanding-error-handling)
    - [What is Error Handling?](#what-is-error-handling)
    - [When Do We Need It?](#when-do-we-need-it)
  - [**Error Flow Diagrams**](#error-flow-diagrams)
    - [Synchronous Error Flow](#synchronous-error-flow)
    - [Asynchronous Error Flow](#asynchronous-error-flow)
    - [Error Propagation](#error-propagation)
    - [Error Object Structure](#error-object-structure)
  - [**Code Examples**](#code-examples)
    - [Basic Try-Catch](#basic-try-catch)
    - [Try-Catch-Finally](#try-catch-finally)
    - [Custom Error Classes](#custom-error-classes)
    - [Async Error Handling](#async-error-handling)
      - [With Promises](#with-promises)
      - [With Async/Await](#with-asyncawait)
    - [Global Error Handlers](#global-error-handlers)
      - [Browser](#browser)
      - [Node.js](#nodejs)
    - [Error Boundary Pattern](#error-boundary-pattern)
    - [Retry Logic with Error Handling](#retry-logic-with-error-handling)
    - [Safe JSON Parsing](#safe-json-parsing)
    - [Advanced: Async Error Wrapper](#advanced-async-error-wrapper)
    - [Error Logging Service](#error-logging-service)
  - [**Common Interview Traps**](#common-interview-traps)
    - [Trap 1: Finally Always Executes](#trap-1-finally-always-executes)
    - [Trap 2: Async Errors in setTimeout](#trap-2-async-errors-in-settimeout)
    - [Trap 3: Unhandled Promise Rejections](#trap-3-unhandled-promise-rejections)
    - [Trap 4: Error Swallowing](#trap-4-error-swallowing)
    - [Trap 5: Not Checking Response Status](#trap-5-not-checking-response-status)
    - [Trap 6: Multiple Async Operations Without Error Handling](#trap-6-multiple-async-operations-without-error-handling)
  - [**Real-World Use Cases**](#real-world-use-cases)
    - [1. API Request Handling](#1-api-request-handling)
    - [2. Form Validation](#2-form-validation)
    - [3. File Processing](#3-file-processing)
    - [4. Database Operations](#4-database-operations)
    - [5. Rate Limiting with Error Recovery](#5-rate-limiting-with-error-recovery)
  - [**Error Handling Cheatsheet**](#error-handling-cheatsheet)
  - [**Best Practices**](#best-practices)
    - [`DO`](#do)
    - [`DON'T`](#dont)
  - [**Error Types Reference**](#error-types-reference)
    - [Built-in Error Types](#built-in-error-types)
    - [Custom Error Types](#custom-error-types)
  - [**Quick Reference**](#quick-reference)
    - [Error Handling Decision Tree](#error-handling-decision-tree)
  - [**Interview Questions to Practice**](#interview-questions-to-practice)
  - [**Summary**](#summary)

---

## **Understanding Error Handling**

### What is Error Handling?

JavaScript error handling is the mechanism to **catch, handle, and recover** from runtime errors without crashing the entire application.

**Basic Structure:**

```javascript
try {
  // Code that might throw an error
} catch (error) {
  // Handle the error
} finally {
  // Always runs (optional)
}
```

### When Do We Need It?

- Variable doesn't exist
- Network request fails
- JSON parsing breaks
- Type mismatches
- Null/undefined access

---

## **Error Flow Diagrams**

### Synchronous Error Flow

```text
┌─────────────────────────────────────────────┐
│          SYNCHRONOUS ERROR FLOW             │
├─────────────────────────────────────────────┤
│                                             │
│  try {                                      │
│    throw new Error("Oops");  ───┐           │
│  }                              |           │
│                                 ▼           │
│  catch (error) { ◄────────  ERROR THROWN    │
│    console.log(error.message);              │
│  }                                          │
│                                   │         │
│  finally {        ◄───────────────┘         │
│    // cleanup                               │
│  }                                          │
└─────────────────────────────────────────────┘
```

### Asynchronous Error Flow

```text
┌─────────────────────────────────────────────┐
│         ASYNCHRONOUS ERROR FLOW             │
├─────────────────────────────────────────────┤
│                                             │
│  Promise-based:                             │
│    fetch(url)                               │
│      .then(res => res.json())               │
│      .catch(err => handle(err)) ◄── CATCH   │
│                                             │
│  Async/Await:                               │
│    try {                                    │
│      await fetch(url);                      │
│    } catch (err) { ◄─────────── CATCH       │
│      handle(err);                           │
│    }                                        │
└─────────────────────────────────────────────┘
```

### Error Propagation

```text
┌─────────────────────────────────────────────┐
│           ERROR PROPAGATION                 │
├─────────────────────────────────────────────┤
│                                             │
│  function a() { throw Error("A"); }         │
│  function b() { a(); }                      │
│  function c() { b(); }                      │
│                                             │
│  try { c(); }                               │
│  catch (e) { // Catches error from a() }    │
│                                             │
│  Call Stack:                                │
│    c() → b() → a() → throw → catch          │
└─────────────────────────────────────────────┘
```

### Error Object Structure

```javascript
{
  name: "TypeError",           // Error type
  message: "Cannot read...",   // Description
  stack: "TypeError: at..."    // Stack trace
}
```

---

## **Code Examples**

### Basic Try-Catch

```javascript
// Simple version
try {
  let user = JSON.parse('{"name": "Alice"');  // Missing closing brace
} catch (error) {
  console.log("Error:", error.message);
}
// Output: Error: Unexpected end of JSON input
```

---

### Try-Catch-Finally

```javascript
function readFile(path) {
  let file;
  try {
    file = openFile(path);  // Imagine this can fail
    return processFile(file);
  } catch (error) {
    console.error("Failed to read file:", error.message);
    return null;
  } finally {
    // Always runs, even if return/throw in try/catch
    if (file) closeFile(file);
    console.log("Cleanup complete");
  }
}
```

**Key Point:** `finally` executes even if `return` or `throw` happens in `try` or `catch`.

---

### Custom Error Classes

```javascript
// Define custom error class
class ValidationError extends Error {
  constructor(message) {
    super(message);
    this.name = "ValidationError";
  }
}

class NetworkError extends Error {
  constructor(message, statusCode) {
    super(message);
    this.name = "NetworkError";
    this.statusCode = statusCode;
  }
}

// Usage
function validateAge(age) {
  if (age < 0) {
    throw new ValidationError("Age cannot be negative");
  }
  if (age > 150) {
    throw new ValidationError("Age too high");
  }
  return age;
}

try {
  validateAge(-5);
} catch (error) {
  if (error instanceof ValidationError) {
    console.log("Validation failed:", error.message);
  } else {
    console.log("Unknown error:", error);
  }
}
```

---

### Async Error Handling

#### With Promises

```javascript
fetch('https://api.example.com/data')
  .then(response => {
    if (!response.ok) {
      throw new Error(`HTTP ${response.status}`);
    }
    return response.json();
  })
  .then(data => console.log(data))
  .catch(error => console.error("Fetch failed:", error.message))
  .finally(() => console.log("Request complete"));
```

#### With Async/Await

```javascript
async function fetchData(url) {
  try {
    const response = await fetch(url);
    if (!response.ok) {
      throw new Error(`HTTP ${response.status}`);
    }
    const data = await response.json();
    return data;
  } catch (error) {
    console.error("Error:", error.message);
    return null;
  } finally {
    console.log("Cleanup");
  }
}
```

---

### Global Error Handlers

#### Browser

```javascript
// Catch unhandled errors
window.addEventListener('error', (event) => {
  console.error("Global error:", event.error);
  // Send to logging service
  logToServer(event.error);
});

// Catch unhandled promise rejections
window.addEventListener('unhandledrejection', (event) => {
  console.error("Unhandled promise rejection:", event.reason);
  event.preventDefault();  // Prevent default error logging
});
```

#### Node.js

```javascript
process.on('uncaughtException', (error) => {
  console.error("Uncaught exception:", error);
  process.exit(1);  // Exit gracefully
});

process.on('unhandledRejection', (reason, promise) => {
  console.error("Unhandled rejection at:", promise, "reason:", reason);
});
```

---

### Error Boundary Pattern

```javascript
class ErrorHandler {
  constructor() {
    this.handlers = [];
  }

  register(handler) {
    this.handlers.push(handler);
  }

  handle(error) {
    for (const handler of this.handlers) {
      if (handler.canHandle(error)) {
        handler.handle(error);
        return;
      }
    }
    console.error("Unhandled error:", error);
  }
}

// Custom handlers
const networkErrorHandler = {
  canHandle: (e) => e.name === 'NetworkError',
  handle: (e) => console.log("Network issue:", e.message)
};

const validationErrorHandler = {
  canHandle: (e) => e instanceof ValidationError,
  handle: (e) => console.log("Validation failed:", e.message)
};

// Usage
const errorHandler = new ErrorHandler();
errorHandler.register(networkErrorHandler);
errorHandler.register(validationErrorHandler);

try {
  throw new ValidationError("Invalid email");
} catch (error) {
  errorHandler.handle(error);
}
```

---

### Retry Logic with Error Handling

```javascript
async function fetchWithRetry(url, retries = 3, delay = 1000) {
  for (let i = 0; i < retries; i++) {
    try {
      const response = await fetch(url);
      if (!response.ok) throw new Error(`HTTP ${response.status}`);
      return await response.json();
    } catch (error) {
      if (i === retries - 1) throw error;  // Last attempt failed
      console.log(`Retry ${i + 1}/${retries} after error:`, error.message);
      await new Promise(resolve => setTimeout(resolve, delay));
    }
  }
}

// Usage
fetchWithRetry('https://api.example.com/data')
  .then(data => console.log(data))
  .catch(error => console.error("All retries failed:", error));
```

---

### Safe JSON Parsing

```javascript
function safeParse(jsonString, fallback = null) {
  try {
    return JSON.parse(jsonString);
  } catch (error) {
    console.warn("JSON parse failed:", error.message);
    return fallback;
  }
}

// Usage
const data = safeParse('{"name": "Alice"}');  // Works
const invalid = safeParse('{bad json}', {});   // Returns {}
```

---

### Advanced: Async Error Wrapper

```javascript
// Wrapper to avoid try-catch repetition
const asyncHandler = (fn) => {
  return async (...args) => {
    try {
      return await fn(...args);
    } catch (error) {
      console.error("Async error:", error);
      throw error;
    }
  };
};

// Usage
const getUser = asyncHandler(async (id) => {
  const response = await fetch(`/api/users/${id}`);
  return await response.json();
});
```

---

### Error Logging Service

```javascript
class Logger {
  static async logError(error, context = {}) {
    try {
      await fetch('/api/logs', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({
          message: error.message,
          stack: error.stack,
          name: error.name,
          timestamp: new Date().toISOString(),
          userAgent: navigator.userAgent,
          url: window.location.href,
          ...context
        })
      });
    } catch (loggingError) {
      console.error("Failed to log error:", loggingError);
    }
  }
}

// Usage
try {
  throw new Error("Something broke");
} catch (error) {
  Logger.logError(error, { userId: 123, action: 'checkout' });
}
```

---

## **Common Interview Traps**

### Trap 1: Finally Always Executes

```javascript
function test() {
  try {
    return "try";
  } finally {
    return "finally";  // This overrides the try return!
  }
}
console.log(test());  // "finally" (not "try")
```

**Why?** `finally` executes after `try`/`catch` and can override their return values.

---

### Trap 2: Async Errors in setTimeout

```javascript
// ❌ WRONG - Catch won't work
try {
  setTimeout(() => {
    throw new Error("Async error");
  }, 100);
} catch (e) {
  console.log("Caught");  // NEVER RUNS!
}

// ✅ CORRECT
setTimeout(() => {
  try {
    throw new Error("Async error");
  } catch (e) {
    console.log("Caught:", e.message);
  }
}, 100);
```

**Why?** `setTimeout` executes outside the try-catch context.

---

### Trap 3: Unhandled Promise Rejections

```javascript
// ❌ WRONG
async function bad() {
  await Promise.reject("Error");  // Unhandled!
}
bad();  // Silent failure

// ✅ CORRECT
async function good() {
  try {
    await Promise.reject("Error");
  } catch (e) {
    console.log("Handled:", e);
  }
}
```

---

### Trap 4: Error Swallowing

```javascript
// ❌ BAD - Empty catch
try {
  somethingDangerous();
} catch (e) {
  // Silent failure - very hard to debug
}

// ✅ BETTER
try {
  somethingDangerous();
} catch (e) {
  console.error("Error occurred:", e);
  // or rethrow: throw e;
}
```

---

### Trap 5: Not Checking Response Status

```javascript
// ❌ WRONG
try {
  const response = await fetch('/api/data');
  const data = await response.json();  // Might fail silently
} catch (e) {
  console.error(e);
}

// ✅ CORRECT
try {
  const response = await fetch('/api/data');
  if (!response.ok) {
    throw new Error(`HTTP ${response.status}: ${response.statusText}`);
  }
  const data = await response.json();
} catch (e) {
  console.error(e);
}
```

---

### Trap 6: Multiple Async Operations Without Error Handling

```javascript
// ❌ WRONG - If any promise fails, others continue
Promise.all([
  fetch('/api/users'),
  fetch('/api/posts'),
  fetch('/api/comments')
]);

// ✅ CORRECT
try {
  const [users, posts, comments] = await Promise.all([
    fetch('/api/users').then(r => r.json()),
    fetch('/api/posts').then(r => r.json()),
    fetch('/api/comments').then(r => r.json())
  ]);
} catch (error) {
  console.error("One or more requests failed:", error);
}
```

---

## **Real-World Use Cases**

### 1. API Request Handling

```javascript
class ApiClient {
  static async request(endpoint, options = {}) {
    try {
      const response = await fetch(endpoint, {
        headers: {
          'Content-Type': 'application/json',
          ...options.headers
        },
        ...options
      });

      if (!response.ok) {
        throw new NetworkError(
          `Request failed: ${response.statusText}`,
          response.status
        );
      }

      return await response.json();
    } catch (error) {
      if (error instanceof NetworkError) {
        // Handle network-specific errors
        console.error(`Network error: ${error.statusCode}`);
      } else {
        // Handle other errors
        console.error("Unexpected error:", error);
      }
      throw error;
    }
  }
}

// Usage
try {
  const user = await ApiClient.request('/api/users/123');
  console.log(user);
} catch (error) {
  showErrorMessage("Failed to load user data");
}
```

---

### 2. Form Validation

```javascript
class FormValidator {
  static validate(data) {
    const errors = [];

    try {
      this.validateEmail(data.email);
    } catch (e) {
      errors.push(e.message);
    }

    try {
      this.validateAge(data.age);
    } catch (e) {
      errors.push(e.message);
    }

    try {
      this.validatePassword(data.password);
    } catch (e) {
      errors.push(e.message);
    }

    if (errors.length > 0) {
      throw new ValidationError(errors.join(', '));
    }

    return true;
  }

  static validateEmail(email) {
    const regex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    if (!regex.test(email)) {
      throw new ValidationError("Invalid email format");
    }
  }

  static validateAge(age) {
    if (age < 18) {
      throw new ValidationError("Must be 18 or older");
    }
    if (age > 120) {
      throw new ValidationError("Invalid age");
    }
  }

  static validatePassword(password) {
    if (password.length < 8) {
      throw new ValidationError("Password must be at least 8 characters");
    }
  }
}

// Usage
function submitForm(data) {
  try {
    FormValidator.validate(data);
    saveToDatabase(data);
    showSuccessMessage();
  } catch (error) {
    if (error instanceof ValidationError) {
      showErrorMessage(error.message);
    } else {
      showErrorMessage("Something went wrong");
      Logger.logError(error);
    }
  }
}
```

---

### 3. File Processing

```javascript
async function processCSV(file) {
  let fileHandle;
  try {
    fileHandle = await openFile(file);
    const content = await readFile(fileHandle);
    const rows = parseCSV(content);
    
    const validated = rows.map((row, index) => {
      try {
        return validateRow(row);
      } catch (error) {
        console.warn(`Row ${index} validation failed:`, error.message);
        return null;
      }
    }).filter(Boolean);

    await saveToDatabase(validated);
    return { success: true, processed: validated.length };
  } catch (error) {
    console.error("CSV processing failed:", error);
    await logError(error, { filename: file.name });
    await notifyAdmin(error);
    return { success: false, error: error.message };
  } finally {
    if (fileHandle) {
      await closeFile(fileHandle);
    }
    cleanup();
  }
}
```

---

### 4. Database Operations

```javascript
class Database {
  static async transaction(operations) {
    const connection = await this.connect();
    
    try {
      await connection.beginTransaction();
      
      for (const operation of operations) {
        await operation(connection);
      }
      
      await connection.commit();
      return { success: true };
    } catch (error) {
      await connection.rollback();
      console.error("Transaction failed:", error);
      throw new DatabaseError("Transaction rolled back", error);
    } finally {
      await connection.close();
    }
  }
}

// Usage
try {
  await Database.transaction([
    (conn) => conn.query("INSERT INTO users..."),
    (conn) => conn.query("UPDATE accounts..."),
    (conn) => conn.query("INSERT INTO logs...")
  ]);
} catch (error) {
  console.error("Database operation failed:", error);
}
```

---

### 5. Rate Limiting with Error Recovery

```javascript
class RateLimiter {
  constructor(maxRequests, timeWindow) {
    this.maxRequests = maxRequests;
    this.timeWindow = timeWindow;
    this.requests = [];
  }

  async execute(fn) {
    try {
      this.checkLimit();
      const result = await fn();
      this.recordRequest();
      return result;
    } catch (error) {
      if (error.name === 'RateLimitError') {
        const waitTime = this.getWaitTime();
        console.log(`Rate limited. Waiting ${waitTime}ms`);
        await this.wait(waitTime);
        return this.execute(fn);  // Retry
      }
      throw error;
    }
  }

  checkLimit() {
    this.cleanOldRequests();
    if (this.requests.length >= this.maxRequests) {
      throw new RateLimitError("Rate limit exceeded");
    }
  }

  cleanOldRequests() {
    const now = Date.now();
    this.requests = this.requests.filter(
      time => now - time < this.timeWindow
    );
  }

  recordRequest() {
    this.requests.push(Date.now());
  }

  getWaitTime() {
    const oldest = Math.min(...this.requests);
    return this.timeWindow - (Date.now() - oldest);
  }

  wait(ms) {
    return new Promise(resolve => setTimeout(resolve, ms));
  }
}

// Usage
const limiter = new RateLimiter(5, 60000);  // 5 requests per minute

try {
  const data = await limiter.execute(() => fetch('/api/data'));
} catch (error) {
  console.error("Request failed:", error);
}
```

---

## **Error Handling Cheatsheet**

| Scenario                 | Solution                    | Example                               |
| ------------------------ | --------------------------- | ------------------------------------- |
| **Synchronous error**    | `try-catch`                 | `try { throw Error() } catch(e) {}`   |
| **Async (Promise)**      | `.catch()`                  | `promise.catch(e => {})`              |
| **Async (async/await)**  | `try-catch` around `await`  | `try { await fn() } catch(e) {}`      |
| **Cleanup needed**       | `finally` block             | `finally { cleanup() }`               |
| **Custom errors**        | `class extends Error`       | `class MyError extends Error {}`      |
| **Global errors**        | Event listeners             | `window.onerror`, `process.on()`      |
| **Unhandled rejections** | `unhandledrejection`        | `window.addEventListener()`           |
| **Retry logic**          | Loop with delay             | `for(let i=0; i<3; i++) try{}catch{}` |
| **Multiple operations**  | `Promise.all()` + try-catch | `try { await Promise.all() }`         |
| **Safe parsing**         | Wrapper function            | `safeParse(json, fallback)`           |

---

## **Best Practices**

### `DO`

1. **Always handle errors** - Never let them fail silently
2. **Use custom error classes** - Makes error handling more precise
3. **Log errors** - Send to monitoring service (Sentry, LogRocket)
4. **Provide context** - Include relevant data in error messages
5. **Use finally for cleanup** - Close connections, clear timers
6. **Validate early** - Catch errors before they propagate
7. **Fail fast** - Don't continue with invalid state
8. **Use specific error types** - Catch only errors you can handle

### `DON'T`

1. **Don't swallow errors** - Empty catch blocks hide bugs
2. **Don't catch everything** - Only catch errors you can handle
3. **Don't use errors for control flow** - Use conditionals instead
4. **Don't ignore async errors** - Always handle promise rejections
5. **Don't trust user input** - Always validate and sanitize
6. **Don't expose sensitive info** - Sanitize error messages for users
7. **Don't retry indefinitely** - Set max retry limits
8. **Don't block with sync errors** - Use async error handling

---

## **Error Types Reference**

### Built-in Error Types

```javascript
// Generic error
new Error("Something went wrong")

// Type errors
new TypeError("Expected string, got number")

// Reference errors
new ReferenceError("Variable not defined")

// Syntax errors
new SyntaxError("Invalid JSON")

// Range errors
new RangeError("Array length invalid")

// URI errors
new URIError("Invalid URI")
```

### Custom Error Types

```javascript
class ValidationError extends Error {
  constructor(message) {
    super(message);
    this.name = "ValidationError";
  }
}

class NetworkError extends Error {
  constructor(message, statusCode) {
    super(message);
    this.name = "NetworkError";
    this.statusCode = statusCode;
  }
}

class DatabaseError extends Error {
  constructor(message, query) {
    super(message);
    this.name = "DatabaseError";
    this.query = query;
  }
}

class AuthenticationError extends Error {
  constructor(message) {
    super(message);
    this.name = "AuthenticationError";
  }
}
```

---

## **Quick Reference**

### Error Handling Decision Tree

```text
Is it synchronous?
├─ YES → Use try-catch
└─ NO → Is it a Promise?
    ├─ YES → Use .catch() or async/await + try-catch
    └─ NO → Is it a callback?
        └─ YES → Handle error in callback (error-first pattern)

Do you need cleanup?
└─ YES → Add finally block

Is this a specific error type?
└─ YES → Create custom error class

Do you need to retry?
└─ YES → Wrap in retry logic

Should this be logged?
└─ YES → Send to logging service
```

---

## **Interview Questions to Practice**

1. **What's the difference between `throw` and `return`?**
2. **Why doesn't try-catch work with setTimeout?**
3. **What happens if you return inside finally?**
4. **How do you handle errors in Promise.all()?**
5. **What's the difference between Error and Exception?**
6. **How do you create custom error types?**
7. **What's error bubbling/propagation?**
8. **How do you handle async errors in event handlers?**
9. **What's the difference between handled and unhandled errors?**
10. **How do you implement a retry mechanism?**

---

## **Summary**

Error handling in JavaScript is essential for building robust applications. Key takeaways:

- **Synchronous**: Use `try-catch-finally`
- **Asynchronous**: Use `.catch()` or `try-catch` with `async/await`
- **Always clean up**: Use `finally` for resource cleanup
- **Custom errors**: Extend `Error` class for specific error types
- **Global handlers**: Set up fallback error handling
- **Never swallow errors**: Always log or handle appropriately
- **Validate early**: Catch errors before they propagate
- **Test error paths**: Write tests for error scenarios

---
