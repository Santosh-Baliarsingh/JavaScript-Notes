<!-- markdownlint-disable MD036 -->
<!-- markdownlint-disable MD060 -->
<!-- markdownlint-disable MD024 -->

# JavaScript Objects & All Object Methods

## Table of Contents

- [JavaScript Objects \& All Object Methods](#javascript-objects--all-object-methods)
  - [Table of Contents](#table-of-contents)
  - [**Beginner Explanation**](#beginner-explanation)
    - [What is a JavaScript Object?](#what-is-a-javascript-object)
    - [How to Access Object Properties?](#how-to-access-object-properties)
    - [Adding and Modifying Properties](#adding-and-modifying-properties)
    - [Why Use Object Methods?](#why-use-object-methods)
  - [**Advanced Explanation**](#advanced-explanation)
    - [Internal Representation \& Memory Model](#internal-representation--memory-model)
    - [Property Descriptors - The Hidden Metadata](#property-descriptors---the-hidden-metadata)
    - [Prototype Chain - The Inheritance Mechanism](#prototype-chain---the-inheritance-mechanism)
    - [Object vs Map - When to Use What?](#object-vs-map---when-to-use-what)
    - [Memory \& Performance Implications](#memory--performance-implications)
    - [V8 Hidden Classes \& Optimization](#v8-hidden-classes--optimization)
    - [Deep vs Shallow Operations](#deep-vs-shallow-operations)
  - [**Understanding `this` Keyword**](#understanding-this-keyword)
    - [Beginner Explanation](#beginner-explanation-1)
    - [The `4` Rules of `this` Binding](#the-4-rules-of-this-binding)
      - [**1. Default Binding (Global Context)**](#1-default-binding-global-context)
      - [**2. Implicit Binding (Method Call)**](#2-implicit-binding-method-call)
      - [**3. Explicit Binding (call, apply, bind)**](#3-explicit-binding-call-apply-bind)
      - [**4. `new` Binding (Constructor Functions)**](#4-new-binding-constructor-functions)
    - [`Arrow Functions` and `this`](#arrow-functions-and-this)
    - [`this` in Event Handlers](#this-in-event-handlers)
    - [Priority of `this` Binding](#priority-of-this-binding)
    - [Common `this` Interview Questions](#common-this-interview-questions)
  - [**`Constructor Functions` \& `new` Operator**](#constructor-functions--new-operator)
    - [What is a Constructor Function?](#what-is-a-constructor-function)
    - [What Happens When You Use `new`?](#what-happens-when-you-use-new)
    - [What if You Forget `new`?](#what-if-you-forget-new)
    - [`Constructor` vs `Factory Function`](#constructor-vs-factory-function)
    - [Prototype Pattern (Memory Efficient)](#prototype-pattern-memory-efficient)
    - [Understanding the Prototype Chain](#understanding-the-prototype-chain)
    - [ES6 Classes (Syntactic Sugar)](#es6-classes-syntactic-sugar)
    - [Constructor Return Behavior](#constructor-return-behavior)
    - [Advanced: Custom `new` Implementation](#advanced-custom-new-implementation)
    - [Common Constructor Interview Questions](#common-constructor-interview-questions)
  - [**Creating Objects**](#creating-objects)
    - [`Object.create(proto, [descriptors])`](#objectcreateproto-descriptors)
    - [`Object.assign(target, ...sources)`](#objectassigntarget-sources)
  - [**Property Inspection**](#property-inspection)
    - [`Object.keys(obj)`](#objectkeysobj)
    - [`Object.values(obj)`](#objectvaluesobj)
    - [`Object.entries(obj)`](#objectentriesobj)
    - [`Object.getOwnPropertyNames(obj)`](#objectgetownpropertynamesobj)
    - [`Object.getOwnPropertySymbols(obj)`](#objectgetownpropertysymbolsobj)
  - [**Property Descriptors**](#property-descriptors)
    - [`Object.getOwnPropertyDescriptor(obj, prop)`](#objectgetownpropertydescriptorobj-prop)
    - [`Object.getOwnPropertyDescriptors(obj)`](#objectgetownpropertydescriptorsobj)
    - [`Object.defineProperty(obj, prop, descriptor)`](#objectdefinepropertyobj-prop-descriptor)
    - [`Object.defineProperties(obj, descriptors)`](#objectdefinepropertiesobj-descriptors)
  - [**Prototype Methods**](#prototype-methods)
    - [`Object.getPrototypeOf(obj)`](#objectgetprototypeofobj)
    - [`Object.setPrototypeOf(obj, proto)`](#objectsetprototypeofobj-proto)
  - [**Object Protection**](#object-protection)
    - [`Object.preventExtensions(obj)`](#objectpreventextensionsobj)
    - [`Object.seal(obj)`](#objectsealobj)
    - [`Object.freeze(obj)`](#objectfreezeobj)
  - [**Comparison \& Checking**](#comparison--checking)
    - [`Object.is(val1, val2)`](#objectisval1-val2)
    - [`Object.hasOwn(obj, prop)` *(ES2022)*](#objecthasownobj-prop-es2022)
  - [**Conversion Methods**](#conversion-methods)
    - [`Object.fromEntries(entries)`](#objectfromentriesentries)
  - [**Instance Methods (on `Object.prototype`)**](#instance-methods-on-objectprototype)
    - [`obj.hasOwnProperty(prop)`](#objhasownpropertyprop)
    - [`obj.toString()`](#objtostring)
    - [`obj.valueOf()`](#objvalueof)
    - [`obj.propertyIsEnumerable(prop)`](#objpropertyisenumerableprop)
  - [**Interview Traps**](#interview-traps)
    - [**Trap 1: Shallow Copy**](#trap-1-shallow-copy)
    - [**Trap 2: Freeze is Shallow**](#trap-2-freeze-is-shallow)
    - [**Trap 3: Keys vs GetOwnPropertyNames**](#trap-3-keys-vs-getownpropertynames)
    - [**Trap 4: Object.is vs ===**](#trap-4-objectis-vs-)
    - [**Trap 5: Prototype Pollution**](#trap-5-prototype-pollution)
  - [**Real-World Use Cases**](#real-world-use-cases)
    - [**1. Clone \& Merge Objects**](#1-clone--merge-objects)
    - [**2. Object to Query String**](#2-object-to-query-string)
    - [**3. Filter Object**](#3-filter-object)
    - [**4. Immutable Config**](#4-immutable-config)
    - [**5. Detect Changes**](#5-detect-changes)
    - [**6. Group By**](#6-group-by)
    - [**7. Pick Properties**](#7-pick-properties)
    - [**8. Omit Properties**](#8-omit-properties)
  - [**Quick Reference Table**](#quick-reference-table)
  - [**Advanced Patterns**](#advanced-patterns)
    - [**Create Immutable Object with Getters**](#create-immutable-object-with-getters)
    - [**Object Factory with Private Variables**](#object-factory-with-private-variables)
    - [**Proxy for Validation**](#proxy-for-validation)
  - [**Performance Tips**](#performance-tips)

---

## **Beginner Explanation**

### What is a JavaScript Object?

Think of an object as a **container that stores related information**. Just like a real-world object (like a person or a car) has properties (name, age, color), JavaScript objects store data in **key-value pairs**.

```javascript
// Creating an object
const person = {
  name: "Alice",        // property: name
  age: 25,              // property: age
  city: "New York",     // property: city
  greet: function() {   // method: greet
    console.log("Hello!");
  }
};
```

### How to Access Object Properties?

**Two ways:**

1. **Dot notation** (most common):

   ```javascript
   console.log(person.name);  // "Alice"
   console.log(person.age);   // 25
   person.greet();            // "Hello!"
   ```

2. **Bracket notation** (when property name has spaces or is dynamic):

   ```javascript
   console.log(person["name"]);     // "Alice"
   const prop = "age";
   console.log(person[prop]);       // 25
   ```

### Adding and Modifying Properties

```javascript
// Add new property
person.job = "Engineer";

// Modify existing property
person.age = 26;

// Delete property
delete person.city;

console.log(person);
// { name: "Alice", age: 26, job: "Engineer", greet: [Function] }
```

### Why Use Object Methods?

JavaScript provides built-in methods to work with objects efficiently:

```javascript
// Get all property names
Object.keys(person);     // ["name", "age", "job", "greet"]

// Get all values
Object.values(person);   // ["Alice", 26, "Engineer", [Function]]

// Copy an object
const copy = Object.assign({}, person);

// Make object read-only
Object.freeze(person);
person.age = 30;  // Won't work!
```

---

## **Advanced Explanation**

### Internal Representation & Memory Model

Objects in JavaScript are **reference types** stored in heap memory. When you create an object, JavaScript:

1. **Allocates heap memory** for the object data
2. **Returns a reference** (pointer) to that memory location
3. **Stores the reference** in the variable

```javascript
const obj1 = { a: 1 };
const obj2 = obj1;  // Copies reference, NOT the object

obj2.a = 2;
console.log(obj1.a); // 2 (both point to same object!)

// Comparison compares references
console.log(obj1 === obj2);  // true (same reference)
console.log({ a: 1 } === { a: 1 });  // false (different references)
```

### Property Descriptors - The Hidden Metadata

Every property has **four hidden attributes** that control its behavior:

```javascript
const obj = { name: "Alice" };

Object.getOwnPropertyDescriptor(obj, 'name');
// {
//   value: "Alice",           // The actual value
//   writable: true,           // Can the value be changed?
//   enumerable: true,         // Will it show in for...in loops?
//   configurable: true        // Can the property be deleted?
// }
```

**Why this matters:**

```javascript
// Make a property read-only
Object.defineProperty(obj, 'id', {
  value: 123,
  writable: false,      // Cannot modify
  enumerable: false,    // Won't show in Object.keys()
  configurable: false   // Cannot delete
});

obj.id = 456;  // Silent fail (throws error in strict mode)
console.log(obj.id);  // 123

Object.keys(obj);  // ["name"] - id is not enumerable!
delete obj.id;     // Fails silently
```

### Prototype Chain - The Inheritance Mechanism

Every object has a hidden `[[Prototype]]` link that enables inheritance:

```javascript
const animal = {
  eats: true,
  walk() {
    console.log("Walking...");
  }
};

const dog = Object.create(animal);
dog.barks = true;

// Prototype chain: dog → animal → Object.prototype → null
console.log(dog.barks);  // true (own property)
console.log(dog.eats);   // true (inherited from animal)
dog.walk();              // "Walking..." (inherited method)

Object.getPrototypeOf(dog) === animal;  // true
```

**How Property Lookup Works:**

1. Check if property exists on the object itself
2. If not, check the prototype
3. If not, check the prototype's prototype
4. Continue until reaching `null`

```javascript
dog.toString();  // "[object Object]"
// Lookup chain: dog → animal → Object.prototype.toString()
```

### Object vs Map - When to Use What?

| Feature | Object | Map |
|---------|--------|-----|
| **Key types** | Strings, Symbols only | Any type (objects, functions, primitives) |
| **Order** | Not guaranteed (mostly insertion order) | Guaranteed insertion order |
| **Size** | Manual (`Object.keys().length`) | Built-in (`map.size`) |
| **Performance** | Slower for frequent add/delete | Optimized for frequent operations |
| **Iteration** | `for...in`, `Object.keys()` | `map.forEach()`, `for...of` |
| **JSON** | ✅ `JSON.stringify()` works | ❌ Not directly serializable |

**Use Object when:**

- Working with JSON data
- Need property access syntax (`obj.prop`)
- Fixed set of known properties

**Use Map when:**

- Keys are not strings
- Frequent additions/deletions
- Need guaranteed iteration order
- Performance-critical operations

### Memory & Performance Implications

```javascript
// ❌ BAD: Creating objects in loops
for (let i = 0; i < 10000; i++) {
  const obj = { id: i, data: "test" };  // Creates 10k objects!
}

// ✅ GOOD: Reuse object structure
const cache = new Map();
for (let i = 0; i < 10000; i++) {
  cache.set(i, { id: i, data: "test" });
}

// ✅ GOOD: Object pooling for performance
class ObjectPool {
  constructor() {
    this.pool = [];
  }
  
  acquire() {
    return this.pool.pop() || {};
  }
  
  release(obj) {
    Object.keys(obj).forEach(key => delete obj[key]);
    this.pool.push(obj);
  }
}
```

### V8 Hidden Classes & Optimization

V8 engine optimizes objects using **hidden classes**. Objects with the same property order share hidden classes:

```javascript
// ✅ GOOD: Same order = same hidden class = optimized
function createPoint1(x, y) {
  return { x, y };
}

const p1 = createPoint1(1, 2);
const p2 = createPoint1(3, 4);
// Both share hidden class - FAST!

// ❌ BAD: Different order = different hidden classes
function createPoint2(x, y, random) {
  const obj = {};
  if (random) {
    obj.y = y;
    obj.x = x;  // Different order!
  } else {
    obj.x = x;
    obj.y = y;
  }
  return obj;
}
// Creates multiple hidden classes - SLOW!
```

**Best Practices for Performance:**

1. Initialize all properties in constructor
2. Keep property order consistent
3. Avoid adding properties after creation
4. Avoid deleting properties (marks object as "slow")
5. Use `Object.freeze()` for constants (enables optimizations)

### Deep vs Shallow Operations

Most built-in methods are **shallow**:

```javascript
const original = {
  name: "Alice",
  address: {
    city: "NYC",
    zip: 10001
  }
};

// Shallow copy
const copy = Object.assign({}, original);
copy.address.city = "LA";
console.log(original.address.city);  // "LA" ⚠️

// Shallow freeze
Object.freeze(original);
original.name = "Bob";  // ❌ Fails
original.address.city = "Boston";  // ✅ Works! (nested not frozen)

// Solutions for deep operations:
// 1. JSON (loses functions, dates, undefined)
const deepCopy1 = JSON.parse(JSON.stringify(original));

// 2. structuredClone (modern, preserves more types)
const deepCopy2 = structuredClone(original);

// 3. Recursive deep freeze
function deepFreeze(obj) {
  Object.freeze(obj);
  Object.values(obj).forEach(val => {
    if (typeof val === 'object' && val !== null) {
      deepFreeze(val);
    }
  });
  return obj;
}
```

---

## **Understanding `this` Keyword**

### Beginner Explanation

`this` is a special keyword that refers to the **object that is executing the current function**. Think of it as "who is calling this function right now?"

```javascript
const person = {
  name: "Alice",
  greet: function() {
    console.log("Hello, I'm " + this.name);
    // 'this' refers to 'person' object
  }
};

person.greet();  // "Hello, I'm Alice"
```

### The `4` Rules of `this` Binding

#### **1. Default Binding (Global Context)**

When a function is called without any context, `this` refers to the global object (or `undefined` in strict mode).

```javascript
function showThis() {
  console.log(this);
}

showThis();  
// Browser: Window object
// Node.js: global object
// Strict mode: undefined
```

#### **2. Implicit Binding (Method Call)**

When a function is called as a method of an object, `this` refers to that object.

```javascript
const user = {
  name: "Bob",
  age: 30,
  showInfo: function() {
    console.log(this.name, this.age);
  }
};

user.showInfo();  // "Bob 30" - 'this' is 'user'
```

**⚠️ Common Trap - Losing Context:**

```javascript
const user = {
  name: "Bob",
  greet: function() {
    console.log("Hello " + this.name);
  }
};

const greetFunc = user.greet;
greetFunc();  // "Hello undefined" ⚠️
// 'this' is now global object, not 'user'!

// Solution 1: Use bind
const boundGreet = user.greet.bind(user);
boundGreet();  // "Hello Bob" ✅

// Solution 2: Arrow function
const user2 = {
  name: "Bob",
  greet: () => {
    console.log("Hello " + this.name);  // Lexical 'this'
  }
};
```

#### **3. Explicit Binding (call, apply, bind)**

You can explicitly set what `this` should be using `call()`, `apply()`, or `bind()`.

```javascript
function introduce(greeting, punctuation) {
  console.log(greeting + ", I'm " + this.name + punctuation);
}

const person = { name: "Alice" };

// call() - pass arguments individually
introduce.call(person, "Hello", "!");  
// "Hello, I'm Alice!"

// apply() - pass arguments as array
introduce.apply(person, ["Hi", "."]);  
// "Hi, I'm Alice."

// bind() - returns new function with bound 'this'
const boundIntroduce = introduce.bind(person);
boundIntroduce("Hey", "!!!");  
// "Hey, I'm Alice!!!"
```

**Difference between call, apply, bind:**

```javascript
const obj = { name: "Test" };

function sum(a, b, c) {
  console.log(this.name, a + b + c);
}

// call - invoke immediately, args individually
sum.call(obj, 1, 2, 3);  // "Test 6"

// apply - invoke immediately, args as array
sum.apply(obj, [1, 2, 3]);  // "Test 6"

// bind - returns new function, doesn't invoke
const boundSum = sum.bind(obj, 1, 2, 3);
boundSum();  // "Test 6"

// Partial application with bind
const partialSum = sum.bind(obj, 1, 2);
partialSum(3);  // "Test 6"
```

#### **4. `new` Binding (Constructor Functions)**

When a function is called with `new`, `this` refers to the newly created object.

```javascript
function Person(name, age) {
  this.name = name;
  this.age = age;
}

const alice = new Person("Alice", 25);
console.log(alice.name);  // "Alice"
// 'this' referred to the new object being created
```

### `Arrow Functions` and `this`

**Arrow functions don't have their own `this`**. They inherit `this` from the surrounding scope (`lexical scoping`).

```javascript
// Regular function - 'this' depends on how it's called
const obj1 = {
  name: "Regular",
  greet: function() {
    console.log(this.name);
  }
};

// Arrow function - 'this' is lexically bound
const obj2 = {
  name: "Arrow",
  greet: () => {
    console.log(this.name);  // 'this' from outer scope!
  }
};

obj1.greet();  // "Regular" ✅
obj2.greet();  // undefined ⚠️ ('this' is global, not obj2)
```

**When Arrow Functions are Useful:**

```javascript
const counter = {
  count: 0,
  start: function() {
    // Regular function - would lose 'this' context
    setInterval(function() {
      this.count++;  // ❌ 'this' is global/undefined
      console.log(this.count);
    }, 1000);
  }
};

const counter2 = {
  count: 0,
  start: function() {
    // Arrow function - preserves 'this' context
    setInterval(() => {
      this.count++;  // ✅ 'this' is counter2
      console.log(this.count);
    }, 1000);
  }
};

counter2.start();  // 1, 2, 3, 4...
```

### `this` in Event Handlers

```javascript
const button = document.querySelector('button');

// Regular function - 'this' is the element
button.addEventListener('click', function() {
  console.log(this);  // <button> element
});

// Arrow function - 'this' is lexical (outer scope)
button.addEventListener('click', () => {
  console.log(this);  // Window or outer context
});

// Best practice - use event parameter
button.addEventListener('click', (event) => {
  console.log(event.target);  // <button> element
});
```

### Priority of `this` Binding

When multiple rules apply, this is the order of precedence:

1. **`new` binding** (highest priority)
2. **Explicit binding** (call, apply, bind)
3. **Implicit binding** (method call)
4. **Default binding** (lowest priority)

```javascript
function test() {
  console.log(this.name);
}

const obj1 = { name: "Object 1", test };
const obj2 = { name: "Object 2" };

// Implicit binding
obj1.test();  // "Object 1"

// Explicit binding overrides implicit
obj1.test.call(obj2);  // "Object 2"

// 'new' binding overrides explicit
const boundTest = test.bind(obj1);
const instance = new boundTest();  
// 'this' is new object, not obj1
```

### Common `this` Interview Questions

**Q1: What will this output?**

```javascript
const obj = {
  name: "Object",
  getName: function() {
    return this.name;
  }
};

const getName = obj.getName;
console.log(getName());  // undefined (lost context)
```

**Q2: Fix this code:**

```javascript
const obj = {
  count: 0,
  increment: function() {
    setTimeout(function() {
      this.count++;  // ❌ Wrong 'this'
    }, 1000);
  }
};

// Solution 1: Arrow function
const obj1 = {
  count: 0,
  increment: function() {
    setTimeout(() => {
      this.count++;  // ✅
    }, 1000);
  }
};

// Solution 2: Store reference
const obj2 = {
  count: 0,
  increment: function() {
    const self = this;
    setTimeout(function() {
      self.count++;  // ✅
    }, 1000);
  }
};

// Solution 3: bind
const obj3 = {
  count: 0,
  increment: function() {
    setTimeout(function() {
      this.count++;  // ✅
    }.bind(this), 1000);
  }
};
```

---

## **`Constructor Functions` & `new` Operator**

### What is a Constructor Function?

A constructor function is a regular function used to create multiple objects with the same structure. By convention, constructor names start with a capital letter.

```javascript
function Person(name, age) {
  this.name = name;
  this.age = age;
  this.greet = function() {
    console.log("Hi, I'm " + this.name);
  };
}

const alice = new Person("Alice", 25);
const bob = new Person("Bob", 30);

alice.greet();  // "Hi, I'm Alice"
bob.greet();    // "Hi, I'm Bob"
```

### What Happens When You Use `new`?

When you call a function with `new`, JavaScript automatically does 4 things:

```javascript
function Person(name) {
  // 1. Create empty object: const this = {};
  // 2. Set prototype: this.__proto__ = Person.prototype;
  this.name = name;
  // 3. Return this object: return this;
}

const alice = new Person("Alice");
```

**Step-by-step breakdown:**

```javascript
function Person(name, age) {
  // Step 1: A new empty object is created
  // let this = {};
  
  // Step 2: The new object's [[Prototype]] is set to Person.prototype
  // this.__proto__ = Person.prototype;
  
  // Step 3: The constructor function is executed with 'this' bound to the new object
  this.name = name;
  this.age = age;
  
  // Step 4: The new object is returned (implicit return)
  // return this;
}

const person1 = new Person("Alice", 25);
console.log(person1);  // Person { name: "Alice", age: 25 }
```

### What if You Forget `new`?

```javascript
function Person(name) {
  this.name = name;
}

// ❌ Without 'new' - 'this' is global object!
const person = Person("Alice");
console.log(person);  // undefined
console.log(window.name);  // "Alice" (polluted global!)

// ✅ With 'new'
const person2 = new Person("Bob");
console.log(person2.name);  // "Bob"
```

**Safeguard Against Missing `new`:**

```javascript
function Person(name) {
  // Check if called with 'new'
  if (!(this instanceof Person)) {
    return new Person(name);  // Call it correctly
  }
  this.name = name;
}

// Works both ways now
const p1 = new Person("Alice");
const p2 = Person("Bob");  // Automatically calls with 'new'
```

### `Constructor` vs `Factory Function`

**Constructor Function:**

```javascript
function Person(name, age) {
  this.name = name;
  this.age = age;
}

const alice = new Person("Alice", 25);  // Need 'new'
```

**Factory Function (returns object):**

```javascript
function createPerson(name, age) {
  return {
    name,
    age,
    greet() {
      console.log(`Hi, I'm ${this.name}`);
    }
  };
}

const bob = createPerson("Bob", 30);  // No 'new' needed
```

**Comparison:**

| Feature | Constructor | Factory |
|---------|------------|---------|
| **`Syntax`** | `new Person()` | `createPerson()` |
| **`this binding`** | Automatic | Manual |
| **`Prototype`** | Shared methods | Each object has own methods |
| **`Memory`** | More efficient | Less efficient (if methods included) |
| **`Flexibility`** | Less flexible | More flexible (can return anything) |

### Prototype Pattern (Memory Efficient)

**❌ Problem: Each instance gets its own copy of methods**

```javascript
function Person(name) {
  this.name = name;
  this.greet = function() {  // New function for EACH instance!
    console.log("Hi, I'm " + this.name);
  };
}

const alice = new Person("Alice");
const bob = new Person("Bob");

console.log(alice.greet === bob.greet);  // false ⚠️
// Each has separate function in memory
```

**✅ Solution: Use prototype to share methods**

```javascript
function Person(name) {
  this.name = name;
}

// Add method to prototype (shared by all instances)
Person.prototype.greet = function() {
  console.log("Hi, I'm " + this.name);
};

const alice = new Person("Alice");
const bob = new Person("Bob");

console.log(alice.greet === bob.greet);  // true ✅
// Same function reference in memory!

alice.greet();  // "Hi, I'm Alice"
bob.greet();    // "Hi, I'm Bob"
```

### Understanding the Prototype Chain

```javascript
function Person(name) {
  this.name = name;
}

Person.prototype.species = "Homo sapiens";
Person.prototype.greet = function() {
  console.log("Hello!");
};

const alice = new Person("Alice");

// Property lookup chain:
console.log(alice.name);     // "Alice" (own property)
console.log(alice.species);  // "Homo sapiens" (from Person.prototype)
console.log(alice.toString());  // "[object Object]" (from Object.prototype)

// Check where property exists
console.log(alice.hasOwnProperty('name'));     // true
console.log(alice.hasOwnProperty('species'));  // false
console.log('species' in alice);               // true (checks chain)

// The chain:
// alice → Person.prototype → Object.prototype → null
```

### ES6 Classes (Syntactic Sugar)

ES6 classes are just syntactic sugar over constructor functions and prototypes:

```javascript
// ES6 Class syntax
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }
  
  greet() {
    console.log(`Hi, I'm ${this.name}`);
  }
  
  static species = "Homo sapiens";  // Static property
  
  static info() {  // Static method
    console.log("This is the Person class");
  }
}

const alice = new Person("Alice", 25);
alice.greet();  // "Hi, I'm Alice"
Person.info();  // "This is the Person class"

// Under the hood, it's doing:
// function Person(name, age) { ... }
// Person.prototype.greet = function() { ... }
```

### Constructor Return Behavior

```javascript
// Normal: returns 'this' (the new object)
function Person(name) {
  this.name = name;
}
const p1 = new Person("Alice");
console.log(p1);  // Person { name: "Alice" }

// If you return primitive: ignored
function Person2(name) {
  this.name = name;
  return 42;  // Ignored
}
const p2 = new Person2("Bob");
console.log(p2);  // Person2 { name: "Bob" }

// If you return object: that object is returned instead
function Person3(name) {
  this.name = name;
  return { custom: "object" };  // This is returned!
}
const p3 = new Person3("Charlie");
console.log(p3);  // { custom: "object" }
```

### Advanced: Custom `new` Implementation

```javascript
function myNew(constructor, ...args) {
  // 1. Create new object with constructor's prototype
  const obj = Object.create(constructor.prototype);
  
  // 2. Execute constructor with 'this' bound to new object
  const result = constructor.apply(obj, args);
  
  // 3. Return object (or constructor's return value if it's an object)
  return (typeof result === 'object' && result !== null) ? result : obj;
}

// Usage
function Person(name, age) {
  this.name = name;
  this.age = age;
}

const alice = myNew(Person, "Alice", 25);
console.log(alice);  // Person { name: "Alice", age: 25 }
```

### Common Constructor Interview Questions

**Q1: What's wrong with this code?**

```javascript
function Car(brand) {
  this.brand = brand;
  this.drive = function() {
    console.log("Driving " + this.brand);
  };
}

const car1 = new Car("Toyota");
const car2 = new Car("Honda");

console.log(car1.drive === car2.drive);  // false ⚠️
// Problem: Each instance has its own drive function (memory waste)

// Solution: Move to prototype
Car.prototype.drive = function() {
  console.log("Driving " + this.brand);
};
```

**Q2: Implement inheritance without ES6 classes:**

```javascript
function Animal(name) {
  this.name = name;
}

Animal.prototype.eat = function() {
  console.log(this.name + " is eating");
};

function Dog(name, breed) {
  Animal.call(this, name);  // Call parent constructor
  this.breed = breed;
}

// Set up prototype chain
Dog.prototype = Object.create(Animal.prototype);
Dog.prototype.constructor = Dog;

Dog.prototype.bark = function() {
  console.log(this.name + " is barking");
};

const dog = new Dog("Buddy", "Golden Retriever");
dog.eat();   // "Buddy is eating" (inherited)
dog.bark();  // "Buddy is barking" (own method)
```

**Q3: Why does this fail?**

```javascript
function Person(name) {
  this.name = name;
  setTimeout(function() {
    console.log(this.name);  // undefined ⚠️
  }, 1000);
}

new Person("Alice");

// Problem: 'this' in setTimeout refers to global object

// Solution 1: Arrow function
function Person(name) {
  this.name = name;
  setTimeout(() => {
    console.log(this.name);  // ✅ "Alice"
  }, 1000);
}

// Solution 2: bind
function Person(name) {
  this.name = name;
  setTimeout(function() {
    console.log(this.name);  // ✅ "Alice"
  }.bind(this), 1000);
}
```

---

## **Creating Objects**

### `Object.create(proto, [descriptors])`

Creates new object with specified prototype

```javascript
const proto = { greet() { console.log("Hi"); } };
const obj = Object.create(proto);
obj.greet(); // "Hi"

// With descriptors
const obj2 = Object.create(null, {
  name: {
    value: "Alice",
    writable: true,
    enumerable: true,
    configurable: true
  }
});
```

### `Object.assign(target, ...sources)`

Copies properties from sources to target (shallow copy)

```javascript
const target = { a: 1 };
const source = { b: 2, c: 3 };
Object.assign(target, source);
console.log(target); // { a: 1, b: 2, c: 3 }

// Clone object
const clone = Object.assign({}, obj);

// Merge multiple
const merged = Object.assign({}, obj1, obj2, obj3);
```

---

## **Property Inspection**

### `Object.keys(obj)`

Returns array of enumerable own property **keys**

```javascript
const obj = { a: 1, b: 2, c: 3 };
Object.keys(obj); // ["a", "b", "c"]
```

### `Object.values(obj)`

Returns array of enumerable own property **values**

```javascript
const obj = { a: 1, b: 2, c: 3 };
Object.values(obj); // [1, 2, 3]
```

### `Object.entries(obj)`

Returns array of `[key, value]` pairs

```javascript
const obj = { a: 1, b: 2, c: 3 };
Object.entries(obj); // [["a", 1], ["b", 2], ["c", 3]]

// Useful for iteration
Object.entries(obj).forEach(([key, val]) => {
  console.log(`${key}: ${val}`);
});
```

### `Object.getOwnPropertyNames(obj)`

Returns all own properties (including non-enumerable)

```javascript
const obj = Object.create({}, {
  prop: { value: 1, enumerable: false }
});
Object.keys(obj); // []
Object.getOwnPropertyNames(obj); // ["prop"]
```

### `Object.getOwnPropertySymbols(obj)`

Returns array of Symbol properties

```javascript
const sym = Symbol('id');
const obj = { [sym]: 123, name: "Alice" };
Object.getOwnPropertySymbols(obj); // [Symbol(id)]
```

---

## **Property Descriptors**

### `Object.getOwnPropertyDescriptor(obj, prop)`

Get descriptor for single property

```javascript
const obj = { name: "Alice" };
Object.getOwnPropertyDescriptor(obj, 'name');
// { value: "Alice", writable: true, enumerable: true, configurable: true }
```

### `Object.getOwnPropertyDescriptors(obj)`

Get all descriptors

```javascript
const obj = { name: "Alice", age: 25 };
Object.getOwnPropertyDescriptors(obj);
// {
//   name: { value: "Alice", writable: true, enumerable: true, configurable: true },
//   age: { value: 25, writable: true, enumerable: true, configurable: true }
// }
```

### `Object.defineProperty(obj, prop, descriptor)`

Define/modify single property

```javascript
const obj = {};
Object.defineProperty(obj, 'name', {
  value: "Alice",
  writable: false,    // read-only
  enumerable: true,
  configurable: false // can't delete
});

obj.name = "Bob"; // Silent fail (strict mode: error)
console.log(obj.name); // "Alice"
```

### `Object.defineProperties(obj, descriptors)`

Define multiple properties

```javascript
const obj = {};
Object.defineProperties(obj, {
  firstName: {
    value: "John",
    writable: true
  },
  lastName: {
    value: "Doe",
    writable: true
  }
});
```

---

## **Prototype Methods**

### `Object.getPrototypeOf(obj)`

Get prototype of object

```javascript
const arr = [];
Object.getPrototypeOf(arr) === Array.prototype; // true

function Dog() {}
const dog = new Dog();
Object.getPrototypeOf(dog) === Dog.prototype; // true
```

### `Object.setPrototypeOf(obj, proto)`

Set prototype (⚠️ slow, use `Object.create` instead)

```javascript
const animal = { eats: true };
const dog = { barks: true };
Object.setPrototypeOf(dog, animal);
dog.eats; // true
```

---

## **Object Protection**

### `Object.preventExtensions(obj)`

Prevent adding new properties

```javascript
const obj = { name: "Alice" };
Object.preventExtensions(obj);
obj.age = 25; // Silent fail
console.log(obj.age); // undefined

Object.isExtensible(obj); // false
```

### `Object.seal(obj)`

Prevent adding/deleting properties (can modify existing)

```javascript
const obj = { name: "Alice" };
Object.seal(obj);

obj.name = "Bob";  // ✅ Works
obj.age = 25;      // ❌ Fails
delete obj.name;   // ❌ Fails

Object.isSealed(obj); // true
```

### `Object.freeze(obj)`

Make object completely immutable (shallow)

```javascript
const obj = { name: "Alice", skills: ["JS"] };
Object.freeze(obj);

obj.name = "Bob";         // ❌ Fails
obj.age = 25;             // ❌ Fails
delete obj.name;          // ❌ Fails
obj.skills.push("React"); // ✅ Works (nested not frozen!)

Object.isFrozen(obj); // true
```

**Deep Freeze** (recursive):

```javascript
function deepFreeze(obj) {
  Object.freeze(obj);
  Object.values(obj).forEach(val => {
    if (typeof val === 'object' && val !== null) {
      deepFreeze(val);
    }
  });
  return obj;
}
```

---

## **Comparison & Checking**

### `Object.is(val1, val2)`

Same-value equality (better than `===`)

```javascript
Object.is(25, 25);          // true
Object.is(NaN, NaN);        // true  (unlike ===)
Object.is(-0, +0);          // false (unlike ===)
Object.is({}, {});          // false (different references)
```

### `Object.hasOwn(obj, prop)` *(ES2022)*

Check if object has own property (better than `hasOwnProperty`)

```javascript
const obj = { name: "Alice" };
Object.hasOwn(obj, 'name');     // true
Object.hasOwn(obj, 'toString'); // false (inherited)

// Old way (avoid):
obj.hasOwnProperty('name'); // true
```

---

## **Conversion Methods**

### `Object.fromEntries(entries)`

Convert entries to object (reverse of `Object.entries`)

```javascript
const entries = [["a", 1], ["b", 2]];
Object.fromEntries(entries); // { a: 1, b: 2 }

// Use case: filter object
const obj = { a: 1, b: 2, c: 3 };
const filtered = Object.fromEntries(
  Object.entries(obj).filter(([k, v]) => v > 1)
);
// { b: 2, c: 3 }
```

---

## **Instance Methods (on `Object.prototype`)**

### `obj.hasOwnProperty(prop)`

Check own property (use `Object.hasOwn` instead)

```javascript
const obj = { name: "Alice" };
obj.hasOwnProperty('name');     // true
obj.hasOwnProperty('toString'); // false
```

### `obj.toString()`

Convert object to string

```javascript
const obj = { name: "Alice" };
obj.toString(); // "[object Object]"

// Override for custom output
obj.toString = function() {
  return `Person: ${this.name}`;
};
obj.toString(); // "Person: Alice"
```

### `obj.valueOf()`

Return primitive value

```javascript
const obj = { value: 42 };
obj.valueOf(); // { value: 42 } (returns itself)

// Custom valueOf
obj.valueOf = function() { return this.value; };
obj + 10; // 52
```

### `obj.propertyIsEnumerable(prop)`

Check if property is enumerable

```javascript
const obj = { name: "Alice" };
obj.propertyIsEnumerable('name'); // true

Object.defineProperty(obj, 'id', {
  value: 123,
  enumerable: false
});
obj.propertyIsEnumerable('id'); // false
```

---

## **Interview Traps**

### **Trap 1: Shallow Copy**

```javascript
const obj = { a: 1, nested: { b: 2 } };
const copy = Object.assign({}, obj);
copy.nested.b = 999;
console.log(obj.nested.b); // 999 ⚠️

// Solution: Deep clone
const deepCopy = JSON.parse(JSON.stringify(obj));
// Or use structuredClone (modern browsers)
const deepCopy2 = structuredClone(obj);
```

### **Trap 2: Freeze is Shallow**

```javascript
const obj = Object.freeze({ arr: [1, 2] });
obj.arr.push(3); // ✅ Works! Array not frozen
console.log(obj.arr); // [1, 2, 3]
```

### **Trap 3: Keys vs GetOwnPropertyNames**

```javascript
const obj = Object.create({}, {
  hidden: { value: 1, enumerable: false }
});
Object.keys(obj); // []
Object.getOwnPropertyNames(obj); // ["hidden"]
```

### **Trap 4: Object.is vs ===**

```javascript
NaN === NaN;        // false
Object.is(NaN, NaN); // true

-0 === +0;          // true
Object.is(-0, +0);  // false
```

### **Trap 5: Prototype Pollution**

```javascript
const obj = {};
obj.__proto__.polluted = "hacked";
const newObj = {};
console.log(newObj.polluted); // "hacked" ⚠️

// Always use Object.create(null) for dictionaries
const safeObj = Object.create(null);
```

---

## **Real-World Use Cases**

### **1. Clone & Merge Objects**

```javascript
const defaults = { theme: "dark", lang: "en" };
const userPrefs = { theme: "light" };
const config = Object.assign({}, defaults, userPrefs);
// { theme: "light", lang: "en" }
```

### **2. Object to Query String**

```javascript
const params = { page: 1, limit: 10, sort: "asc" };
const query = Object.entries(params)
  .map(([k, v]) => `${k}=${v}`)
  .join('&');
// "page=1&limit=10&sort=asc"
```

### **3. Filter Object**

```javascript
const user = { name: "Alice", age: 25, password: "123" };
const safe = Object.fromEntries(
  Object.entries(user).filter(([k]) => k !== 'password')
);
// { name: "Alice", age: 25 }
```

### **4. Immutable Config**

```javascript
const config = Object.freeze({
  API_KEY: "abc123",
  BASE_URL: "https://api.example.com"
});
```

### **5. Detect Changes**

```javascript
function trackChanges(obj) {
  const original = Object.assign({}, obj);
  return {
    get changes() {
      return Object.entries(obj).filter(
        ([k, v]) => original[k] !== v
      );
    }
  };
}
```

### **6. Group By**

```javascript
const items = [
  { category: "fruit", name: "apple" },
  { category: "fruit", name: "banana" },
  { category: "veg", name: "carrot" }
];

const grouped = Object.groupBy(items, item => item.category);
// {
//   fruit: [{category: "fruit", name: "apple"}, {category: "fruit", name: "banana"}],
//   veg: [{category: "veg", name: "carrot"}]
// }
```

### **7. Pick Properties**

```javascript
function pick(obj, keys) {
  return Object.fromEntries(
    keys.filter(key => key in obj).map(key => [key, obj[key]])
  );
}

const user = { id: 1, name: "Alice", password: "secret", age: 25 };
const safe = pick(user, ['id', 'name', 'age']);
// { id: 1, name: "Alice", age: 25 }
```

### **8. Omit Properties**

```javascript
function omit(obj, keys) {
  return Object.fromEntries(
    Object.entries(obj).filter(([key]) => !keys.includes(key))
  );
}

const user = { id: 1, name: "Alice", password: "secret" };
const safe = omit(user, ['password']);
// { id: 1, name: "Alice" }
```

---

## **Quick Reference Table**

| Method | Purpose | Returns |
|--------|---------|---------|
| `Object.keys()` | Get enumerable keys | Array |
| `Object.values()` | Get enumerable values | Array |
| `Object.entries()` | Get [key, value] pairs | Array |
| `Object.assign()` | Merge objects (shallow) | Target object |
| `Object.create()` | Create with prototype | New object |
| `Object.freeze()` | Make immutable (shallow) | Object |
| `Object.seal()` | Prevent add/delete | Object |
| `Object.is()` | Strict equality | Boolean |
| `Object.hasOwn()` | Check own property | Boolean |
| `Object.fromEntries()` | Convert entries to object | Object |
| `Object.getPrototypeOf()` | Get prototype | Object/null |
| `Object.defineProperty()` | Define property | Object |

---

## **Advanced Patterns**

### **Create Immutable Object with Getters**

```javascript
function createUser(name, age) {
  return Object.freeze({
    get name() { return name; },
    get age() { return age; },
    get info() { return `${name} is ${age} years old`; }
  });
}

const user = createUser("Alice", 25);
user.name; // "Alice"
user.name = "Bob"; // ❌ Fails silently
```

### **Object Factory with Private Variables**

```javascript
function createCounter() {
  let count = 0; // Private
  
  return Object.freeze({
    increment() { return ++count; },
    decrement() { return --count; },
    get value() { return count; }
  });
}

const counter = createCounter();
counter.increment(); // 1
counter.value; // 1
counter.count; // undefined (private)
```

### **Proxy for Validation**

```javascript
const validator = {
  set(target, prop, value) {
    if (prop === 'age' && (typeof value !== 'number' || value < 0)) {
      throw new Error('Invalid age');
    }
    target[prop] = value;
    return true;
  }
};

const person = new Proxy({}, validator);
person.age = 25; // ✅ Works
person.age = -5; // ❌ Error: Invalid age
```

---

## **Performance Tips**

1. **Use `Object.hasOwn()` over `hasOwnProperty()`** - safer and faster

2. **Avoid `Object.setPrototypeOf()`** - very slow, use `Object.create()` instead
  
3. **Use `Object.freeze()` for constants** - enables V8 optimizations

4. **Cache `Object.keys()` results** - avoid repeated calls in loops

5. **Use `Map` for frequent additions/deletions** - faster than objects for dynamic data

---
