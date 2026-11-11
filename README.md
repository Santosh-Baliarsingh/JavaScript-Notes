<!-- markdownlint-disable MD036 -->
<!-- markdownlint-disable MD060 -->

# Complete JavaScript Functions Guide

## Table of Contents

- [Complete JavaScript Functions Guide](#complete-javascript-functions-guide)
  - [Table of Contents](#table-of-contents)
  - [Function Declaration](#function-declaration)
  - [Function Expression](#function-expression)
  - [Arrow Function](#arrow-function)
  - [IIFE](#iife)
  - [Constructor Function](#constructor-function)
  - [Generator Function](#generator-function)
  - [Async Function](#async-function)
  - [Async Generator Function](#async-generator-function)
  - [Method Definition](#method-definition)
  - [Higher-Order Functions](#higher-order-functions)
  - [Callback Functions](#callback-functions)
  - [Recursive Functions](#recursive-functions)
  - [Curried Functions](#curried-functions)
  - [Pure Functions](#pure-functions)
  - [Function Composition](#function-composition)
  - [Partial Application](#partial-application)
  - [Quick Reference Templates](#quick-reference-templates)
    - [Debounce](#debounce)
    - [Throttle](#throttle)
    - [Memoization](#memoization)
    - [Deep Clone](#deep-clone)
    - [Promise Wrapper](#promise-wrapper)
  - [Common Interview Traps](#common-interview-traps)
  - [Real-World Use Cases](#real-world-use-cases)

---

## Function Declaration

**Classic, hoisted function definition**

```js
// Function Declaration
function greet(name) {
  return `Hello, ${name}!`;
}

console.log(greet('Alice')); // Hello, Alice!

// ✅ Hoisted - can be called before declaration
sayHi();
function sayHi() {
  console.log('Hi!');
}
```

**Characteristics:**

- ✅ Hoisted (available before definition)
- ✅ Has its own `this` context
- ✅ Can be used as constructor
- ✅ Has `arguments` object

---

## Function Expression

**Function assigned to a variable**

```js
// Function Expression
const greet = function(name) {
  return `Hello, ${name}!`;
};

console.log(greet('Bob')); // Hello, Bob!

// ❌ NOT hoisted - cannot call before definition
// greet(); // ReferenceError
const greet = function() { console.log('Hi'); };
```

**Named Function Expression:**

```js
const factorial = function fact(n) {
  return n <= 1 ? 1 : n * fact(n - 1);
};

console.log(factorial(5)); // 120
// fact(5); // ReferenceError - 'fact' only exists inside the function
```

**Characteristics:**

- ❌ Not hoisted
- ✅ Has its own `this` context
- ✅ Can be named for recursion
- ✅ Has `arguments` object

---

## Arrow Function

**Concise syntax, lexical `this` binding**

```js
// Arrow Function - Basic
const greet = (name) => {
  return `Hello, ${name}!`;
};

// Arrow Function - Implicit Return
const greet = name => `Hello, ${name}!`;

// Arrow Function - No Parameters
const sayHi = () => 'Hi!';

// Arrow Function - Multiple Parameters
const add = (a, b) => a + b;

// Arrow Function - Object Return (must wrap in parentheses)
const makePerson = (name, age) => ({ name, age });

console.log(greet('Charlie')); // Hello, Charlie!
console.log(add(5, 3)); // 8
console.log(makePerson('Dave', 30)); // { name: 'Dave', age: 30 }
```

**Lexical `this` Example:**

```js
function Person() {
  this.age = 0;
  
  // Arrow function inherits 'this' from Person
  setInterval(() => {
    this.age++;
    console.log(this.age);
  }, 1000);
}

const p = new Person(); // Logs: 1, 2, 3...

// ❌ Regular function would lose 'this' context
function PersonBroken() {
  this.age = 0;
  setInterval(function() {
    this.age++; // 'this' is window/undefined
  }, 1000);
}
```

**Characteristics:**

- ❌ Not hoisted
- ❌ No own `this` (inherits from parent scope)
- ❌ Cannot be used as constructor
- ❌ No `arguments` object
- ✅ Concise syntax
- ✅ Implicit return for single expressions

---

## IIFE

**Immediately Invoked Function Expression**

```js
// IIFE - Classic
(function() {
  console.log('I run immediately!');
})();

// IIFE - With Parameters
(function(name) {
  console.log(`Hello, ${name}!`);
})('Eve');

// IIFE - Arrow Function
(() => {
  console.log('Arrow IIFE!');
})();

// IIFE - Module Pattern
const counter = (function() {
  let count = 0; // Private variable
  return {
    increment() { return ++count; },
    decrement() { return --count; },
    getCount() { return count; }
  };
})();

console.log(counter.increment()); // 1
console.log(counter.increment()); // 2
console.log(counter.getCount()); // 2
```

**Characteristics:**

- ✅ Creates private scope
- ✅ Prevents global pollution
- ✅ Used in module pattern
- ✅ Self-executing

---

## Constructor Function

**Function used with `new` keyword to create objects**

```js
// Constructor Function
function Person(name, age) {
  this.name = name;
  this.age = age;
  this.greet = function() {
    return `Hi, I'm ${this.name}`;
  };
}

// Better approach - methods on prototype
Person.prototype.sayAge = function() {
  return `I am ${this.age} years old`;
};

const person1 = new Person('Frank', 25);
const person2 = new Person('Grace', 30);

console.log(person1.greet()); // Hi, I'm Frank
console.log(person2.sayAge()); // I am 30 years old
console.log(person1.greet === person2.greet); // false (separate instances)
console.log(person1.sayAge === person2.sayAge); // true (shared on prototype)
```

**Characteristics:**

- ✅ Used with `new` keyword
- ✅ Creates new object
- ✅ Sets prototype
- ✅ Returns object automatically

---

## Generator Function

**Function that can pause and resume execution**

```js
// Generator Function
function* numberGenerator() {
  yield 1;
  yield 2;
  yield 3;
}

const gen = numberGenerator();
console.log(gen.next()); // { value: 1, done: false }
console.log(gen.next()); // { value: 2, done: false }
console.log(gen.next()); // { value: 3, done: false }
console.log(gen.next()); // { value: undefined, done: true }

// Generator - Infinite Sequence
function* idGenerator() {
  let id = 1;
  while (true) {
    yield id++;
  }
}

const ids = idGenerator();
console.log(ids.next().value); // 1
console.log(ids.next().value); // 2
console.log(ids.next().value); // 3

// Generator - Fibonacci
function* fibonacci() {
  let [a, b] = [0, 1];
  while (true) {
    yield a;
    [a, b] = [b, a + b];
  }
}

const fib = fibonacci();
console.log(fib.next().value); // 0
console.log(fib.next().value); // 1
console.log(fib.next().value); // 1
console.log(fib.next().value); // 2
console.log(fib.next().value); // 3

// Generator - Passing Values
function* twoWayGenerator() {
  const x = yield 'First yield';
  console.log('Received:', x);
  const y = yield 'Second yield';
  console.log('Received:', y);
}

const gen2 = twoWayGenerator();
console.log(gen2.next()); // { value: 'First yield', done: false }
console.log(gen2.next(10)); // Received: 10, { value: 'Second yield', done: false }
console.log(gen2.next(20)); // Received: 20, { value: undefined, done: true }
```

**Characteristics:**

- ✅ Can pause execution (`yield`)
- ✅ Can resume execution (`next()`)
- ✅ Lazy evaluation
- ✅ Memory efficient for sequences
- ✅ Can send/receive values

---

## Async Function

**Function that returns a Promise**

```js
// Async Function
async function fetchData() {
  return 'Data fetched';
}

fetchData().then(data => console.log(data)); // Data fetched

// Async with await
async function getUserData(userId) {
  try {
    const response = await fetch(`https://api.example.com/users/${userId}`);
    const data = await response.json();
    return data;
  } catch (error) {
    console.error('Error:', error);
  }
}

// Async Arrow Function
const fetchUser = async (id) => {
  const user = await fetch(`/api/users/${id}`);
  return user.json();
};

// Async IIFE
(async () => {
  const data = await fetchData();
  console.log(data);
})();

// Multiple Async Operations
async function parallelFetch() {
  const [users, posts] = await Promise.all([
    fetch('/api/users').then(r => r.json()),
    fetch('/api/posts').then(r => r.json())
  ]);
  return { users, posts };
}
```

**Characteristics:**

- ✅ Always returns a Promise
- ✅ Can use `await` inside
- ✅ Better error handling with try/catch
- ✅ Cleaner than Promise chains

---

## Async Generator Function

**Combines async and generator capabilities**

```js
// Async Generator
async function* asyncNumberGenerator() {
  yield await Promise.resolve(1);
  yield await Promise.resolve(2);
  yield await Promise.resolve(3);
}

(async () => {
  for await (const num of asyncNumberGenerator()) {
    console.log(num); // 1, 2, 3
  }
})();

// Async Generator - Fetch Pages
async function* fetchPages(url) {
  let page = 1;
  while (page <= 3) {
    const response = await fetch(`${url}?page=${page}`);
    const data = await response.json();
    yield data;
    page++;
  }
}

(async () => {
  for await (const pageData of fetchPages('/api/data')) {
    console.log('Page:', pageData);
  }
})();
```

**Characteristics:**

- ✅ Combines async and generator
- ✅ Can `yield` Promises
- ✅ Use with `for await...of`
- ✅ Perfect for paginated APIs

---

## Method Definition

**Functions defined inside objects or classes**

```js
// Object Methods - ES5
const obj = {
  name: 'Henry',
  greet: function() {
    return `Hello, I'm ${this.name}`;
  }
};

// Object Methods - ES6 Shorthand
const obj2 = {
  name: 'Iris',
  greet() {
    return `Hello, I'm ${this.name}`;
  },
  async fetchData() {
    return await fetch('/api/data');
  },
  *generateIds() {
    let id = 1;
    while (true) yield id++;
  }
};

console.log(obj2.greet()); // Hello, I'm Iris

// Class Methods
class Person {
  constructor(name) {
    this.name = name;
  }
  
  // Instance method
  greet() {
    return `Hello, I'm ${this.name}`;
  }
  
  // Static method
  static species() {
    return 'Homo sapiens';
  }
  
  // Getter
  get displayName() {
    return this.name.toUpperCase();
  }
  
  // Setter
  set displayName(value) {
    this.name = value.toLowerCase();
  }
  
  // Async method
  async fetchProfile() {
    return await fetch(`/api/users/${this.name}`);
  }
  
  // Generator method
  *getLetters() {
    for (const char of this.name) {
      yield char;
    }
  }
}

const person = new Person('Jack');
console.log(person.greet()); // Hello, I'm Jack
console.log(Person.species()); // Homo sapiens
console.log(person.displayName); // JACK
```

---

## Higher-Order Functions

**Functions that take or return other functions**

```js
// Higher-Order Function - Takes function as argument
function repeat(n, action) {
  for (let i = 0; i < n; i++) {
    action(i);
  }
}

repeat(3, console.log); // 0, 1, 2

// Higher-Order Function - Returns function
function multiplier(factor) {
  return function(number) {
    return number * factor;
  };
}

const double = multiplier(2);
const triple = multiplier(3);

console.log(double(5)); // 10
console.log(triple(5)); // 15

// Array HOFs
const numbers = [1, 2, 3, 4, 5];

const doubled = numbers.map(n => n * 2);
const evens = numbers.filter(n => n % 2 === 0);
const sum = numbers.reduce((acc, n) => acc + n, 0);

console.log(doubled); // [2, 4, 6, 8, 10]
console.log(evens); // [2, 4]
console.log(sum); // 15
```

---

## Callback Functions

**Functions passed as arguments**

```js
// Callback Function
function processData(data, callback) {
  const result = data * 2;
  callback(result);
}

processData(5, (result) => {
  console.log('Result:', result); // Result: 10
});

// Async Callback
function fetchData(url, callback) {
  setTimeout(() => {
    callback({ id: 1, name: 'User' });
  }, 1000);
}

fetchData('/api/user', (data) => {
  console.log('User:', data);
});

// Error-first Callback (Node.js style)
function readFile(path, callback) {
  setTimeout(() => {
    if (path === 'error') {
      callback(new Error('File not found'), null);
    } else {
      callback(null, 'File contents');
    }
  }, 100);
}

readFile('test.txt', (error, data) => {
  if (error) {
    console.error(error);
  } else {
    console.log(data);
  }
});
```

---

## Recursive Functions

**Functions that call themselves**

```js
// Recursive Function - Factorial
function factorial(n) {
  if (n <= 1) return 1;
  return n * factorial(n - 1);
}

console.log(factorial(5)); // 120

// Recursive Function - Fibonacci
function fibonacci(n) {
  if (n <= 1) return n;
  return fibonacci(n - 1) + fibonacci(n - 2);
}

console.log(fibonacci(6)); // 8

// Recursive Function - Optimized with Memoization
function memoizedFib() {
  const cache = {};
  return function fib(n) {
    if (n in cache) return cache[n];
    if (n <= 1) return n;
    cache[n] = fib(n - 1) + fib(n - 2);
    return cache[n];
  };
}

const fastFib = memoizedFib();
console.log(fastFib(50)); // 12586269025

// Recursive Function - Tree Traversal
function sumTree(node) {
  if (!node) return 0;
  return node.value + sumTree(node.left) + sumTree(node.right);
}

const tree = {
  value: 1,
  left: { value: 2, left: null, right: null },
  right: { value: 3, left: null, right: null }
};

console.log(sumTree(tree)); // 6
```

---

## Curried Functions

**Functions that return functions for partial application**

```js
// Currying - Manual
function add(a) {
  return function(b) {
    return function(c) {
      return a + b + c;
    };
  };
}

console.log(add(1)(2)(3)); // 6

// Currying - Arrow Functions
const multiply = a => b => c => a * b * c;
console.log(multiply(2)(3)(4)); // 24

// Generic Curry Function
function curry(fn) {
  return function curried(...args) {
    if (args.length >= fn.length) {
      return fn.apply(this, args);
    }
    return function(...nextArgs) {
      return curried.apply(this, args.concat(nextArgs));
    };
  };
}

function sum(a, b, c) {
  return a + b + c;
}

const curriedSum = curry(sum);
console.log(curriedSum(1)(2)(3)); // 6
console.log(curriedSum(1, 2)(3)); // 6
console.log(curriedSum(1)(2, 3)); // 6

// Practical Example
const discount = percentage => price => price * (1 - percentage / 100);

const tenPercentOff = discount(10);
const twentyPercentOff = discount(20);

console.log(tenPercentOff(100)); // 90
console.log(twentyPercentOff(100)); // 80
```

---

## Pure Functions

**Functions with no side effects**

```js
// Pure Function
function add(a, b) {
  return a + b;
}

// Pure Function - Array operations (non-mutating)
function double(arr) {
  return arr.map(n => n * 2);
}

const numbers = [1, 2, 3];
const doubled = double(numbers);
console.log(numbers); // [1, 2, 3] (unchanged)
console.log(doubled); // [2, 4, 6]

// Impure Function (has side effects)
let total = 0;
function addToTotal(n) {
  total += n; // Modifies external state
  return total;
}

// Impure Function (mutates input)
function impureDouble(arr) {
  for (let i = 0; i < arr.length; i++) {
    arr[i] *= 2; // Mutates original array
  }
  return arr;
}
```

---

## Function Composition

**Combining multiple functions**

```js
// Function Composition
const compose = (...fns) => x => fns.reduceRight((acc, fn) => fn(acc), x);

const pipe = (...fns) => x => fns.reduce((acc, fn) => fn(acc), x);

// Example functions
const add5 = x => x + 5;
const multiply2 = x => x * 2;
const subtract3 = x => x - 3;

// Compose (right to left)
const calculate = compose(subtract3, multiply2, add5);
console.log(calculate(10)); // (10 + 5) * 2 - 3 = 27

// Pipe (left to right)
const calculate2 = pipe(add5, multiply2, subtract3);
console.log(calculate2(10)); // (10 + 5) * 2 - 3 = 27

// Practical Example
const toLowerCase = str => str.toLowerCase();
const removeSpaces = str => str.replace(/\s/g, '');
const addPrefix = str => `user_${str}`;

const sanitizeUsername = pipe(toLowerCase, removeSpaces, addPrefix);
console.log(sanitizeUsername('John Doe')); // user_johndoe
```

---

## Partial Application

**Pre-filling some arguments**

```js
// Partial Application
function partial(fn, ...presetArgs) {
  return function(...laterArgs) {
    return fn(...presetArgs, ...laterArgs);
  };
}

function greet(greeting, name, punctuation) {
  return `${greeting}, ${name}${punctuation}`;
}

const sayHello = partial(greet, 'Hello');
const sayHelloFriendly = partial(greet, 'Hello', 'Friend');

console.log(sayHello('Kate', '!')); // Hello, Kate!
console.log(sayHelloFriendly('!')); // Hello, Friend!

// Practical Example - Logging
function log(level, timestamp, message) {
  console.log(`[${level}] ${timestamp}: ${message}`);
}

const errorLog = partial(log, 'ERROR');
const infoLog = partial(log, 'INFO');

errorLog(new Date().toISOString(), 'Something went wrong');
infoLog(new Date().toISOString(), 'Process completed');
```

---

## Quick Reference Templates

### Debounce

```js
function debounce(fn, delay) {
  let timer;
  return function(...args) {
    clearTimeout(timer);
    timer = setTimeout(() => fn.apply(this, args), delay);
  };
}
```

### Throttle

```js
function throttle(fn, limit) {
  let last = 0;
  return function(...args) {
    const now = Date.now();
    if (now - last >= limit) {
      last = now;
      fn.apply(this, args);
    }
  };
}
```

### Memoization

```js
function memoize(fn) {
  const cache = {};
  return function(n) {
    if (cache[n]) return cache[n];
    cache[n] = fn(n);
    return cache[n];
  }
}
```

### Deep Clone

```js
const deepClone = obj => JSON.parse(JSON.stringify(obj));
```

### Promise Wrapper

```js
const wait = ms => new Promise(res => setTimeout(res, ms));
```

---

## Common Interview Traps

1. **Arrow functions don't have `this`** — they inherit it from the parent scope
2. **Function declarations are hoisted**, function expressions are not
3. **Arrow functions cannot be constructors** — no `new` keyword
4. **Generator functions return iterators**, not values directly
5. **Async functions always return Promises**, even if you return a value
6. **Closures keep references to variables**, not values
7. **`arguments` object** doesn't exist in arrow functions
8. **IIFE syntax requires parentheses** around the function

---

## Real-World Use Cases

| Function Type | Use Case |
|---------------|----------|
| **Arrow Functions** | Callbacks, array methods, event handlers |
| **Async Functions** | API calls, file operations, database queries |
| **Generators** | Infinite sequences, lazy evaluation, custom iterators |
| **IIFE** | Module pattern, avoiding global scope pollution |
| **HOFs** | Data transformation, functional programming |
| **Currying** | Configuration, partial application, reusable utilities |
| **Recursive** | Tree traversal, divide-and-conquer algorithms |
| **Callbacks** | Event handling, async operations (legacy) |

---

**Reference:** [MDN JavaScript Functions Documentation](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Functions)

---

**Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/) by Santosh Kumar Baliarsingh**
