<!-- markdownlint-disable MD036 -->
<!-- markdownlint-disable MD060 -->
<!-- markdownlint-disable MD050 -->
<!-- markdownlint-disable MD024 -->

# **JavaScript Promises and Async/Await**

## **Table Of Contents**

- [**JavaScript Promises and Async/Await**](#javascript-promises-and-asyncawait)
  - [**Table Of Contents**](#table-of-contents)
  - [**Initial Understanding**](#initial-understanding)
  - [**Beginner Explanation**](#beginner-explanation)
    - [What's the Problem?](#whats-the-problem)
    - [Promise States](#promise-states)
    - [Basic Promise Example](#basic-promise-example)
  - [**Advanced Explanation**](#advanced-explanation)
    - [Internal Promise Mechanism](#internal-promise-mechanism)
    - [How Promises Work Internally](#how-promises-work-internally)
  - [**Code Examples**](#code-examples)
    - [Example 1: Basic Promise Chain](#example-1-basic-promise-chain)
    - [Example 2: Promise.all (Parallel Execution)](#example-2-promiseall-parallel-execution)
    - [Example 3: Promise.race (First to Settle Wins)](#example-3-promiserace-first-to-settle-wins)
    - [Example 4: Promise.allSettled (Wait for All, Ignore Failures)](#example-4-promiseallsettled-wait-for-all-ignore-failures)
  - [**Async/Await - Syntactic Sugar**](#asyncawait---syntactic-sugar)
    - [Basic Syntax](#basic-syntax)
    - [How Async/Await Works Internally](#how-asyncawait-works-internally)
    - [Example 1: Sequential vs Parallel Execution](#example-1-sequential-vs-parallel-execution)
    - [Example 2: Error Handling Patterns](#example-2-error-handling-patterns)
    - [Example 3: Advanced Async Patterns](#example-3-advanced-async-patterns)
    - [Example 4: Async Iteration](#example-4-async-iteration)
  - [**Common Interview Traps**](#common-interview-traps)
    - [Trap 1: Forgetting to Return in Promise Chains](#trap-1-forgetting-to-return-in-promise-chains)
    - [Trap 2: Async Function Always Returns a Promise](#trap-2-async-function-always-returns-a-promise)
    - [Trap 3: Error Handling in Promise Chains](#trap-3-error-handling-in-promise-chains)
    - [Trap 4: Mixing Callbacks and Promises](#trap-4-mixing-callbacks-and-promises)
    - [Trap 5: Forgotten await](#trap-5-forgotten-await)
  - [**Real-World Use Cases**](#real-world-use-cases)
    - [Use Case 1: API Orchestration](#use-case-1-api-orchestration)
    - [Use Case 2: Rate Limiting API Calls](#use-case-2-rate-limiting-api-calls)
    - [Use Case 3: Graceful Degradation](#use-case-3-graceful-degradation)
  - [**Performance Comparison**](#performance-comparison)
  - [**Key Takeaways**](#key-takeaways)
  - [**Additional Resources**](#additional-resources)

## **Initial Understanding**

Promises and async/await are JavaScript's solutions for handling **asynchronous operations**. They allow us to write non-blocking code that waits for operations (like API calls, file reads, timers) to complete without freezing the entire program.

- **Promises**: Objects representing the eventual completion (or failure) of an async operation
- **Async/Await**: Syntactic sugar over Promises that makes async code look synchronous

---

## **Beginner Explanation**

### What's the Problem?

**Imagine ordering food online:**

1. You place an order (async operation starts)
2. You don't wait by the door doing nothing
3. You do other things (watch TV, work)
4. When food arrives, you eat it

**Promises work the same way** - they represent a value that will be available in the future.

### Promise States

A Promise can be in one of three states:

```text
┌─────────────┐
│  PENDING    │  (Initial state - operation in progress)
└──────┬──────┘
       │
       ├───────────┐
       │           │
       ▼           ▼
┌──────────┐  ┌──────────┐
│ FULFILLED│  │ REJECTED │
│ (Success)│  │ (Error)  │
└──────────┘  └──────────┘
```

### Basic Promise Example

```js
// Creating a Promise
const myPromise = new Promise((resolve, reject) => {
  const success = true;
  
  if (success) {
    resolve("Operation succeeded!"); // Fulfills the promise
  } else {
    reject("Operation failed!"); // Rejects the promise
  }
});

// Consuming the Promise
myPromise
  .then(result => console.log(result)) // If fulfilled
  .catch(error => console.log(error))  // If rejected
  .finally(() => console.log("Done")); // Always runs
```

---

## **Advanced Explanation**

### Internal Promise Mechanism

```text
EVENT LOOP ARCHITECTURE
═══════════════════════════════════════════

CALL STACK          MICROTASK QUEUE       MACROTASK QUEUE
┌──────────┐       ┌──────────────┐      ┌──────────────┐
│          │       │ .then()      │      │ setTimeout() │
│ main()   │       │ .catch()     │      │ setInterval()│
│ foo()    │       │ async/await  │      │ I/O ops      │
│ bar()    │       │ Promise cb   │      │              │
└──────────┘       └──────────────┘      └──────────────┘
     ↑                    ↑                      ↑
     └────────────────────┴──────────────────────┘
              Event Loop Priority:
              1. Call Stack (synchronous)
              2. Microtask Queue (promises)
              3. Macrotask Queue (timers, I/O)
```

### How Promises Work Internally

1. **Promise Creation**: Creates a new Promise object in pending state
2. **Executor Function**: Runs immediately and synchronously
3. **Resolution**: `resolve()` or `reject()` moves promise to settled state
4. **Handlers**: `.then()`, `.catch()`, `.finally()` are registered as microtasks
5. **Microtask Queue**: Promise callbacks execute before macrotasks

```js
console.log('1: Sync Start');

setTimeout(() => console.log('2: Macrotask'), 0);

Promise.resolve()
  .then(() => console.log('3: Microtask 1'))
  .then(() => console.log('4: Microtask 2'));

console.log('5: Sync End');

// OUTPUT:
// 1: Sync Start
// 5: Sync End
// 3: Microtask 1
// 4: Microtask 2
// 2: Macrotask
```

**Why?** Microtasks (Promises) have higher priority than macrotasks (setTimeout).

---

## **Code Examples**

### Example 1: Basic Promise Chain

```js
// Simple version
function fetchUser(id) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (id > 0) {
        resolve({ id, name: 'John' });
      } else {
        reject('Invalid ID');
      }
    }, 1000);
  });
}

fetchUser(1)
  .then(user => {
    console.log('User:', user);
    return fetchUser(2); // Chaining
  })
  .then(user => console.log('User 2:', user))
  .catch(err => console.error('Error:', err))
  .finally(() => console.log('Cleanup'));
```

### Example 2: Promise.all (Parallel Execution)

```js
const promise1 = Promise.resolve(3);
const promise2 = new Promise(resolve => setTimeout(() => resolve('foo'), 100));
const promise3 = fetch('https://api.github.com/users/github')
  .then(res => res.json());

// Wait for ALL promises to resolve
Promise.all([promise1, promise2, promise3])
  .then(values => {
    console.log(values); // [3, "foo", {...}]
  })
  .catch(err => {
    console.error('One failed:', err); // Fails if ANY promise rejects
  });
```

### Example 3: Promise.race (First to Settle Wins)

```js
const slow = new Promise(resolve => setTimeout(() => resolve('slow'), 2000));
const fast = new Promise(resolve => setTimeout(() => resolve('fast'), 100));

Promise.race([slow, fast])
  .then(result => console.log(result)); // "fast"
```

### Example 4: Promise.allSettled (Wait for All, Ignore Failures)

```js
const promises = [
  Promise.resolve(1),
  Promise.reject('Error'),
  Promise.resolve(3)
];

Promise.allSettled(promises).then(results => {
  console.log(results);
  // [
  //   { status: 'fulfilled', value: 1 },
  //   { status: 'rejected', reason: 'Error' },
  //   { status: 'fulfilled', value: 3 }
  // ]
});
```

---

## **Async/Await - Syntactic Sugar**

### Basic Syntax

```js
// Promise version
function fetchData() {
  return fetch('/api/data')
    .then(res => res.json())
    .then(data => console.log(data))
    .catch(err => console.error(err));
}

// Async/Await version
async function fetchData() {
  try {
    const res = await fetch('/api/data');
    const data = await res.json();
    console.log(data);
  } catch (err) {
    console.error(err);
  }
}
```

### How Async/Await Works Internally

```js
async function foo() {
  const result = await promise;
  console.log(result);
}

// Transforms to:
function foo() {
  return promise.then(result => {
    console.log(result);
  });
}
```

### Example 1: Sequential vs Parallel Execution

```js
// ❌ BAD: Sequential (slow - 3 seconds total)
async function sequential() {
  const user = await fetchUser(1);    // 1 sec
  const posts = await fetchPosts(1);  // 1 sec
  const comments = await fetchComments(1); // 1 sec
  return { user, posts, comments };
}

// ✅ GOOD: Parallel (fast - 1 second total)
async function parallel() {
  const [user, posts, comments] = await Promise.all([
    fetchUser(1),
    fetchPosts(1),
    fetchComments(1)
  ]);
  return { user, posts, comments };
}
```

### Example 2: Error Handling Patterns

```js
// Pattern 1: Try-Catch
async function pattern1() {
  try {
    const data = await fetchData();
    return data;
  } catch (error) {
    console.error('Error:', error);
    return null;
  }
}

// Pattern 2: .catch() on await
async function pattern2() {
  const data = await fetchData().catch(err => {
    console.error('Error:', err);
    return null;
  });
  return data;
}

// Pattern 3: Wrapper function
async function safeAsync(promise) {
  try {
    const data = await promise;
    return [null, data];
  } catch (error) {
    return [error, null];
  }
}

const [error, data] = await safeAsync(fetchData());
if (error) console.error(error);
```

### Example 3: Advanced Async Patterns

```js
// Retry logic
async function fetchWithRetry(url, retries = 3) {
  for (let i = 0; i < retries; i++) {
    try {
      return await fetch(url);
    } catch (err) {
      if (i === retries - 1) throw err;
      await new Promise(resolve => setTimeout(resolve, 1000 * (i + 1)));
    }
  }
}

// Timeout wrapper
function withTimeout(promise, ms) {
  return Promise.race([
    promise,
    new Promise((_, reject) => 
      setTimeout(() => reject(new Error('Timeout')), ms)
    )
  ]);
}

// Usage
await withTimeout(fetch('/api/slow'), 5000);
```

### Example 4: Async Iteration

```js
// for...of with async/await
async function processUsers(userIds) {
  for (const id of userIds) {
    const user = await fetchUser(id); // Sequential
    console.log(user);
  }
}

// Parallel processing with map
async function processUsersParallel(userIds) {
  const promises = userIds.map(id => fetchUser(id));
  const users = await Promise.all(promises);
  console.log(users);
}

// Async generators
async function* fetchPages() {
  let page = 1;
  while (page <= 5) {
    yield await fetch(`/api/page/${page}`);
    page++;
  }
}

for await (const response of fetchPages()) {
  console.log(await response.json());
}
```

---

## **Common Interview Traps**

### Trap 1: Forgetting to Return in Promise Chains

```js
// ❌ WRONG
promise1
  .then(result => {
    promise2(result); // Forgotten return!
  })
  .then(result => console.log(result)); // undefined!

// ✅ CORRECT
promise1
  .then(result => promise2(result))
  .then(result => console.log(result));
```

### Trap 2: Async Function Always Returns a Promise

```js
async function getValue() {
  return 42; // Wrapped in Promise.resolve(42)
}

getValue().then(val => console.log(val)); // 42
```

### Trap 3: Error Handling in Promise Chains

```js
promise1
  .then(result => {
    throw new Error('Oops');
  })
  .then(result => console.log('Never runs'))
  .catch(err => console.log('Caught:', err.message)); // Runs
```

### Trap 4: Mixing Callbacks and Promises

```js
// ❌ ANTI-PATTERN
async function bad() {
  setTimeout(() => {
    return 'done'; // Doesn't work!
  }, 1000);
}

// ✅ CORRECT
function good() {
  return new Promise(resolve => {
    setTimeout(() => resolve('done'), 1000);
  });
}
```

### Trap 5: Forgotten await

```js
async function danger() {
  const data = fetchData(); // Missing await!
  console.log(data); // Promise object, not data!
}
```

---

## **Real-World Use Cases**

### Use Case 1: API Orchestration

```js
async function getUserDashboard(userId) {
  try {
    // Fetch user profile first
    const user = await fetchUser(userId);
    
    // Then fetch related data in parallel
    const [posts, followers, activity] = await Promise.all([
      fetchUserPosts(userId),
      fetchFollowers(userId),
      fetchActivity(userId)
    ]);
    
    return {
      user,
      stats: {
        postsCount: posts.length,
        followersCount: followers.length
      },
      recentActivity: activity.slice(0, 10)
    };
  } catch (error) {
    console.error('Dashboard load failed:', error);
    throw error;
  }
}
```

### Use Case 2: Rate Limiting API Calls

```js
async function batchProcess(items, limit = 3) {
  const results = [];
  
  for (let i = 0; i < items.length; i += limit) {
    const batch = items.slice(i, i + limit);
    const batchResults = await Promise.all(
      batch.map(item => processItem(item))
    );
    results.push(...batchResults);
  }
  
  return results;
}
```

### Use Case 3: Graceful Degradation

```js
async function loadWithFallback() {
  try {
    return await fetch('/api/primary');
  } catch {
    console.warn('Primary API failed, trying backup...');
    try {
      return await fetch('/api/backup');
    } catch {
      console.error('All APIs failed, using cache');
      return getCachedData();
    }
  }
}
```

---

## **Performance Comparison**

```js
// Sequential: 3000ms total
async function sequential() {
  const a = await delay(1000); // 0-1000ms
  const b = await delay(1000); // 1000-2000ms
  const c = await delay(1000); // 2000-3000ms
}

// Parallel: 1000ms total
async function parallel() {
  const [a, b, c] = await Promise.all([
    delay(1000), // All start at 0ms
    delay(1000),
    delay(1000)
  ]); // All finish at ~1000ms
}
```

---

## **Key Takeaways**

1. **Promises** represent future values and have three states: pending, fulfilled, rejected
2. **Microtasks** (Promise callbacks) execute before macrotasks (setTimeout)
3. **Async/await** is syntactic sugar that makes Promises easier to work with
4. **Always use `await`** when you need the resolved value
5. **Use `Promise.all()`** for parallel operations
6. **Handle errors** with try-catch or .catch()
7. **Don't mix** callbacks and async/await
8. **Return Promises** in .then() for chaining

---

## **Additional Resources**

- [MDN - Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)
- [MDN - async function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)
- [MDN - await](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/await)
- [JavaScript.info - Promises](https://javascript.info/promise-basics)
- [Event Loop Visualizer](http://latentflip.com/loupe/)

---

**Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/) by Santosh Kumar Baliarsingh**
