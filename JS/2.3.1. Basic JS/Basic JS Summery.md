
# Day 1
## Table of Contents

- [[#JavaScript Overview]]
- [[#JavaScript Type System]]
    - [[#Primitive Types]]
    - [[#Object Types]]
    - [[#Language Objects vs Browser Objects vs DOM]]
- [[#Where to Write JavaScript]]
- [[#Loosely Typed]]
- [[#Variable Scope]]
- [[#Numbers in JavaScript]]
    - [[#Number Literals]]
    - [[#Number Methods]]
- [[#Type Coercion]]
- [[#Parsing Numbers]]
    - [[#Dialogs]]
    - [[#NaN]]
- [[#Number and + Algorithm]]
- [[#Document Write]]

---

## JavaScript Overview

> [!tip] JavaScript is a Scripting Language
> 
> - **Scripting Language**: Needs a host (e.g., browser, Node.js) to run.
> - **Interpreted Language**: Not compiled to machine code.
> - **Loosely Typed**: Variable type is determined by the value assigned to it.

> [!example] Example:
> 
> ```javascript
> var a; // a is undefined
> var a = 5; // a is number
> var a = "hello"; // a is string
> ```

---

## JavaScript Type System

### Primitive Types

> [!tip] Primitive types:
> 
> - Undefined
> - Number
> - String
> - Boolean
> - Null
> - Symbol
> - BigInt

### Object Types

> [!tip] Object types:
> 
> - Date
> - Array

### Language Objects vs Browser Objects vs DOM

> [!tip] Object Types Breakdown:
> 
> - **Language Objects**: String, Number, Boolean, Date, Array, Math, RegExp, Function, Object
> - **Browser Objects (BOM)**: Window, Navigator, Screen, History, Location, Document
> - **HTML Objects (DOM)**: Document, Element, Attribute, Text
> - **User-Defined Objects**: Any object created by the user

---

## Where to Write JavaScript

> [!tip] JavaScript can be written:
> 
> - **Inline**: Inside HTML tags
> - **Internal**: Inside `<script>` tag in the head
> - **External**: In a separate file with `.js` extension

```html
<script src="myScript.js"></script>
<script>
  document.getElementById("demo").innerHTML = "Hello JavaScript!";
</script>
```

---

## Loosely Typed

```javascript
var myVar; // undefined
console.log(typeof myVar); // "undefined"
myVar = "hamda";
console.log(typeof myVar); // "string"
myVar = 5.2;
console.log(typeof myVar); // "number"
myVar = null;
console.log(typeof myVar); // "object"
```

---

## Variable Scope

```javascript
var globalVar = 5; // Global Scope
function test() {
  var localVar = 10; // Function Scope
  console.log(localVar);
}
console.log(globalVar);
test();
```

---

## Numbers in JavaScript

### Number Literals

```javascript
var myNum = 1024;
var myNum2 = 0x400; // Hexadecimal
var myNum3 = 0b10000000000; // Binary
var myNum4 = 1e6; // Scientific notation
```

### Number Methods

```javascript
(111111.123).toFixed(1); // "111111.1"
Number.isInteger(5); // true
Number.isFinite(10 / 0); // false
```

> [!success] Use `Number.isFinite()` to check if a value is a number.

---

## Type Coercion

> [!tip] Type coercion implicitly converts values:
> 
> ```javascript
> "5" == 5; // true
> "5" + 5; // "55"
> 3 * "5"; // 15
> ```

---

## Parsing Numbers

```javascript
parseInt("1234"); // 1234
parseFloat("1234.5"); // 1234.5
```

### Dialogs

```javascript
var name = prompt("Enter your name", "Default");
alert("Hello " + name);
var confirmed = confirm("Are you sure?");
```

---

## NaN

- **Type**: `number`
- **Properties**: Not equal to anything, including itself.

```javascript
isNaN("123"); // false
isNaN("123A"); // true
```

---

## Number and + Algorithm

```javascript
Number("1234"); // 1234
+"1234"; // 1234
```

---

## Document Write

```javascript
document.writeln("<p>Hello</p>");
var arr = [1, 2, 3];
document.write("<table>");
for (var i = 0; i < arr.length; i++) {
  document.write("<tr><td>" + arr[i] + "</td></tr>");
}
document.write("</table>");
```


---

# Day 2

## Table of Contents

1. [[#Number Data Type]]
2. [[#String Data Type]]
    - [[#String Methods]]
3. [[#Array Data Type]]
    - [[#Key Array Methods]]
    - [[#Sorting Algorithm in JavaScript's sort() Method]]
4. [[#Math Object]]
5. [[#Boolean Data Type]]
6. [[#Date Object]]
7. [[#Functional Array Methods]]

---

## Number Data Type

```js
var myNum = 5523;
// Object
// toString(?radix)
myNum.toString(); // "5523"
```

> [!info] **Advantage of `parseInt()` vs `Number()`** The advantage of `parseInt()` is that it takes a **radix** as a second parameter. The radix specifies the numeral system to be used. For example:
> 
> ```js
> parseInt("10A5B", 16); // 68315
> parseInt("10A5Bg", 16); // 68315 (g is ignored)
> parseInt("10A5Bg", 17); // 1099483 (g is included in radix 17)
> ```
> 
> Radix range: 2 to 36.

> [!tip] **Using `toString(radix)`**
> 
> ```js
> (1024).toString(2); // "10000000000" (Binary)
> (1024).toString(16); // "400" (Hexadecimal)
> ```

---

## String Data Type

> [!danger] **String is Immutable** A string cannot be modified directly. Operations return a new string.

### String Methods

- `charAt(index)`: Access a character.
- `indexOf(searchValue, fromIndex)`: Find the first occurrence.
- `replace(searchValue, replaceValue)`: Replace a value with optional regular expressions.
- `split(separator)`: Split into an array.
- `substring(start, end)` / `slice(start, end)` / `substr(start, length)`: Extract portions of strings.

Example:

```js
var myStr = "Hello World";
myStr.replace(/hello/i, "Hi"); // "Hi World" (case-insensitive)
```

---

## Array Data Type

> [!tip] **Array Characteristics**
> 
> - Arrays can contain elements of different types.
> - Declared using literal `[]` or `new Array()`.

### Key Array Methods

1. `push()` and `unshift()`: Add elements.
2. `pop()` and `shift()`: Remove elements.
3. `splice(start, deleteCount, ...items)`: Add/remove elements.
4. `slice(start, end)`: Extract a portion (non-destructive).
5. `sort(callback)`: Sort elements.

### Sorting Algorithm in JavaScript's `sort()` Method

JavaScript's `Array.prototype.sort()` method uses the **Timsort algorithm**, a hybrid sorting algorithm derived from merge sort and insertion sort.

#### Characteristics of Timsort:

1. **Best Case (already sorted):** O(n)
2. **Average Case:** O(n log n)
3. **Worst Case:** O(n log n)

This makes Timsort efficient for partially sorted data, commonly encountered in real-world scenarios.

Example of custom sort:

```js
var arr = [100, 2, 20, 10];
arr.sort((a, b) => a - b); // Ascending order
arr.sort((a, b) => b - a); // Descending order
```

---

## Math Object

```js
Math.PI; // 3.14159...
Math.random(); // Random number between 0 and 1
Math.floor(Math.random() * 10); // Random integer between 0 and 9
```

### Generating a Random Integer in Range:

```js
function getRandomInt(min, max) {
  return Math.floor(Math.random() * (max - min + 1)) + min;
}
```

---

## Boolean Data Type

> Falsy Values:
> 
> - `false`
> - `0`
> - `""` (empty string)
> - `null`
> - `undefined`
> - `NaN`

Truthy values are all values that are **not falsy**.

Example:

```js
"hello" && "world"; // "world" (truthy chaining)
null || "fallback"; // "fallback"
```

---

## Date Object

```js
var today = new Date();
today.getFullYear(); // Current year
today.toLocaleDateString("en-US"); // "MM/DD/YYYY"
```

---

## Functional Array Methods

1. **`forEach(callback)`**
2. **`filter(predicate)`**: Returns a new array with elements satisfying a condition.
3. **`reduce(callback, initialValue)`**: Reduces an array to a single value.
4. **`map(callback)`**: Transforms every element in an array and returns a new array.

Example:

```js
var names = ['Mohamed','Ahmed','Hassan']
names.forEach(s =>{
	console.log(s);
});


var ages = [12, 17, 22, 30, 15, 19]; // Filter out ages under 18 const adults = ages.filter(age => age >= 18); console.log(adults);

var salaries = [1000, 2000, 3000];
var netSalaries = salaries.map(s => s * 0.75); // 25% tax deduction

const numbers = [1, 2, 3, 4, 5]; // Sum all numbers 
const sum = numbers.reduce((acc, current) => acc + current, 0); 
console.log(sum); // Output: 15
```


---

# Day 3

## Table of Contents

1. [[#Functions]]
    - [[#Function Statement]]
    - [[#Function Expression]]
    - [[#Constructor Function]]
2. [[#Models]]
    - [[#BOM]]
        - [[#Window Object]]
        - [[#Window Methods]]
    - [[#Timers]]
        - [[#Slide Show]]
    - [[#Location Object]]

---

## Functions

### Function Statement

```js
// function statement
function functionName(arguments) {
  // code block
} // returns undefined
// function call
functionName(arguments);
```

Key Points:

- Functions without a `return` statement return `undefined`.
- **No Overloading in JS**: Multiple functions with the same name override previous ones.

```js
function myFunc(a, b) {
  return a + b;
}
function myFunc(a, b, c) {
  return a + b + c;
}
myFunc(1, 2); // 3rd function overrides the 2nd
```

---

### Function Expression

```js
var sum = function (a, b) {
  return a + b;
}; // Expression
```

> [!tip] **Functions in JS Are First-Class Objects**
> 
> 1. Can be assigned to variables: `var sum = function () {}`
> 2. Passed as arguments: Callback functions.
> 3. Returned from functions: Closures.
> 4. Assigned as properties: Object methods.

Example:

```js
var student = {
  name: "Ali",
  print: function () {
    return this.name;
  },
};
student.print(); // "Ali"
```

---

### Constructor Function

> [!tip] No classes in ES5; use constructor functions to create objects.

```js
function Person(id) {
  this.id = id; // Public field
  this.print = function () {
    return this.id;
  };
}
var person1 = new Person(1);
person1.id; // 1
person1.print(); // 1
```

---

## Models

### BOM (Browser Object Model)

---

#### Window Object

> [!tip] `window` is the global object in the browser.  
> All browser objects (e.g., `alert`, `prompt`, `document`) are properties or methods of `window`.

Example:

```js
window.alert("Hello World"); // Same as alert("Hello World");
```

#### Accessing Object Properties

```js
var student = {
  name: "Ahmed",
  age: 20,
};
student.name; // "Ahmed"
student["name"]; // "Ahmed"

// Looping through properties
for (var prop in student) {
  console.log(prop); // Keys
  console.log(student[prop]); // Values
}
```

---

#### Window Methods

```js
// Opening a new window
var childWindow = open("child.html", "", "width=300,height=300");

// Scrolling and moving
childWindow.scrollTo(0, 100);
childWindow.moveBy(50, 50);

// Closing a window
childWindow.close();
```

---

### Timers

> [!tip] **`setInterval` and `setTimeout`**
> 
> - `setInterval`: Repeats execution at specified intervals.
> - `setTimeout`: Executes after a delay.
> - **Not accurate** due to JS being single-threaded.

Example:

```js
function startClock() {
  setInterval(() => {
    console.log(new Date().toLocaleTimeString());
  }, 1000);
}
```

#### Slide Show Example

```js
var images = ["img1.jpg", "img2.jpg", "img3.jpg"];
var index = 0;
function nextImage() {
  index = (index + 1) % images.length;
  document.getElementById("img").src = images[index];
}
function startSlideShow() {
  setInterval(nextImage, 2000);
}
```

---

### Location Object

> [!tip] **Location Properties**
> 
> - `location.href`: Full URL.
> - `location.origin` Base 
> - `location.search`: Query string.
> - `location.reload()`: Reload the page.

Example:

```js
// Access query parameters
var params = location.search.substr(1).split("&"); // ["id=1", "name=Ali"]
```

---

### Lab Tasks

- **Slide Show**
    - Create a basic slideshow using timers and DOM manipulation.

---

# Day 4: DOM

## Table of Contents

1. [[#DOM Overview]]
    - [[#JQuery Introduction]]
    - [[#Document Object]]
    - [[#Node Types]]
2. [[#Accessing DOM Elements]]
    - [[#Collections (images, forms, links)]]
    - [[#Getters (getElementById, getElementsByClassName)]]
    - [[#querySelector]]
3. [[#CRUD Operations on DOM]]
    - [[#innerHTML, innerText, value]]
    - [[#Class Manipulation (className, classList)]]
    - [[#Element Creation]]
    - [[#insertAdjacentHTML]]
    - [[#Remove Elements]]
4. [[#Cookies in JavaScript]]
5. [[#Tasks]]
    - [[#Slide Show]]
    - [[#Form Saved in Cookie]]
    - [[#Dynamic Table Operations]]
    - [[#Todo List]]

---

## DOM Overview

> [!tip] **Document Object Model**
> 
> - The DOM represents the HTML document as a tree of objects.
> - Allows you to access and manipulate HTML elements programmatically.

---

### JQuery Introduction

> [!tip] **JQuery Overview**
> 
> - Enables selecting and manipulating DOM elements using CSS selectors and methods.
> - More details to follow later.

---

### Document Object

> [!info] **Structure of the Document Object**
> 
> - The document object is the root node of the HTML document.
> - The hierarchy is structured as:
>     - `<html>` contains `<head>` and `<body>`.
>     - `<body>` contains elements like `<div>`, `<h1>`, etc.
> 
> Example:

```html
<html>
  <head>
    <title>ITI</title>
  </head>
  <body>
    <div>
      <p>pone</p>
      <p>ptwo</p>
    </div>
	<div>
	  <p>pone</p>
	  <p>ptwo</p>
	</div>
  </body>
</html>
```

![[Pasted image 20250128122058.png]]

---

### Node Types

> [!tip] **Node Types**
> 
> - **Element Nodes**: Represent HTML elements.
> - **Attribute Nodes**: Represent HTML attributes.
> - **Text Nodes**: Represent the text inside elements.
> - **Comment Nodes**: Represent comments in the HTML.

Example:

```js
document.childNodes[0].nodeType; // 1 => Element
document.childNodes[0].childNodes[1].nodeType; // 3 => Text
NodeTypes = 
{
	"Text"   :   3,
	"Comment":   8,
	"Attribute": 4,

}
```

> [!warning] **Whitespace in the DOM**
> 
> - Spaces and new lines are also treated as text nodes.
> - Any changes to the HTML structure (like adding or removing spaces) may affect the DOM indexes.

---

## Accessing DOM Elements

## Nodes

- `childNodes`
- `nodeType`
- `parentNode`
- `nextSibling`
- `nextElementSibling`
- `previousSibling`
- `previousElementSibling`
- `attributes`
- `children`
- `elements`
- `options`
- `firstChild`
- `firstElementChild`

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>DOM Nodes Example</title>
</head>
<body>
  <div id="parent" class="container" data-role="main">
    <!-- A comment -->
    <p id="child1">Child 1</p>
    <p id="child2">Child 2</p>
    <span id="child3">Child 3</span>
  </div>

  <form id="myForm">
    <input type="text" name="username" />
    <input type="password" name="password" />
    <select id="dropdown">
      <option value="1">Option 1</option>
      <option value="2">Option 2</option>
    </select>
  </form>

  <script>
    const parent = document.getElementById("parent");
    const child1 = document.getElementById("child1");
    const dropdown = document.getElementById("dropdown");
    const form = document.getElementById("myForm");

    // Accessing various properties
    console.log("childNodes:", parent.childNodes); // Includes text, comment, and element nodes
    console.log("children:", parent.children); // Only element nodes
    console.log("nodeType (child1):", child1.nodeType); // 1 (element)
    console.log("parentNode (child1):", child1.parentNode); // Parent element
    console.log("nextSibling (child1):", child1.nextSibling); // Text node or comment
    console.log("nextElementSibling (child1):", child1.nextElementSibling); // <p>Child 2</p>
    console.log("previousSibling (child2):", child1.nextElementSibling.previousSibling); // Text node
    console.log("previousElementSibling (child2):", child1.nextElementSibling.previousElementSibling); // <p>Child 1</p>
    console.log("attributes (parent):", parent.attributes); // All attributes of the parent div
    console.log("elements (form):", form.elements); // All form controls (inputs, select, etc.)
    console.log("options (dropdown):", dropdown.options); // All options in the select element
    console.log("firstChild (parent):", parent.firstChild); // First node (may include comments/text)
    console.log("firstElementChild (parent):", parent.firstElementChild); // First element (<p>Child 1</p>)
  </script>
</body>
</html>

```

## Summary Table

| **Property**                 | **Functionality**                                                                        |
| ---------------------------- | ---------------------------------------------------------------------------------------- |
| **`childNodes`**             | `Returns a NodeList of all child nodes (elements, text, comments).`                      |
| **`nodeType`**               | `Returns the type of the node (e.g., 1 for elements, 3 for text, 8 for comments).`       |
| **`parentNode`**             | `Returns the parent node of the specified node.`                                         |
| **`nextSibling`**            | `Returns the next sibling node (can be text or comment).`                                |
| **`nextElementSibling`**     | `Returns the next sibling element (skips text and comments).`                            |
| **`previousSibling`**        | `Returns the previous sibling node (can be text or comment).`                            |
| **`previousElementSibling`** | `Returns the previous sibling element (skips text and comments).`                        |
| **`attributes`**             | `Returns a NamedNodeMap of the element’s attributes.`                                    |
| **`children`**               | `Returns an HTMLCollection of the element’s child elements (ignores text and comments).` |
| **`elements`**               | `Returns an HTMLCollection of form controls inside a <form>.`                            |
| **`options`**                | `Returns an HTMLCollection of all <option> elements in a <select>.`                      |
| **`firstChild`**             | `Returns the first child node (can include text or comments).`                           |
| **`firstElementChild`**      | `Returns the first child element (skips text and comments).`                             |


### Collections (images, forms, links)

```js
document.images; // HTMLCollection of all <img> elements
document.forms; // HTMLCollection of all <form> elements
document.links; // HTMLCollection of all <a> elements
```

Example:

```js
document.images[0].src = "newImage.png"; // Change the first image's source
document.links[0].innerText = "Updated Link Text"; // Change link text
```

---

### Getters (getElementById, getElementsByClassName)

> [!bug] `document.getElementsByClassName()` , `document.getElementsByName ()`, 
`document.getElementsByName()`, returns a collection, not a single element.  
> Always access elements with an index if targeting a specific one.

Examples:

```js
var element = document.getElementById("id");
var collection = document.getElementsByClassName("className")[0];
```

---

### querySelector: by far the best

```js
document.querySelector(".className"); // Returns the first match
document.querySelectorAll(".className"); // Returns all matches as a NodeList
```

---

## CRUD Operations on DOM

### innerHTML, innerText, value

```js
// innerHTML: Gets or sets the HTML content
document.getElementById("div1").innerHTML = "<h1>New HTML</h1>";

// innerText: Gets or sets the text content
document.getElementById("div1").innerText = "Plain Text";

// value: Gets or sets the value of input elements
document.querySelector("input[type='text']").value = "New Value";
```

---

### Class Manipulation (className, classList)

```js
var element = document.getElementById("target");

// Add class
element.classList.add("newClass");

// Remove class
element.classList.remove("newClass");

// Toggle class
element.classList.toggle("toggleClass");
```

---

### Element Creation

```js
// Create elements dynamically
var newDiv = document.createElement("div");
var newText = document.createTextNode("Hello World");

// Append child nodes
newDiv.appendChild(newText);
document.body.appendChild(newDiv);
```

---

### insertAdjacentHTML

```js
// Insert HTML at specific positions
document.getElementById("target").insertAdjacentHTML("beforeend", "<p>New Paragraph</p>");
```

---

### Remove Elements

```js
// Remove an element
document.getElementById("target").remove();

// Remove a child element
var parent = document.getElementById("parent");
var child = document.getElementById("child");
parent.removeChild(child);
```

---

## Cookies in JavaScript

> [!warning] **Cookies**
> 
> - Small pieces of data stored in the browser. Stored Per Domain meaning you have cookie file for `facebook` or `google`
> - Used for session management, personalization, and tracking.
> - Limited to `4KB` in size.
> - Cookie is Sent With each Request

```js
// Set a cookie
document.cookie = "username=John; expires=Fri, 31 Dec 2024 23:59:59 GMT";

// Read cookies
console.log(document.cookie); // "username=John"

// Delete a cookie
document.cookie = "username=; expires=Thu, 01 Jan 1970 00:00:00 UTC;";
```

---

## Tasks

### Slide Show

- Create a slideshow with **next**, **previous**, and **start/stop** controls.

```js
var images = ["img1.jpg", "img2.jpg", "img3.jpg"];
var index = 0;

function showNextImage() {
  index = (index + 1) % images.length;
  document.getElementById("slide").src = images[index];
}

function startSlideshow() {
  setInterval(showNextImage, 2000);
}
```

---

### Form Saved in Cookie

1. Create a form that stores user input in cookies.
2. Retrieve and display the stored data upon revisiting the page.

---

### Dynamic Table Operations

1. Add rows to a table dynamically based on user input.
2. Include a delete option for rows.

Example:

```js
function addRow() {
  var table = document.getElementById("table");
  var row = table.insertRow();
  var cell1 = row.insertCell(0);
  var cell2 = row.insertCell(1);

  cell1.innerHTML = "New Row";
  cell2.innerHTML = "Data";
}

function deleteRow() {
  var table = document.getElementById("table");
  table.deleteRow(-1); // Deletes the last row
}
```

---

### Todo List

1. Create a dynamic to-do list with add, remove, and edit functionality.
2. Store the list in cookies or local storage.



---

# Day 5: Events

## Table of Contents

1. [[#Event Handling Model]]
2. [[#Selectors Specificity]]
3. [[#Events Overview]]
    - [[#Event Object (this vs target)]]
    - [[#Separation of Concerns]]
    - [[#Event Registration in JavaScript]]
4. [[#Mouse Events]]
5. [[#Keyboard Events]]
6. [[#Propagation]]
    - [[#Bubbling vs Capture]]
    - [[#Stop Propagation vs Immediate Propagation]]
7. [[#Regular Expressions (Regex)]]
8. [[#Form Validation]]

---

## Event Handling Model

> [!tip] **Event Registration** Registering an event handler to an event source:
> 
> ```html
> <button onclick="myFunction()">Click me</button>
> ```
> 
> > [!warning] Avoid registering events directly in HTML. Use JavaScript for separation of concerns.

> [!tip] **Event Firing** Or **Event Dispaching** Firing an event notifies all registered handlers.

> [!tip] **Event Handling** Capturing an event, processing it, and responding.

---

## Selectors Specificity

> [!tip] **CSS Selector Weight**
> 
> - Lowest: `*` → `0`
> - Element: `10`
> - Class: `100`
> - ID: `1000`
> - Inline: `10,000`
> - `!important`: Infinity

Example:

```html
<p id="para1" class="myClass" style="color: black">Hello World!</p>
```

Result: Inline style (`black`) overrides others.

---

## Events Overview

### Event Object (`this` vs `target`)

```js
function handleEvent(e) {
  console.log(this); // The element listening to the event
  console.log(e.target); // The element that fired the event
}
```

> [!info] **Key Difference**
> 
> - `this`: Refers to the element listening for the event.
> - `target`: Refers to the element that triggered the event.

![[Pasted image 20250129121445.png]]
---

### Separation of Concerns

> [!faq] **Separate HTML and JS** Always separate HTML structure and JS behavior:

```html
<!-- Avoid this -->
<button onclick="handleClick()">Click Me</button>

<!-- Preferred -->
<button id="btn1">Click Me</button>
<script>
  document.getElementById("btn1").addEventListener("click", handleClick);
</script>
```

---

### Event Registration in JavaScript

#### Using `addEventListener`

```js
document.getElementById("btn").addEventListener("click", function (e) {
  console.log(e.target.value); // Access event properties
});
```

#### Remove Event Listener

```js
function handleEvent() {
  console.log("Event triggered");
}
document.getElementById("btn").addEventListener("click", handleEvent);
// Remove the handler
document.getElementById("btn").removeEventListener("click", handleEvent);
```

---

## Mouse Events

```html
<div style="width: 200px; height: 200px; border: 1px solid black;"></div>
```

Example:

```js
document.querySelector("div").addEventListener("mousemove", function (e) {
  this.innerHTML = `x: ${e.clientX}, y: ${e.clientY}`;
  this.style.backgroundColor = `rgb(${e.clientX}, ${e.clientY}, 100)`;
});
```

---

## Keyboard Events

> [!tip] **Key Events**
> 
> - `keydown`: Fires when a key is pressed down.
> - `keyup`: Fires when a key is released.
> - `keypress`: Deprecated (use `keydown`).

Example:

```js
document.querySelector("input").addEventListener("keydown", function (e) {
  console.log(`Key pressed: ${e.key}, Code: ${e.code}`);
});
```

> [!warning] **Prevent Non-Digit Input**

```js
document.querySelector("input").addEventListener("keydown", function (e) {
  if (e.key < "0" || e.key > "9") {
    e.preventDefault();
  }
});
```

---

## Propagation

### Bubbling vs Capture

> [!tip] **Propagation Phases**
> 
> 1. Capture: From window to target.
> 2. Target: Event reaches the target element.
> 3. Bubbling: From target back to window.

Set event direction:

```js
element.addEventListener("click", handler, true); // Capture phase
element.addEventListener("click", handler, false); // Bubbling phase
```

---

### Stop Propagation vs Immediate Propagation

- `stopPropagation()`: Stops the event from bubbling or capturing further.
- `stopImmediatePropagation()`: Prevents other listeners on the same element from firing.

Example:

```js
function handler1(e) {
  console.log("Handler 1");
}
function handler2(e) {
  e.stopImmediatePropagation();
  console.log("Handler 2");
}
function handler3(e) {
  console.log("Handler 3");
}

element.addEventListener("click", handler1);
element.addEventListener("click", handler2);
element.addEventListener("click", handler3);
// Only Handler 1 and Handler 2 will execute.
```

---

## Regular Expressions (Regex)

> [!info] **Regex Basics**
> 
> - **Literal**: `/pattern/`
> - **Constructor**: `new RegExp("pattern")`
> - Flags: `i` (case insensitive), `g` (global), `m` (multiline)

```js
var pattern = /^[a-zA-Z]{4,6}$/;
console.log("hello".match(pattern)); // ["hello"]
console.log(pattern.test("hello")); // true
```

---

## Form Validation

Example:

```js
document.querySelector("form").addEventListener("submit", function (e) {
  if (!document.getElementById("username").value.match(/^[a-zA-Z]{4,6}$/)) {
    e.preventDefault(); // Cancel submission
    alert("Invalid username!");
  }
});
```

> [!info] **Validation Triggers**
> 
> - `blur`: Validate when the field loses focus.
> - `submit`: Validate when the form is submitted.
> - `reset`: Confirm before resetting the form.

