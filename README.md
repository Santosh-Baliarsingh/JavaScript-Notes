<!-- markdownlint-disable MD036 -->
<!-- markdownlint-disable MD060 -->
<!-- markdownlint-disable MD050 -->
<!-- markdownlint-disable MD024 -->

# **JavaScript Modules**

---

## **Table Of Contents**

- [**JavaScript Modules**](#javascript-modules)
  - [**Table Of Contents**](#table-of-contents)
  - [**Initial Understanding**](#initial-understanding)
  - [**Beginner Explanation**](#beginner-explanation)
  - [**Advanced Explanation**](#advanced-explanation)
    - [Module System Evolution](#module-system-evolution)
    - [How Modules Work Internally](#how-modules-work-internally)
  - [**Code Examples**](#code-examples)
    - [Basic Export \& Import](#basic-export--import)
      - [***Named Exports***](#named-exports)
      - [***Default Export***](#default-export)
      - [***Mixed Exports***](#mixed-exports)
    - [**Import Variations**](#import-variations)
    - [**Re-exporting (Barrel Pattern)**](#re-exporting-barrel-pattern)
    - [**Dynamic Imports (Code Splitting)**](#dynamic-imports-code-splitting)
  - [**Visual Diagrams (Text-Based)**](#visual-diagrams-text-based)
    - [Module Loading Flow](#module-loading-flow)
    - [Module Scope vs Global Scope](#module-scope-vs-global-scope)
    - [Module Caching Example](#module-caching-example)
  - [**CommonJS vs ES Modules**](#commonjs-vs-es-modules)
    - [CommonJS (Node.js)](#commonjs-nodejs)
    - [ES Modules](#es-modules)
    - [Live Binding Example](#live-binding-example)
  - [**Browser Usage**](#browser-usage)
    - [HTML with ES Modules](#html-with-es-modules)
    - [Inline Module](#inline-module)
  - [**Common Interview Traps**](#common-interview-traps)
    - [Trap 1: Forgetting File Extensions](#trap-1-forgetting-file-extensions)
    - [Trap 2: Circular Dependencies](#trap-2-circular-dependencies)
    - [Trap 3: Default Export Confusion](#trap-3-default-export-confusion)
    - [Trap 4: Mutating Imports](#trap-4-mutating-imports)
  - [**Real-World Use Cases**](#real-world-use-cases)
    - [1. Code Splitting (Performance)](#1-code-splitting-performance)
    - [2. Feature Flags](#2-feature-flags)
    - [3. Tree-Shaking (Webpack/Vite)](#3-tree-shaking-webpackvite)
    - [4. Microfrontends](#4-microfrontends)
  - [**Interview Checklist**](#interview-checklist)
  - [**Final Pro Tips**](#final-pro-tips)

## **Initial Understanding**

**JavaScript Modules** are a way to split code into reusable, maintainable, and encapsulated pieces. Each module has its own scope and explicitly exports/imports functionality.

**Key Benefits:**

- Code organization & reusability
- Namespace isolation (no global pollution)
- Dependency management
- Lazy loading & tree-shaking (in bundlers)

---

## **Beginner Explanation**

Think of a module as a **JavaScript file that exports specific functions, objects, or values** so other files can import and use them.

**Without modules:**

```js
// everything is global
var userName = "Alice";
function greet() { console.log("Hi"); }
```

**With modules:**

```js
// math.js
export function add(a, b) { return a + b; }

// app.js
import { add } from './math.js';
console.log(add(2, 3)); // 5
```

Each file has **its own scope**. Variables/functions aren't global unless explicitly exported.

---

## **Advanced Explanation**

### Module System Evolution

| Type                 | Environment       | Syntax                         | Loading      |
| -------------------- | ----------------- | ------------------------------ | ------------ |
| **CommonJS**         | Node.js           | `require()` / `module.exports` | Synchronous  |
| **ES Modules (ESM)** | Browser + Node.js | `import` / `export`            | Asynchronous |
| **AMD**              | Browser (legacy)  | `define()` / `require()`       | Asynchronous |
| **UMD**              | Universal         | Combines CommonJS + AMD        | Both         |

**Modern Standard:** ES Modules (ESM) — officially part of ECMAScript since ES6 (2015).

---

### How Modules Work Internally

When you use ES modules:

1. **Parse Phase**: Module is parsed (not executed yet)
2. **Module Resolution**: Browser/Node resolves import paths
3. **Module Loading**: Fetches the module file
4. **Module Linking**: Connects imports/exports
5. **Module Evaluation**: Code executes (once per module — **cached**)

**Key Internal Behavior:**

- Modules are **singletons** — evaluated once, cached thereafter
- `import` statements are **hoisted** (processed before code runs)
- Modules run in **strict mode** by default
- Top-level `this` is `undefined` (not `window`)

---

## **Code Examples**

### Basic Export & Import

#### ***Named Exports***

```js
// utils.js
export const PI = 3.14;
export function square(x) { return x * x; }
export class Calculator {
  add(a, b) { return a + b; }
}
```

```js
// app.js
import { PI, square, Calculator } from './utils.js';
console.log(PI); // 3.14
console.log(square(5)); // 25
const calc = new Calculator();
console.log(calc.add(2, 3)); // 5
```

---

#### ***Default Export***

```js
// logger.js
export default function log(msg) {
  console.log(`[LOG]: ${msg}`);
}
```

```js
// app.js
import log from './logger.js'; // can name it anything
log("Hello"); // [LOG]: Hello
```

---

#### ***Mixed Exports***

```js
// config.js
export const API_URL = "https://api.example.com";
export default { timeout: 5000, retries: 3 };
```

```js
// app.js
import config, { API_URL } from './config.js';
console.log(config.timeout); // 5000
console.log(API_URL); // https://api.example.com
```

---

### **Import Variations**

```js
// Import everything as namespace
import * as Utils from './utils.js';
console.log(Utils.PI);
console.log(Utils.square(4));

// Rename imports
import { square as sq } from './utils.js';
console.log(sq(3)); // 9

// Import for side effects only (runs code, doesn't import anything)
import './polyfill.js';

// Dynamic import (async, returns Promise)
const module = await import('./heavy-module.js');
module.doSomething();
```

---

### **Re-exporting (Barrel Pattern)**

```js
// utils/index.js (barrel file)
export { add, subtract } from './math.js';
export { debounce, throttle } from './timing.js';
export { default as logger } from './logger.js';
```

```js
// app.js
import { add, debounce, logger } from './utils/index.js';
```

---

### **Dynamic Imports (Code Splitting)**

```js
// Load module conditionally
async function loadFeature() {
  if (user.isPremium) {
    const premium = await import('./premium-features.js');
    premium.activate();
  }
}

// With error handling
import('./module.js')
  .then(module => module.init())
  .catch(err => console.error("Module load failed", err));
```

---

## **Visual Diagrams (Text-Based)**

### Module Loading Flow

```text
┌─────────────────────────────────────────────┐
│          Browser / Node.js Engine           │
└─────────────────────────────────────────────┘
                    │
          ┌─────────▼─────────┐
          │  Import Statement │
          │  import { x }     │
          └─────────┬─────────┘
                    │
          ┌─────────▼─────────┐
          │  Module Resolution│
          │  Find file path   │
          └─────────┬─────────┘
                    │
          ┌─────────▼─────────┐
          │   Fetch Module    │
          │   (HTTP/File)     │
          └─────────┬─────────┘
                    │
          ┌─────────▼─────────┐
          │   Parse Module    │
          │   (Syntax check)  │
          └─────────┬─────────┘
                    │
          ┌─────────▼─────────┐
          │  Link Imports     │
          │  Connect exports  │
          └─────────┬─────────┘
                    │
          ┌─────────▼─────────┐
          │  Execute Once     │
          │  (Cached forever) │
          └───────────────────┘
```

---

### Module Scope vs Global Scope

```text
┌──────────────────────────────────────────┐
│           Global Scope (Old Way)         │
│  ┌──────────────────────────────────┐    │
│  │  var x = 10;                     │    │
│  │  function foo() {}               │    │
│  │  // Accessible everywhere!       │    │
│  └──────────────────────────────────┘    │
└──────────────────────────────────────────┘

┌──────────────────────────────────────────┐
│         Module Scope (Modern Way)        │
│  ┌──────────────┐  ┌──────────────┐      │
│  │  Module A    │  │  Module B    │      │
│  │ let x = 10;  │  │ let x = 20;  │      │
│  │ export x;    │  │ export x;    │      │
│  └──────────────┘  └──────────────┘      │
│         │                  │             │
│         └──────┬───────────┘             │
│                │                         │
│         ┌──────▼──────┐                  │
│         │   App.js    │                  │
│         │ import both │                  │
│         └─────────────┘                  │
└──────────────────────────────────────────┘
```

---

### Module Caching Example

```js
// counter.js
let count = 0;
export function increment() { count++; }
export function getCount() { return count; }
```

```js
// moduleA.js
import { increment, getCount } from './counter.js';
increment();
console.log(getCount()); // 1

// moduleB.js
import { increment, getCount } from './counter.js';
increment();
console.log(getCount()); // 2 (same instance!)
```

**Diagram:**

```text
┌─────────────────────────────────────────┐
│         Module Cache (Singleton)        │
│  ┌───────────────────────────────────┐  │
│  │  counter.js (loaded once)         │  │
│  │  count = 2                        │  │
│  └───────────────────────────────────┘  │
│             ▲           ▲               │
│             │           │               │
│       ┌─────┴─────┐ ┌──-┴─────-┐        │
│       │ moduleA.js│ │moduleB.js│        │
│       └───────────┘ └──────────┘        │
└─────────────────────────────────────────┘
```

---

## **CommonJS vs ES Modules**

### CommonJS (Node.js)

```js
// math.js
function add(a, b) { return a + b; }
module.exports = { add };

// app.js
const math = require('./math');
console.log(math.add(2, 3)); // 5
```

**Characteristics:**

- Synchronous loading
- \`require()\` can be called anywhere (dynamic)
- Exports are **value copies** (not live bindings)
- Default in Node.js (\`.js\` files)

---

### ES Modules

```js
// math.js
export function add(a, b) { return a + b; }

// app.js
import { add } from './math.js';
console.log(add(2, 3)); // 5
```

**Characteristics:**

- Asynchronous loading (non-blocking)
- \`import\` statements are **hoisted** and static
- Exports are **live bindings** (changes reflect)
- Use \`.mjs\` extension or \`"type": "module"\` in \`package.json\`

---

### Live Binding Example

```js
// counter.js (ES Module)
export let count = 0;
export function increment() { count++; }

// app.js
import { count, increment } from './counter.js';
console.log(count); // 0
increment();
console.log(count); // 1 ✅ Live binding!
```

```js
// counter.js (CommonJS)
let count = 0;
module.exports = { count, increment: () => count++ };

// app.js
const { count, increment } = require('./counter');
console.log(count); // 0
increment();
console.log(count); // 0 ❌ Copy, not live!
```

---

## **Browser Usage**

### HTML with ES Modules

```html
<!DOCTYPE html>
<html>
<head>
  <script type="module" src="app.js"></script>
</head>
<body>
  <!-- Module script runs in defer mode automatically -->
</body>
</html>
```

```js
// app.js
import { greet } from './utils.js';
greet("World");
```

**Key Points:**

- Must use `type="module"` in `<script>` tag
- Modules are **deferred** by default (non-blocking)
- Modules must be served with correct MIME type (`text/javascript`)
- File extensions **required** in browser (`.js`)

---

### Inline Module

```html
<script type="module">
  import { add } from './math.js';
  console.log(add(2, 3));
</script>
```

---

## **Common Interview Traps**

### Trap 1: Forgetting File Extensions

```js
// ❌ Error in browser
import { foo } from './module';

// ✅ Correct
import { foo } from './module.js';
```

---

### Trap 2: Circular Dependencies

```js
// a.js
import { b } from './b.js';
export const a = "A";
console.log(b);

// b.js
import { a } from './a.js';
export const b = "B";
console.log(a); // ⚠️ undefined (not yet initialized)
```

**Solution:** Use dynamic imports or restructure code.

---

### Trap 3: Default Export Confusion

```js
// utils.js
export default function log() {}
export function warn() {}

// ❌ Wrong
import { log } from './utils.js'; // undefined

// ✅ Correct
import log, { warn } from './utils.js';
```

---

### Trap 4: Mutating Imports

```js
// config.js
export const settings = { theme: 'dark' };

// app.js
import { settings } from './config.js';
settings.theme = 'light'; // ⚠️ Mutates original! (objects are references)
```

---

## **Real-World Use Cases**

### 1. Code Splitting (Performance)

```js
// Load heavy chart library only when needed
document.getElementById('show-chart').addEventListener('click', async () => {
  const { renderChart } = await import('./chart.js');
  renderChart(data);
});
```

---

### 2. Feature Flags

```js
async function loadFeatures() {
  if (user.hasAccess('analytics')) {
    const analytics = await import('./analytics.js');
    analytics.init();
  }
}
```

---

### 3. Tree-Shaking (Webpack/Vite)

```js
// lodash-es (ES modules version)
import { debounce } from 'lodash-es'; // Only imports debounce
```

---

### 4. Microfrontends

```js
// Load team A's module
const teamAModule = await import('https://team-a.com/module.js');
teamAModule.render(container);
```

---

## **Interview Checklist**

✅ **Understand:**

- Named vs default exports
- Static vs dynamic imports
- Module caching & singletons
- Live bindings in ESM
- CommonJS vs ES Modules
- Circular dependencies
- Tree-shaking & code splitting

✅ **Be Ready to:**

- Refactor global code into modules
- Debug import/export issues
- Explain module resolution
- Optimize bundle size with dynamic imports

---

## **Final Pro Tips**

1. **Always use ES Modules** in modern projects (better tooling support)
2. **Use dynamic imports** for code splitting (improves performance)
3. **Avoid circular dependencies** (hard to debug)
4. **Prefer named exports** for better refactoring (easier to search/rename)
5. **Use barrel files** (`index.js`) to simplify imports

---
