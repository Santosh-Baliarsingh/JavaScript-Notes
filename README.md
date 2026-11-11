<!-- markdownlint-disable MD036 -->
<!-- markdownlint-disable MD060 -->
<!-- markdownlint-disable MD042 -->
# JavaScript String Methods

---

## Table of Contents

- [JavaScript String Methods](#javascript-string-methods)
  - [Table of Contents](#table-of-contents)
  - [A. CASE CONVERSION](#a-case-conversion)
    - [`toUpperCase()` / `toLowerCase()`](#touppercase--tolowercase)
    - [`toLocaleUpperCase()` / `toLocaleLowerCase()`](#tolocaleuppercase--tolocalelowercase)
  - [B. SEARCHING \& CHECKING](#b-searching--checking)
    - [**indexOf(`searchValue`, `fromIndex`)**](#indexofsearchvalue-fromindex)
    - [**lastIndexOf(`searchValue`, `fromIndex`)**](#lastindexofsearchvalue-fromindex)
    - [**includes(`searchString`, `position`)** ✨ `ES6`](#includessearchstring-position--es6)
    - [**startsWith(`searchString`, `position`)** ✨ ES6](#startswithsearchstring-position--es6)
    - [**endsWith(`searchString`, `length`)** ✨ ES6](#endswithsearchstring-length--es6)
    - [**search(`regexp`)**](#searchregexp)
    - [**match(`regexp`)**](#matchregexp)
    - [**matchAll(`regexp`)** ✨ `ES2020`](#matchallregexp--es2020)
  - [C. EXTRACTING SUBSTRINGS](#c-extracting-substrings)
    - [**slice(`startIndex`, `endIndex`)**](#slicestartindex-endindex)
    - [**substring(`startIndex`, `endIndex`)**](#substringstartindex-endindex)
    - [**substr(`startIndex`, `length`)** ⚠️ `DEPRECATED`](#substrstartindex-length-️-deprecated)
    - [**charAt(`index`)**](#charatindex)
    - [**charCodeAt(`index`)**](#charcodeatindex)
    - [**codePointAt(`index`)** ✨ `ES6`](#codepointatindex--es6)
    - [**at(`index`)** ✨ `ES2022`](#atindex--es2022)
  - [D. MODIFYING STRINGS](#d-modifying-strings)
    - [**concat(...`strings`)**](#concatstrings)
    - [**repeat(`count`)** ✨ `ES6`](#repeatcount--es6)
    - [**padStart(`targetLength`, `padString`)** ✨ `ES2017`](#padstarttargetlength-padstring--es2017)
    - [**padEnd(`targetLength`, `padString`)** ✨ `ES2017`](#padendtargetlength-padstring--es2017)
  - [E. TRIMMING](#e-trimming)
    - [`trim()` ✨ `ES5`](#trim--es5)
    - [`trimStart()` / `trimLeft()` ✨ `ES2019`](#trimstart--trimleft--es2019)
    - [`trimEnd`() / `trimRight`() ✨ `ES2019`](#trimend--trimright--es2019)
  - [F. REPLACING](#f-replacing)
    - [**replace(`search`, `replacement`)**](#replacesearch-replacement)
    - [**replaceAll(`search`, `replacement`)** ✨ `ES2021`](#replaceallsearch-replacement--es2021)
    - [`With callback`](#with-callback)
  - [G. SPLITTING](#g-splitting)
    - [**split(`separator`, `limit`)**](#splitseparator-limit)
  - [H. LOCALIZATION](#h-localization)
    - [**localeCompare(`compareString`, `locales`, `options`)**](#localecomparecomparestring-locales-options)
  - [I. CONVERSION (STATIC METHODS)](#i-conversion-static-methods)
    - [**String.fromCharCode(...`codes`)**](#stringfromcharcodecodes)
    - [**String.fromCodePoint(...`codes`)** ✨ `ES6`](#stringfromcodepointcodes--es6)
    - [`String.raw` ✨ `ES6` (`Template Tag`)](#stringraw--es6-template-tag)
  - [J. ITERATION](#j-iteration)
    - [`[Symbol.iterator]()` ✨ `ES6`](#symboliterator--es6)
  - [PRACTICAL USE CASES](#practical-use-cases)
    - [`Email Validation`](#email-validation)
    - [`URL Slug Generator`](#url-slug-generator)
    - [`Capitalize Words`](#capitalize-words)
    - [`Truncate with Ellipsis`](#truncate-with-ellipsis)
    - [`Count Word Frequency`](#count-word-frequency)
  - [COMMON INTERVIEW TRAPS](#common-interview-traps)
    - [**Trap 1: Strings are mutable**](#trap-1-strings-are-mutable)
    - [**Trap 2: substr() is safe to use**](#trap-2-substr-is-safe-to-use)
    - [**Trap 3: substring(-1) works like slice(-1)**](#trap-3-substring-1-works-like-slice-1)
    - [**Trap 4: indexOf() returns boolean**](#trap-4-indexof-returns-boolean)
    - [**Trap 5: split("") works correctly with emoji**](#trap-5-split-works-correctly-with-emoji)
    - [**Trap 6: replace() replaces all matches**](#trap-6-replace-replaces-all-matches)
  - [PERFORMANCE TIPS](#performance-tips)
    - [**String Concatenation**](#string-concatenation)
  - [QUICK REFERENCE TABLE](#quick-reference-table)
  - [KEY TAKEAWAYS](#key-takeaways)

## A. CASE CONVERSION

### `toUpperCase()` / `toLowerCase()`

```js
const str = "Hello World";
console.log(str.toUpperCase()); // "HELLO WORLD"
console.log(str.toLowerCase()); // "hello world"
```

### `toLocaleUpperCase()` / `toLocaleLowerCase()`

```js
const turkish = "istanbul";
console.log(turkish.toLocaleUpperCase('tr-TR')); // "İSTANBUL"
```

---

## B. SEARCHING & CHECKING

### **indexOf(`searchValue`, `fromIndex`)**

```js
const text = "hello world hello";
console.log(text.indexOf("hello"));      // 0
console.log(text.indexOf("hello", 1));   // 12
console.log(text.indexOf("xyz"));        // -1
```

### **lastIndexOf(`searchValue`, `fromIndex`)**

```js
console.log(text.lastIndexOf("hello"));  // 12
```

### **includes(`searchString`, `position`)** ✨ `ES6`

```js
console.log("JavaScript".includes("Script")); // true
console.log("JavaScript".includes("script")); // false
console.log("JavaScript".includes("Java", 1)); // false
```

### **startsWith(`searchString`, `position`)** ✨ ES6

```js
const url = "https://example.com";
console.log(url.startsWith("https"));     // true
console.log(url.startsWith("example", 8)); // true
```

### **endsWith(`searchString`, `length`)** ✨ ES6

```js
const file = "image.png";
console.log(file.endsWith(".png"));       // true
console.log(file.endsWith("image", 5));   // true
```

### **search(`regexp`)**

```js
const text = "The year is 2025";
console.log(text.search(/\d+/));          // 12
console.log(text.search("year"));         // 4
```

### **match(`regexp`)**

```js
const str = "cat, bat, rat";
console.log(str.match(/[cbr]at/));        // ["cat"]
console.log(str.match(/[cbr]at/g));       // ["cat", "bat", "rat"]
```

### **matchAll(`regexp`)** ✨ `ES2020`

```js
const text = "test1 test2";
const matches = [...text.matchAll(/test(\d)/g)];
console.log(matches[0][1]); // "1"
console.log(matches[1][1]); // "2"
```

---

## C. EXTRACTING SUBSTRINGS

### **slice(`startIndex`, `endIndex`)**

```js
const str = "JavaScript";
console.log(str.slice(0, 4));    // "Java"
console.log(str.slice(4));       // "Script"
console.log(str.slice(-6));      // "Script"
console.log(str.slice(-6, -2));  // "Scri"
```

### **substring(`startIndex`, `endIndex`)**

```js
const str = "JavaScript";
console.log(str.substring(0, 4));  // "Java"
console.log(str.substring(4, 0));  // "Java" (swaps)
console.log(str.substring(-3));    // "JavaScript"
```

### **substr(`startIndex`, `length`)** ⚠️ `DEPRECATED`

```js
const str = "JavaScript";
console.log(str.substr(4, 6));   // "Script"
console.log(str.substr(-6, 4));  // "Scri"
```

### **charAt(`index`)**

```js
console.log("Hello"[0]);         // "H"
console.log("Hello".charAt(0));  // "H"
console.log("Hello".charAt(10)); // ""
```

### **charCodeAt(`index`)**

```js
console.log("A".charCodeAt(0));  // 65
console.log("😀".charCodeAt(0)); // 55357
```

### **codePointAt(`index`)** ✨ `ES6`

```js
console.log("😀".codePointAt(0)); // 128512
console.log("A".codePointAt(0));  // 65
```

### **at(`index`)** ✨ `ES2022`

```js
const str = "JavaScript";
console.log(str.at(0));   // "J"
console.log(str.at(-1));  // "t"
console.log(str.at(-6));  // "S"
```

---

## D. MODIFYING STRINGS

### **concat(...`strings`)**

```js
const s1 = "Hello";
const s2 = " World";
console.log(s1.concat(s2, "!"));  // "Hello World!"
// Better: use template literals
console.log(`${s1}${s2}!`);       // "Hello World!"
```

### **repeat(`count`)** ✨ `ES6`

```js
console.log("Ha".repeat(3));      // "HaHaHa"
console.log("─".repeat(10));      // "──────────"
```

### **padStart(`targetLength`, `padString`)** ✨ `ES2017`

```js
console.log("5".padStart(3, "0"));        // "005"
console.log("hello".padStart(10, "*"));   // "*****hello"
```

### **padEnd(`targetLength`, `padString`)** ✨ `ES2017`

```js
console.log("5".padEnd(3, "0"));          // "500"
console.log("hello".padEnd(10, "."));     // "hello....."
```

---

## E. TRIMMING

### `trim()` ✨ `ES5`

```js
const str = "  hello  ";
console.log(str.trim());          // "hello"
```

### `trimStart()` / `trimLeft()` ✨ `ES2019`

```js
console.log("  hello  ".trimStart()); // "hello  "
```

### `trimEnd`() / `trimRight`() ✨ `ES2019`

```js
console.log("  hello  ".trimEnd());   // "  hello"
```

---

## F. REPLACING

### **replace(`search`, `replacement`)**

```js
const str = "cat cat cat";
console.log(str.replace("cat", "dog"));       // "dog cat cat"
console.log(str.replace(/cat/g, "dog"));      // "dog dog dog"
```

### **replaceAll(`search`, `replacement`)** ✨ `ES2021`

```js
const str = "cat cat cat";
console.log(str.replaceAll("cat", "dog"));    // "dog dog dog"
```

### `With callback`

```js
const str = "hello world";
const result = str.replace(/\w+/g, match => match.toUpperCase());
console.log(result); // "HELLO WORLD"
```

---

## G. SPLITTING

### **split(`separator`, `limit`)**

```js
const csv = "apple,banana,cherry";
console.log(csv.split(","));              // ["apple", "banana", "cherry"]
console.log(csv.split(",", 2));           // ["apple", "banana"]
console.log("hello".split(""));           // ["h", "e", "l", "l", "o"]

const text = "a-b-c";
console.log(text.split(/-/));             // ["a", "b", "c"]
```

---

## H. LOCALIZATION

### **localeCompare(`compareString`, `locales`, `options`)**

```js
const a = "ä";
const b = "z";
console.log(a.localeCompare(b, 'de'));    // -1 (ä < z in German)
console.log(a.localeCompare(b, 'sv'));    // 1 (ä > z in Swedish)

// Numeric sorting
console.log("2".localeCompare("10"));                    // 1
console.log("2".localeCompare("10", undefined, {numeric: true})); // -1
```

---

## I. CONVERSION (STATIC METHODS)

### **String.fromCharCode(...`codes`)**

```js
console.log(String.fromCharCode(72, 105)); // "Hi"
```

### **String.fromCodePoint(...`codes`)** ✨ `ES6`

```js
console.log(String.fromCodePoint(128512)); // "😀"
```

### `String.raw` ✨ `ES6` (`Template Tag`)

```js
console.log(String.raw`C:\new\folder`);    // "C:\new\folder"
console.log(`C:\new\folder`);              // "C:
ew\folder"
```

---

## J. ITERATION

### `[Symbol.iterator]()` ✨ `ES6`

```js
const str = "hi";
for (const char of str) {
  console.log(char); // "h", "i"
}

const iterator = str[Symbol.iterator]();
console.log(iterator.next()); // {value: "h", done: false}
console.log(iterator.next()); // {value: "i", done: false}
console.log(iterator.next()); // {value: undefined, done: true}
```

---

## PRACTICAL USE CASES

### `Email Validation`

```js
const isValidEmail = email => /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
console.log(isValidEmail("test@example.com")); // true
```

### `URL Slug Generator`

```js
const createSlug = str => str
  .toLowerCase()
  .trim()
  .replace(/[^\w\s-]/g, '')
  .replace(/[\s_-]+/g, '-')
  .replace(/^-+|-+$/g, '');

console.log(createSlug("Hello World! 123")); // "hello-world-123"
```

### `Capitalize Words`

```js
const capitalize = str => str
  .split(' ')
  .map(word => word.charAt(0).toUpperCase() + word.slice(1))
  .join(' ');

console.log(capitalize("hello world")); // "Hello World"
```

### `Truncate with Ellipsis`

```js
const truncate = (str, maxLen) => 
  str.length > maxLen ? str.slice(0, maxLen - 3) + '...' : str;

console.log(truncate("This is a long text", 10)); // "This is..."
```

### `Count Word Frequency`

```js
const wordFrequency = str => {
  const words = str.toLowerCase().match(/\b\w+\b/g);
  return words.reduce((acc, word) => {
    acc[word] = (acc[word] || 0) + 1;
    return acc;
  }, {});
};

console.log(wordFrequency("hello world hello")); 
// { hello: 2, world: 1 }
```

---

## COMMON INTERVIEW TRAPS

### **Trap 1: Strings are mutable**

- ❌ `WRONG`: Strings can be modified in place

- ✅ `CORRECT`: Strings are immutable – methods return new strings

### **Trap 2: substr() is safe to use**

- ❌ `WRONG`: substr() is recommended

- ✅ `CORRECT`: substr() is deprecated – use slice() or substring()

### **Trap 3: substring(-1) works like slice(-1)**

- ❌ `WRONG`: substring(-1) returns last character

- ✅ `CORRECT`: substring() treats negative as 0

### **Trap 4: indexOf() returns boolean**

- ❌ `WRONG`: indexOf() returns true/false

- ✅ `CORRECT`: indexOf() returns number (-1 if not found)

### **Trap 5: split("") works correctly with emoji**

- ❌ `WRONG`: "😀".split("") returns ["😀"]

- ✅ `CORRECT`: Use Array.from() or [...str] for Unicode

### **Trap 6: replace() replaces all matches**

- ❌ `WRONG`: str.replace("cat", "dog") replaces all "cat"

- ✅ `CORRECT`: Only replaces first – use replaceAll() or /g flag

---

## PERFORMANCE TIPS

### **String Concatenation**

```js
// ❌ SLOW (creates many intermediate strings)
let result = "";
for (let i = 0; i < 1000; i++) {
  result += "text";
}

// ✅ FAST (uses array join)
const parts = [];
for (let i = 0; i < 1000; i++) {
  parts.push("text");
}
const result = parts.join("");

// ✅ EVEN BETTER (uses repeat)
const result = "text".repeat(1000);
```

---

## QUICK REFERENCE TABLE

| Method           | Returns    | Mutates? | ES Version |
| ---------------- | ---------- | -------- | ---------- |
| toLowerCase()    | string     | No       | ES1        |
| toUpperCase()    | string     | No       | ES1        |
| charAt(i)        | string     | No       | ES1        |
| charCodeAt(i)    | number     | No       | ES1        |
| indexOf(s)       | number     | No       | ES1        |
| lastIndexOf(s)   | number     | No       | ES1        |
| slice(s, e)      | string     | No       | ES3        |
| substring(s, e)  | string     | No       | ES1        |
| split(sep)       | array      | No       | ES1        |
| concat(...s)     | string     | No       | ES1        |
| replace(s, r)    | string     | No       | ES1        |
| search(re)       | number     | No       | ES3        |
| match(re)        | array/null | No       | ES3        |
| trim()           | string     | No       | ES5        |
| repeat(n)        | string     | No       | ES6        |
| startsWith(s)    | boolean    | No       | ES6        |
| endsWith(s)      | boolean    | No       | ES6        |
| includes(s)      | boolean    | No       | ES6        |
| padStart(l, s)   | string     | No       | ES2017     |
| padEnd(l, s)     | string     | No       | ES2017     |
| trimStart()      | string     | No       | ES2019     |
| trimEnd()        | string     | No       | ES2019     |
| matchAll(re)     | iterator   | No       | ES2020     |
| replaceAll(s, r) | string     | No       | ES2021     |
| at(i)            | string     | No       | ES2022     |

---

## KEY TAKEAWAYS

1. **Strings are immutable** – all methods return new strings
2. **Use modern methods** – includes(), startsWith(), endsWith() over indexOf()
3. **Performance matters** – avoid string concatenation in loops
4. **Unicode awareness** – use codePointAt(), Array.from() for emoji/symbols
5. **Regex is powerful** – combine with match(), replace(), search()
6. **Template literals** – preferred over concat() for readability
7. **Deprecated methods** – avoid substr(), use slice() instead

---

**Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/) by Santosh Kumar Baliarsingh**
