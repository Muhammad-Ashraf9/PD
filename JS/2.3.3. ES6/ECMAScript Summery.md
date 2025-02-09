
# Day 1 
### Table of Contents
1. [[#ECMAScript Overview]]
2. [[#Key ES6+ Features]]
3. [[#JavaScript Classes]]
4. [[#Modules in JavaScript]]

---

### ECMAScript Overview
- **ECMAScript (ES)**: The standard on which JavaScript is based.
- **Versions**:
  - **ES 5 (2009)**: Introduced `strict mode`, `JSON`, and array methods like `forEach`.
  - **ES 6 (2015)**: Major update with `let`, `const`, arrow functions, classes, modules, promises, and more.
  - **ES 7+ (2016 and beyond)**: Added `async/await`, optional chaining, nullish coalescing, and more.

---

### Key ES 6+ Features

#### 1. `let` & `const` vs `var`
| Feature           | `var`                          | `let`                  | `const`                |
| ----------------- | ------------------------------ | ---------------------- | ---------------------- |
| **Hoisting**      | ✅ (initialized as `undefined`) | ❌ (Temporal Dead Zone) | ❌ (Temporal Dead Zone) |
| **Scope**         | Function-scoped                | Block-scoped           | Block-scoped           |
| **Reassignment**  | ✅                              | ✅                      | ❌                      |
| **Redeclaration** | ✅                              | ❌                      | ❌                      |

#### 2. Function Hoisting
| Function Type        | Hoisted?                      |
| -------------------- | ----------------------------- |
| Function Declaration | ✅ Fully Hoisted               |
| Function Expression  | ❌ Only name hoisted, not body |
| Arrow Function       | ❌ Not hoisted                 |

#### 3. Default Parameters
```js
function greet(name = "Guest") {
  console.log(`Hello, ${name}!`);
}
greet(); // Hello, Guest!
```

#### 4. Template Literals
```js
const name = "Alice";
console.log(`Hello, ${name}!`); // Hello, Alice!
```

#### 5. Spread & Rest Operators
- **Spread**: Expands an array/object.
- **Rest**: Collects multiple arguments into an array.
```js
const nums = [1, 2, 3];
console.log(...nums); // 1 2 3

function sum(...args) {
  return args.reduce((acc, val) => acc + val, 0);
}
console.log(sum(1, 2, 3, 4)); // 10
```

#### 6. Arrow Functions
| Feature                | Arrow Function  | Regular Function  |
| ---------------------- | --------------- | ----------------- |
| **`this` Binding**     | Lexically bound | Defined by caller |
| **`arguments` Object** | ❌ Not available | ✅ Available       |
| **Constructor?**       | ❌ No            | ✅ Yes             |
```js
	function sum(x,y) => x+y;
```
#### 7. Destructuring
```js
const user = { name: "Alice", age: 25 };
const { name, age } = user;
console.log(name, age); // Alice 25

const colors = ["red", "green", "blue"];
const [first, , third] = colors;
console.log(first, third); // red blue
```

#### 8. Object Property Shorthand & Computed Properties
```js
const name = "Alice";
const user = { name }; // Same as { name: name }

const key = "score";
const student = { [key]: 100 };
console.log(student.score); // 100
```

#### 9. Classes & Modules
```js
class Person {
  constructor(name) {
    this.name = name;
  }
  greet() {
    return `Hello, my name is ${this.name}`;
  }
}
const p1 = new Person("Alice");
console.log(p1.greet()); // Hello, my name is Alice
```

#### 10. Promises & Async/Await
```js
const myPromise = new Promise((resolve, reject) => {
  setTimeout(() => resolve("Success"), 1000);
});

myPromise.then(console.log).catch(console.error);

async function fetchData() {
  let data = await fetch("https://api.example.com");
  return data.json();
}
fetchData().then(console.log);
```

#### 11. Optional Chaining & Nullish Coalescing
```js
const user = { profile: { name: "Alice" } };
console.log(user?.profile?.name); // Alice
console.log(user?.address?.street); // undefined

const value = null;
console.log(value ?? "Default Value"); // Default Value
```

#### 12. New Data Types & Methods
| Feature               | Example                          |
|-----------------------|----------------------------------|
| `BigInt`              | `const big = 9007199254740991n;` |
| `Map` & `Set`         | `new Map()` & `new Set()`        |
| `Object.entries`      | `Object.entries({a:1,b:2})`      |
| `Array.flat()`        | `[1, [2, 3]].flat();`            |

---

### JavaScript Classes

#### 1. Defining a Class
```js
class Person {
  constructor(name, age) {
    this.name = name; // Public field
    this.age = age;   // Public field
  }
  print() {
    console.log(`Name: ${this.name}, Age: ${this.age}`);
  }
}

let p1 = new Person("Alice", 30);
console.log(p1.name); // Alice
p1.print(); // Name: Alice, Age: 30
```

#### 2. Private Fields (`#`)
```js
class Person {
  #id = 1; // Private field
  #name = "Unknown"; // Private field

  constructor(id, name) {
    this.#id = id;
    this.#name = name;
  }

  print() {
    console.log(`ID: ${this.#id}, Name: ${this.#name}`);
  }

  // Getters & Setters
  get id() {
    return this.#id;
  }
  set id(newId) {
    if (newId > 0) this.#id = newId;
  }
}
```

#### 3. Static Methods
```js
class MathUtil {
  static add(a, b) {
    return a + b;
  }
}
console.log(MathUtil.add(5, 10)); // 15
```

#### 4. Inheritance (`extends`, `super()`)
```js
class Person {
  constructor(name) {
    this.name = name;
  }
  greet() {
    return `Hello, I'm ${this.name}`;
  }
}

class Student extends Person {
  constructor(name, grade) {
    super(name); // Calls Person constructor
    this.grade = grade;
  }
  getDetails() {
    return `${super.greet()} and I am in grade ${this.grade}`;
  }
}

let s1 = new Student("Alice", "10th");
console.log(s1.getDetails()); // Hello, I'm Alice and I am in grade 10th
```

---

### Modules in JavaScript

#### 1. Exporting & Importing
- **Named Exports**:
  ```js
  // module1.js
  export const PI = 3.14;
  export function add(a, b) { return a + b; }
  export class Person {
    constructor(name) { this.name = name; }
  }
  ```
  ```js
  // module2.js
  import { PI, add, Person } from "./module1.js";
  console.log(add(2, 3)); // 5
  let p = new Person("Alice");
  console.log(p.name); // Alice
  ```

- **Default Export**:
  ```js
  // module1.js
  export default function sayHello() {
    console.log("Hello World");
  }
  ```
  ```js
  // module2.js
  import sayHello from "./module1.js";
  sayHello(); // Hello World
  ```

- **Import All**:
  ```js
  import * as m1 from "./module1.js";
  console.log(m1.PI); // 3.14
  ```

#### 2. Module Constraints
- Cannot import inside functions or conditionals.
- Imported members are read-only.
- Modules run in strict mode by default.

---

### Summary Table

| Feature             | Description                                                 |
|---------------------|-------------------------------------------------------------|
| **Classes**         | Encapsulation, Inheritance, Static Methods                  |
| **Public Fields**   | Accessible outside the class                                |
| **Private Fields**  | Declared with `#`, only accessible inside the class         |
| **Getters/Setters** | Control access to private fields                            |
| **Static Methods**  | Belong to the class, not instances                          |
| **Inheritance**     | `extends` for subclassing, `super()` for parent constructor |
| **Modules**         | Reusable code across files                                  |
| **Named Export**    | Export multiple items (`export {}`)                         |
| **Default Export**  | Only one per module (`export default`)                      |
| **Import All**      | `import * as alias from "./module.js";`                     |

---


# Day 2


### Table of Contents
1. [[#Self Study: Generators and Iterators]]
2. [[#JavaScript Classes]]
3. [[#Modules in JavaScript]]
4. [[#Summary Table]]

---

### Self Study: Generators and Iterators
- **Generators**: Functions that can be paused and resumed, yielding multiple values.
- **Iterators**: Objects that define a sequence and return values one at a time.

---

### JavaScript Classes

#### 1. **Class Basics**
- **Classes are not hoisted**: Must be defined before use.
- **Cannot be called like functions**: `Person()` ❌.
- **Constructor**: Initializes instance fields.
- **Public Fields**: Accessible outside the class.
- **Private Fields**: Declared with `#`, accessible only inside the class.
- **Methods**: Do not require the `function` keyword.
- **Static Members**: Belong to the class, not instances.

```js
class Person {
  constructor(name, age) {
    this.name = name; // Public field
    this.age = age;   // Public field
  }
  print() {
    console.log(`Name: ${this.name}, Age: ${this.age}`);
  }
}
```

#### 2. **Private Fields**
```js
class Person {
  #id = 1; // Private field
  #name = "Unknown"; // Private field

  constructor(id, name) {
    this.#id = id;
    this.#name = name;
  }

  print() {
    console.log(`ID: ${this.#id}, Name: ${this.#name}`);
  }

  // Getters & Setters
  get id() {
    return this.#id;
  }
  set id(newId) {
    if (newId > 0) this.#id = newId;
  }
}
```

#### 3. **Static Methods**
```js
class MathUtil {
  static add(a, b) {
    return a + b;
  }
}
console.log(MathUtil.add(5, 10)); // 15
```

#### 4. **Inheritance**
- **`extends`**: Creates a subclass.
- **`super()`**: Calls the parent constructor.

```js
class Person {
  constructor(name) {
    this.name = name;
  }
  greet() {
    return `Hello, I'm ${this.name}`;
  }
}

class Student extends Person {
  constructor(name, grade) {
    super(name); // Calls Person constructor
    this.grade = grade;
  }
  getDetails() {
    return `${super.greet()} and I am in grade ${this.grade}`;
  }
}

let s1 = new Student("Alice", "10th");
console.log(s1.getDetails()); // Hello, I'm Alice and I am in grade 10th
```

---

### Modules in JavaScript

#### 1. **Need for Modules**
- Avoid **naming conflicts**.
- Prevent **namespace pollution**.
- Support **code reusability**.

#### 2. **Exporting & Importing**
- **Named Exports**:
  ```js
  // module1.js
  export const PI = 3.14;
  export function add(a, b) { return a + b; }
  export class Person {
    constructor(name) { this.name = name; }
  }
  ```
  ```js
  // module2.js
  import { PI, add, Person } from "./module1.js";
  console.log(add(2, 3)); // 5
  let p = new Person("Alice");
  console.log(p.name); // Alice
  ```

- **Default Export**:
  ```js
  // module1.js
  export default function sayHello() {
    console.log("Hello World");
  }
  ```
  ```js
  // module2.js
  import sayHello from "./module1.js";
  sayHello(); // Hello World
  ```

- **Import All**:
  ```js
  import * as m1 from "./module1.js";
  console.log(m1.PI); // 3.14
  ```

#### 3. **Module Constraints**
- Cannot import inside functions or conditionals.
- Imported members are read-only.
- Modules run in strict mode by default.

---

### Summary Table

| Feature             | Description                                                 |
|---------------------|-------------------------------------------------------------|
| **Classes**         | Encapsulation, Inheritance, Static Methods                  |
| **Public Fields**   | Accessible outside the class                                |
| **Private Fields**  | Declared with `#`, only accessible inside the class         |
| **Getters/Setters** | Control access to private fields                            |
| **Static Methods**  | Belong to the class, not instances                          |
| **Inheritance**     | `extends` for subclassing, `super()` for parent constructor |
| **Modules**         | Reusable code across files                                  |
| **Named Export**    | Export multiple items (`export {}`)                         |
| **Default Export**  | Only one per module (`export default`)                      |
| **Import All**      | `import * as alias from "./module.js";`                     |


---

# Day 3

### 1. Synchronous vs Asynchronous Execution

- **Synchronous Code**
    
    - Executes line by line in the global execution context.
    - Example:
        
        ```js
        console.log("start");
        function myFunc() {
          console.log("myFunc");
        }
        myFunc();
        console.log("end");
        // Output: start, myFunc, end
        ```
        
- **Asynchronous Code**
    
    - Certain operations (e.g., timers, network requests) execute outside the normal flow.
    - Example using `setTimeout`:
        
        ```js
        console.log("start");
        setTimeout(() => {
          console.log("data loaded");
        }, 2000);
        console.log("end");
        // Output: start, end, data loaded (after 2 sec)
        ```
        

---

### 2. Global Execution Context, Event Loop, and Queues

- **Global Execution Context (GEC) & Execution Stack**
    
    - The GEC handles all top-level code.
    - Once the GEC is empty, the engine checks for asynchronous tasks.
- **Web APIs & Callback Queue**
    
    - Asynchronous functions (like `setTimeout`) are handled by Web APIs.
    - Completed callbacks are placed in the callback queue (FIFO) until the stack is free.
- **Event Loop**
    
    - Continuously monitors the execution stack and queues.
    - Moves tasks from the microtask queue (e.g., promise callbacks) and then the callback queue into the execution stack.
- **Microtask vs. Callback Queue**
    
    - **Microtask Queue:** Processes promise callbacks and mutation observers with higher priority.
    - **Callback Queue:** Processes callbacks from APIs like `setTimeout` once microtasks are complete.

---

### 3. Callback Hell

- **Problem:**
    
    - Excessive nesting of callbacks makes code difficult to read, maintain, and debug.
- **Examples & Solutions:**
    
    - **Nested Callbacks:** Each asynchronous function calls the next within its callback.
    - **Passing Function Names:** Functions are passed as parameters to manage flow, though nesting can still be problematic.

---

### 4. Promises

- **Definition:**
    
    - An object representing the eventual completion (or failure) of an asynchronous operation.
- **States:**
    
    - **Pending:** Initial state.
    - **Fulfilled (Resolved):** Operation completed successfully.
    - **Rejected:** Operation failed.
- **Usage:**
    
    - Use `.then()` to handle resolved values and `.catch()` for errors.
    - Promises can be chained to avoid deep nesting:
        
        ```js
        getAllStudents()
          .then(studentsArray => printAllStudents(studentsArray))
          .then(deptArray => displayDepartments(deptArray))
          .then(deptNames => displayDeptNames(deptNames))
          .catch(error => console.log(error));
        ```
        

---

### 5. Async/Await

- **Concept:**
    
    - Syntactic sugar over promises that allows asynchronous code to be written in a synchronous-like manner.
- **Usage:**
    
    - Mark functions with `async` to enable `await`.
    - Use `await` to pause execution until a promise resolves or rejects.
    - Error handling can be implemented using `try/catch`:
        
        ```js
        async function caller() {
          try {
            const stdArray = await getAllStudents();
            const deptArray = await printAllStudents(stdArray);
            const deptNames = await displayDepartments(deptArray);
            await displayDeptNames(deptNames);
          } catch (error) {
            console.log(error);
          } finally {
            console.log("finally");
          }
        }
        caller();
        ```
        

---

### 6. Fetch API

- **Overview:**
    
    - Modern API for performing network requests.
    - Returns a promise that resolves to a `Response` object.
- **Usage Example:**
    
    ```js
    async function loadPosts() {
      const response = await fetch("https://jsonplaceholder.typicode.com/users");
      const allPosts = await response.json();
      allPosts.forEach(post => {
        document.write(`<li>${post.name}</li>`);
      });
    }
    loadPosts();
    ```
    
- **Related Concepts:**
    
    - Other promise-based methods include `Promise.all`, `Promise.any`, and `Promise.race` for managing multiple asynchronous operations.
