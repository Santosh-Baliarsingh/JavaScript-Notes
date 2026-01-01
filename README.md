<!-- markdownlint-disable MD060 -->
<!-- markdownlint-disable MD026 -->
<!-- markdownlint-disable MD033 -->
# 🧠 How JavaScript Works Behind the Scenes

## 📋 Table of Contents

1. [JavaScript Engine Architecture](#javascript-engine-architecture)
2. [Execution Context](#execution-context)
3. [Call Stack & Memory Heap](#call-stack--memory-heap)
4. [Event Loop Architecture](#event-loop-architecture)
5. [Microtasks vs Macrotasks](#microtasks-vs-macrotasks)
6. [Complete Execution Flow](#complete-execution-flow)
7. [Code Examples](#code-examples)
8. [setTimeout & Promise Execution Flow](#settimeout--promise-execution-flow)
9. [Code Examples & Patterns](#code-examples--patterns)
10. [Practice Questions](#practice-questions)
11. [Common Interview Traps](#common-interview-traps)
12. [Real-World Implications](#real-world-implications)

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

## setTimeout & Promise Execution Flow

### The Original Question

```javascript
setTimeout(() => {
  console.log('2');
  
  Promise.resolve().then(() => console.log('3'));
  
  setTimeout(() => console.log('4'), 0);
}, 0);
```

**Output:** `2 → 3 → 4`

**Why?** Microtasks (Promises) execute BEFORE the next Macrotask (setTimeout)

---

### **Visual Step-by-Step Breakdown**

```text
┌─────────────────────────────────────────────────────────────────┐
│  STEP 1: INITIAL STATE                                          │
├─────────────────────────────────────────────────────────────────┤
│  Call Stack:      [Empty]                                       │
│  Macrotask Queue: []                                            │
│  Microtask Queue: []                                            │
└─────────────────────────────────────────────────────────────────┘

                         ↓

┌─────────────────────────────────────────────────────────────────┐
│  STEP 2: setTimeout REGISTERED                                  │
├─────────────────────────────────────────────────────────────────┤
│  setTimeout(() => { ... }, 0);                                  │
│                                                                 │
│  Call Stack:      [Empty]                                       │
│  Macrotask Queue: [callback1] ← First setTimeout added          │
│  Microtask Queue: []                                            │
└─────────────────────────────────────────────────────────────────┘

                         ↓

┌─────────────────────────────────────────────────────────────────┐
│  STEP 3: EVENT LOOP PICKS FIRST MACROTASK                       │
├─────────────────────────────────────────────────────────────────┤
│  Event Loop: "Call Stack empty, taking one macrotask"           │
│                                                                 │
│  Call Stack:      [callback1] ← Executing                       │
│  Macrotask Queue: []                                            │
│  Microtask Queue: []                                            │
└─────────────────────────────────────────────────────────────────┘

                         ↓

┌─────────────────────────────────────────────────────────────────┐
│  STEP 4: console.log('2') EXECUTES                              │
├─────────────────────────────────────────────────────────────────┤
│  console.log('2');  ✅ OUTPUT: 2                                │
│                                                                 │
│  Call Stack:      [callback1]                                   │
│  Macrotask Queue: []                                            │
│  Microtask Queue: []                                            │
└─────────────────────────────────────────────────────────────────┘

                         ↓

┌─────────────────────────────────────────────────────────────────┐
│  STEP 5: PROMISE CREATED                                        │
├─────────────────────────────────────────────────────────────────┤
│  Promise.resolve().then(() => console.log('3'));                │
│                                                                 │
│  Call Stack:      [callback1]                                   │
│  Macrotask Queue: []                                            │
│  Microtask Queue: [promise_callback] ← Promise added            │
└─────────────────────────────────────────────────────────────────┘

                         ↓

┌─────────────────────────────────────────────────────────────────┐
│  STEP 6: SECOND setTimeout REGISTERED                           │
├─────────────────────────────────────────────────────────────────┤
│  setTimeout(() => console.log('4'), 0);                         │
│                                                                 │
│  Call Stack:      [callback1]                                   │
│  Macrotask Queue: [callback2] ← Second setTimeout added         │
│  Microtask Queue: [promise_callback]                            │
└─────────────────────────────────────────────────────────────────┘

                         ↓

┌─────────────────────────────────────────────────────────────────┐
│  STEP 7: FIRST MACROTASK COMPLETES                              │
├─────────────────────────────────────────────────────────────────┤
│  callback1 execution finished                                   │
│                                                                 │
│  Call Stack:      [Empty] ← callback1 finished                  │
│  Macrotask Queue: [callback2] ← Waiting                         │
│  Microtask Queue: [promise_callback] ← Must execute now!        │
└─────────────────────────────────────────────────────────────────┘

                         ↓

┌─────────────────────────────────────────────────────────────────┐
│  STEP 8: MICROTASK EXECUTES (PRIORITY!)                         │
├─────────────────────────────────────────────────────────────────┤
│  🚨 CRITICAL: After macrotask, ALL microtasks execute first!    │
│                                                                 │
│  console.log('3');  ✅ OUTPUT: 3                                │
│                                                                 │
│  Call Stack:      [promise_callback] ← Executing                │
│  Macrotask Queue: [callback2] ← Still waiting                   │
│  Microtask Queue: []                                            │
└─────────────────────────────────────────────────────────────────┘

                         ↓

┌─────────────────────────────────────────────────────────────────┐
│  STEP 9: MICROTASKS EMPTY - NEXT MACROTASK                      │
├─────────────────────────────────────────────────────────────────┤
│  Event Loop: "Microtasks done, taking next macrotask"           │
│                                                                 │
│  Call Stack:      [Empty]                                       │
│  Macrotask Queue: [callback2]                                   │
│  Microtask Queue: [] ← Empty, so move to macrotask              │
└─────────────────────────────────────────────────────────────────┘

                         ↓

┌─────────────────────────────────────────────────────────────────┐
│  STEP 10: SECOND setTimeout EXECUTES                            │
├─────────────────────────────────────────────────────────────────┤
│  console.log('4');  ✅ OUTPUT: 4                                │
│                                                                 │
│  Call Stack:      [callback2] ← Executing                       │
│  Macrotask Queue: []                                            │
│  Microtask Queue: []                                            │
└─────────────────────────────────────────────────────────────────┘

                         ↓

┌─────────────────────────────────────────────────────────────────┐
│  FINAL STATE: ALL DONE                                          │
├─────────────────────────────────────────────────────────────────┤
│  Call Stack:      [Empty]                                       │
│  Macrotask Queue: []                                            │
│  Microtask Queue: []                                            │
│                                                                 │
│  ✅ COMPLETE OUTPUT: 2 → 3 → 4                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## Timeline View

```text
Time  Event                           Queue State                 Output
────────────────────────────────────────────────────────────────────────
T1    setTimeout registered           Macro: [cb1]                
                                      Micro: []

T2    Event Loop picks cb1            Macro: []
                                      Micro: []

T3    Execute console.log('2')        Macro: []                   2 ✅
                                      Micro: []

T4    Promise added                   Macro: []
                                      Micro: [promise]

T5    Second setTimeout added         Macro: [cb2]
                                      Micro: [promise]

T6    cb1 completes                   Macro: [cb2]
      Check microtasks ⚡              Micro: [promise]

T7    Execute Promise                 Macro: [cb2]                3 ✅
                                      Micro: []

T8    Microtasks empty                Macro: [cb2]
      Take next macrotask             Micro: []

T9    Execute second setTimeout       Macro: []                   4 ✅
                                      Micro: []

T10   All done                        Macro: []
                                      Micro: []
────────────────────────────────────────────────────────────────────────
```

---

## The Golden Rule

```text
╔═══════════════════════════════════════════════════════════════╗
║                    EVENT LOOP PRIORITY                        ║
╠═══════════════════════════════════════════════════════════════╣
║                                                               ║
║  After EVERY Macrotask:                                       ║
║                                                               ║
║  1️⃣  Execute ALL Microtasks (until queue is empty)            ║
║  2️⃣  Then take ONE Macrotask                                  ║
║  3️⃣  Repeat                                                   ║
║                                                               ║
║  ⚡ Microtasks ALWAYS have priority over next Macrotask       ║
║                                                               ║
╚═══════════════════════════════════════════════════════════════╝
```

---

## Code Examples & Patterns

### **Pattern 1: Basic Nesting**

```javascript
setTimeout(() => {
  console.log('A');
  Promise.resolve().then(() => console.log('B'));
  setTimeout(() => console.log('C'), 0);
}, 0);
```

**Output:** `A → B → C`

**Execution:**

1. First setTimeout (Macrotask) → prints `A`
2. Promise (Microtask) → prints `B`
3. Second setTimeout (Macrotask) → prints `C`

---

### **Pattern 2: Multiple Microtasks**

```javascript
setTimeout(() => {
  console.log('A');
  Promise.resolve().then(() => console.log('B'));
  Promise.resolve().then(() => console.log('C'));
}, 0);
```

**Output:** `A → B → C`

**Why?** ALL microtasks execute before next macrotask.

```text
Step 1: setTimeout executes → A
Step 2: Two promises added to Microtask Queue
Step 3: Macrotask completes
Step 4: Execute ALL microtasks → B, C
```

---

### **Pattern 3: Chained Promises**

```javascript
setTimeout(() => {
  console.log('1');
  
  Promise.resolve()
    .then(() => {
      console.log('2');
      return Promise.resolve();
    })
    .then(() => console.log('3'));
    
}, 0);
```

**Output:** `1 → 2 → 3`

**Why?** Chained promises are still microtasks, all execute together.

---

### **Pattern 4: Mixed Order**

```javascript
setTimeout(() => console.log('1'), 0);

Promise.resolve().then(() => {
  console.log('2');
  setTimeout(() => console.log('3'), 0);
});

setTimeout(() => console.log('4'), 0);
```

**Output:** `2 → 1 → 4 → 3`

**Breakdown:**

```text
Initial State:
  Macrotasks: [setTimeout(1), setTimeout(4)]
  Microtasks: [Promise(2)]

Step 1: Execute microtasks first
  → console.log('2')
  → setTimeout(3) added to Macrotasks
  
Step 2: Execute first macrotask
  → console.log('1')
  
Step 3: Execute second macrotask
  → console.log('4')
  
Step 4: Execute third macrotask
  → console.log('3')
```

---

### **Pattern 5: Complex Nesting**

```javascript
setTimeout(() => {
  console.log('A');
  
  Promise.resolve().then(() => {
    console.log('B');
    setTimeout(() => console.log('C'), 0);
  });
  
  setTimeout(() => console.log('D'), 0);
}, 0);

setTimeout(() => console.log('E'), 0);
```

**Output:** `A → B → E → D → C`

**Step-by-step:**

```text
Initial:
  Macro: [setTimeout(A), setTimeout(E)]
  Micro: []

1. Execute setTimeout(A)
   → Print 'A'
   → Add Promise(B) to Microtasks
   → Add setTimeout(D) to Macrotasks
   State: Macro: [setTimeout(E), setTimeout(D)]
          Micro: [Promise(B)]

2. Execute ALL Microtasks
   → Print 'B'
   → Add setTimeout(C) to Macrotasks
   State: Macro: [setTimeout(E), setTimeout(D), setTimeout(C)]
          Micro: []

3. Execute setTimeout(E)
   → Print 'E'
   State: Macro: [setTimeout(D), setTimeout(C)]
          Micro: []

4. Execute setTimeout(D)
   → Print 'D'
   State: Macro: [setTimeout(C)]
          Micro: []

5. Execute setTimeout(C)
   → Print 'C'
   State: Macro: []
          Micro: []
```

---

## Practice Questions

### **Question 1:**

```javascript
setTimeout(() => {
  console.log('X');
  Promise.resolve().then(() => console.log('Y'));
}, 0);

Promise.resolve().then(() => console.log('Z'));
```

**What's the output?**

<details>
<summary>Click to reveal answer</summary>

**Answer:** `Z → X → Y`

**Explanation:**

1. Promise(Z) is in Microtask queue immediately
2. setTimeout(X) is in Macrotask queue
3. Microtasks execute first → Z
4. Then macrotask → X
5. Then microtask from inside setTimeout → Y

</details>

---

### **Question 2:**

```javascript
console.log('1');

setTimeout(() => console.log('2'), 0);

Promise.resolve()
  .then(() => console.log('3'))
  .then(() => console.log('4'));

console.log('5');
```

**What's the output?**

<details>
<summary>Click to reveal answer</summary>

**Answer:** `1 → 5 → 3 → 4 → 2`

**Explanation:**

1. Synchronous: 1, 5
2. Microtasks: 3, 4 (chained promises)
3. Macrotask: 2

</details>

---

### **Question 3:**

```javascript
setTimeout(() => {
  console.log('A');
  Promise.resolve().then(() => {
    console.log('B');
    Promise.resolve().then(() => console.log('C'));
  });
}, 0);
```

**What's the output?**

<details>
<summary>Click to reveal answer</summary>

**Answer:** `A → B → C`

**Explanation:**

1. setTimeout executes → A
2. Promise(B) added to Microtask queue
3. Macrotask completes, execute microtasks
4. Promise(B) executes → B, adds Promise(C)
5. Continue microtasks → C

</details>

---

### **Question 4: Advanced**

```javascript
setTimeout(() => console.log('1'), 0);

Promise.resolve().then(() => {
  console.log('2');
  setTimeout(() => {
    console.log('3');
    Promise.resolve().then(() => console.log('4'));
  }, 0);
  Promise.resolve().then(() => console.log('5'));
});

setTimeout(() => console.log('6'), 0);
```

**What's the output?**

<details>
<summary>Click to reveal answer</summary>

**Answer:** `2 → 5 → 1 → 6 → 3 → 4`

**Detailed Breakdown:**

```text
Initial:
  Macro: [setTimeout(1), setTimeout(6)]
  Micro: [Promise(2)]

Step 1: Microtask
  → Print 2
  → Add setTimeout(3) to Macros
  → Add Promise(5) to Micros
  State: Macro: [setTimeout(1), setTimeout(6), setTimeout(3)]
         Micro: [Promise(5)]

Step 2: Continue Microtasks
  → Print 5
  State: Macro: [setTimeout(1), setTimeout(6), setTimeout(3)]
         Micro: []

Step 3: Macrotask
  → Print 1
  State: Macro: [setTimeout(6), setTimeout(3)]
         Micro: []

Step 4: Macrotask
  → Print 6
  State: Macro: [setTimeout(3)]
         Micro: []

Step 5: Macrotask
  → Print 3
  → Add Promise(4) to Micros
  State: Macro: []
         Micro: [Promise(4)]

Step 6: Microtask
  → Print 4
  State: Macro: []
         Micro: []
```

</details>

---

## Common Interview Traps

| ❌ Wrong Assumption | ✅ Correct Understanding |
|---------------------|--------------------------|
| `setTimeout(..., 0)` executes immediately | It waits in Macrotask queue |
| Promises execute after setTimeout | Promises (Microtasks) execute BEFORE setTimeout |
| Only one microtask executes per cycle | ALL microtasks execute before next macrotask |
| Order is based on registration | Order is based on task type priority |
| Nested setTimeout executes before promises | Promises inside setTimeout execute before next setTimeout |

---

## Real-World Implications

### **1. UI Responsiveness**

```javascript
// BAD: Blocks UI
setTimeout(() => {
  // Heavy computation
  for (let i = 0; i < 1e9; i++) {}
}, 0);

// GOOD: Allows UI updates between chunks
function processInChunks(data) {
  const chunk = data.splice(0, 100);
  processChunk(chunk);
  
  if (data.length > 0) {
    setTimeout(() => processInChunks(data), 0);
  }
}
```

### **2. State Management**

```javascript
// Guaranteed execution before next render
setState(newValue);
Promise.resolve().then(() => {
  // This runs before next macrotask/render
  updateDependentState();
});
```

### **3. Event Handling**

```javascript
button.addEventListener('click', () => {
  // Synchronous
  updateUI();
  
  // Microtask - runs before next click can be processed
  Promise.resolve().then(() => {
    validateState();
  });
  
  // Macrotask - might run after next click
  setTimeout(() => {
    logAnalytics();
  }, 0);
});
```

---

## Key Takeaway

1. **Microtasks > Macrotasks** in priority
2. **ALL microtasks** execute before next macrotask
3. **Promises** are microtasks, **setTimeout** is macrotask
4. After completing a macrotask, Event Loop **ALWAYS checks microtasks first**
5. New microtasks added during microtask execution are **processed immediately**
6. Understanding this prevents race conditions and timing bugs

---
