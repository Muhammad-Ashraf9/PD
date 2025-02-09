
# Day 1
## Table of Contents
1. [[#Global Namespace Pollution]]
2. [[#IIFE (Immediately Invoked Function Expression)]]
3. [[#Closure]]
4. [[#Explicit Binding (call, apply, bind)]]
5. [[#Constructor Function]]
6. [[#Factory Function]]

---


#### Global Namespace Pollution
- **Problem**: Multiple JavaScript files can pollute the global namespace, leading to variable and function conflicts.
- **Example**:
  ```js
  // fileone.js
  var firstName = "ahmed-fileone";
  // filetwo.js
  var firstName = "nasr-filetwo"; // Overwrites firstName from fileone.js
  ```
- **Solution**: Use **IIFE** to encapsulate code and avoid global pollution.

#### IIFE (Immediately Invoked Function Expression)
- **Purpose**: Encapsulate code to avoid polluting the global namespace.
- **Syntax**:
  ```js
  (function () {
    var firstName = "ahmed-fileone"; // Local scope
  })();
  ```
- **Example**:
  ```js
	var namesArray = ["Ahmed","Mohamed","Ibraheem"];
	for(var i = 0; i < namesArray.length; i++)
	{
		(function(_names){
			setTimeout(function(){
			console.log(_names)
			},5000);
		})(namesArray[i]);
	}
  ```

#### Closure
- **Definition**: A function that retains access to its lexical scope even after the outer function has finished execution.
- **Use Case**: Encapsulate state and avoid global variables.
- **Example**:
  ```js
  function increaseCounter() {
    var counter = 0;
    return function () {
      counter++;
      console.log(counter);
    };
  }
  var caller = increaseCounter();
  caller(); // 1
  caller(); // 2
  ```

#### Explicit Binding (call, apply, bind)
- **Purpose**: Explicitly set the `this` value for a function.
- **Methods**:
```js
var myObject1 = {
	id: 1,
	name : "Mohamed",
    print:function() { console.log( this.id + " / " + this.name)}
};
var myObject2 = {
	id: 2,
	name : "Mostafa",
	print:function() { console.log( this.id + " | " + this.name)}
};
```


  - **call**: Calls a function with a given `this` value and arguments.
    ```js
    myObject1.print.call(myObject2); // Calls print with myObject2 as `this`
    ```
  - **apply**: Similar to `call`, but accepts arguments as an array.
    ```js
    Math.max.apply(null, [1, 2, 3]); // 3
    ```
  - **bind**: Returns a new function with a bound `this` value.
    ```js
	var myObject3 = {
		id: 3,
		name : "Ali"
	};
	var myObject3Print = myObject1.print.bind(myObject3);
	myObject3Print();
    ```

#### Constructor Function
- **Purpose**: Create objects with shared properties and methods.
- **Syntax**:
  ```js
  function Person(name, id) {
    this.name = name;
    this.id = id;
    this.print = function () {
      console.log(this.name + " " + this.id);
    };
  }
  var pone = new Person("ahmed", 1); // Creates a new Person object
  ```
- **Instance Check**:
  ```js
  pone instanceof Person; // true
  ```

#### Factory Function
- **Purpose**: Create objects without using the `new` keyword.
- **Syntax**:
  ```js
  function Person(id, name) {
    return {
      id: id,
      name: name,
      print: function () {
        console.log(this.id + " " + this.name);
      },
    };
  }
  var pone = Person(1, "ahmed"); // Creates a new object
  ```
- **Instance Check**:
  ```js
  pone instanceof Person; // false (factory functions do not create instances of a class)
  ```

---

### Lab: Simple DataTables. Net
- **Objective**: Implement a simple DataTables. Net example.
- **Steps**:
  1. Include the DataTables. Net library in your HTML.
  2. Create a table and initialize DataTables.
  3. Add data dynamically using JavaScript.

```html
<!DOCTYPE html>
<html>
  <head>
    <link rel="stylesheet" type="text/css" href="https://cdn.datatables.net/1.10.21/css/jquery.dataTables.css">
    <script type="text/javascript" src="https://code.jquery.com/jquery-3.5.1.js"></script>
    <script type="text/javascript" src="https://cdn.datatables.net/1.10.21/js/jquery.dataTables.min.js"></script>
  </head>
  <body>
    <table id="example" class="display" style="width:100%">
      <thead>
        <tr>
          <th>Name</th>
          <th>Position</th>
          <th>Office</th>
          <th>Age</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>Tiger Nixon</td>
          <td>System Architect</td>
          <td>Edinburgh</td>
          <td>61</td>
        </tr>
        <!-- Add more rows as needed -->
      </tbody>
    </table>
    <script>
      $(document).ready(function () {
        $('#example').DataTable();
      });
    </script>
  </body>
</html>
```

---

# Day 2 

### Table of Contents
1. [[#Object object Methods]]
2. [[#Object Prototypes]]
3. [[#Prototype Chain]]
4. [[#User-Defined Constructor Functions]]
5. [[#Inheritance]]
6. [[#Arguments with Arrays]]
7. [[#Freeze and Seal]]

---

### Object object Methods
- **Methods**:
	- `defineproperty`, `defineproperties`
  ```js
  var arr = [1,2,3,4];  // arr is an object of type array (Array)
  arr.push(5);          // push() ==> Called instance method
  
  Array.isArray();             // isArray() ==> Called Type Method / Static Method
  ```
- **Accessors**:
	- `writable`, `enumerable`, `configurable`
  ```js
   var test = {};
   Object.defineProperty( test, "Id",{
   		value:200,
   		enumerable:true,
   		writable:true,
   		configurable: true
   });
   
  ```


### Object Prototypes

![[Pasted image 20250202161703.png]]
- **Object**: The base object for all objects in JavaScript.
  ```js
  var myObj = {};
  myObj.__proto__; // Refers to Object.prototype
  Object.prototype; // Contains default methods like toString, hasOwnProperty, etc.
  ```
- **Prototype Chain**: Objects inherit properties and methods from their prototype.
  ```js
  test.toString(); // Searches for toString in test, then test.__proto__, and so on.
  ```

### Prototype Chain
- **Prototype Inheritance**: Objects can inherit properties and methods from other objects.
  ```js
  var test1 = { id: 1, name: "ahmed" };
  var test2 = { address: "cairo" };
  test2.__proto__ = test1; // test2 inherits from test1
  test2.id; // 1 (inherited from test1)
  ```

### User-Defined Constructor Functions
- **Constructor Functions**: Used to create objects with shared properties and methods.
  ```js
  function Student(id, name) {
    this.id = id;
    this.name = name;
  }
  Student.prototype.print = function () {
    console.log(this.id, this.name);
  };
  var std1 = new Student(1, "ahmed");
  std1.print(); // 1 "ahmed"
  ```

### Inheritance
- **Classical Inheritance**: Child prototype is set to an instance of the parent.
  ```js
  Student.prototype = new Person();
  ```
- **Prototypal Inheritance**: Child prototype is set to the parent prototype.
  ```js
  Student.prototype = Object.create(Person.prototype);
  Student.prototype.constructor = Student; // Fix constructor reference
  ```

### Arguments with Arrays
- **Arguments Object**: A special object available within functions, similar to an array but not an array.
  ```js
  function myfun() {
    arguments.__proto__ = Array.prototype; // Enable array methods
    arguments.forEach(function (item) {
      console.log(item);
    });
  }
  myfun(1, 2, 3, 4, 5); // 1 2 3 4 5
  ```

### Freeze and Seal
- **Object.Seal ()**: Prevents adding or deleting properties but allows modifying existing ones.
  ```js
  Object.seal(myObj);
  myObj.name = "ali"; // Allowed
  delete myObj.name; // Not allowed
  ```
- **Object.Freeze ()**: Prevents adding, deleting, or modifying properties.
  ```js
  Object.freeze(myObj);
  myObj.name = "ali"; // Not allowed
  delete myObj.name; // Not allowed
  ```

---

# Day 3
### Table of Contents
8. [[#AJAX Overview]]
9. [[#XMLHttpRequest]]
10. [[#Loading Text, HTML, JSON, and XML]]
11. [[#JS Object VS. JSON ]]
12. [[#LocalStorage]]
13. [[#Associative Arrays]]

---

### AJAX Overview
- **Client-Server Model**: The client sends a request to the server, and the server sends a response back.
- **AJAX**: Asynchronous JavaScript and XML allows for asynchronous communication with the server without reloading the page.
- **Synchronous vs Asynchronous**:
  - **Synchronous**: Blocks further execution until the request is completed.
  - **Asynchronous**: Allows multiple tasks to run concurrently.
  - **Note:** later you will deal with `fetch` and AJAX calls using `jquery`

### XMLHttpRequest
- **XMLHttpRequest Object**: Used to make AJAX calls.
  ```js
  var xhr = new XMLHttpRequest();
  xhr.open("GET", "file.txt", true); // Method, URL, Async
  xhr.send();
  xhr.onreadystatechange = function () {
    if (xhr.readyState === 4 && xhr.status === 200) {
      console.log(xhr.responseText); // Handle response
    }
  };
  ```
- **readyState**:
  - 0: Request not initialized.
  - 1: Server connection established.
  - 2: Request received.
  - 3: Processing request.
  - 4: Request finished and response is ready.
- **status**: HTTP status codes (e.g., 200 for success, 404 for not found).

### Loading Text, HTML, JSON, and XML
- **Loading Text**:
  ```js
  xhr.open("GET", "file.txt", true);
  xhr.onreadystatechange = function () {
    if (xhr.readyState === 4 && xhr.status === 200) {
      document.getElementById("targetDiv").innerText = xhr.responseText;
    }
  };
  ```
- **Loading HTML**:
  ```js
  xhr.open("GET", "file.html", true);
  xhr.onreadystatechange = function () {
    if (xhr.readyState === 4 && xhr.status === 200) {
      document.getElementById("targetDiv").innerHTML = xhr.responseText;
    }
  };
  ```
- **Loading JSON**:
  ```js
  xhr.open("GET", "file.json", true);
  xhr.onreadystatechange = function () {
    if (xhr.readyState === 4 && xhr.status === 200) {
      var data = JSON.parse(xhr.responseText);
      console.log(data);
    }
  };
  ```
- **Loading XML**:
  ```js
  xhr.open("GET", "file.xml", true);
  xhr.onreadystatechange = function () {
    if (xhr.readyState === 4 && xhr.status === 200) {
      var xmlDoc = xhr.responseXML;
      console.log(xmlDoc);
    }
  };
  ```

## JS Object VS. JSON 
- **JS object:**
- **JSON `Javascript Object Notation`**

```js
var std = {
	Id:100,
	Name:"Ahmed",
	State: true
	}
	
var stdJSON = {
	"Id":100,
	"Name":"Ahmed",
	"State": true
	}
	
	var newStdJSON = JSON.stringfy(std);
	var stdJObject = JSON.parse(stdJSON);
```
### LocalStorage
- **LocalStorage**: Stores data in the browser without expiration.
  ```js
  localStorage.setItem("key", "value"); // Save data
  var value = localStorage.getItem("key"); // Retrieve data
  localStorage.removeItem("key"); // Remove data
  ```
- **Example**:
  ```js
  localStorage.setItem("ourObjects", JSON.stringify(data)); // Save JSON data
  var ourObjects = JSON.parse(localStorage.getItem("ourObjects")); // Retrieve and parse JSON data
  ```

### Associative Arrays
- **Associative Arrays**: Arrays with named keys instead of numeric indices.
  ```js
  var arr = [];
  arr["id"] = 100;
  arr["name"] = "ahmed";
  arr.length; // 0 (not counted as part of the array)
  ```
- **Iteration**:
  ```js
  for (var key in arr) {
    console.log(key, arr[key]); // Access keys and values
  }
  ```

---

### Lab: AJAX and LocalStorage
- **Objective**: Implement an AJAX call to fetch data from a JSON file and store it in `localStorage`.
- **Steps**:
  1. Create an AJAX request to fetch JSON data.
  2. Parse the JSON data and store it in `localStorage`.
  3. Retrieve the data from `localStorage` and display it in a table.

```html
<!DOCTYPE html>
<html>
  <head>
    <title>AJAX and LocalStorage Lab</title>
  </head>
  <body>
    <div id="targetDiv"></div>
    <input type="button" value="Load Data" />
    <script>
      window.addEventListener("load", function () {
        document
          .getElementById("loadButton")
          .addEventListener("click", function () {
            var xhr = new XMLHttpRequest();
            xhr.open("GET", "data.json", true);
            xhr.send();
            xhr.onreadystatechange = function () {
              if (xhr.readyState === 4 && xhr.status === 200) {
                var data = JSON.parse(xhr.responseText);
                localStorage.setItem("ourObjects", JSON.stringify(data));
                displayData(data);
              }
            };
          });

        function displayData(data) {
          var table = document.createElement("table");
          var headers = Object.keys(data[0]);
          var headerRow = document.createElement("tr");
          headers.forEach(function (header) {
            var th = document.createElement("th");
            th.innerText = header;
            headerRow.appendChild(th);
          });
          table.appendChild(headerRow);

          data.forEach(function (item) {
            var row = document.createElement("tr");
            headers.forEach(function (header) {
              var cell = document.createElement("td");
              cell.innerText = item[header];
              row.appendChild(cell);
            });
            table.appendChild(row);
          });

          document.getElementById("targetDiv").appendChild(table);
        }
      });
    </script>
  </body>
</html>
```
