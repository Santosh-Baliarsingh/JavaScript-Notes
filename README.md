<!-- markdownlint-disable MD060 -->
<!-- markdownlint-disable MD026 -->
# 🧠 How JavaScript Works Behind the Scenes

## 📋 Table of Contents

1. [JavaScript Engine Architecture](#javascript-engine-architecture)
2. [Execution Context](#execution-context)
3. [Call Stack & Memory Heap](#call-stack--memory-heap)
4. [Event Loop Architecture](#event-loop-architecture)
5. [Microtasks vs Macrotasks](#microtasks-vs-macrotasks)
6. [Complete Execution Flow](#complete-execution-flow)
7. [Code Examples](#code-examples)

---

## JavaScript Engine Architecture

```text
┌─────────────────────────────────────────────────────────-┐
│                   JAVASCRIPT ENGINE                      │
│                   (V8, SpiderMonkey)                     │
├─────────────────────────────────────────────────────────-┤
│                                                          │
│  ┌──────────────┐    ┌───────────────┐                   │
│  │   PARSER     │───▶│     AST       │                   │
│  │              │    │ (Abstract     │                   │
│  │ Source Code  │    │ Syntax Tree)  │                   │
│  └──────────────┘    └───────────────┘                   │
│                             │                            │
│                             ▼                            │
│                      ┌─────────────┐                     │
│                      │  COMPILER   │                     │
│                      │    (JIT)    │                     │
│                      └─────────────┘                     │
│                             │                            │
│                             ▼                            │
│                   ┌──────────────────┐                   │
│                   │  MACHINE CODE    │                   │
│                   │   (Execution)    │                   │
│                   └──────────────────┘                   │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

### Key Components:

- **Parser**: Converts source code → Abstract Syntax Tree (AST)
- **Compiler**: JIT (Just-In-Time) compilation → Machine code
- **Execution**: Runs the optimized machine code

---

## Execution Context

### Types of Execution Contexts:

```text
┌────────────────────────────────────────────────────────┐
│         GLOBAL EXECUTION CONTEXT (GEC)                 │
│  Created when script first loads                       │
│  ┌──────────────────────────────────────────────────-┐ │
│  │  Variable Environment                             │ │
│  │  - Variables, Functions, Objects                  │ │
│  ├──────────────────────────────────────────────────-┤ │
│  │  Scope Chain                                      │ │
│  │  - Links to outer scopes                          │ │
│  ├──────────────────────────────────────────────────-┤ │
│  │  this keyword                                     │ │
│  │  - window (browser) / global (Node.js)            │ │
│  └──────────────────────────────────────────────────-┘ │
└────────────────────────────────────────────────────────┘

┌────────────────────────────────────────────────────────┐
│      FUNCTION EXECUTION CONTEXT (FEC)                  │
│  Created when function is called                       │
│  ┌──────────────────────────────────────────────────-┐ │
│  │  Variable Environment                             │ │
│  │  - Local variables, arguments                     │ │
│  ├──────────────────────────────────────────────────-┤ │
│  │  Scope Chain                                      │ │
│  │  - Access to parent scope                         │ │
│  ├──────────────────────────────────────────────────-┤ │
│  │  this keyword                                     │ │
│  │  - Depends on how function is called              │ │
│  └──────────────────────────────────────────────────-┘ │
└────────────────────────────────────────────────────────┘
```

### Execution Context Phases:

1. **Creation Phase**:
   - Set up memory space for variables and functions
   - Create scope chain
   - Determine `this` value

2. **Execution Phase**:
   - Assign values to variables
   - Execute function code line by line

---

## Call Stack & Memory Heap

```text
┌────────────────────────────────────────────────────────┐
│                    JAVASCRIPT RUNTIME                  │
├──────────────────────────┬─────────────────────────────┤
│                          │                             │
│    CALL STACK            │      MEMORY HEAP            │
│    (Execution Order)     │      (Data Storage)         │
│                          │                             │
│  ┌────────────────┐      │   ┌───────────────────┐     │
│  │   [Empty]      │      │   │   Objects         │     │
│  ├────────────────┤      │   │   Arrays          │     │
│  │   main()       │◀─────┼───│   Functions       │     │
│  ├────────────────┤      │   │   Closures        │     │
│  │   foo()        │      │   │   (Dynamic Alloc) │     │
│  ├────────────────┤      │   └───────────────────┘     │
│  │   bar()        │◀─ Currently Executing              │
│  └────────────────┘      │                             │
│       (LIFO)             │                             │
│  Last In, First Out      │                             │
│                          │                             │
└──────────────────────────┴─────────────────────────────┘
```

### Call Stack Example:

```javascript
function first() {
  console.log('First');
  second();
  console.log('First Again');
}

function second() {
  console.log('Second');
  third();
}

function third() {
  console.log('Third');
}

first();
```

**Stack Progression:**

```text
Step 1:               Step 2:               Step 3:               Step 4:
┌──────────┐          ┌──────────┐          ┌──────────┐          ┌──────────┐
│ first()  │          │ second() │          │ third()  │          │ second() │
├──────────┤          ├──────────┤          ├──────────┤          ├──────────┤
│ [main]   │          │ first()  │          │ second() │          │ first()  │
└──────────┘          ├──────────┤          ├──────────┤          ├──────────┤
                      │ [main]   │          │ first()  │          │ [main]   │
                      └──────────┘          ├──────────┤          └──────────┘
                                            │ [main]   │
                                            └──────────┘

Step 5:               Step 6:
┌──────────┐          ┌──────────┐
│ first()  │          │ [Empty]  │
├──────────┤          └──────────┘
│ [main]   │
└──────────┘
```

---

## Event Loop Architecture

```text
┌────────────────────────────────────────────────────────────────-┐
│                     JAVASCRIPT ENGINE                           │
│                                                                 │
│  ┌────────────────┐              ┌─────────────────────┐        │
│  │  CALL STACK    │              │   MEMORY HEAP       │        │
│  │  (Execution)   │              │   (Data Storage)    │        │
│  └────────────────┘              └─────────────────────┘        │
│         ▲                                                       │
└─────────┼───────────────────────────────────────────────────────┘
          │
          │ ⑥ Push callback to stack when empty
          │
┌─────────┴──────────────────────────────────────────────────────-─┐
│                       EVENT LOOP                                 │
│  (Continuously checks if Call Stack is empty)                    │
│                                                                  │
│  while(true) {                                                   │
│    if (callStack.isEmpty()) {                                    │
│      if (microtaskQueue.hasItems()) {                            │
│        callStack.push(microtaskQueue.dequeue())                  │
│      } else if (macrotaskQueue.hasItems()) {                     │
│        callStack.push(macrotaskQueue.dequeue())                  │
│      }                                                           │
│    }                                                             │
│  }                                                               │
└──────────────────────────────────────────────────────────────────┘
          ▲                            ▲
          │                            │
          │ ⑤ Event Loop checks        │
          │   queues                   │
          │                            │
┌─────────┴────────────┐    ┌──────────┴──────────────┐
│  MICROTASK QUEUE     │    │  MACROTASK QUEUE        │
│  (Higher Priority)   │    │  (Lower Priority)       │
│                      │    │                         │
│  • Promise.then()    │    │  • setTimeout()         │
│  • async/await       │    │  • setInterval()        │
│  • queueMicrotask()  │    │  • setImmediate()       │
│  • MutationObserver  │    │  • I/O operations       │
│  • process.nextTick()│    │  • UI rendering         │
└──────────────────────┘    └─────────────────────────┘
          ▲                            ▲
          │                            │
          │ ④ Callback registered      │
          │                            │
┌─────────┴────────────────────────────┴──────────────--┐
│              WEB APIs / NODE APIs                     │
│  (Browser Runtime / Node.js Runtime)                  │
│                                                       │
│  ② Async operation handed off                         |
│                                                       │
│  • setTimeout / setInterval                           │
│  • fetch / XMLHttpRequest                             │
│  • DOM events (click, scroll, etc.)                   │
│  • File System (fs)                                   │
│  • Promises                                           │
│                                                       │
│  ③ When complete, callback sent to appropriate queue  │
|                                                       │
└─────────────────────────────────────────────────────--┘
          ▲
          │
          │ ① Call async function
          │
┌─────────┴─────────────────────────────────────────-─┐
│           YOUR JAVASCRIPT CODE                      │
└─────────────────────────────────────────────────────┘
```

### Event Loop Flow:

1. ✅ **Execute synchronous code** on Call Stack
2. ✅ **Process ALL Microtasks** (until queue is empty)
3. ✅ **Execute ONE Macrotask**
4. ✅ **Render** (if in browser)
5. 🔁 **Repeat from step 2**

---

## Microtasks vs Macrotasks

### Complete Comparison:

```text
┌──────────────────────────────────────────────────────────────────┐
│                    PRIORITY HIERARCHY                            │
├──────────────────────────────────────────────────────────────────┤
│                                                                  │
│  1️⃣  SYNCHRONOUS CODE (Highest)                                  │
│      └─ Executed immediately on Call Stack                       │
│                                                                  │
│  2️⃣  MICROTASKS (High Priority)                                  │
│      ├─ process.nextTick() [Node.js - Highest microtask]         │
│      ├─ Promise.then() / Promise.catch()                         │
│      ├─ async/await                                              │
│      ├─ queueMicrotask()                                         │
│      └─ MutationObserver [Browser]                               │
│                                                                  │
│  3️⃣  MACROTASKS (Lower Priority)                                 │
│      ├─ setTimeout()                                             │
│      ├─ setInterval()                                            │
│      ├─ setImmediate() [Node.js]                                 │
│      ├─ I/O operations                                           │
│      ├─ UI rendering [Browser]                                   │
│      └─ requestAnimationFrame() [Browser]                        │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### Feature Comparison:

| Feature | Microtasks | Macrotasks |
|---------|-----------|------------|
| **Priority** | ⚡ Higher | 🐢 Lower |
| **Execution** | ALL at once | ONE per cycle |
| **Examples** | Promises, async/await | setTimeout, setInterval |
| **Queue Clearing** | Complete queue before next step | One task, then check microtasks |
| **Can Block?** | ⚠️ Yes (if too many) | ✅ No (allows rendering) |
| **Starvation Risk** | Can starve macrotasks | Cannot starve microtasks |

---

## Complete Execution Flow

### Detailed Step-by-Step:

```text
┌────────────────────────────────────────────────────────────────-┐
│  STEP 1: SCRIPT LOADING                                         │
│  ┌──────────────────────────────────────────────────────────-┐  │
│  │  • Global Execution Context created                       │  │
│  │  • Variables and functions hoisted                        │  │
│  │  • Code parsed and compiled                               │  │
│  └──────────────────────────────────────────────────────────-┘  │
└────────────────────────────────────────────────────────────────-┘
                           ↓
┌───────────────────────────────────────────────────────────────-─┐
│  STEP 2: SYNCHRONOUS EXECUTION                                  │
│  ┌─────────────────────────────────────────────────────────-─┐  │
│  │  • Execute code line by line                              │  │
│  │  • Function calls pushed to Call Stack                    │  │
│  │  • Async operations sent to Web APIs                      │  │
│  └─────────────────────────────────────────────────────────-─┘  │
└───────────────────────────────────────────────────────────────-─┘
                           ↓
┌────────────────────────────────────────────────────────────────-┐
│  STEP 3: CALL STACK EMPTY?                                      │
│  ┌─────────────────────────────────────────────────────────-─┐  │
│  │  Event Loop checks: Is Call Stack empty?                  │  │
│  │  • NO  → Wait and check again                             │  │
│  │  • YES → Proceed to Step 4                                │  │
│  └──────────────────────────────────────────────────────────-┘  │
└───────────────────────────────────────────────────────────────-─┘
                           ↓
┌────────────────────────────────────────────────────────────────-┐
│  STEP 4: PROCESS MICROTASKS                                     │
│  ┌─────────────────────────────────────────────────────────-─┐  │
│  │  • Execute ALL microtasks in queue                        │  │
│  │  • New microtasks added during execution are processed    │  │
│  │  • Continue until microtask queue is COMPLETELY empty     │  │
│  └──────────────────────────────────────────────────────────-┘  │
└───────────────────────────────────────────────────────────────-─┘
                           ↓
┌──────────────────────────────────────────────────────────────-──┐
│  STEP 5: PROCESS ONE MACROTASK                                  │
│  ┌─────────────────────────────────────────────────────────-─┐  │
│  │  • Take ONE macrotask from queue                          │  │
│  │  • Execute it on Call Stack                               │  │
│  │  • Go back to Step 3                                      │  │
│  └─────────────────────────────────────────────────────────-─┘  │
└──────────────────────────────────────────────────────────────-──┘
                           ↓
┌──────────────────────────────────────────────────────────────-──┐
│  STEP 6: RENDER (Browser Only)                                  │
│  ┌─────────────────────────────────────────────────────────-─┐  │
│  │  • Update UI if needed (60fps = every ~16ms)              │  │
│  │  • requestAnimationFrame callbacks executed               │  │
│  └─────────────────────────────────────────────────────────-─┘  │
└─────────────────────────────────────────────────────────────-───┘
                           ↓
                    🔁 LOOP BACK TO STEP 3
```

---

## Code Examples

### Example 1: Basic Execution Order

```javascript
console.log('1 - Start');

setTimeout(() => console.log('2 - Timeout'), 0);

Promise.resolve().then(() => console.log('3 - Promise'));

console.log('4 - End');
```

**Output:**

```text
1 - Start
4 - End
3 - Promise
2 - Timeout
```

**Explanation:**

1. Synchronous: `1`, `4`
2. Microtask: `3` (Promise)
3. Macrotask: `2` (setTimeout)

---

### Example 2: Multiple Tasks

```javascript
console.log('Script start');

setTimeout(() => {
  console.log('setTimeout 1');
}, 0);

Promise.resolve()
  .then(() => {
    console.log('Promise 1');
  })
  .then(() => {
    console.log('Promise 2');
  });

setTimeout(() => {
  console.log('setTimeout 2');
}, 0);

Promise.resolve().then(() => {
  console.log('Promise 3');
});

console.log('Script end');
```

**Output:**

```text
Script start
Script end
Promise 1
Promise 3
Promise 2
setTimeout 1
setTimeout 2
```

**Step-by-step:**

```text
Step 1: Synchronous Code
├─ console.log('Script start')     ✅ Output: Script start
├─ setTimeout → Macrotask Queue
├─ Promise → Microtask Queue
├─ setTimeout → Macrotask Queue
├─ Promise → Microtask Queue
└─ console.log('Script end')       ✅ Output: Script end

Step 2: Process ALL Microtasks
├─ Promise 1                       ✅ Output: Promise 1
├─ Promise 3                       ✅ Output: Promise 3
└─ Promise 2 (chained)             ✅ Output: Promise 2

Step 3: Process ONE Macrotask
└─ setTimeout 1                    ✅ Output: setTimeout 1

Step 4: Check Microtasks (empty)

Step 5: Process Next Macrotask
└─ setTimeout 2                    ✅ Output: setTimeout 2
```

---

### Example 3: Microtask Starvation

```javascript
console.log('Start');

setTimeout(() => console.log('Timeout - NEVER RUNS'), 0);

function recursiveMicrotask() {
  Promise.resolve().then(() => {
    console.log('Microtask');
    recursiveMicrotask(); // Creates infinite microtasks
  });
}

recursiveMicrotask();

console.log('End');
```

**Output:**

```text
Start
End
Microtask
Microtask
Microtask
... (infinite)
```

**⚠️ Warning:** Timeout never executes because microtasks keep adding more microtasks!

---

### Example 4: Nested Async Operations

```javascript
console.log('1');

setTimeout(() => {
  console.log('2');
  
  Promise.resolve().then(() => console.log('3'));
  
  setTimeout(() => console.log('4'), 0);
}, 0);

Promise.resolve()
  .then(() => {
    console.log('5');
    
    setTimeout(() => console.log('6'), 0);
  })
  .then(() => console.log('7'));

console.log('8');
```

**Output:**

```text
1
8
5
7
2
3
6
4
```

**Visual Flow:**

```text
[Sync]       → 1, 8
[Microtask]  → 5, 7
[Macrotask]  → 2
[Microtask]  → 3  (from inside setTimeout)
[Macrotask]  → 6
[Macrotask]  → 4
```

---

### Example 5: Node.js process.nextTick()

```javascript
console.log('1');

setTimeout(() => console.log('2'), 0);

Promise.resolve().then(() => console.log('3'));

process.nextTick(() => console.log('4'));

console.log('5');
```

**Output (Node.js):**

```text
1
5
4
3
2
```

**Priority Order:**

1. Synchronous: `1`, `5`
2. `process.nextTick()`: `4` ← Highest microtask priority
3. Promise: `3`
4. setTimeout: `2`

---

## Key Takeaways

### ✅ Critical Rules:

1. **JavaScript is single-threaded**
   - One Call Stack = One thing at a time

2. **Execution Order**:

   ```text
   Synchronous → Microtasks → Macrotasks → Render
   ```

3. **Microtasks = ALL processed**
   - Queue completely empties before next step

4. **Macrotasks = ONE per cycle**
   - After each macrotask, check microtasks again

5. **Priority Hierarchy**:

   ```text
   Sync Code > Microtasks > Macrotasks
   ```

6. **Common Microtasks**:
   - Promises, async/await, queueMicrotask()

7. **Common Macrotasks**:
   - setTimeout, setInterval, I/O

8. **Beware of Starvation**:
   - Too many microtasks can block macrotasks

---

## Quick Reference

### Debugging Event Loop Issues:

```javascript
// Visualize execution order
const log = (msg, type) => console.log(`[${type}] ${msg}`);

log('Sync 1', 'SYNC');

setTimeout(() => log('Macro 1', 'MACRO'), 0);

Promise.resolve().then(() => log('Micro 1', 'MICRO'));

queueMicrotask(() => log('Micro 2', 'MICRO'));

log('Sync 2', 'SYNC');

// Output:
// [SYNC] Sync 1
// [SYNC] Sync 2
// [MICRO] Micro 1
// [MICRO] Micro 2
// [MACRO] Macro 1
```

---
