<!-- markdownlint-disable MD036 -->
<!-- markdownlint-disable MD060 -->
<!-- markdownlint-disable MD024 -->

# JavaScript Classes - Complete Guide

---

## Table of Contents

- [JavaScript Classes - Complete Guide](#javascript-classes---complete-guide)
  - [Table of Contents](#table-of-contents)
  - [**What is a Class?**](#what-is-a-class)
  - [**1. Creating a Basic Class**](#1-creating-a-basic-class)
  - [**2. The `new` Keyword**](#2-the-new-keyword)
  - [**3. Constructor Method**](#3-constructor-method)
  - [**4. Instance Methods**](#4-instance-methods)
  - [**5. The `extends` Keyword**](#5-the-extends-keyword)
  - [**6. The `super` Keyword**](#6-the-super-keyword)
    - [A) Using `super()` in Constructor](#a-using-super-in-constructor)
    - [B) Using `super` to Call Parent Methods](#b-using-super-to-call-parent-methods)
  - [**7. Static Methods**](#7-static-methods)
    - [Factory Method Example](#factory-method-example)
  - [**8. Getters and Setters**](#8-getters-and-setters)
    - [`Getter` (`get`)](#getter-get)
    - [`Setter` (`set`)](#setter-set)
  - [**9. Private Properties \& Methods**](#9-private-properties--methods)
    - [`Private Fields`](#private-fields)
    - [`Private Methods`](#private-methods)
  - [**10. Protected Properties**](#10-protected-properties)
  - [**11. Complete Example**](#11-complete-example)
  - [**12. Summary Table**](#12-summary-table)
  - [**13. `Public` vs `Private` vs `Protected`**](#13-public-vs-private-vs-protected)
  - [**14. `Instance` vs `Static`**](#14-instance-vs-static)
  - [**15. Common Mistakes**](#15-common-mistakes)
    - [`Mistake 1`: Forgetting `new`](#mistake-1-forgetting-new)
    - [`Mistake 2`: Not calling `super()` first](#mistake-2-not-calling-super-first)
    - [`Mistake 3`: Accessing private outside class](#mistake-3-accessing-private-outside-class)
    - [`Mistake 4`: Using `this` before `super()`](#mistake-4-using-this-before-super)
    - [`Mistake 5`: Multiple constructors](#mistake-5-multiple-constructors)
  - [**16. Quick Reference**](#16-quick-reference)
    - [Basic Class Structure](#basic-class-structure)
    - [Inheritance Structure](#inheritance-structure)
  - [**17. Real-World Examples**](#17-real-world-examples)
    - [`Example 1`: User Authentication System](#example-1-user-authentication-system)
    - [`Example 2`: Shopping Cart](#example-2-shopping-cart)
    - [`Example 3`: Employee Management](#example-3-employee-management)
  - [**18. Interview Tips**](#18-interview-tips)
  - [**Key Takeaways**](#key-takeaways)

## **What is a Class?**

A **class** is a blueprint for creating objects with predefined properties and methods.

**Think of it:** Class = Cookie Cutter | Objects = Cookies

---

## **1. Creating a Basic Class**

```javascript
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }
  
  greet() {
    console.log(`Hello, my name is ${this.name}`);
  }
}

const person1 = new Person('Alice', 25);
person1.greet(); // "Hello, my name is Alice"
```

**Key Parts:**

- `class Person` → Declares class
- `constructor()` → Runs when creating object
- `this.name` → Object property
- `greet()` → Object method

---

## **2. The `new` Keyword**

Creates a new instance (object) from a class.

```javascript
const person = new Person('Alice', 25); // ✅ Correct
const person = Person('Alice', 25);     // ❌ TypeError
```

**What `new` does:**

1. Creates empty object `{}`
2. Sets prototype
3. Runs constructor with `this`
4. Returns new object

---

## **3. Constructor Method**

Special method that initializes objects.

```javascript
class Car {
  constructor(brand, model, year) {
    this.brand = brand;
    this.model = model;
    this.year = year;
    this.speed = 0; // default value
  }
}

const myCar = new Car('Tesla', 'Model 3', 2023);
console.log(myCar.brand); // "Tesla"
```

**Rules:**

- Only ONE constructor per class
- Runs automatically with `new`
- Optional (JavaScript adds empty one if missing)

---

## **4. Instance Methods**

Methods that belong to each object.

```javascript
class Calculator {
  constructor() {
    this.result = 0;
  }
  
  add(num) {
    this.result += num;
  }
  
  subtract(num) {
    this.result -= num;
  }
  
  getResult() {
    return this.result;
  }
}

const calc = new Calculator();
calc.add(10);
calc.subtract(3);
console.log(calc.getResult()); // 7
```

---

## **5. The `extends` Keyword**

Allows a class to **inherit** from another class.

```javascript
// Parent class
class Animal {
  constructor(name) {
    this.name = name;
  }
  
  speak() {
    console.log(`${this.name} makes a sound`);
  }
}

// Child class
class Dog extends Animal {
  bark() {
    console.log(`${this.name} barks: Woof!`);
  }
}

const dog = new Dog('Buddy');
dog.speak(); // Inherited from Animal
dog.bark();  // Own method
```

**Key Points:**

- `extends` = Inheritance
- Child gets all parent properties/methods
- Child can add its own methods

---

## **6. The `super` Keyword**

Calls parent class constructor and methods.

### A) Using `super()` in Constructor

```javascript
class Animal {
  constructor(name) {
    this.name = name;
  }
}

class Dog extends Animal {
  constructor(name, breed) {
    super(name);      // ✅ Call parent first
    this.breed = breed;
  }
}

const dog = new Dog('Buddy', 'Golden Retriever');
```

**IMPORTANT RULE:**

```javascript
// ❌ WRONG
class Dog extends Animal {
  constructor(name, breed) {
    this.breed = breed; // Error!
    super(name);
  }
}

// ✅ CORRECT
class Dog extends Animal {
  constructor(name, breed) {
    super(name);        // Call super FIRST
    this.breed = breed;
  }
}
```

### B) Using `super` to Call Parent Methods

```javascript
class Animal {
  speak() {
    return `${this.name} makes a sound`;
  }
}

class Dog extends Animal {
  speak() {
    const parentMsg = super.speak();
    return `${parentMsg} and barks`;
  }
}

const dog = new Dog('Buddy');
dog.name = 'Buddy';
console.log(dog.speak());
// "Buddy makes a sound and barks"
```

---

## **7. Static Methods**

Belong to the **class itself**, not instances.

```javascript
class MathHelper {
  static add(a, b) {
    return a + b;
  }
  
  static multiply(a, b) {
    return a * b;
  }
}

// ✅ Call on class
console.log(MathHelper.add(5, 3)); // 8

// ❌ Cannot call on instance
const helper = new MathHelper();
console.log(helper.add(5, 3)); // TypeError
```

### Factory Method Example

```javascript
class User {
  constructor(name, role) {
    this.name = name;
    this.role = role;
  }
  
  static createAdmin(name) {
    return new User(name, 'admin');
  }
  
  static createGuest(name) {
    return new User(name, 'guest');
  }
}

const admin = User.createAdmin('Alice');
const guest = User.createGuest('Bob');
```

**Use Cases:**

- Utility functions
- Factory methods
- Constants

---

## **8. Getters and Setters**

Control how properties are accessed/modified.

### `Getter` (`get`)

```javascript
class Circle {
  constructor(radius) {
    this.radius = radius;
  }
  
  get diameter() {
    return this.radius * 2;
  }
  
  get area() {
    return Math.PI * this.radius ** 2;
  }
}

const circle = new Circle(5);
console.log(circle.diameter); // 10 (no parentheses!)
console.log(circle.area);     // 78.54
```

### `Setter` (`set`)

```javascript
class Rectangle {
  constructor(width, height) {
    this._width = width;
    this._height = height;
  }
  
  get area() {
    return this._width * this._height;
  }
  
  set width(value) {
    if (value <= 0) {
      throw new Error('Width must be positive');
    }
    this._width = value;
  }
  
  get width() {
    return this._width;
  }
}

const rect = new Rectangle(10, 5);
console.log(rect.area); // 50

rect.width = 20;
console.log(rect.area); // 100
```

---

## **9. Private Properties & Methods**

Only accessible **inside the class**. Use `#` prefix.

### `Private Fields`

```javascript
class BankAccount {
  #balance = 0; // Private field
  
  constructor(owner) {
    this.owner = owner; // Public field
  }
  
  deposit(amount) {
    this.#balance += amount;
  }
  
  withdraw(amount) {
    if (amount <= this.#balance) {
      this.#balance -= amount;
    } else {
      console.log('Insufficient funds');
    }
  }
  
  getBalance() {
    return this.#balance;
  }
}

const account = new BankAccount('Alice');
account.deposit(100);
console.log(account.getBalance()); // 100
console.log(account.owner);        // "Alice"

// ❌ Cannot access private
console.log(account.#balance); // SyntaxError
```

### `Private Methods`

```javascript
class User {
  #password;
  
  constructor(username, password) {
    this.username = username;
    this.#password = this.#hashPassword(password);
  }
  
  #hashPassword(password) {
    return password.split('').reverse().join('');
  }
  
  verifyPassword(password) {
    return this.#hashPassword(password) === this.#password;
  }
}

const user = new User('alice', 'secret123');
console.log(user.verifyPassword('secret123')); // true

// ❌ Cannot call private method
user.#hashPassword('test'); // SyntaxError
```

---

## **10. Protected Properties**

JavaScript doesn't have true "`protected`", but uses `_` convention.

```javascript
class Person {
  constructor(name) {
    this._name = name; // Convention: _ = "protected"
  }
  
  get name() {
    return this._name;
  }
}

class Employee extends Person {
  constructor(name, salary) {
    super(name);
    this._salary = salary;
  }
  
  displayInfo() {
    console.log(`${this._name} earns $${this._salary}`);
  }
}

const emp = new Employee('Alice', 50000);
emp.displayInfo(); // "Alice earns $50000"

// ⚠️ Still accessible (not truly protected)
console.log(emp._name); // "Alice"
```

**Note:** `_` is just a convention, not enforced like `#`

---

## **11. Complete Example**

```javascript
// Parent class
class Vehicle {
  #vin; // Private
  
  constructor(brand, year) {
    this.brand = brand;  // Public
    this.year = year;    // Public
    this.#vin = this.#generateVIN();
  }
  
  #generateVIN() {
    return 'VIN' + Math.random().toString(36).substr(2, 9);
  }
  
  getInfo() {
    return `${this.year} ${this.brand}`;
  }
  
  static compare(v1, v2) {
    return v1.year - v2.year;
  }
}

// Child class
class Car extends Vehicle {
  constructor(brand, year, model) {
    super(brand, year);
    this.model = model;
  }
  
  getInfo() {
    return `${super.getInfo()} ${this.model}`;
  }
  
  get age() {
    return new Date().getFullYear() - this.year;
  }
}

// Usage
const car1 = new Car('Tesla', 2020, 'Model 3');
const car2 = new Car('BMW', 2022, 'X5');

console.log(car1.getInfo()); // "2020 Tesla Model 3"
console.log(car1.age);       // 5
console.log(Vehicle.compare(car1, car2)); // -2
```

---

## **12. Summary Table**

| Feature | Syntax | Access | Description |
|---------|--------|--------|-------------|
| **Public Property** | `this.name = value` | Everywhere | Default accessibility |
| **Private Property** | `#name = value` | Inside class only | True privacy with `#` |
| **Protected Property** | `_name = value` | Convention only | Not enforced |
| **Constructor** | `constructor() {}` | Auto-called | Initializes instance |
| **Instance Method** | `methodName() {}` | Via instances | Object behavior |
| **Static Method** | `static method() {}` | Via class | Utilities, factories |
| **Getter** | `get prop() {}` | Like property | Computed values |
| **Setter** | `set prop(val) {}` | Like assignment | Validation |
| **Private Method** | `#method() {}` | Inside class only | Internal logic |
| **Inheritance** | `extends ParentClass` | - | Inherit properties/methods |
| **Super Constructor** | `super(args)` | In child constructor | Call parent constructor |
| **Super Method** | `super.method()` | In child methods | Call parent method |

---

## **13. `Public` vs `Private` vs `Protected`**

```javascript
class Example {
  publicField = 'everyone can access';
  #privateField = 'only inside class';
  _protectedField = 'convention (not enforced)';
  
  showAll() {
    console.log(this.publicField);     // ✅
    console.log(this.#privateField);   // ✅
    console.log(this._protectedField); // ✅
  }
}

const obj = new Example();
console.log(obj.publicField);     // ✅ Works
console.log(obj.#privateField);   // ❌ SyntaxError
console.log(obj._protectedField); // ✅ Works (shouldn't use)
```

**Comparison:**

| Type | Symbol | Enforced? | Use Case |
|------|--------|-----------|----------|
| **Public** | `this.prop` | - | General properties |
| **Private** | `#prop` | ✅ Yes | Sensitive data, internal logic |
| **Protected** | `_prop` | ❌ No | Inheritance (convention) |

---

## **14. `Instance` vs `Static`**

```javascript
class MyClass {
  instanceMethod() {
    console.log('Called on instance');
  }
  
  static staticMethod() {
    console.log('Called on class');
  }
}

const obj = new MyClass();

// ✅ Correct usage
obj.instanceMethod();
MyClass.staticMethod();

// ❌ Wrong usage
obj.staticMethod();       // Error
MyClass.instanceMethod(); // Error
```

**When to use Static:**

- Math utilities
- Factory functions
- Configuration constants
- Helper functions

---

## **15. Common Mistakes**

### `Mistake 1`: Forgetting `new`

```javascript
const person = Person('Alice');     // ❌ TypeError
const person = new Person('Alice'); // ✅ Correct
```

### `Mistake 2`: Not calling `super()` first

```javascript
class Child extends Parent {
  constructor(value) {
    this.value = value; // ❌ ReferenceError
    super();
  }
}

// ✅ Correct
class Child extends Parent {
  constructor(value) {
    super();           // Must be first!
    this.value = value;
  }
}
```

### `Mistake 3`: Accessing private outside class

```javascript
class Test {
  #secret = 'hidden';
}

const t = new Test();
console.log(t.#secret); // ❌ SyntaxError
```

### `Mistake 4`: Using `this` before `super()`

```javascript
class Child extends Parent {
  constructor() {
    console.log(this.name); // ❌ ReferenceError
    super();
  }
}

// ✅ Correct
class Child extends Parent {
  constructor() {
    super();
    console.log(this.name); // ✅ Works
  }
}
```

### `Mistake 5`: Multiple constructors

```javascript
class Wrong {
  constructor(name) {
    this.name = name;
  }
  
  constructor(age) { // ❌ SyntaxError
    this.age = age;
  }
}

// ✅ Use default parameters instead
class Correct {
  constructor(name = 'Unknown', age = 0) {
    this.name = name;
    this.age = age;
  }
}
```

---

## **16. Quick Reference**

### Basic Class Structure

```javascript
class ClassName {
  // Public field
  publicProp = 'value';
  
  // Private field
  #privateProp = 'secret';
  
  // Constructor
  constructor(param) {
    this.param = param;
  }
  
  // Instance method
  method() {
    return this.#privateProp;
  }
  
  // Static method
  static staticMethod() {
    return 'utility';
  }
  
  // Getter
  get computed() {
    return this.publicProp;
  }
  
  // Setter
  set value(val) {
    this.publicProp = val;
  }
  
  // Private method
  #privateMethod() {
    return 'internal';
  }
}
```

### Inheritance Structure

```javascript
class Parent {
  constructor(param1) {
    this.param1 = param1;
  }
  
  parentMethod() {
    return 'parent';
  }
}

class Child extends Parent {
  constructor(param1, param2) {
    super(param1);
    this.param2 = param2;
  }
  
  childMethod() {
    return super.parentMethod() + ' child';
  }
}
```

---

## **17. Real-World Examples**

### `Example 1`: User Authentication System

```javascript
class User {
  #password;
  
  constructor(username, password) {
    this.username = username;
    this.#password = this.#hash(password);
    this.isLoggedIn = false;
  }
  
  #hash(str) {
    return btoa(str); // Simple encoding
  }
  
  login(password) {
    if (this.#hash(password) === this.#password) {
      this.isLoggedIn = true;
      return true;
    }
    return false;
  }
  
  logout() {
    this.isLoggedIn = false;
  }
}

const user = new User('alice', 'secret123');
console.log(user.login('secret123')); // true
```

### `Example 2`: Shopping Cart

```javascript
class ShoppingCart {
  #items = [];
  
  addItem(product, quantity) {
    this.#items.push({ product, quantity });
  }
  
  removeItem(productName) {
    this.#items = this.#items.filter(
      item => item.product !== productName
    );
  }
  
  get total() {
    return this.#items.reduce(
      (sum, item) => sum + (item.product.price * item.quantity),
      0
    );
  }
  
  get itemCount() {
    return this.#items.length;
  }
}
```

### `Example 3`: Employee Management

```javascript
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }
  
  introduce() {
    return `Hi, I'm ${this.name}`;
  }
}

class Employee extends Person {
  static employeeCount = 0;
  
  constructor(name, age, position, salary) {
    super(name, age);
    this.position = position;
    this._salary = salary;
    Employee.employeeCount++;
  }
  
  get monthlySalary() {
    return this._salary / 12;
  }
  
  introduce() {
    return `${super.introduce()} and I work as ${this.position}`;
  }
  
  static getEmployeeCount() {
    return Employee.employeeCount;
  }
}

const emp1 = new Employee('Alice', 30, 'Developer', 60000);
const emp2 = new Employee('Bob', 25, 'Designer', 50000);

console.log(emp1.introduce());
console.log(Employee.getEmployeeCount()); // 2
```

---

## **18. Interview Tips**

**Q: What's the difference between class and function constructor?**

```javascript
// Function constructor (old way)
function Person(name) {
  this.name = name;
}
Person.prototype.greet = function() {
  return `Hi, ${this.name}`;
};

// Class (new way - ES6+)
class Person {
  constructor(name) {
    this.name = name;
  }
  
  greet() {
    return `Hi, ${this.name}`;
  }
}
```

**Key Differences:**

- Classes can't be called without `new`
- Class methods are non-enumerable
- Classes have built-in strict mode
- Classes support `extends` and `super` naturally

**Q: When to use static methods?**

- Utility functions (Math.max, Array.from)
- Factory patterns (User.createAdmin)
- Constants (Config.API_URL)

**Q: Why use private fields?**

- Data encapsulation
- Prevent external modification
- Hide implementation details
- Avoid naming conflicts

---

## **Key Takeaways**

✅ Classes are blueprints for objects

✅ `new` creates instances

✅ `constructor` initializes objects

✅ `extends` enables inheritance

✅ `super()` calls parent constructor (must be first)

✅ `super.method()` calls parent methods

✅ `static` methods belong to class, not instances

✅ `#` creates truly private fields/methods

✅ `_` is convention for "protected" (not enforced)

✅ Getters/setters control property access

---

**Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/) by Santosh Kumar Baliarsingh**
