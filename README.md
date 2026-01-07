<!-- markdownlint-disable MD036 -->
<!-- markdownlint-disable MD060 -->
<!-- markdownlint-disable MD050 -->
<!-- markdownlint-disable MD024 -->

# JavaScript Regular Expressions (Regex)

## Table of Contents

- [JavaScript Regular Expressions (Regex)](#javascript-regular-expressions-regex)
  - [Table of Contents](#table-of-contents)
  - [Initial Understanding](#initial-understanding)
  - [Beginner Explanation](#beginner-explanation)
  - [Advanced Explanation](#advanced-explanation)
    - [**Internal Regex Engine Behavior:**](#internal-regex-engine-behavior)
    - [**Performance Considerations:**](#performance-considerations)
  - [Complete Regex Character Reference](#complete-regex-character-reference)
    - [**Basic Characters**](#basic-characters)
    - [**Character Classes**](#character-classes)
    - [**Predefined Character Classes (Shortcuts)**](#predefined-character-classes-shortcuts)
    - [**Quantifiers (How Many Times)**](#quantifiers-how-many-times)
    - [**Greedy vs Lazy Quantifiers**](#greedy-vs-lazy-quantifiers)
    - [**Anchors (Position Matching)**](#anchors-position-matching)
    - [**Groups and Capturing**](#groups-and-capturing)
    - [**Lookahead and Lookbehind (Zero-Width Assertions)**](#lookahead-and-lookbehind-zero-width-assertions)
    - [**Flags**](#flags)
  - [Common Regex Patterns (Interview Favorites)](#common-regex-patterns-interview-favorites)
    - [**Email Validation**](#email-validation)
    - [**Phone Number (US Format)**](#phone-number-us-format)
    - [**URL Validation**](#url-validation)
    - [**Password Strength (min 8 chars, 1 uppercase, 1 digit)**](#password-strength-min-8-chars-1-uppercase-1-digit)
    - [**Extract All Words**](#extract-all-words)
    - [**Remove Extra Spaces**](#remove-extra-spaces)
    - [**Validate Hex Color**](#validate-hex-color)
  - [Common Interview Traps](#common-interview-traps)
    - [**Trap 1: Forgetting to escape special characters**](#trap-1-forgetting-to-escape-special-characters)
    - [**Trap 2: Greedy matching in HTML/XML**](#trap-2-greedy-matching-in-htmlxml)
    - [**Trap 3: Not using global flag for `replace`**](#trap-3-not-using-global-flag-for-replace)
    - [**Trap 4: Anchors with global flag**](#trap-4-anchors-with-global-flag)
    - [**Trap 5: Catastrophic backtracking**](#trap-5-catastrophic-backtracking)
  - [Real-World Use Cases](#real-world-use-cases)
    - [**1. Form Validation**](#1-form-validation)
    - [**2. URL Slug Generation**](#2-url-slug-generation)
    - [**3. Syntax Highlighting**](#3-syntax-highlighting)
    - [**4. Credit Card Masking**](#4-credit-card-masking)
  - [Quick Reference Card](#quick-reference-card)
  - [Practice Problems](#practice-problems)
  - [Additional Resources](#additional-resources)

## Initial Understanding

**Regular Expressions (Regex)** are patterns used to match, search, replace, and validate strings in JavaScript. They're powerful tools for text processing and are essential in form validation, parsing, and string manipulation.

**A regex in JavaScript can be created in two ways:**

```js
const regex1 = /pattern/flags;           // Literal notation
const regex2 = new RegExp('pattern', 'flags'); // Constructor
```

---

## Beginner Explanation

Think of regex as a **search recipe** for strings. Instead of looking for exact text like `"hello"`, you can search for:

- "Any 3 digits" → `/\d{3}/`
- "Email format" → `/\w+@\w+\.\w+/`
- "Words starting with 'A'" → `/\bA\w*/g`

**Basic Example:**

```js
const text = "My phone is 123-456-7890";
const hasPhone = /\d{3}-\d{3}-\d{4}/.test(text);
console.log(hasPhone); // true
```

---

## Advanced Explanation

### **Internal Regex Engine Behavior:**

1. **Compilation**: When you create a regex, the JS engine compiles it into a state machine
2. **Matching**: The engine walks through the string character by character
3. **Backtracking**: If a pattern fails, the engine backtracks and tries alternative paths
4. **Flags**: Modify the behavior globally (g), case-insensitively (i), multiline (m), etc.

### **Performance Considerations:**

- Catastrophic backtracking can occur with nested quantifiers like `/(a+)+b/`
- Lookaheads/lookbehinds are non-capturing but still evaluate
- Anchors (`^`, `$`) make patterns more efficient by limiting search space

---

## Complete Regex Character Reference

### **Basic Characters**

| Character | Meaning                               | Example                             |
| --------- | ------------------------------------- | ----------------------------------- |
| `.`       | Any single character (except newline) | `/a.c/` matches "abc", "a2c"        |
| `\`       | Escape special character              | `/\./` matches literal "."          |
| `\|`      | OR operator                           | `/cat\|dog/` matches "cat" or "dog" |

```js
/a.c/.test("abc");     // true
/a.c/.test("a\nc");    // false (. doesn't match \n by default)
/cat|dog/.test("dog"); // true
```

---

### **Character Classes**

| Pattern  | Meaning                       | Example                       |
| -------- | ----------------------------- | ----------------------------- |
| `[abc]`  | Match any one of a, b, c      | `/[aeiou]/` matches any vowel |
| `[^abc]` | Match anything EXCEPT a, b, c | `/[^0-9]/` matches non-digits |
| `[a-z]`  | Range: lowercase letters      | `/[a-z]/` matches "a" to "z"  |
| `[A-Z]`  | Range: uppercase letters      | `/[A-Z]/` matches "A" to "Z"  |
| `[0-9]`  | Range: digits                 | `/[0-9]/` matches "0" to "9"  |

```js
"hello".match(/[aeiou]/g);    // ["e", "o"]
"a1b2c3".match(/[^0-9]/g);    // ["a", "b", "c"]
"Test123".match(/[A-Z]/g);    // ["T"]
```

---

### **Predefined Character Classes (Shortcuts)**

| Pattern | Meaning            | Equivalent       | Example                         |
| ------- | ------------------ | ---------------- | ------------------------------- |
| `\d`    | Digit              | `[0-9]`          | `/\d/` matches "5"              |
| `\D`    | Non-digit          | `[^0-9]`         | `/\D/` matches "a"              |
| `\w`    | Word character     | `[A-Za-z0-9_]`   | `/\w/` matches "a", "5", "_"    |
| `\W`    | Non-word character | `[^A-Za-z0-9_]`  | `/\W/` matches "@", " "         |
| `\s`    | Whitespace         | `[ \t\n\r\f\v]`  | `/\s/` matches space, tab       |
| `\S`    | Non-whitespace     | `[^ \t\n\r\f\v]` | `/\S/` matches any visible char |

```js
"Hello 123".match(/\d/g);     // ["1", "2", "3"]
"Hello 123".match(/\D/g);     // ["H", "e", "l", "l", "o", " "]
"user_name".match(/\w/g);     // ["u","s","e","r","_","n","a","m","e"]
"a b c".match(/\s/g);         // [" ", " "]
```

---

### **Quantifiers (How Many Times)**

| Pattern | Meaning               | Example                                  |
| ------- | --------------------- | ---------------------------------------- |
| `*`     | 0 or more             | `/a*/` matches "", "a", "aa", "aaa"      |
| `+`     | 1 or more             | `/a+/` matches "a", "aa", "aaa" (not "") |
| `?`     | 0 or 1 (optional)     | `/colou?r/` matches "color" or "colour"  |
| `{n}`   | Exactly n times       | `/\d{3}/` matches "123"                  |
| `{n,}`  | n or more times       | `/\d{2,}/` matches "12", "123", "1234"   |
| `{n,m}` | Between n and m times | `/\d{2,4}/` matches "12", "123", "1234"  |

```js
"aaa".match(/a*/);      // ["aaa"]
"".match(/a*/);         // [""] (0 matches)
"aaa".match(/a+/);      // ["aaa"]
"".match(/a+/);         // null (requires at least 1)

"color".match(/colou?r/);  // ["color"]
"colour".match(/colou?r/); // ["colour"]

"12345".match(/\d{3}/);    // ["123"]
"12345".match(/\d{2,4}/);  // ["1234"]
```

---

### **Greedy vs Lazy Quantifiers**

| Pattern | Behavior                             | Example                                                  |
| ------- | ------------------------------------ | -------------------------------------------------------- |
| `*`     | Greedy (matches as much as possible) | `/<.*>/` in `"<p>text</p>"` matches full `"<p>text</p>"` |
| `*?`    | Lazy (matches as little as possible) | `/<.*?>/` matches `"<p>"` only                           |
| `+?`    | Lazy 1 or more                       | `/\d+?/` in "123" matches "1"                            |
| `??`    | Lazy 0 or 1                          | `/a??/` matches "" first                                 |

```js
"<p>Hello</p>".match(/<.*>/);   // ["<p>Hello</p>"] (greedy)
"<p>Hello</p>".match(/<.*?>/);  // ["<p>"] (lazy)

"123".match(/\d+/);   // ["123"] (greedy)
"123".match(/\d+?/);  // ["1"] (lazy)
```

---

### **Anchors (Position Matching)**

| Pattern | Meaning              | Example                                                |
| ------- | -------------------- | ------------------------------------------------------ |
| `^`     | Start of string/line | `/^Hello/` matches "Hello world" but not "Say Hello"   |
| `$`     | End of string/line   | `/world$/` matches "Hello world" but not "world peace" |
| `\b`    | Word boundary        | `/\bcat\b/` matches "cat" but not "catch"              |
| `\B`    | Non-word boundary    | `/\Bcat\B/` matches "scat" in "scatter"                |

```js
/^Hello/.test("Hello world");  // true
/^Hello/.test("Say Hello");    // false

/world$/.test("Hello world");  // true
/world$/.test("world peace");  // false

/\bcat\b/.test("cat");         // true
/\bcat\b/.test("catch");       // false
/\bcat\b/.test("the cat runs");// true
```

---

### **Groups and Capturing**

| Pattern     | Meaning                         | Example                                            |
| ----------- | ------------------------------- | -------------------------------------------------- |
| `(abc)`     | Capturing group                 | `/(foo)(bar)/` captures "foo" and "bar" separately |
| `(?:abc)`   | Non-capturing group             | `/(?:foo)(bar)/` only captures "bar"               |
| `\1`, `\2`  | Backreference to captured group | `/(\w)\1/` matches "aa", "bb" (repeated char)      |
| `(?<name>)` | Named capturing group           | `/(?<year>\d{4})/`                                 |

```js
const match = "John Doe".match(/(\w+) (\w+)/);
console.log(match[1]); // "John"
console.log(match[2]); // "Doe"

// Backreference example
/(\w)\1/.test("book");  // true (matches "oo")
/(\w)\1/.test("chat");  // false

// Named groups
const {groups} = "2024-01-15".match(/(?<year>\d{4})-(?<month>\d{2})-(?<day>\d{2})/);
console.log(groups.year);  // "2024"
console.log(groups.month); // "01"
```

---

### **Lookahead and Lookbehind (Zero-Width Assertions)**

| Pattern    | Meaning             | Example                                               |
| ---------- | ------------------- | ----------------------------------------------------- |
| `(?=...)`  | Positive lookahead  | `/\d(?=px)/` matches "5" in "5px" but not "5em"       |
| `(?!...)`  | Negative lookahead  | `/\d(?!px)/` matches "5" in "5em" but not "5px"       |
| `(?<=...)` | Positive lookbehind | `/(?<=\$)\d+/` matches "100" in "$100"                |
| `(?<!...)` | Negative lookbehind | `/(?<!\$)\d+/` matches "100" in "€100" but not "$100" |

```js
// Lookahead
"5px 10em".match(/\d+(?=px)/g);    // ["5"]
"5px 10em".match(/\d+(?!px)/g);    // ["10"]

// Lookbehind
"$100 €200".match(/(?<=\$)\d+/g);  // ["100"]
"$100 €200".match(/(?<!\$)\d+/g);  // ["200"]
```

---

### **Flags**

| Flag | Meaning                               | Example                               |
| ---- | ------------------------------------- | ------------------------------------- |
| `g`  | Global (find all matches)             | `/a/g` finds all "a" in string        |
| `i`  | Case-insensitive                      | `/hello/i` matches "Hello", "HELLO"   |
| `m`  | Multiline (^ and $ match line breaks) | `/^test/m` matches "test" on any line |
| `s`  | Dotall (. matches newline)            | `/a.b/s` matches "a\nb"               |
| `u`  | Unicode                               | `/\u{1F600}/u` matches 😀              |
| `y`  | Sticky (matches from lastIndex)       | Used with `regex.lastIndex`           |

```js
"aaa".match(/a/);    // ["a"] (first match only)
"aaa".match(/a/g);   // ["a", "a", "a"] (all matches)

/hello/i.test("HELLO");   // true

const text = "line1\nline2\nline3";
text.match(/^line/gm);    // ["line", "line", "line"]

/a.b/.test("a\nb");       // false
/a.b/s.test("a\nb");      // true
```

---

## Common Regex Patterns (Interview Favorites)

### **Email Validation**

```js
const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
emailRegex.test("user@example.com");  // true
emailRegex.test("invalid.email");     // false
```

### **Phone Number (US Format)**

```js
const phoneRegex = /^\d{3}-\d{3}-\d{4}$/;
phoneRegex.test("123-456-7890");  // true
```

### **URL Validation**

```js
const urlRegex = /^https?:\/\/(www\.)?[-a-zA-Z0-9@:%._\+~#=]{1,256}\.[a-zA-Z0-9()]{1,6}\b/;
urlRegex.test("https://example.com");  // true
```

### **Password Strength (min 8 chars, 1 uppercase, 1 digit)**

```js
const passwordRegex = /^(?=.*[A-Z])(?=.*\d).{8,}$/;
passwordRegex.test("Pass1234");  // true
passwordRegex.test("weak");      // false
```

### **Extract All Words**

```js
"Hello, world! 123".match(/\b\w+\b/g);  // ["Hello", "world", "123"]
```

### **Remove Extra Spaces**

```js
"Hello    world".replace(/\s+/g, " ");  // "Hello world"
```

### **Validate Hex Color**

```js
const hexRegex = /^#([A-Fa-f0-9]{6}|[A-Fa-f0-9]{3})$/;
hexRegex.test("#FFF");      // true
hexRegex.test("#123456");   // true
hexRegex.test("#GGGGGG");   // false
```

---

## Common Interview Traps

### **Trap 1: Forgetting to escape special characters**

```js
// Wrong
/user.name/.test("user@name");  // true (. matches any char)

// Correct
/user\.name/.test("user@name"); // false
```

### **Trap 2: Greedy matching in HTML/XML**

```js
"<p>Text</p>".match(/<.*>/);   // ["<p>Text</p>"] (wrong - too greedy)
"<p>Text</p>".match(/<.*?>/);  // ["<p>"] (correct - lazy)
```

### **Trap 3: Not using global flag for `replace`**

```js
"aaa".replace(/a/, "b");   // "baa" (replaces first only)
"aaa".replace(/a/g, "b");  // "bbb" (replaces all)
```

### **Trap 4: Anchors with global flag**

```js
/^test/g.test("test");  // true (first call)
/^test/g.test("test");  // false (second call - lastIndex issue)
```

### **Trap 5: Catastrophic backtracking**

```js
// This regex can hang on long strings!
/(a+)+b/.test("aaaaaaaaaaaaaaaaaaaaaaaac");

// Better:
/a+b/.test("aaaaaaaaaaaaaaaaaaaaaaaac");
```

---

## Real-World Use Cases

### **1. Form Validation**

```js
function validateForm(email, phone) {
  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  const phoneRegex = /^\d{3}-\d{3}-\d{4}$/;
  
  return emailRegex.test(email) && phoneRegex.test(phone);
}
```

### **2. URL Slug Generation**

```js
function createSlug(title) {
  return title
    .toLowerCase()
    .replace(/[^\w\s-]/g, '')  // Remove special chars
    .replace(/\s+/g, '-')      // Replace spaces with hyphens
    .replace(/-+/g, '-');      // Remove duplicate hyphens
}

createSlug("Hello World! 2024");  // "hello-world-2024"
```

### **3. Syntax Highlighting**

```js
function highlightCode(code) {
  return code
    .replace(/\b(function|const|let|var)\b/g, '<span class="keyword">$1</span>')
    .replace(/\b\d+\b/g, '<span class="number">$&</span>');
}
```

### **4. Credit Card Masking**

```js
function maskCard(cardNumber) {
  return cardNumber.replace(/\d(?=\d{4})/g, "*");
}

maskCard("1234567812345678");  // "************5678"
```

---

## Quick Reference Card

```js
// Character classes
\d = digit        \D = non-digit
\w = word char    \W = non-word
\s = whitespace   \S = non-whitespace

// Quantifiers
* = 0+     + = 1+     ? = 0 or 1
{n} = exactly n    {n,m} = n to m

// Anchors
^ = start    $ = end    \b = word boundary

// Groups
(...) = capture    (?:...) = non-capture
\1 = backreference

// Lookaround
(?=...) = lookahead      (?!...) = negative lookahead
(?<=...) = lookbehind    (?<!...) = negative lookbehind

// Flags
g = global    i = case-insensitive    m = multiline
s = dotall    u = unicode             y = sticky
```

---

## Practice Problems

1. **Validate date format (YYYY-MM-DD)**
2. **Extract hashtags from a tweet**
3. **Replace all URLs with clickable links**
4. **Validate IPv4 address**
5. **Count vowels in a string**

---

## Additional Resources

- [MDN Regex Guide](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions)
- [Regex101](https://regex101.com/) - `Test and debug regex patterns`
- [RegExr](https://regexr.com/) - `Learn, build & test regex`

---
