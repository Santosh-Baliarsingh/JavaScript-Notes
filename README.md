<!-- markdownlint-disable MD036 -->
<!-- markdownlint-disable MD060 -->
<!-- markdownlint-disable MD050 -->
<!-- markdownlint-disable MD024 -->

# **Prototype & Inheritance in JavaScript: Deep Dive**

---

## **Table Of Contents**

- [**Prototype \& Inheritance in JavaScript: Deep Dive**](#prototype--inheritance-in-javascript-deep-dive)
  - [**Table Of Contents**](#table-of-contents)
  - [**Initial Understanding**](#initial-understanding)
  - [**Beginner Explanation**](#beginner-explanation)
  - [**Advanced Explanation**](#advanced-explanation)
    - [The Core Mechanism](#the-core-mechanism)
    - [`prototype` vs `__proto__`](#prototype-vs-__proto__)
    - [Key Rules](#key-rules)
  - [**Code Examples**](#code-examples)
    - [Example 1: Basic Prototype Chain](#example-1-basic-prototype-chain)
    - [Example 2: Inheritance (Manual)](#example-2-inheritance-manual)
    - [Example 3: ES6 Class Syntax (Under the Hood)](#example-3-es6-class-syntax-under-the-hood)
    - [Example 4: Direct Object Linking](#example-4-direct-object-linking)
  - [**Visual Diagram (Text-Based)**](#visual-diagram-text-based)
    - [Scenario: Constructor Function Inheritance](#scenario-constructor-function-inheritance)
  - [**Common Interview Traps**](#common-interview-traps)
    - [❌ Trap 1: Confusing `prototype` and `__proto__`](#-trap-1-confusing-prototype-and-__proto__)
    - [❌ Trap 2: Forgetting to Fix `constructor`](#-trap-2-forgetting-to-fix-constructor)
    - [❌ Trap 3: Direct Assignment Breaks Chain](#-trap-3-direct-assignment-breaks-chain)
    - [❌ Trap 4: Modifying Built-in Prototypes](#-trap-4-modifying-built-in-prototypes)
    - [❌ Trap 5: `hasOwnProperty` vs `in` operator](#-trap-5-hasownproperty-vs-in-operator)
  - [**Real-World Use Cases**](#real-world-use-cases)
    - [1. Framework Internals (React, Vue)](#1-framework-internals-react-vue)
    - [2. Polyfills](#2-polyfills)
    - [3. Method Sharing (Memory Optimization)](#3-method-sharing-memory-optimization)
    - [4. Mixins \& Composition](#4-mixins--composition)
  - [**Modern Best Practices**](#modern-best-practices)
    - [Use `Object.getPrototypeOf()` instead of `__proto__`](#use-objectgetprototypeof-instead-of-__proto__)
    - [Use ES6 Classes for Cleaner Syntax](#use-es6-classes-for-cleaner-syntax)
    - [Avoid Deep Inheritance Chains](#avoid-deep-inheritance-chains)
  - [**Quick Cheat Sheet - Detailed Explained**](#quick-cheat-sheet---detailed-explained)
    - [1. `obj.hasOwnProperty('prop')` - Check if property is DIRECTLY on the object](#1-objhasownpropertyprop---check-if-property-is-directly-on-the-object)
    - [2. `'prop' in obj` - Check if property exists ANYWHERE in the chain](#2-prop-in-obj---check-if-property-exists-anywhere-in-the-chain)
    - [3. `Object.getPrototypeOf(obj)` - Get the prototype of an object](#3-objectgetprototypeofobj---get-the-prototype-of-an-object)
    - [4. `Object.create(proto)` - Create new object with specific prototype](#4-objectcreateproto---create-new-object-with-specific-prototype)
    - [5. `obj instanceof Constructor` - Check if constructor is in prototype chain](#5-obj-instanceof-constructor---check-if-constructor-is-in-prototype-chain)
  - [**Comparison Table**](#comparison-table)
  - [**Quick Decision Guide**](#quick-decision-guide)
  - [**Practice Example - All Methods Together**](#practice-example---all-methods-together)
  - [**Summary**](#summary)
  - [**Interview Power Questions**](#interview-power-questions)
    - [Q1: What's the difference between `__proto__` and `prototype`?](#q1-whats-the-difference-between-__proto__-and-prototype)
    - [Q2: How do you implement inheritance without ES6 classes?](#q2-how-do-you-implement-inheritance-without-es6-classes)
    - [Q3: What happens when you do `obj.method()`?](#q3-what-happens-when-you-do-objmethod)
    - [Q4: Why shouldn't you modify `Object.prototype`?](#q4-why-shouldnt-you-modify-objectprototype)
    - [Q5: What's the output?](#q5-whats-the-output)
  - [**PROTOTYPE POLLUTION - Security Threat**](#prototype-pollution---security-threat)
    - [What is `Prototype Pollution`?](#what-is-prototype-pollution)
  - [**Beginner Explanation**](#beginner-explanation-1)
  - [**How Prototype Pollution Happens**](#how-prototype-pollution-happens)
    - [Vulnerable Code Example](#vulnerable-code-example)
  - [**Visual Diagram - How Pollution Spreads**](#visual-diagram---how-pollution-spreads)
    - [Before Attack](#before-attack)
    - [After Pollution Attack](#after-pollution-attack)
  - [**Real-World Attack Scenarios**](#real-world-attack-scenarios)
    - [Scenario 1: Privilege Escalation](#scenario-1-privilege-escalation)
    - [Scenario 2: Remote Code Execution (RCE)](#scenario-2-remote-code-execution-rce)
    - [Scenario 3: Denial of Service (DoS)](#scenario-3-denial-of-service-dos)
  - [**How to Prevent Prototype Pollution**](#how-to-prevent-prototype-pollution)
    - [✅ Solution 1: Use `Object.create(null)` for Dictionary Objects](#-solution-1-use-objectcreatenull-for-dictionary-objects)
    - [✅ Solution 2: Validate Keys (Blocklist Approach)](#-solution-2-validate-keys-blocklist-approach)
    - [✅ Solution 3: Use `hasOwnProperty` Check](#-solution-3-use-hasownproperty-check)
    - [✅ Solution 4: Use Modern Libraries](#-solution-4-use-modern-libraries)
    - [✅ Solution 5: Freeze Prototypes (Nuclear Option)](#-solution-5-freeze-prototypes-nuclear-option)
  - [**How to Detect Prototype Pollution**](#how-to-detect-prototype-pollution)
    - [Manual Testing](#manual-testing)
    - [Use Security Scanners](#use-security-scanners)
  - [**Real-World CVEs (Common Vulnerabilities)**](#real-world-cves-common-vulnerabilities)
    - [Notable Prototype Pollution Vulnerabilities](#notable-prototype-pollution-vulnerabilities)
  - [**Interview Questions on Prototype Pollution**](#interview-questions-on-prototype-pollution)
    - [Q1: What is `prototype pollution`?](#q1-what-is-prototype-pollution)
    - [Q2: How does `__proto__` enable prototype pollution?](#q2-how-does-__proto__-enable-prototype-pollution)
    - [Q3: Why is JSON.parse() safe from prototype pollution?](#q3-why-is-jsonparse-safe-from-prototype-pollution)
    - [Q4: Show a safe recursive merge function](#q4-show-a-safe-recursive-merge-function)
  - [**Key Takeaways**](#key-takeaways)
  - [**Summary: Why Prototype Pollution is Dangerous**](#summary-why-prototype-pollution-is-dangerous)

## **Initial Understanding**

JavaScript uses **prototype-based inheritance**, not class-based (like Java/C++). Every object in JS has an internal link to another object called its **prototype**. This chain of prototypes is called the **prototype chain**, and it's how inheritance works in JavaScript.

**Key players:**

- `prototype` (property on constructor functions)
- `__proto__` (internal link to prototype, now standardized as `[[Prototype]]`)
- `Object.getPrototypeOf()` / `Object.setPrototypeOf()` (modern API)

---

## **Beginner Explanation**

Think of prototypes like a **family tree for objects**:

- When you create an object, it can "inherit" properties from a parent object
- If JavaScript doesn't find a property on the object, it looks up the chain
- This chain ends at `Object.prototype`, which is the root ancestor

**Simple analogy:**
You ask your mom for money. She doesn't have it, so she asks grandma. Grandma gives it. That's prototype lookup.

---

## **Advanced Explanation**

### The Core Mechanism

**When you access a property:**

```js
obj.property;
```

**JavaScript**:

1. Checks if `obj` has `property`
2. If not, checks `obj.__proto__`
3. Then `obj.__proto__.__proto__`
4. Continues until it reaches `null` (end of chain)
5. Returns `undefined` if not found

### `prototype` vs `__proto__`

| Concept         | What it is                                              | Where it lives           |
| --------------- | ------------------------------------------------------- | ------------------------ |
| `prototype`     | Blueprint object attached to **constructor functions**  | `Function.prototype`     |
| `__proto__`     | Internal link pointing to the object's actual prototype | Every object instance    |
| `[[Prototype]]` | Official internal slot (use `Object.getPrototypeOf()`)  | Hidden internal property |

### Key Rules

```js
function Person(name) {
  this.name = name;
}

const john = new Person("John");

// TRUE statements:
john.__proto__ === Person.prototype; // ✅
Person.prototype.__proto__ === Object.prototype; // ✅
Object.prototype.__proto__ === null; // ✅ (end of chain)
```

---

## **Code Examples**

### Example 1: Basic Prototype Chain

```js
function Animal(name) {
  this.name = name;
}

Animal.prototype.eat = function () {
  console.log(`${this.name} is eating`);
};

const dog = new Animal("Dog");
dog.eat(); // "Dog is eating"

console.log(dog.__proto__ === Animal.prototype); // true
console.log(Animal.prototype.__proto__ === Object.prototype); // true
console.log(Object.prototype.__proto__); // null
```

**Prototype chain:**

```js
dog → Animal.prototype → Object.prototype → null
```

---

### Example 2: Inheritance (Manual)

```js
function Animal(name) {
  this.name = name;
}

Animal.prototype.eat = function () {
  console.log(`${this.name} eats food`);
};

function Dog(name, breed) {
  Animal.call(this, name); // Call parent constructor
  this.breed = breed;
}

// Link Dog.prototype to Animal.prototype
Dog.prototype = Object.create(Animal.prototype);
Dog.prototype.constructor = Dog; // Fix constructor reference

Dog.prototype.bark = function () {
  console.log(`${this.name} barks`);
};

const rex = new Dog("Rex", "Labrador");
rex.eat(); // "Rex eats food" (inherited)
rex.bark(); // "Rex barks" (own method)

console.log(rex.__proto__ === Dog.prototype); // true
console.log(Dog.prototype.__proto__ === Animal.prototype); // true
```

**Prototype chain:**

```js
rex → Dog.prototype → Animal.prototype → Object.prototype → null
```

---

### Example 3: ES6 Class Syntax (Under the Hood)

```js
class Animal {
  constructor(name) {
    this.name = name;
  }

  eat() {
    console.log(`${this.name} eats`);
  }
}

class Dog extends Animal {
  constructor(name, breed) {
    super(name); // Calls Animal constructor
    this.breed = breed;
  }

  bark() {
    console.log(`${this.name} barks`);
  }
}

const max = new Dog("Max", "Beagle");
max.eat(); // "Max eats"
max.bark(); // "Max barks"

// Under the hood:
console.log(max.__proto__ === Dog.prototype); // true
console.log(Dog.prototype.__proto__ === Animal.prototype); // true
```

**Classes are just syntactic sugar over prototypes!**

---

### Example 4: Direct Object Linking

```js
const animal = {
  eat() {
    console.log("Eating...");
  },
};

const dog = Object.create(animal); // dog.__proto__ = animal
dog.bark = function () {
  console.log("Barking...");
};

dog.eat(); // "Eating..." (from animal)
dog.bark(); // "Barking..." (own method)

console.log(dog.__proto__ === animal); // true
```

---

## **Visual Diagram (Text-Based)**

### Scenario: Constructor Function Inheritance

```js
function Animal(name) { this.name = name; }
Animal.prototype.eat = function() { ... };

function Dog(name, breed) {
  Animal.call(this, name);
  this.breed = breed;
}
Dog.prototype = Object.create(Animal.prototype);
Dog.prototype.constructor = Dog;
Dog.prototype.bark = function() { ... };

const rex = new Dog('Rex', 'Husky');
```

**Memory Structure:**

```text
┌────────────────────────────────────────────────────────┐
│                      rex (instance)                    │
│  ┌──────────────────────────────────────────────────┐  │
│  │ name: "Rex"                                      │  │
│  │ breed: "Husky"                                   │  │
│  │ __proto__  ──────────────────┐                   │  │
│  └──────────────────────────────┼──────────────-────┘  │
└─────────────────────────────────┼───────────────────-──┘
                                  │
                                  ▼
┌─────────────────────────────────────────────────────────┐
│                    Dog.prototype                        │
│  ┌───────────────────────────────────────────────── ─┐  │
│  │ constructor: Dog                                  │  │
│  │ bark: function() { ... }                          │  │
│  │ __proto__  ──────────────────┐                    │  │
│  └──────────────────────────────┼────────────-────---┘  │
└─────────────────────────────────┼──────────────────--───┘
                                  │
                                  ▼
┌─────────────────────────────────────────────────────────┐
│                  Animal.prototype                       │
│  ┌───────────────────────────────────────────────────┐  │
│  │ constructor: Animal                               │  │
│  │ eat: function() { ... }                           │  │
│  │ __proto__  ──────────────────┐                    │  │
│  └──────────────────────────────┼────────────────-─-─┘  │
└────────────────────────────────-┼────────────────-──-───┘
                                  │
                                  ▼
┌─────────────────────────────────────────────────────────┐
│                  Object.prototype                       │
│  ┌───────────────────────────────────────────────────┐  │
│  │ toString: function() { ... }                      │  │
│  │ hasOwnProperty: function() { ... }                │  │
│  │ __proto__: null                                   │  │
│  └───────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────┘
```

**Property Lookup:**

```text
rex.bark()     →  Found on Dog.prototype ✅
rex.eat()      →  Not on rex, not on Dog.prototype, found on Animal.prototype ✅
rex.toString() →  Not on rex, Dog.prototype, Animal.prototype, found on Object.prototype ✅
rex.fly()      →  Not found anywhere → undefined
```

---

## **Common Interview Traps**

### ❌ Trap 1: Confusing `prototype` and `__proto__`

```js
function Person() {}
const john = new Person();

// WRONG understanding:
john.prototype; // undefined (instances don't have .prototype)

// CORRECT:
john.__proto__ === Person.prototype; // true
```

**Rule:** Only **constructor functions** have `.prototype`. Instances have `__proto__`.

---

### ❌ Trap 2: Forgetting to Fix `constructor`

```js
function Dog() {}
Dog.prototype = Object.create(Animal.prototype);

// BUG: Dog.prototype.constructor now points to Animal
console.log(Dog.prototype.constructor === Animal); // true ⚠️

// FIX:
Dog.prototype.constructor = Dog;
```

---

### ❌ Trap 3: Direct Assignment Breaks Chain

```js
// WRONG:
Dog.prototype = Animal.prototype; // Both share same object!

// CORRECT:
Dog.prototype = Object.create(Animal.prototype); // Creates new object with Animal.prototype as __proto__
```

---

### ❌ Trap 4: Modifying Built-in Prototypes

```js
// BAD PRACTICE (but works):
Array.prototype.last = function () {
  return this[this.length - 1];
};

[1, 2, 3].last(); // 3

// Problem: Pollutes global namespace, breaks libraries
```

---

### ❌ Trap 5: `hasOwnProperty` vs `in` operator

```js
function Dog() {}
Dog.prototype.breed = "Unknown";

const rex = new Dog();
rex.name = "Rex";

console.log("name" in rex); // true
console.log("breed" in rex); // true (inherited)

console.log(rex.hasOwnProperty("name")); // true
console.log(rex.hasOwnProperty("breed")); // false (on prototype)
```

---

## **Real-World Use Cases**

### 1. Framework Internals (React, Vue)

**Component inheritance uses prototypes under the hood:**

```js
class MyComponent extends React.Component {
  // inherits lifecycle methods via prototype chain
}
```

### 2. Polyfills

```js
if (!Array.prototype.includes) {
  Array.prototype.includes = function (val) {
    return this.indexOf(val) !== -1;
  };
}
```

### 3. Method Sharing (Memory Optimization)

```js
// BAD: Every instance gets its own copy
function User(name) {
  this.name = name;
  this.greet = function () {
    console.log("Hi");
  }; // ❌
}

// GOOD: All instances share one method
function User(name) {
  this.name = name;
}
User.prototype.greet = function () {
  console.log("Hi");
}; // ✅
```

### 4. Mixins & Composition

```js
const canEat = {
  eat() {
    console.log("Eating");
  },
};

const canWalk = {
  walk() {
    console.log("Walking");
  },
};

function Person() {}
Object.assign(Person.prototype, canEat, canWalk);

const john = new Person();
john.eat(); // "Eating"
john.walk(); // "Walking"
```

---

## **Modern Best Practices**

### Use `Object.getPrototypeOf()` instead of `__proto__`

```js
// OLD (works but discouraged):
obj.__proto__;

// MODERN:
Object.getPrototypeOf(obj);
```

### Use ES6 Classes for Cleaner Syntax

```js
class Animal {
  constructor(name) {
    this.name = name;
  }
  eat() {
    console.log("Eating");
  }
}

class Dog extends Animal {
  bark() {
    console.log("Barking");
  }
}
```

### Avoid Deep Inheritance Chains

```text
Animal → Mammal → Carnivore → Dog → Labrador → MyLabrador
```

❌ **Too deep, hard to maintain**

**Prefer composition over inheritance:**

```js
const labrador = Object.assign({}, dogBehavior, fetchBehavior, swimBehavior);
```

---

## **Quick Cheat Sheet - Detailed Explained**

### 1. `obj.hasOwnProperty('prop')` - Check if property is DIRECTLY on the object

**What it does:** Returns `true` only if the property exists directly on the object, NOT inherited from prototype.

```js
function Dog() {}
Dog.prototype.species = "Canine"; // On prototype

const rex = new Dog();
rex.name = "Rex"; // Direct property

console.log(rex.hasOwnProperty("name")); // true ✅ (directly on rex)
console.log(rex.hasOwnProperty("species")); // false ❌ (on prototype)
console.log(rex.hasOwnProperty("toString")); // false ❌ (on Object.prototype)
```

**Use case:** When you want to check if an object has its OWN property, not inherited ones.

---

### 2. `'prop' in obj` - Check if property exists ANYWHERE in the chain

**What it does:** Returns `true` if property exists on object OR anywhere in its prototype chain.

```js
function Dog() {}
Dog.prototype.species = "Canine";

const rex = new Dog();
rex.name = "Rex";

console.log("name" in rex); // true ✅ (on rex)
console.log("species" in rex); // true ✅ (on prototype)
console.log("toString" in rex); // true ✅ (on Object.prototype)
console.log("fly" in rex); // false ❌ (nowhere in chain)
```

**Use case:** When you want to check if a property is accessible, regardless of where it comes from.

---

### 3. `Object.getPrototypeOf(obj)` - Get the prototype of an object

**What it does:** Returns the prototype (parent) of the object. Modern replacement for `__proto__`.

```js
function Dog(name) {
  this.name = name;
}

Dog.prototype.bark = function () {
  console.log("Woof!");
};

const rex = new Dog("Rex");

// Get rex's prototype
const proto = Object.getPrototypeOf(rex);
console.log(proto === Dog.prototype); // true ✅

// You can access prototype methods
console.log(proto.bark); // function() { console.log('Woof!'); }

// Check the chain
console.log(Object.getPrototypeOf(proto) === Object.prototype); // true ✅
console.log(Object.getPrototypeOf(Object.prototype)); // null (end of chain)
```

**Use case:** When you need to inspect or traverse the prototype chain programmatically.

**Comparison with `__proto__`:**

```js
// Old way (still works but discouraged):
rex.__proto__ === Dog.prototype; // true

// Modern way (recommended):
Object.getPrototypeOf(rex) === Dog.prototype; // true
```

---

### 4. `Object.create(proto)` - Create new object with specific prototype

**What it does:** Creates a NEW object and sets its `__proto__` to the provided prototype.

```js
const animal = {
  eat() {
    console.log("Eating...");
  },
};

// Create dog with animal as prototype
const dog = Object.create(animal);
dog.bark = function () {
  console.log("Barking...");
};

dog.eat(); // "Eating..." ✅ (inherited from animal)
dog.bark(); // "Barking..." ✅ (own method)

console.log(Object.getPrototypeOf(dog) === animal); // true ✅
```

**Real inheritance example:**

```js
function Animal() {}
Animal.prototype.eat = function () {
  console.log("eating");
};

function Dog() {}

// Set Dog.prototype to inherit from Animal.prototype
Dog.prototype = Object.create(Animal.prototype);
Dog.prototype.constructor = Dog; // Fix constructor

const rex = new Dog();
rex.eat(); // "eating" ✅ (inherited)
```

**Use case:** When you want to create objects with a specific prototype, especially for inheritance.

**Why not just `Dog.prototype = Animal.prototype`?**

```js
// BAD - Both share the SAME object:
Dog.prototype = Animal.prototype;
Dog.prototype.bark = function () {}; // This adds bark to Animal.prototype too! ❌

// GOOD - Creates NEW object with Animal.prototype as parent:
Dog.prototype = Object.create(Animal.prototype);
Dog.prototype.bark = function () {}; // Only on Dog.prototype ✅
```

---

### 5. `obj instanceof Constructor` - Check if constructor is in prototype chain

**What it does:** Returns `true` if `Constructor.prototype` exists anywhere in `obj`'s prototype chain.

```js
function Animal() {}
function Dog() {}

Dog.prototype = Object.create(Animal.prototype);

const rex = new Dog();

console.log(rex instanceof Dog); // true ✅
console.log(rex instanceof Animal); // true ✅
console.log(rex instanceof Object); // true ✅ (everything inherits from Object)
console.log(rex instanceof Array); // false ❌
```

**How it works internally:**

```js
// rex instanceof Dog checks:
// Is Dog.prototype in rex's prototype chain?

rex.__proto__ === Dog.prototype; // true ✅
rex.__proto__.__proto__ === Animal.prototype; // true ✅
rex.__proto__.__proto__.__proto__ === Object.prototype; // true ✅
```

**Use case:** Type checking and validation.

```js
function processAnimal(animal) {
  if (animal instanceof Dog) {
    animal.bark();
  } else if (animal instanceof Cat) {
    animal.meow();
  }
}
```

**Gotcha with primitive wrappers:**

```js
const str = "hello";
const strObj = new String("hello");

console.log(str instanceof String); // false (primitive)
console.log(strObj instanceof String); // true (object)
```

---

## **Comparison Table**

| Method                    | Checks Own Property | Checks Prototype Chain | Returns     |
| ------------------------- | ------------------- | ---------------------- | ----------- |
| `hasOwnProperty()`        | ✅ Yes               | ❌ No                   | boolean     |
| `in` operator             | ✅ Yes               | ✅ Yes                  | boolean     |
| `Object.getPrototypeOf()` | ❌ No                | Returns prototype      | object/null |
| `Object.create()`         | Creates new object  | Sets prototype         | new object  |
| `instanceof`              | ❌ No                | ✅ Yes                  | boolean     |

---

## **Quick Decision Guide**

**When to use what:**

```js
// Want to check if property is directly on object?
obj.hasOwnProperty("name"); // ✅

// Want to check if property is accessible (own or inherited)?
"name" in obj; // ✅

// Want to get the parent prototype?
Object.getPrototypeOf(obj); // ✅

// Want to create object with specific prototype?
Object.create(proto); // ✅

// Want to check if object is instance of constructor?
obj instanceof Constructor; // ✅
```

---

## **Practice Example - All Methods Together**

```js
function Animal(name) {
  this.name = name;
}
Animal.prototype.eat = function () {
  console.log(`${this.name} is eating`);
};

function Dog(name, breed) {
  Animal.call(this, name);
  this.breed = breed;
}
Dog.prototype = Object.create(Animal.prototype);
Dog.prototype.constructor = Dog;
Dog.prototype.bark = function () {
  console.log(`${this.name} barks`);
};

const rex = new Dog("Rex", "Husky");

// 1. hasOwnProperty - check direct properties
console.log(rex.hasOwnProperty("name")); // true (own property)
console.log(rex.hasOwnProperty("breed")); // true (own property)
console.log(rex.hasOwnProperty("eat")); // false (on Animal.prototype)
console.log(rex.hasOwnProperty("bark")); // false (on Dog.prototype)

// 2. 'in' operator - check entire chain
console.log("name" in rex); // true
console.log("breed" in rex); // true
console.log("eat" in rex); // true (found in chain)
console.log("bark" in rex); // true (found in chain)
console.log("fly" in rex); // false (not in chain)

// 3. Object.getPrototypeOf - traverse chain
console.log(Object.getPrototypeOf(rex) === Dog.prototype); // true
console.log(Object.getPrototypeOf(Dog.prototype) === Animal.prototype); // true
console.log(Object.getPrototypeOf(Animal.prototype) === Object.prototype); // true

// 4. Object.create - was used to set up inheritance
// Dog.prototype = Object.create(Animal.prototype);

// 5. instanceof - check type
console.log(rex instanceof Dog); // true
console.log(rex instanceof Animal); // true
console.log(rex instanceof Object); // true
console.log(rex instanceof Array); // false
```

---

## **Summary**

- **`prototype`** is a property on constructor functions (the blueprint)
- **`__proto__`** is the internal link every object has to its prototype
- **Inheritance** happens via the prototype chain
- **ES6 classes** are syntactic sugar over prototypes
- Always use `Object.create()` for proper inheritance
- Don't forget to fix `.constructor` after linking prototypes

---

## **Interview Power Questions**

### Q1: What's the difference between `__proto__` and `prototype`?

**Answer:** `prototype` is a property on constructor functions that serves as the blueprint for instances. `__proto__` is the actual internal link that every object has pointing to its prototype.

```js
function Dog() {}
const rex = new Dog();

Dog.prototype; // The blueprint object
rex.__proto__; // Points to Dog.prototype
rex.__proto__ === Dog.prototype; // true
```

---

### Q2: How do you implement inheritance without ES6 classes?

**Answer:**

```js
function Animal(name) {
  this.name = name;
}
Animal.prototype.eat = function () {
  console.log("eating");
};

function Dog(name, breed) {
  Animal.call(this, name); // Inherit properties
  this.breed = breed;
}

Dog.prototype = Object.create(Animal.prototype); // Inherit methods
Dog.prototype.constructor = Dog; // Fix constructor
```

---

### Q3: What happens when you do `obj.method()`?

**Answer:**

1. JS checks if `method` exists on `obj`
2. If not, checks `obj.__proto__`
3. Continues up the chain until found or reaches `null`
4. If found, executes with `this` bound to `obj`
5. If not found, throws `TypeError`

---

### Q4: Why shouldn't you modify `Object.prototype`?

**Answer:**

- Pollutes global namespace
- Affects ALL objects in the application
- Can break third-party libraries
- Makes debugging harder
- Violates principle of least surprise

---

### Q5: What's the output?

```js
function A() {}
A.prototype.x = 10;

function B() {}
B.prototype = Object.create(A.prototype);
B.prototype.x = 20;

const b = new B();
console.log(b.x); // ?
```

**Answer:** `20` - Found on `B.prototype`, doesn't look further up the chain.

---

## **PROTOTYPE POLLUTION - Security Threat**

### What is `Prototype Pollution`?

**Prototype Pollution** is a critical security vulnerability where an attacker can inject properties into JavaScript's base object prototypes (`Object.prototype`, `Array.prototype`, etc.). Since all objects inherit from these prototypes, the injected properties become accessible to **every object** in the application.

---

## **Beginner Explanation**

Imagine you're in a school where everyone inherits a rulebook from the principal. If someone hacks the principal's rulebook and adds "Students can skip class", **everyone** automatically gets that rule.

**In JavaScript:**

- `Object.prototype` is like the principal's rulebook
- All objects inherit from it
- If you pollute it, ALL objects are affected

---

## **How Prototype Pollution Happens**

### Vulnerable Code Example

```js
// Vulnerable function that merges objects
function merge(target, source) {
  for (let key in source) {
    if (typeof source[key] === "object") {
      target[key] = merge(target[key] || {}, source[key]);
    } else {
      target[key] = source[key];
    }
  }
  return target;
}

// Normal usage
const user = { name: "John" };
const updates = { age: 30 };
merge(user, updates);
console.log(user); // { name: 'John', age: 30 } ✅

// 🚨 ATTACK: Prototype pollution
const maliciousPayload = {
  __proto__: {
    isAdmin: true,
  },
};

merge({}, maliciousPayload);

// Now EVERY object has isAdmin = true!
const normalUser = {};
console.log(normalUser.isAdmin); // true ⚠️⚠️⚠️
```

---

## **Visual Diagram - How Pollution Spreads**

### Before Attack

```text
┌─────────────────────────────────────┐
│       Object.prototype              │
│  ┌──────────────────────────────┐   │
│  │ toString: function()         │   │
│  │ hasOwnProperty: function()   │   │
│  └──────────────────────────────┘   │
└─────────────────────────────────────┘
            ▲           ▲
            │           │
    ┌───────┘           └────────┐
    │                            │
┌───────┐                   ┌────────┐
│ user1 │                   │ user2  │
│ name  │                   │ name   │
└───────┘                   └────────┘
```

### After Pollution Attack

```text
┌─────────────────────────────────────┐
│       Object.prototype              │
│  ┌──────────────────────────────┐   │
│  │ toString: function()         │   │
│  │ hasOwnProperty: function()   │   │
│  │ isAdmin: true  ⚠️ POLLUTED   │   │
│  └──────────────────────────────┘   │
└─────────────────────────────────────┘
            ▲           ▲
            │           │
    ┌───────┘           └────────┐
    │                            │
┌───────┐                   ┌────────┐
│ user1 │ ← isAdmin: true   │ user2  │ ← isAdmin: true
│ name  │   (inherited!)    │ name   │   (inherited!)
└───────┘                   └────────┘
```

**Result:** ALL objects now have `isAdmin: true` 🔥

---

## **Real-World Attack Scenarios**

### Scenario 1: Privilege Escalation

```js
// Vulnerable code in authentication system
function checkAdmin(user) {
  if (user.isAdmin) {
    return "Access granted to admin panel";
  }
  return "Access denied";
}

// Normal user
const regularUser = { name: "Alice" };
console.log(checkAdmin(regularUser)); // "Access denied" ✅

// 🚨 ATTACKER pollutes prototype
Object.prototype.isAdmin = true;

// Now regular user becomes admin!
console.log(checkAdmin(regularUser)); // "Access granted to admin panel" ⚠️
```

---

### Scenario 2: Remote Code Execution (RCE)

```js
// Vulnerable server code (Express.js example)
app.post('/api/update', (req, res) => {
  const user = {};
  merge(user, req.body); // Vulnerable merge!

  // Later in code...
  if (user.isAdmin) {
    // Execute admin commands
    exec(user.command); // 🚨 RCE vulnerability!
  }
});

// Attacker sends:
POST /api/update
{
  "__proto__": {
    "isAdmin": true,
    "command": "rm -rf /"
  }
}
```

---

### Scenario 3: Denial of Service (DoS)

```js
// Vulnerable API endpoint
app.get('/api/data', (req, res) => {
  const options = { timeout: 5000 };
  merge(options, req.query);

  // Make external API call
  fetch(url, options);
});

// 🚨 ATTACKER pollutes with infinite timeout
GET /api/data?__proto__[timeout]=999999999

// Now ALL requests have 999999999ms timeout → DoS
```

---

## **How to Prevent Prototype Pollution**

### ✅ Solution 1: Use `Object.create(null)` for Dictionary Objects

```js
// BAD: Inherits from Object.prototype
const config = {};
console.log(config.toString); // function (inherited)

// GOOD: No prototype, no pollution risk
const safeConfig = Object.create(null);
console.log(safeConfig.toString); // undefined ✅
```

---

### ✅ Solution 2: Validate Keys (Blocklist Approach)

```js
function safeMerge(target, source) {
  const blocklist = ["__proto__", "constructor", "prototype"];

  for (let key in source) {
    // Block dangerous keys
    if (blocklist.includes(key)) {
      continue; // Skip dangerous keys
    }

    if (typeof source[key] === "object") {
      target[key] = safeMerge(target[key] || {}, source[key]);
    } else {
      target[key] = source[key];
    }
  }
  return target;
}

// Attack fails
const payload = { __proto__: { isAdmin: true } };
safeMerge({}, payload);

const user = {};
console.log(user.isAdmin); // undefined ✅ (attack blocked)
```

---

### ✅ Solution 3: Use `hasOwnProperty` Check

```js
function safeMerge(target, source) {
  for (let key in source) {
    // Only copy own properties, not inherited ones
    if (source.hasOwnProperty(key)) {
      target[key] = source[key];
    }
  }
  return target;
}
```

---

### ✅ Solution 4: Use Modern Libraries

```js
// Use lodash's safe merge
const _ = require("lodash");
_.merge(target, source); // Protected against pollution

// Or use JSON schema validation
const Ajv = require("ajv");
const ajv = new Ajv();

const schema = {
  type: "object",
  properties: {
    name: { type: "string" },
    age: { type: "number" },
  },
  additionalProperties: false, // Block unknown properties
};

const validate = ajv.compile(schema);
if (validate(userInput)) {
  // Safe to merge
}
```

---

### ✅ Solution 5: Freeze Prototypes (Nuclear Option)

```js
// Freeze Object.prototype to prevent any modifications
Object.freeze(Object.prototype);
Object.freeze(Array.prototype);

// Now pollution attempts fail silently
Object.prototype.isAdmin = true;
console.log({}.isAdmin); // undefined ✅

// ⚠️ WARNING: This can break some libraries!
```

---

## **How to Detect Prototype Pollution**

### Manual Testing

```js
// Test if your merge function is vulnerable
function testPollution(mergeFunc) {
  const obj = {};
  const payload = JSON.parse('{"__proto__":{"polluted":true}}');

  mergeFunc({}, payload);

  if ({}.polluted === true) {
    console.log("🚨 VULNERABLE to prototype pollution!");
  } else {
    console.log("✅ Safe from prototype pollution");
  }
}

testPollution(yourMergeFunction);
```

---

### Use Security Scanners

```bash
# npm audit (checks for known vulnerabilities)
npm audit

# Snyk (security scanner)
npx snyk test

# ESLint with security plugin
npm install eslint-plugin-security
```

---

## **Real-World CVEs (Common Vulnerabilities)**

### Notable Prototype Pollution Vulnerabilities

| Library                | CVE            | Impact              |
| ---------------------- | -------------- | ------------------- |
| **lodash** (< 4.17.12) | CVE-2019-10744 | RCE possible        |
| **jQuery** (< 3.4.0)   | CVE-2019-11358 | XSS, DoS            |
| **Hoek** (< 5.0.3)     | CVE-2018-3728  | RCE possible        |
| **minimist** (< 1.2.6) | CVE-2021-44906 | Prototype pollution |
| **async** (< 2.6.4)    | CVE-2021-43138 | Prototype pollution |

---

## **Interview Questions on Prototype Pollution**

### Q1: What is `prototype pollution`?

**Answer:** A security vulnerability where attackers inject properties into base prototypes like `Object.prototype`, affecting all objects in the application. It can lead to privilege escalation, RCE, or DoS.

---

### Q2: How does `__proto__` enable prototype pollution?

**Answer:** `__proto__` is a magical property that directly references an object's prototype. When user input contains `__proto__`, vulnerable code might set properties on `Object.prototype` instead of the target object.

```js
const user = {};
user["__proto__"]["isAdmin"] = true; // Pollutes Object.prototype!
```

---

### Q3: Why is JSON.parse() safe from prototype pollution?

**Answer:**JSON.parse() creates normal objects, but it treats `__proto__` as a regular data key (string property) during parsing, not as a special accessor to the prototype chain. This means it doesn't modify `Object.prototype`.

```js
const parsed = JSON.parse('{"__proto__":{"polluted":true}}');
console.log(parsed.__proto__.polluted); // true (own property)
console.log({}.polluted); // undefined ✅ (not polluted)

// Verify: __proto__ is just a regular property
console.log(parsed.hasOwnProperty('__proto__')); // true
console.log(Object.keys(parsed)); // ['__proto__']
```

---

### Q4: Show a safe recursive merge function

**Answer:**

```js
function safeMerge(target, source) {
  const dangerous = ["__proto__", "constructor", "prototype"];

  for (let key in source) {
    if (dangerous.includes(key) || !source.hasOwnProperty(key)) {
      continue;
    }

    if (
      source[key] &&
      typeof source[key] === "object" &&
      !Array.isArray(source[key])
    ) {
      target[key] = safeMerge(target[key] || {}, source[key]);
    } else {
      target[key] = source[key];
    }
  }
  return target;
}
```

---

## **Key Takeaways**

1. **Never trust user input** when setting object properties
2. **Always validate keys** before merging objects
3. **Use `Object.create(null)`** for dictionary-like objects
4. **Keep dependencies updated** (lodash, minimist, etc.)
5. **Use `Object.freeze()`** on critical prototypes if needed
6. **Test your code** for prototype pollution vulnerabilities
7. **Use security linters** (eslint-plugin-security)

---

## **Summary: Why Prototype Pollution is Dangerous**

| Risk                           | Impact                       | Example                          |
| ------------------------------ | ---------------------------- | -------------------------------- |
| **Privilege Escalation**       | Users gain admin access      | `isAdmin: true` pollution        |
| **Remote Code Execution**      | Attacker runs arbitrary code | Polluted `eval` or `exec` params |
| **Denial of Service**          | Application crashes/hangs    | Infinite timeouts, memory leaks  |
| **Data Manipulation**          | Corrupted application state  | Default values overridden        |
| **XSS (Cross-Site Scripting)** | Inject malicious scripts     | Polluted template properties     |

---

**🚨 Remember: Prototype pollution is a CRITICAL vulnerability. Always sanitize and validate user input before merging into objects!**

---
