<!-- markdownlint-disable MD036 -->
<!-- markdownlint-disable MD060 -->
<!-- markdownlint-disable MD024 -->

# JAVASCRIPT LOOPS COMPLETE GUIDE

---

## Table of Contents

- [Loop Types Overview](#loop-types-overview)
- [`For` Loop](#for-loop)
- [`For...In` Loop](#forin-loop)
- [`For...Of` Loop](#forof-loop)
- [`While` Loop](#while-loop)
- [`Do...While` Loop](#dowhile-loop)
- [`Switch` Statement](#switch-statement)
- [Common Interview Traps](#common-interview-traps)
- [Comparison Table](#comparison-table)
- [Use Case Guide](#use-case-guide)
- [Performance Tips](#performance-tips)
- [Key Takeaways](#key-takeaways)

## LOOP TYPES OVERVIEW

- ✅ **`for`** → Classic counter-based loop

- ✅ **`for...in`** → Iterates over object keys
  
- ✅ **`for...of`** → Iterates over iterable values

- ✅ **`while`** → Pre-condition loop
  
- ✅ **`do...while`** → Post-condition loop
  
- ✅ **`switch`** → Multi-condition control flow

---

## `FOR` LOOP

### Syntax

```javascript
for (initialization; condition; increment) {
  // code block
}
```

### Example 1: Basic Counter

```javascript
for (let i = 0; i < 5; i++) {
  console.log(i);
}
// Output: 0, 1, 2, 3, 4
```

### Example 2: Array Iteration

```javascript
const fruits = ['apple', 'banana', 'cherry'];

for (let i = 0; i < fruits.length; i++) {
  console.log(fruits[i]);
}
```

### Execution Steps

```text
Step 1: Initialize (let i = 0)
Step 2: Check condition (i < 5)
Step 3: Execute body
Step 4: Increment (i++)
Step 5: Repeat from Step 2
```

### ✨ Best For

- Fixed iterations
- Array access with index
- Performance-critical code

---

## `FOR...IN` LOOP

### Syntax

```javascript
for (let key in object) {
  // code block
}
```

### Example 1: Object Properties

```javascript
const user = {
  name: 'Alice',
  age: 25,
  city: 'NYC'
};

for (let key in user) {
  console.log(`${key}: ${user[key]}`);
}
// Output:
// name: Alice
// age: 25
// city: NYC
```

### Example 2: Safe Iteration

```javascript
const obj = { a: 1, b: 2 };

for (let key in obj) {
  if (obj.hasOwnProperty(key)) {
    console.log(key, obj[key]);
  }
}
```

### ⚠️ Warning

- ❌ Don't use on arrays
- ❌ Includes inherited properties
- ✅ Use `hasOwnProperty()` for safety

### ✨ Best For

- Object property iteration
- Debugging object keys
- Legacy code compatibility

---

## `FOR...OF` LOOP

### Syntax

```javascript
for (let value of iterable) {
  // code block
}
```

### Example 1: Array

```javascript
const numbers = [10, 20, 30];

for (let num of numbers) {
  console.log(num);
}
// Output: 10, 20, 30
```

### Example 2: String

```javascript
const word = 'hello';

for (let char of word) {
  console.log(char);
}
// Output: h, e, l, l, o
```

### Example 3: Map

```javascript
const map = new Map([
  ['a', 1],
  ['b', 2]
]);

for (let [key, value] of map) {
  console.log(key, value);
}
```

### Example 4: Set

```javascript
const set = new Set([1, 2, 3]);

for (let value of set) {
  console.log(value);
}
```

### ✨ Best For

- Arrays, strings, Maps, Sets
- Clean, readable code
- Modern JavaScript

---

## `WHILE` LOOP

### Syntax

```javascript
while (condition) {
  // code block
}
```

### Example 1: Counter

```javascript
let i = 0;

while (i < 5) {
  console.log(i);
  i++;
}
// Output: 0, 1, 2, 3, 4
```

### Example 2: Queue Processing

```javascript
const queue = [1, 2, 3, 4, 5];

while (queue.length > 0) {
  const item = queue.shift();
  console.log('Processing:', item);
}
```

### Example 3: Conditional Loop

```javascript
let password = '';
let attempts = 0;

while (password !== '1234' && attempts < 3) {
  password = prompt('Enter password:');
  attempts++;
}
```

### Execution Flow

```javascript
Step 1: Check condition
Step 2: If true → Execute body
Step 3: Return to Step 1
Step 4: If false → Exit loop
```

### ✨ Best For

- Unknown iteration count
- Waiting for conditions
- Game loops

---

## `DO...WHILE` LOOP

### Syntax

```javascript
do {
  // code block (runs at least once)
} while (condition);
```

### Example 1: Guaranteed Execution

```javascript
let i = 0;

do {
  console.log(i);
  i++;
} while (i < 5);
// Output: 0, 1, 2, 3, 4
```

### Example 2: Runs Even When False

```javascript
let x = 10;

do {
  console.log('This runs once!');
  x++;
} while (x < 5);
// Output: This runs once!
```

### Example 3: Menu System

```javascript
let choice;

do {
  console.log('1. Option A');
  console.log('2. Option B');
  console.log('3. Exit');
  choice = prompt('Choose:');
} while (choice !== '3');
```

### Execution Flow

```text
Step 1: Execute body (always)
Step 2: Check condition
Step 3: If true → Repeat
Step 4: If false → Exit
```

### ✨ Best For

- Menu systems
- Input validation
- At least one execution needed

---

## `SWITCH` STATEMENT

### Syntax

```javascript
switch (expression) {
  case value1:
    // code
    break;
  case value2:
    // code
    break;
  default:
    // code
}
```

### Example 1: Day of Week

```javascript
const day = 3;

switch (day) {
  case 1:
    console.log('Monday');
    break;
  case 2:
    console.log('Tuesday');
    break;
  case 3:
    console.log('Wednesday');
    break;
  default:
    console.log('Invalid');
}
// Output: Wednesday
```

### Example 2: Fall-through

```javascript
const grade = 'B';

switch (grade) {
  case 'A':
  case 'B':
  case 'C':
    console.log('Pass');
    break;
  case 'D':
  case 'F':
    console.log('Fail');
    break;
}
// Output: Pass
```

### Example 3: Multiple Actions

```javascript
const action = 'save';

switch (action) {
  case 'save':
    console.log('Saving...');
    console.log('File saved');
    break;
  case 'load':
    console.log('Loading...');
    break;
  default:
    console.log('Unknown');
}
```

### ✨ Best For

- Multiple conditions
- State machines
- Action handlers

---

## COMMON INTERVIEW TRAPS

### Trap 1: `for...in` on Arrays

```javascript
❌ WRONG
const arr = [10, 20, 30];
for (let i in arr) {
  console.log(i); // includes properties!
}

✅ CORRECT
for (let val of arr) {
  console.log(val);
}
```

### Trap 2: `var` in Loops

```javascript
❌ WRONG
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100);
}
// Output: 3, 3, 3

✅ CORRECT
for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100);
}
// Output: 0, 1, 2
```

### Trap 3: `Infinite` Loops

```javascript
❌ WRONG
let i = 0;
while (i < 5) {
  console.log(i); // Missing i++
}

✅ CORRECT
let i = 0;
while (i < 5) {
  console.log(i);
  i++;
}
```

### Trap 4: Missing `break`

```javascript
❌ WRONG
switch (x) {
  case 1:
    console.log('One');
  case 2:
    console.log('Two'); // Fall-through!
}

✅ CORRECT
switch (x) {
  case 1:
    console.log('One');
    break;
  case 2:
    console.log('Two');
    break;
}
```

---

## COMPARISON TABLE

| Feature | for | for...in | for...of | while | do...while |
|---------|-----|----------|----------|-------|------------|
| `Arrays` | ✅ | ❌ | ✅ | ✅ | ✅ |
| `Objects` | ❌ | ✅ | ❌ | ✅ | ✅ |
| `Keys` | ❌ | ✅ | ❌ | N/A | N/A |
| `Values` | ✅ | ❌ | ✅ | N/A | N/A |
| `Min 1 Run` | ❌ | ❌ | ❌ | ❌ | ✅ |
| `Block Scope` | ✅ | ✅ | ✅ | ❌ | ❌ |

---

## USE CASE GUIDE

### `for` Loop

- ✅ Fixed iterations
- ✅ Array with index needed
- ✅ Performance critical

### `for...in` Loop

- ✅ Object property iteration
- ✅ Debugging object keys
- ✅ Checking properties

### `for...of` Loop

- ✅ Arrays, strings
- ✅ Maps, Sets, iterables
- ✅ Clean, modern code

### `while` Loop

- ✅ Unknown iterations
- ✅ Waiting for conditions
- ✅ Game loops

### `do...while` Loop

- ✅ Menu systems
- ✅ Input validation
- ✅ Run at least once

### `switch` Statement

- ✅ Multiple conditions
- ✅ State machines
- ✅ Action routing

---

## PERFORMANCE TIPS

```javascript
❌ SLOW
for (let i = 0; i < arr.length; i++) {}

✅ FAST
for (let i = 0, len = arr.length; i < len; i++) {}

✅ MODERN
for (let item of arr) {}
```

---

## KEY TAKEAWAYS

- 1️⃣ **Use `for` for fixed iterations**
- 2️⃣ **Use `for...of` for arrays (not `for...in`)**
- 3️⃣ **Use `for...in` for objects only**
- 4️⃣ **Use `while` for unknown iterations**
- 5️⃣ **Use `do...while` when body must run once**
- 6️⃣ **Always use `break` in `switch cases`**
- 7️⃣ **Use `let` instead of `var` in loops**
- 8️⃣ **Avoid `modifying` loop variables inside body**

---

## QUICK REFERENCE

### Loop Syntax Cheat Sheet

```javascript
// FOR
for (let i = 0; i < n; i++) { }

// FOR...IN
for (let key in obj) { }

// FOR...OF
for (let val of arr) { }

// WHILE
while (condition) { }

// DO...WHILE
do { } while (condition);

// SWITCH
switch (x) {
  case 1: break;
  default: break;
}
```

---

**Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/) by Santosh Kumar Baliarsingh**
