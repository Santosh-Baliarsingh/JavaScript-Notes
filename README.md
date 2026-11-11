<!-- markdownlint-disable MD036 -->
<!-- markdownlint-disable MD060 -->
<!-- markdownlint-disable MD050 -->
<!-- markdownlint-disable MD024 -->

# **JavaScript `this` Keyword**

---

## **Table Of Contents**

- [**JavaScript `this` Keyword**](#javascript-this-keyword)
  - [**Table Of Contents**](#table-of-contents)
  - [**Initial Understanding**](#initial-understanding)
  - [**Beginner Explanation**](#beginner-explanation)
  - [**Advanced Explanation**](#advanced-explanation)
    - [***How `this` is determined (Binding Rules)***](#how-this-is-determined-binding-rules)
    - [***Special Cases:***](#special-cases)
  - [**Code Examples with Visual Diagrams**](#code-examples-with-visual-diagrams)
    - [***Case 1: Default Binding (Global Context)***](#case-1-default-binding-global-context)
    - [***Case 2: Implicit Binding (Object Method)***](#case-2-implicit-binding-object-method)
    - [***Case 3: Losing `this` Context***](#case-3-losing-this-context)
    - [***Case 4: Explicit Binding (`.call()`, `.apply()`, `.bind()`)***](#case-4-explicit-binding-call-apply-bind)
    - [***Case 5: `new` Binding (Constructor Functions)***](#case-5-new-binding-constructor-functions)
    - [***Case 6: Arrow Functions (Lexical `this`)***](#case-6-arrow-functions-lexical-this)
    - [***Case 7: Event Listeners***](#case-7-event-listeners)
    - [***Case 8: Class Methods***](#case-8-class-methods)
  - [**Common Interview Traps**](#common-interview-traps)
    - [***Trap 1: Losing context in callbacks***](#trap-1-losing-context-in-callbacks)
    - [***Trap 2: Arrow functions don't have `this`***](#trap-2-arrow-functions-dont-have-this)
    - [***Trap 3: `this` in nested functions***](#trap-3-this-in-nested-functions)
  - [**Real-World Use Cases**](#real-world-use-cases)
    - [***React Class Components***](#react-class-components)
    - [***jQuery / DOM Manipulation***](#jquery--dom-manipulation)
    - [***Method Chaining***](#method-chaining)
  - [**Quick Reference Table**](#quick-reference-table)
  - [**Additional Resources**](#additional-resources)

## **Initial Understanding**

The `this` keyword in JavaScript is a **context-dependent reference** to the object that is currently executing the code. Its value is determined by **how a function is called**, not where it's defined.

---

## **Beginner Explanation**

Think of `this` as a **pronoun** in English:

- "John loves **his** dog" — "his" refers to John
- In JavaScript, `this` refers to the "owner" of the function being executed

**Key Rule**: `this` changes based on **who calls the function**.

```javascript
const person = {
  name: "Alice",
  greet() {
    console.log(this.name); // 'this' refers to 'person'
  }
};

person.greet(); // "Alice"
```

---

## **Advanced Explanation**

### ***How `this` is determined (Binding Rules)***

JavaScript uses **4 binding rules** in order of precedence:

1. **`new` Binding** (Constructor calls)
2. **Explicit Binding** (`.call()`, `.apply()`, `.bind()`)
3. **Implicit Binding** (Method calls: `obj.method()`)
4. **Default Binding** (Standalone function calls)

### ***Special Cases:***

- **Arrow functions** don't have their own `this` — they inherit from the enclosing scope (lexical `this`)
- **Strict mode** changes default binding behavior

---

## **Code Examples with Visual Diagrams**

### ***Case 1: Default Binding (Global Context)***

```javascript
function sayHi() {
  console.log(this);
}

sayHi(); // Window (browser) / global (Node) / undefined (strict mode)
```

**Diagram:**

```js
┌─────────────────────┐
│   Global Scope      │
│   this = window     │
│                     │
│   sayHi() ───────►  │
└─────────────────────┘
```

---

### ***Case 2: Implicit Binding (Object Method)***

```javascript
const user = {
  name: "Bob",
  greet() {
    console.log(this.name);
  }
};

user.greet(); // "Bob"
```

**Diagram:**

```js
┌────────────────────┐
│   user object      │
│   ┌──────────────┐ │
│   │ name: "Bob"  │ │
│   │ greet()      │ │
│   └──────────────┘ │
│         ▲          │
│         │          │
│    this points     │
│    to user         │
└────────────────────┘
```

---

### ***Case 3: Losing `this` Context***

```javascript
const user = {
  name: "Charlie",
  greet() {
    console.log(this.name);
  }
};

const greetFunc = user.greet;
greetFunc(); // undefined (or error in strict mode)
```

**Why?** The function is called standalone, so `this` defaults to `global`.

**Diagram:**

```js
user.greet()  ──► this = user ✅
greetFunc()   ──► this = window/undefined ❌
```

**Fix with `.bind()`:**

```javascript
const boundGreet = user.greet.bind(user);
boundGreet(); // "Charlie"
```

---

### ***Case 4: Explicit Binding (`.call()`, `.apply()`, `.bind()`)***

```javascript
function introduce(age, city) {
  console.log(`${this.name}, ${age}, ${city}`);
}

const person = { name: "Diana" };

introduce.call(person, 25, "NYC");    // "Diana, 25, NYC"
introduce.apply(person, [25, "NYC"]); // "Diana, 25, NYC"
const bound = introduce.bind(person);
bound(25, "NYC");                     // "Diana, 25, NYC"
```

**Diagram:**

```js
introduce.call(person, ...)
          ▲
          │
     Forces 'this' to be 'person'
```

---

### ***Case 5: `new` Binding (Constructor Functions)***

```javascript
function Person(name) {
  this.name = name;
}

const p1 = new Person("Eve");
console.log(p1.name); // "Eve"
```

**What `new` does:**

1. Creates a new empty object: `{}`
2. Sets `this` to that object
3. Links prototype
4. Returns `this` (the new object)

**Diagram:**

```js
new Person("Eve")
     │
     ├─ Step 1: {} created
     ├─ Step 2: this = {}
     ├─ Step 3: this.name = "Eve"
     └─ Step 4: return this
```

---

### ***Case 6: Arrow Functions (Lexical `this`)***

```javascript
const team = {
  name: "DevTeam",
  members: ["Alice", "Bob"],
  showMembers() {
    this.members.forEach(function(member) {
      console.log(this.name, member); // undefined, member (this = global)
    });
  }
};

team.showMembers();
```

**Fix with Arrow Function:**

```javascript
const team = {
  name: "DevTeam",
  members: ["Alice", "Bob"],
  showMembers() {
    this.members.forEach(member => {
      console.log(this.name, member); // "DevTeam", member (this = team)
    });
  }
};

team.showMembers();
```

**Diagram:**

```js
Regular function:  this = ???  (depends on caller)
Arrow function:    this = inherited from outer scope
```

---

### ***Case 7: Event Listeners***

```javascript
const button = document.querySelector('button');

button.addEventListener('click', function() {
  console.log(this); // <button> element
});

button.addEventListener('click', () => {
  console.log(this); // Window (lexical this from outer scope)
});
```

---

### ***Case 8: Class Methods***

```javascript
class Counter {
  constructor() {
    this.count = 0;
  }
  
  increment() {
    this.count++;
  }
}

const c = new Counter();
c.increment();
console.log(c.count); // 1

// Losing context:
const inc = c.increment;
inc(); // Error: Cannot read 'count' of undefined

// Fix 1: Bind in constructor
class Counter {
  constructor() {
    this.count = 0;
    this.increment = this.increment.bind(this);
  }
  increment() {
    this.count++;
  }
}

// Fix 2: Arrow function (class field)
class Counter {
  count = 0;
  increment = () => {
    this.count++;
  }
}
```

---

## **Common Interview Traps**

### ***Trap 1: Losing context in callbacks***

```javascript
const obj = {
  name: "Test",
  greet() { console.log(this.name); }
};

setTimeout(obj.greet, 1000); // undefined

```

**Fix:** `setTimeout(obj.greet.bind(obj), 1000);`

---

### ***Trap 2: Arrow functions don't have `this`***

```javascript
const obj = {
  name: "Arrow",
  greet: () => console.log(this.name)
};

obj.greet(); // undefined (this = window, not obj)
```

---

### ***Trap 3: `this` in nested functions***

```javascript
const obj = {
  name: "Nested",
  outer() {
    function inner() {
      console.log(this.name); // undefined
    }
    inner();
  }
};

obj.outer();

```

**Fix:** Use arrow function or save `this`

```javascript
const self = this;
function inner() { console.log(self.name); }
```

---

## **Real-World Use Cases**

### ***React Class Components***

```javascript
class MyComponent extends React.Component {
  handleClick() {
    console.log(this.props); // Need to bind 'this'
  }
  
  render() {
    return <button onClick={this.handleClick.bind(this)}>Click</button>;
  }
}
```

### ***jQuery / DOM Manipulation***

```javascript
$('button').on('click', function() {
  $(this).addClass('active'); // 'this' = clicked button
});
```

### ***Method Chaining***

```javascript
class Calculator {
  constructor() { this.value = 0; }
  add(n) { this.value += n; return this; }
  multiply(n) { this.value *= n; return this; }
}

new Calculator().add(5).multiply(2); // Chaining works via 'return this'
```

---

## **Quick Reference Table**

| Context                          | `this` refers to                           |
| -------------------------------- | ------------------------------------------ |
| Global function                  | `window` / `global` / `undefined` (strict) |
| Object method                    | The object                                 |
| Constructor (`new`)              | New instance                               |
| `.call()`, `.apply()`, `.bind()` | Explicitly passed object                   |
| Arrow function                   | Lexical scope (outer `this`)               |
| Event listener                   | Event target (element)                     |
| Class method                     | Class instance                             |

---

## **Additional Resources**

- [MDN: this](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this)
- [JavaScript.info: Object methods, "this"](https://javascript.info/object-methods)
- [You Don't Know JS: this & Object Prototypes](https://github.com/getify/You-Dont-Know-JS)

---

**Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/) by Santosh Kumar Baliarsingh**
