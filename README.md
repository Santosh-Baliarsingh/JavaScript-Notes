<!-- markdownlint-disable MD036 -->
<!-- markdownlint-disable MD060 -->

# JavaScript Arrays

## Table of Contents

- [JavaScript Arrays](#javascript-arrays)
  - [Table of Contents](#table-of-contents)
  - [What is an Array?](#what-is-an-array)
  - [Creating Arrays](#creating-arrays)
  - [Performance Characteristics](#performance-characteristics)
  - [Array Methods](#array-methods)
    - [A. MUTATING METHODS (Change original array)](#a-mutating-methods-change-original-array)
      - [1. `push()` - Add to end](#1-push---add-to-end)
      - [2. `pop()` - Remove from end](#2-pop---remove-from-end)
      - [3. `unshift()` - Add to beginning](#3-unshift---add-to-beginning)
      - [4. `shift()` - Remove from beginning](#4-shift---remove-from-beginning)
      - [5. `splice()` - Add/remove at any position](#5-splice---addremove-at-any-position)
      - [6. `reverse()` - Reverse in place](#6-reverse---reverse-in-place)
      - [7. `sort()` - Sort in place](#7-sort---sort-in-place)
      - [8. `fill()` - Fill with static value](#8-fill---fill-with-static-value)
      - [9. `copyWithin()` - Copy part to another location](#9-copywithin---copy-part-to-another-location)
    - [B. NON-MUTATING METHODS (Return new array/value)](#b-non-mutating-methods-return-new-arrayvalue)
      - [10. `concat()` - Merge arrays](#10-concat---merge-arrays)
      - [11. `slice()` - Extract portion](#11-slice---extract-portion)
      - [12. `join()` - Convert to string](#12-join---convert-to-string)
      - [13. `toString()` - Comma-separated string](#13-tostring---comma-separated-string)
    - [C. ITERATION METHODS](#c-iteration-methods)
      - [14. `forEach()` - Execute function for each](#14-foreach---execute-function-for-each)
      - [15. `map()` - Transform each element](#15-map---transform-each-element)
      - [16. `filter()` - Keep matching elements](#16-filter---keep-matching-elements)
      - [17. `reduce()` - Reduce to single value](#17-reduce---reduce-to-single-value)
      - [18. `reduceRight()` - Reduce from right](#18-reduceright---reduce-from-right)
      - [19. `find()` - First matching element](#19-find---first-matching-element)
      - [20. `findIndex()` - Index of first match](#20-findindex---index-of-first-match)
      - [21. `findLast()` - Last matching element (ES2023)](#21-findlast---last-matching-element-es2023)
      - [22. `findLastIndex()` - Index of last match (ES2023)](#22-findlastindex---index-of-last-match-es2023)
      - [23. `some()` - Check if ANY matches](#23-some---check-if-any-matches)
      - [24. `every()` - Check if ALL match](#24-every---check-if-all-match)
    - [D. SEARCH METHODS](#d-search-methods)
      - [25. `indexOf()` - First index of element](#25-indexof---first-index-of-element)
      - [26. `lastIndexOf()` - Last index of element](#26-lastindexof---last-index-of-element)
      - [27. `includes()` - Check existence (ES2016)](#27-includes---check-existence-es2016)
    - [E. FLAT \& TRANSFORMATION](#e-flat--transformation)
      - [28. `flat()` - Flatten nested arrays (ES2019)](#28-flat---flatten-nested-arrays-es2019)
      - [29. `flatMap()` - Map + flatten (ES2019)](#29-flatmap---map--flatten-es2019)
    - [F. STATIC METHODS](#f-static-methods)
      - [30. `Array.isArray()` - Check if array](#30-arrayisarray---check-if-array)
      - [31. `Array.from()` - Create from iterable](#31-arrayfrom---create-from-iterable)
      - [32. `Array.of()` - Create from arguments](#32-arrayof---create-from-arguments)
    - [G. ES2023+ IMMUTABLE METHODS](#g-es2023-immutable-methods)
      - [33. `at()` - Access with negative indices](#33-at---access-with-negative-indices)
      - [34. `toReversed()` - Non-mutating reverse](#34-toreversed---non-mutating-reverse)
      - [35. `toSorted()` - Non-mutating sort](#35-tosorted---non-mutating-sort)
      - [36. `toSpliced()` - Non-mutating splice](#36-tospliced---non-mutating-splice)
      - [37. `with()` - Non-mutating replacement](#37-with---non-mutating-replacement)
  - [Common Interview Traps](#common-interview-traps)
    - [❌ Trap 1: Mutating vs Non-Mutating](#-trap-1-mutating-vs-non-mutating)
    - [❌ Trap 2: forEach can't break](#-trap-2-foreach-cant-break)
    - [❌ Trap 3: map creates new array (memory)](#-trap-3-map-creates-new-array-memory)
    - [❌ Trap 4: Default sort is lexicographic](#-trap-4-default-sort-is-lexicographic)
    - [❌ Trap 5: Sparse arrays](#-trap-5-sparse-arrays)
  - [Real-World Examples](#real-world-examples)
    - [Example 1: Data Transformation Pipeline](#example-1-data-transformation-pipeline)
    - [Example 2: Grouping (SQL GROUP BY)](#example-2-grouping-sql-group-by)
    - [Example 3: Remove Duplicates](#example-3-remove-duplicates)
    - [Example 4: Pagination](#example-4-pagination)
    - [Example 5: Flatten Nested Arrays](#example-5-flatten-nested-arrays)
  - [Quick Reference Table](#quick-reference-table)
  - [Method Return Values Reference](#method-return-values-reference)
  - [Advanced Patterns](#advanced-patterns)
    - [Pattern 1: Method Chaining](#pattern-1-method-chaining)
    - [Pattern 2: Immutable Updates](#pattern-2-immutable-updates)
    - [Pattern 3: Array Destructuring](#pattern-3-array-destructuring)
  - [Resources](#resources)

## What is an Array?

Arrays in JavaScript are **ordered collections** that can hold any data type. They are **zero-indexed**, **dynamically sized**, and internally stored as special objects.

```js
const fruits = ['apple', 'banana', 'cherry'];
//                 0        1         2
```

---

## Creating Arrays

```js
const arr1 = [1, 2, 3];                    // Literal (most common)
const arr2 = new Array(1, 2, 3);           // Constructor
const arr3 = Array.of(1, 2, 3);            // Static method
const arr4 = Array.from('hello');          // From iterable → ['h','e','l','l','o']
const arr5 = Array.from({length: 5}, (_, i) => i); // [0, 1, 2, 3, 4]
```

---

## Performance Characteristics

| Operation | Time Complexity |
|-----------|----------------|
| Access by index | O(1) |
| Push/Pop | O(1) |
| Shift/Unshift | O(n) |
| Splice | O(n) |
| Search (indexOf, includes) | O(n) |

---

## Array Methods

### A. MUTATING METHODS (Change original array)

#### 1. `push()` - Add to end

```js
const arr = [1, 2];
arr.push(3, 4);
console.log(arr); // [1, 2, 3, 4]
// Returns: new length (4)
```

#### 2. `pop()` - Remove from end

```js
const arr = [1, 2, 3];
const last = arr.pop();
console.log(last); // 3
console.log(arr);  // [1, 2]
// Returns: removed element
```

#### 3. `unshift()` - Add to beginning

```js
const arr = [2, 3];
arr.unshift(0, 1);
console.log(arr); // [0, 1, 2, 3]
// Returns: new length
// ⚠️ O(n) - reindexes all elements
```

#### 4. `shift()` - Remove from beginning

```js
const arr = [1, 2, 3];
const first = arr.shift();
console.log(first); // 1
console.log(arr);   // [2, 3]
// Returns: removed element
```

#### 5. `splice()` - Add/remove at any position

```js
const arr = [1, 2, 3, 4, 5];

// Remove 2 elements from index 1
arr.splice(1, 2);
console.log(arr); // [1, 4, 5]

// Insert at index 1
arr.splice(1, 0, 'a', 'b');
console.log(arr); // [1, 'a', 'b', 4, 5]

// Replace
arr.splice(2, 1, 'X');
console.log(arr); // [1, 'a', 'X', 4, 5]
```

#### 6. `reverse()` - Reverse in place

```js
const arr = [1, 2, 3];
arr.reverse();
console.log(arr); // [3, 2, 1]
```

#### 7. `sort()` - Sort in place

```js
// ⚠️ Default: converts to strings
const arr1 = [3, 1, 10, 2];
arr1.sort();
console.log(arr1); // [1, 10, 2, 3] ⚠️

// ✅ Numeric sort
const arr2 = [3, 1, 10, 2];
arr2.sort((a, b) => a - b);
console.log(arr2); // [1, 2, 3, 10]

// Descending
arr2.sort((a, b) => b - a); // [10, 3, 2, 1]
```

#### 8. `fill()` - Fill with static value

```js
const arr = [1, 2, 3, 4];
arr.fill(0, 1, 3); // (value, start, end)
console.log(arr); // [1, 0, 0, 4]

// Create filled array
const arr2 = new Array(5).fill('x'); // ['x', 'x', 'x', 'x', 'x']
```

#### 9. `copyWithin()` - Copy part to another location

```js
const arr = [1, 2, 3, 4, 5];
arr.copyWithin(0, 3, 5); // (target, start, end)
console.log(arr); // [4, 5, 3, 4, 5]
```

---

### B. NON-MUTATING METHODS (Return new array/value)

#### 10. `concat()` - Merge arrays

```js
const arr1 = [1, 2];
const arr2 = [3, 4];
const result = arr1.concat(arr2, [5, 6]);
console.log(result); // [1, 2, 3, 4, 5, 6]

// Modern alternative
const result2 = [...arr1, ...arr2];
```

#### 11. `slice()` - Extract portion

```js
const arr = [1, 2, 3, 4, 5];
const sub = arr.slice(1, 4); // (start, end)
console.log(sub); // [2, 3, 4]

// Negative indices
console.log(arr.slice(-2)); // [4, 5]

// Clone array
const clone = arr.slice();
```

#### 12. `join()` - Convert to string

```js
const arr = ['Hello', 'World'];
console.log(arr.join(' '));  // "Hello World"
console.log(arr.join('-'));  // "Hello-World"
```

#### 13. `toString()` - Comma-separated string

```js
const arr = [1, 2, 3];
console.log(arr.toString()); // "1,2,3"
```

---

### C. ITERATION METHODS

#### 14. `forEach()` - Execute function for each

```js
const arr = [1, 2, 3];
arr.forEach((item, index) => {
  console.log(`${index}: ${item}`);
});
// ⚠️ Cannot break/return early
```

#### 15. `map()` - Transform each element

```js
const arr = [1, 2, 3];
const doubled = arr.map(x => x * 2);
console.log(doubled); // [2, 4, 6]
```

#### 16. `filter()` - Keep matching elements

```js
const arr = [1, 2, 3, 4, 5];
const evens = arr.filter(x => x % 2 === 0);
console.log(evens); // [2, 4]
```

#### 17. `reduce()` - Reduce to single value

```js
// Sum
const arr = [1, 2, 3, 4];
const sum = arr.reduce((acc, curr) => acc + curr, 0);
console.log(sum); // 10

// Object grouping
const people = [
  {name: 'Alice', age: 25},
  {name: 'Bob', age: 30},
  {name: 'Charlie', age: 25}
];
const grouped = people.reduce((acc, person) => {
  acc[person.age] = acc[person.age] || [];
  acc[person.age].push(person.name);
  return acc;
}, {});
// {25: ['Alice', 'Charlie'], 30: ['Bob']}
```

#### 18. `reduceRight()` - Reduce from right

```js
const arr = ['a', 'b', 'c'];
const result = arr.reduceRight((acc, curr) => acc + curr);
console.log(result); // "cba"
```

#### 19. `find()` - First matching element

```js
const arr = [5, 12, 8, 130, 44];
const found = arr.find(x => x > 10);
console.log(found); // 12
```

#### 20. `findIndex()` - Index of first match

```js
const arr = [5, 12, 8, 130, 44];
const index = arr.findIndex(x => x > 10);
console.log(index); // 1
```

#### 21. `findLast()` - Last matching element (ES2023)

```js
const arr = [5, 12, 8, 130, 44];
const found = arr.findLast(x => x > 10);
console.log(found); // 44
```

#### 22. `findLastIndex()` - Index of last match (ES2023)

```js
const arr = [5, 12, 8, 130, 44];
const index = arr.findLastIndex(x => x > 10);
console.log(index); // 4
```

#### 23. `some()` - Check if ANY matches

```js
const arr = [1, 2, 3, 4];
console.log(arr.some(x => x > 3)); // true
```

#### 24. `every()` - Check if ALL match

```js
const arr = [2, 4, 6, 8];
console.log(arr.every(x => x % 2 === 0)); // true
```

---

### D. SEARCH METHODS

#### 25. `indexOf()` - First index of element

```js
const arr = [1, 2, 3, 2, 1];
console.log(arr.indexOf(2));    // 1
console.log(arr.indexOf(2, 2)); // 3 (start from index 2)
console.log(arr.indexOf(5));    // -1 (not found)
```

#### 26. `lastIndexOf()` - Last index of element

```js
const arr = [1, 2, 3, 2, 1];
console.log(arr.lastIndexOf(2)); // 3
```

#### 27. `includes()` - Check existence (ES2016)

```js
const arr = [1, 2, 3, NaN];
console.log(arr.includes(2));   // true
console.log(arr.includes(NaN)); // true ✅ (indexOf can't find NaN)
```

---

### E. FLAT & TRANSFORMATION

#### 28. `flat()` - Flatten nested arrays (ES2019)

```js
const arr = [1, [2, [3, [4]]]];
console.log(arr.flat());     // [1, 2, [3, [4]]]
console.log(arr.flat(2));    // [1, 2, 3, [4]]
console.log(arr.flat(Infinity)); // [1, 2, 3, 4]
```

#### 29. `flatMap()` - Map + flatten (ES2019)

```js
const arr = [1, 2, 3];
const result = arr.flatMap(x => [x, x * 2]);
console.log(result); // [1, 2, 2, 4, 3, 6]
```

---

### F. STATIC METHODS

#### 30. `Array.isArray()` - Check if array

```js
console.log(Array.isArray([1, 2]));     // true
console.log(Array.isArray('hello'));    // false
```

#### 31. `Array.from()` - Create from iterable

```js
// From string
console.log(Array.from('hello')); // ['h', 'e', 'l', 'l', 'o']

// With mapping
console.log(Array.from([1, 2, 3], x => x * 2)); // [2, 4, 6]

// Create range
const range = Array.from({length: 5}, (_, i) => i); // [0, 1, 2, 3, 4]
```

#### 32. `Array.of()` - Create from arguments

```js
console.log(Array.of(7));       // [7]
console.log(new Array(7));      // [empty × 7] ⚠️
```

---

### G. ES2023+ IMMUTABLE METHODS

#### 33. `at()` - Access with negative indices

```js
const arr = [1, 2, 3, 4, 5];
console.log(arr.at(-1));  // 5 (last)
console.log(arr.at(-2));  // 4
```

#### 34. `toReversed()` - Non-mutating reverse

```js
const arr = [1, 2, 3];
const reversed = arr.toReversed();
console.log(reversed); // [3, 2, 1]
console.log(arr);      // [1, 2, 3] ✅ unchanged
```

#### 35. `toSorted()` - Non-mutating sort

```js
const arr = [3, 1, 2];
const sorted = arr.toSorted();
console.log(sorted); // [1, 2, 3]
console.log(arr);    // [3, 1, 2] ✅ unchanged
```

#### 36. `toSpliced()` - Non-mutating splice

```js
const arr = [1, 2, 3, 4];
const spliced = arr.toSpliced(1, 2, 'a', 'b');
console.log(spliced); // [1, 'a', 'b', 4]
console.log(arr);     // [1, 2, 3, 4] ✅ unchanged
```

#### 37. `with()` - Non-mutating replacement

```js
const arr = [1, 2, 3];
const newArr = arr.with(1, 99);
console.log(newArr); // [1, 99, 3]
console.log(arr);    // [1, 2, 3] ✅ unchanged
```

---

## Common Interview Traps

### ❌ Trap 1: Mutating vs Non-Mutating

```js
const arr = [1, 2, 3];
arr.sort(); // ❌ Mutates original!

const sorted = arr.slice().sort(); // ✅ Safe
const sorted2 = arr.toSorted();    // ✅ ES2023
```

### ❌ Trap 2: forEach can't break

```js
// ❌ Won't work
arr.forEach(x => {
  if (x === 5) break; // SyntaxError!
});

// ✅ Use for...of instead
for (const x of arr) {
  if (x === 5) break;
}
```

### ❌ Trap 3: map creates new array (memory)

```js
// ❌ Wasteful
arr.map(x => console.log(x));

// ✅ Use forEach
arr.forEach(x => console.log(x));
```

### ❌ Trap 4: Default sort is lexicographic

```js
[10, 2, 1].sort();              // ❌ [1, 10, 2]
[10, 2, 1].sort((a, b) => a - b); // ✅ [1, 2, 10]
```

### ❌ Trap 5: Sparse arrays

```js
const arr = [1, , 3];
arr.forEach(x => console.log(x)); // Skips index 1
arr.map(x => x * 2);              // [2, empty, 6]
```

---

## Real-World Examples

### Example 1: Data Transformation Pipeline

```js
const users = [
  {name: 'Alice', age: 25, active: true},
  {name: 'Bob', age: 17, active: false},
  {name: 'Charlie', age: 30, active: true}
];

const result = users
  .filter(u => u.active && u.age >= 18)
  .map(u => u.name.toUpperCase())
  .sort();

console.log(result); // ['ALICE', 'CHARLIE']
```

### Example 2: Grouping (SQL GROUP BY)

```js
const sales = [
  {product: 'A', amount: 100},
  {product: 'B', amount: 200},
  {product: 'A', amount: 150}
];

const totals = sales.reduce((acc, sale) => {
  acc[sale.product] = (acc[sale.product] || 0) + sale.amount;
  return acc;
}, {});

console.log(totals); // {A: 250, B: 200}
```

### Example 3: Remove Duplicates

```js
const arr = [1, 2, 2, 3, 3, 4];
const unique = [...new Set(arr)];
console.log(unique); // [1, 2, 3, 4]
```

### Example 4: Pagination

```js
function paginate(arr, page, size) {
  return arr.slice((page - 1) * size, page * size);
}

const items = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
console.log(paginate(items, 2, 3)); // [4, 5, 6]
```

### Example 5: Flatten Nested Arrays

```js
const nested = [[1, 2], [3, 4], [5, 6]];
const flat = nested.flat(); // [1, 2, 3, 4, 5, 6]

// Or with reduce
const flat2 = nested.reduce((acc, arr) => acc.concat(arr), []);
```

---

## Quick Reference Table

| Category | Methods |
|----------|---------|
| **Add/Remove** | push, pop, shift, unshift, splice |
| **Transform** | map, flatMap, flat, reverse, sort, fill |
| **Filter** | filter, slice |
| **Search** | find, findIndex, indexOf, includes, some, every |
| **Iterate** | forEach, map, filter, reduce, reduceRight |
| **Combine** | concat, join |
| **Check** | isArray, includes, some, every |
| **Immutable** | toReversed, toSorted, toSpliced, with, at |

---

## Method Return Values Reference

| Method | Returns | Mutates Original? | Example Return |
|--------|---------|-------------------|----------------|
| **push()** | New length | ✅ Yes | `4` |
| **pop()** | Removed element | ✅ Yes | `3` |
| **shift()** | Removed element | ✅ Yes | `1` |
| **unshift()** | New length | ✅ Yes | `5` |
| **splice()** | Array of removed elements | ✅ Yes | `[2, 3]` |
| **reverse()** | Reversed array (same ref) | ✅ Yes | `[3, 2, 1]` |
| **sort()** | Sorted array (same ref) | ✅ Yes | `[1, 2, 3]` |
| **fill()** | Modified array (same ref) | ✅ Yes | `[0, 0, 0]` |
| **copyWithin()** | Modified array (same ref) | ✅ Yes | `[4, 5, 3]` |
| **concat()** | New array | ❌ No | `[1, 2, 3, 4]` |
| **slice()** | New array | ❌ No | `[2, 3, 4]` |
| **join()** | String | ❌ No | `"1,2,3"` |
| **toString()** | String | ❌ No | `"1,2,3"` |
| **toLocaleString()** | Localized string | ❌ No | `"1,000"` |
| **forEach()** | `undefined` | ❌ No | `undefined` |
| **map()** | New array | ❌ No | `[2, 4, 6]` |
| **filter()** | New array | ❌ No | `[2, 4]` |
| **reduce()** | Single value (any type) | ❌ No | `10` or `{}` |
| **reduceRight()** | Single value (any type) | ❌ No | `"cba"` |
| **find()** | Element or `undefined` | ❌ No | `12` |
| **findIndex()** | Index or `-1` | ❌ No | `3` |
| **findLast()** | Element or `undefined` | ❌ No | `44` |
| **findLastIndex()** | Index or `-1` | ❌ No | `4` |
| **some()** | Boolean | ❌ No | `true` |
| **every()** | Boolean | ❌ No | `false` |
| **indexOf()** | Index or `-1` | ❌ No | `2` |
| **lastIndexOf()** | Index or `-1` | ❌ No | `3` |
| **includes()** | Boolean | ❌ No | `true` |
| **flat()** | New flattened array | ❌ No | `[1, 2, 3]` |
| **flatMap()** | New flattened array | ❌ No | `[1, 2, 2, 4]` |
| **Array.isArray()** | Boolean | ❌ No | `true` |
| **Array.from()** | New array | ❌ No | `['h', 'e']` |
| **Array.of()** | New array |❌ No | `[7]` |
| **at()** | Element or `undefined` | ❌ No | `5` |
| **toReversed()** | New reversed array | ❌ No | `[3, 2, 1]` |
| **toSorted()** | New sorted array | ❌ No | `[1, 2, 3]` |
| **toSpliced()** | New array | ❌ No | `[1, 'a', 4]` |
| **with()** | New array | ❌ No | `[1, 99, 3]` |

---

## Advanced Patterns

### Pattern 1: Method Chaining

```js
const result = arr
  .filter(x => x > 0)
  .map(x => x * 2)
  .reduce((sum, x) => sum + x, 0);
```

### Pattern 2: Immutable Updates

```js
// Add item
const newArr = [...arr, newItem];

// Remove item at index
const removed = [...arr.slice(0, i), ...arr.slice(i + 1)];

// Update item at index
const updated = arr.map((item, idx) => idx === i ? newValue : item);
```

### Pattern 3: Array Destructuring

```js
const [first, second, ...rest] = [1, 2, 3, 4, 5];
console.log(first);  // 1
console.log(second); // 2
console.log(rest);   // [3, 4, 5]
```

---

## Resources

- [MDN Array Documentation](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)

---
