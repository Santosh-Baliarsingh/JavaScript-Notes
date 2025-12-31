<!-- markdownlint-disable MD036 -->
<!-- markdownlint-disable MD060 -->
<!-- markdownlint-disable MD024 -->

# JavaScript Data Types

---

## Table Of Contents

- [JavaScript Data Types](#javascript-data-types)
  - [Table Of Contents](#table-of-contents)
  - [Initial Understanding](#initial-understanding)
  - [Beginner Explanation](#beginner-explanation)
  - [Advanced Explanation](#advanced-explanation)
    - [Memory Model](#memory-model)
    - [Type Checking](#type-checking)
  - [All Data Types with Examples](#all-data-types-with-examples)
    - [**1. Number**](#1-number)
    - [**2. String**](#2-string)
    - [**3. Boolean**](#3-boolean)
    - [**4. Undefined**](#4-undefined)
    - [**5. Null**](#5-null)
    - [**6. Symbol**](#6-symbol)
    - [**7. BigInt**](#7-bigint)
    - [**8. Object**](#8-object)
  - [Common Interview Traps](#common-interview-traps)
    - [**Trap 1: typeof null**](#trap-1-typeof-null)
    - [**Trap 2: NaN weirdness**](#trap-2-nan-weirdness)
    - [**Trap 3: Array type check**](#trap-3-array-type-check)
    - [**Trap 4: Floating point precision**](#trap-4-floating-point-precision)
    - [**Trap 5: Loose vs Strict Equality**](#trap-5-loose-vs-strict-equality)
  - [Real-World Use Cases](#real-world-use-cases)
    - [**1. Type checking before operations**](#1-type-checking-before-operations)
    - [**2. Symbol for private properties**](#2-symbol-for-private-properties)
    - [**3. BigInt for cryptography**](#3-bigint-for-cryptography)
    - [**4. Null for intentional reset**](#4-null-for-intentional-reset)
  - [Quick Reference Table](#quick-reference-table)
  - [Bonus: Type Conversion](#bonus-type-conversion)
    - [**Implicit Coercion**](#implicit-coercion)
    - [**Explicit Conversion**](#explicit-conversion)

## Initial Understanding

JavaScript has **8 data types**, divided into two categories:

- **Primitive Types** (7): Immutable, stored by value
- **Non-Primitive Type** (1): Mutable, stored by reference

---

## Beginner Explanation

**Primitives** are simple values — think of them as atoms.  
**Objects** are complex structures — think of them as molecules made of atoms.

**Key difference:**

- `Primitives` are **copied by value**
- `Objects` are **copied by reference**

```js
let a = 5;
let b = a; // copy value
b = 10;
console.log(a); // 5 (unchanged)

let obj1 = { x: 5 };
let obj2 = obj1; // copy reference
obj2.x = 10;
console.log(obj1.x); // 10 (changed!)
```

---

## Advanced Explanation

### Memory Model

- **Primitives** are stored in the **stack** (fixed size, fast access)
- **Objects** are stored in the **heap** (dynamic size), with references in the stack

### Type Checking

```js
typeof 42;           // "number"
typeof "hello";      // "string"
typeof true;         // "boolean"
typeof undefined;    // "undefined"
typeof Symbol();     // "symbol"
typeof 100n;         // "bigint"
typeof null;         // "object" ⚠️ (historical bug!)
typeof {};           // "object"
typeof [];           // "object" (arrays are objects)
typeof function(){}; // "function" (special object)
```

---

## All Data Types with Examples

### **1. Number**

**Represents integers and floating-point numbers**

```js
let int = 42;
let float = 3.14;
let negative = -100;
let scientific = 5e3; // 5000
let infinity = Infinity;
let notANumber = NaN;

console.log(typeof 42); // "number"
console.log(0.1 + 0.2 === 0.3); // false ⚠️ (floating point precision)
console.log(Number.MAX_SAFE_INTEGER); // 9007199254740991
```

**Interview Traps:**

- `NaN === NaN` → `false` (use `Number.isNaN()` instead)
- `typeof NaN` → `"number"`
- `1/0` → `Infinity`

---

### **2. String**

**Sequence of characters (`immutable`)**

```js
let single = 'hello';
let double = "world";
let template = `Hello ${single}`; // template literals
let multiline = `line 1
line 2`;

console.log(typeof "hello"); // "string"
console.log("hello"[0]); // "h" (indexing)
console.log("hello".length); // 5
```

**Immutability:**

```js
let str = "hello";
str[0] = "H"; // does nothing
console.log(str); // "hello"

str = "Hello"; // reassignment works
```

---

### **3. Boolean**

**Logical `true`/`false`**

```js
let isActive = true;
let isCompleted = false;

console.log(typeof true); // "boolean"

// Truthy vs Falsy
console.log(Boolean(1)); // true
console.log(Boolean(0)); // false
console.log(Boolean("")); // false
console.log(Boolean("text")); // true
console.log(Boolean(null)); // false
console.log(Boolean(undefined)); // false
```

**Falsy values (6 total):**

```js
false, 0, -0, 0n, "", null, undefined, NaN
```

---

### **4. Undefined**

**Variable declared but not assigned**

```js
let x;
console.log(x); // undefined
console.log(typeof x); // "undefined"

function test() {}
console.log(test()); // undefined (no return)

let obj = { a: 1 };
console.log(obj.b); // undefined (missing property)
```

**Interview Trap:**

```js
let x;
console.log(x === undefined); // true
console.log(typeof x === "undefined"); // true (safer check)
```

---

### **5. Null**

**Intentional absence of value**

```js
let user = null; // explicitly "no value"
console.log(typeof null); // "object" ⚠️ (JS bug since 1995)

console.log(null == undefined); // true (loose equality)
console.log(null === undefined); // false (strict equality)
```

**null vs undefined:**

- `undefined`: JS sets this automatically
- `null`: developer sets this intentionally

---

### **6. Symbol**

**Unique identifier (ES6+)**

```js
let sym1 = Symbol();
let sym2 = Symbol();
console.log(sym1 === sym2); // false (always unique)

let sym3 = Symbol("id");
let sym4 = Symbol("id");
console.log(sym3 === sym4); // false

// Use case: unique object keys
let obj = {
  [Symbol("id")]: 123,
  name: "Alice"
};
console.log(Object.keys(obj)); // ["name"] (Symbol ignored)
```

**Real-world use:** `Hidden object properties`, `avoiding key collisions`

---

### **7. BigInt**

**Large integers beyond `Number.MAX_SAFE_INTEGER` (ES2020+)**

```js
let big1 = 9007199254740991n; // add 'n' suffix
let big2 = BigInt("9007199254740991");

console.log(typeof 123n); // "bigint"
console.log(123n + 456n); // 579n

// ⚠️ Cannot mix with Number
console.log(123n + 456); // TypeError
console.log(123n + BigInt(456)); // 579n
```

**Interview Trap:**

```js
console.log(1n === 1); // false (different types)
console.log(1n == 1); // true (coercion)
```

---

### **8. Object**

**Collection of key-value pairs (`mutable`, `stored by reference`)**

```js
let obj = { name: "Alice", age: 25 };
let arr = [1, 2, 3];
let func = function() {};
let date = new Date();
let regex = /abc/;
let map = new Map();
let set = new Set();

console.log(typeof obj); // "object"
console.log(typeof arr); // "object"
console.log(typeof func); // "function"
console.log(typeof date); // "object"
```

**Reference behavior:**

```js
let a = { x: 1 };
let b = a; // copy reference
b.x = 2;
console.log(a.x); // 2 (both point to same object)
```

**Checking arrays:**

```js
console.log(typeof []); // "object" ⚠️
console.log(Array.isArray([])); // true ✅
```

---

## Common Interview Traps

### **Trap 1: typeof null**

```js
console.log(typeof null); // "object" (not "null"!)
// Use strict equality:
console.log(value === null); // ✅
```

### **Trap 2: NaN weirdness**

```js
console.log(NaN === NaN); // false
console.log(Object.is(NaN, NaN)); // true
console.log(Number.isNaN(NaN)); // true ✅
```

### **Trap 3: Array type check**

```js
console.log(typeof []); // "object"
console.log(Array.isArray([])); // true ✅
```

### **Trap 4: Floating point precision**

```js
console.log(0.1 + 0.2); // 0.30000000000000004
console.log((0.1 + 0.2).toFixed(2)); // "0.30"
```

### **Trap 5: Loose vs Strict Equality**

```js
console.log(null == undefined); // true
console.log(null === undefined); // false
console.log(0 == false); // true
console.log(0 === false); // false
```

---

## Real-World Use Cases

### **1. Type checking before operations**

```js
function safeDivide(a, b) {
  if (typeof a !== 'number' || typeof b !== 'number') {
    return 'Invalid input';
  }
  return b === 0 ? Infinity : a / b;
}
```

### **2. Symbol for private properties**

```js
const _id = Symbol('id');
class User {
  constructor(name) {
    this[_id] = Math.random();
    this.name = name;
  }
  getId() {
    return this[_id];
  }
}
```

### **3. BigInt for cryptography**

```js
const largeNumber = 12345678901234567890n;
const encrypted = largeNumber * 7n;
```

### **4. Null for intentional reset**

```js
let cachedData = fetchData();
// ... later
cachedData = null; // clear cache
```

---

## Quick Reference Table

| Type        | Example     | typeof        | Mutable | Stored  |
| ----------- | ----------- | ------------- | ------- | ------- |
| `Number`    | `42`        | `"number"`    | `No`    | `Stack` |
| `String`    | `"hello"`   | `"string"`    | `No`    | `Stack` |
| `Boolean`   | `true`      | `"boolean"`   | `No`    | `Stack` |
| `Undefined` | `undefined` | `"undefined"` | `No`    | `Stack` |
| `Null`      | `null`      | `"object"`⚠️   | `No`    | `Stack` |
| `Symbol`    | `Symbol()`  | `"symbol"`    | `No`    | `Stack` |
| `BigInt`    | `123n`      | `"bigint"`    | `No`    | `Stack` |
| `Object`    | `{ a: 1 }`  | `"object"`    | `Yes`   | `Heap`  |

---

## Bonus: Type Conversion

### **Implicit Coercion**

```js
console.log("5" + 3); // "53" (string concatenation)
console.log("5" - 3); // 2 (numeric subtraction)
console.log(true + 1); // 2
console.log(false + 1); // 1
console.log(null + 1); // 1
console.log(undefined + 1); // NaN
```

### **Explicit Conversion**

```js
// To Number
Number("123"); // 123
parseInt("123px"); // 123
parseFloat("3.14abc"); // 3.14
+"123"; // 123 (unary plus)

// To String
String(123); // "123"
(123).toString(); // "123"
123 + ""; // "123"

// To Boolean
Boolean(1); // true
!!1; // true (double negation)
```

---
